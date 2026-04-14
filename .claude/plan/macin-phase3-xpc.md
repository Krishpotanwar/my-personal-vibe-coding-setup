# Implementation Plan: Macin Download Manager — Phase 3 (XPC Integration)

## Task Type
- [x] Fullstack (Swift XPC service + Rust FFI link + SwiftUI ViewModel update)

---

## Context Summary

**Phase 1 done:** SwiftUI frontend with mock `DownloadViewModel` (Task-based simulation timer).
**Phase 2 done:** Rust engine at `MacinRustEngine/` — staticlib, FFI surface in `MacinEngine.h`, WebSocket on `127.0.0.1:54321`.

**Phase 3 goal:** Wire the two halves together.
- New Xcode target: `DownloadEngineXPC` (XPC Service)
- XPC service hosts the Rust staticlib via FFI
- Main app talks to XPC service via `NSXPCConnection`
- `DownloadViewModel` replaces mock simulation with real XPC calls + WebSocket progress listener

---

## Rust FFI Surface (existing, do not change)

```c
int32_t macin_init(void);
char*   macin_add_download(const char* url, const char* dest_dir);
int32_t macin_pause(const char* id);
int32_t macin_resume(const char* id);
int32_t macin_cancel(const char* id);
char*   macin_get_status(void);
void    macin_free_string(char* ptr);
```

WebSocket: `ws://127.0.0.1:54321`
Progress message shape: `{ "id": "uuid", "downloaded": Int64, "total": Int64, "speed": Double, "status": String }`

---

## ⚠️ Risk Register

| # | Risk | Mitigation |
|---|------|------------|
| 1 | Rust staticlib must be compiled before the Xcode build | Add a `cargo build --release` Run Script build phase **before** the Compile Sources phase in the XPC target. Target `aarch64-apple-darwin` for Apple Silicon dev machines. For CI/distribution, add `x86_64-apple-darwin` and `lipo` into a fat binary. |
| 2 | Code signing — XPC service must be signed by the same team as the app | Both targets share team ID in `project.yml`. For dev: use personal team. The XPC bundle ID must be prefixed by the app's bundle ID: `com.krishpotanwar.macin.DownloadEngineXPC`. |
| 3 | `NSXPCConnection` requires `NSSecureCoding` on all transmitted types | Protocol uses only `String`, `Bool`, `[String: Any]` — all NSSecureCoding-compatible. No custom model types cross the XPC boundary. ✓ |
| 4 | XPC callbacks arrive on XPC's internal queue (not MainActor) | All ViewModel mutations must hop through `await MainActor.run { }`. `EngineXPCClient` is an `actor` to serialize connection management; results are delivered back to callers via `async` returns. |
| 5 | `URLSession.webSocketTask` requires the `com.apple.security.network.client` entitlement on the main app | Add to `MacinApp.entitlements`. The XPC service also needs it (for Rust's `reqwest`). |
| 6 | WebSocket reconnect — if the XPC service crashes mid-download | `WebSocketListener` actor auto-reconnects with exponential backoff (max 5s). XPC service crash surfaced via `NSXPCConnection.invalidationHandler`. |
| 7 | `macin_free_string` must be called for every heap-allocated string returned by Rust | Wrap every FFI call in a RAII helper `RustString` in `DownloadEngineService.swift` that calls `macin_free_string` in `deinit`. |
| 8 | `getStatus` returns `[String: Any]` over XPC — lossy for typed data | Use `getStatus` only for initial state sync at startup. Real-time updates come exclusively from WebSocket. |

---

## Architecture Diagram

```
MacinApp (main process)
  DownloadViewModel (@MainActor @Observable)
    │
    ├── EngineXPCClient (actor)
    │     └── NSXPCConnection ──────────────────────────────────────┐
    │                                                                │
    └── WebSocketListener (actor)                                   │
          └── URLSession.webSocketTask(ws://127.0.0.1:54321)       │
                                                                     ▼
                                              DownloadEngineXPC (XPC Service process)
                                                DownloadEngineService: NSObject
                                                  └── Rust FFI (libmacin_engine.a)
                                                        └── Tokio runtime
                                                              └── WebSocket server
                                                                    └── ws://127.0.0.1:54321
```

---

## Implementation Steps

### Step 1 — Rust Build Integration
**Deliverable:** Rust staticlib compiled as part of Xcode build

1a. Compile command (to be added as Xcode Run Script):
```bash
#!/bin/bash
set -e
RUST_DIR="${SRCROOT}/MacinRustEngine"
cd "$RUST_DIR"
cargo build --release --target aarch64-apple-darwin
cp target/aarch64-apple-darwin/release/libmacin_engine.a "${SRCROOT}/libs/libmacin_engine.a"
```

1b. Create `libs/` directory in project root. Add `libmacin_engine.a` to `.gitignore` (it's a build artifact).

1c. Universal binary for distribution (optional, add to CI):
```bash
cargo build --release --target aarch64-apple-darwin
cargo build --release --target x86_64-apple-darwin
lipo -create \
  target/aarch64-apple-darwin/release/libmacin_engine.a \
  target/x86_64-apple-darwin/release/libmacin_engine.a \
  -output "${SRCROOT}/libs/libmacin_engine.a"
```

---

### Step 2 — XPC Service Target in `project.yml`
**Deliverable:** `DownloadEngineXPC` target wired into xcodegen

```yaml
# Add to project.yml targets section:
DownloadEngineXPC:
  type: bundle.xpc-service
  platform: macOS
  deploymentTarget: "14.0"
  settings:
    SWIFT_VERSION: "6.0"
    SWIFT_STRICT_CONCURRENCY: complete
    PRODUCT_BUNDLE_IDENTIFIER: com.krishpotanwar.macin.DownloadEngineXPC
    INFOPLIST_FILE: DownloadEngineXPC/Info.plist
    OTHER_LDFLAGS:
      - "-L$(SRCROOT)/libs"
      - "-lmacin_engine"
      - "-lresolv"
  sources:
    - DownloadEngineXPC/
  headers:
    - path: MacinRustEngine/MacinEngine.h
      visibility: project
  entitlements:
    path: DownloadEngineXPC/DownloadEngineXPC.entitlements
  preBuildScripts:
    - name: "Build Rust Engine"
      script: |
        cd "${SRCROOT}/MacinRustEngine"
        cargo build --release --target aarch64-apple-darwin
        mkdir -p "${SRCROOT}/libs"
        cp target/aarch64-apple-darwin/release/libmacin_engine.a "${SRCROOT}/libs/"
```

Also update main `MacinApp` target to embed the XPC service:
```yaml
MacinApp:
  ...
  dependencies:
    - target: DownloadEngineXPC
      embed: true
      codeSign: true
```

---

### Step 3 — `DownloadEngineXPC/Info.plist`
**Deliverable:** XPC service info plist

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" ...>
<plist version="1.0">
<dict>
    <key>CFBundleIdentifier</key>
    <string>com.krishpotanwar.macin.DownloadEngineXPC</string>
    <key>CFBundleName</key>
    <string>DownloadEngineXPC</string>
    <key>XPCService</key>
    <dict>
        <key>ServiceType</key>
        <string>Application</string>
    </dict>
</dict>
</plist>
```

---

### Step 4 — `DownloadEngineXPC/DownloadEngineXPC.entitlements`
**Deliverable:** Minimal entitlements for the XPC service

```xml
<dict>
    <key>com.apple.security.app-sandbox</key>
    <true/>
    <key>com.apple.security.network.client</key>
    <true/>
</dict>
```

---

### Step 5 — `MacinApp/XPC/DownloadEngineProtocol.swift`
**Deliverable:** Shared XPC interface — add to **both** targets

```swift
// DownloadEngineProtocol.swift
// Added to both MacinApp target AND DownloadEngineXPC target (shared source)
import Foundation

@objc protocol DownloadEngineProtocol {
    func addDownload(url: String, destinationPath: String, reply: @escaping (String) -> Void)
    func pauseDownload(id: String, reply: @escaping (Bool) -> Void)
    func resumeDownload(id: String, reply: @escaping (Bool) -> Void)
    func cancelDownload(id: String, reply: @escaping (Bool) -> Void)
    func getStatus(reply: @escaping ([String: Any]) -> Void)
}
```

**Note:** In `project.yml`, add this file to both targets' `sources`.

---

### Step 6 — `DownloadEngineXPC/RustString.swift`
**Deliverable:** RAII wrapper ensuring `macin_free_string` is always called

```swift
// RustString.swift — RAII wrapper for Rust-heap C strings
import Foundation

/// Owns a C string returned by the Rust engine and frees it on deinit.
final class RustString {
    private let ptr: UnsafeMutablePointer<CChar>

    init?(_ ptr: UnsafeMutablePointer<CChar>?) {
        guard let ptr else { return nil }
        self.ptr = ptr
    }

    var string: String {
        String(cString: ptr)
    }

    deinit {
        macin_free_string(ptr)
    }
}
```

---

### Step 7 — `DownloadEngineXPC/DownloadEngineService.swift`
**Deliverable:** XPC service implementation — bridges Swift protocol to Rust FFI

```swift
// DownloadEngineService.swift
import Foundation

final class DownloadEngineService: NSObject, DownloadEngineProtocol {

    override init() {
        super.init()
        // Initialise Rust engine + start WebSocket server
        let result = macin_init()
        assert(result == 1, "Rust engine failed to initialise")
    }

    func addDownload(url: String, destinationPath: String, reply: @escaping (String) -> Void) {
        let idPtr = url.withCString { urlC in
            destinationPath.withCString { destC in
                macin_add_download(urlC, destC)
            }
        }
        guard let rs = RustString(idPtr) else {
            reply("")     // empty string signals failure to caller
            return
        }
        reply(rs.string)
    }

    func pauseDownload(id: String, reply: @escaping (Bool) -> Void) {
        let result = id.withCString { macin_pause($0) }
        reply(result == 1)
    }

    func resumeDownload(id: String, reply: @escaping (Bool) -> Void) {
        let result = id.withCString { macin_resume($0) }
        reply(result == 1)
    }

    func cancelDownload(id: String, reply: @escaping (Bool) -> Void) {
        let result = id.withCString { macin_cancel($0) }
        reply(result == 1)
    }

    func getStatus(reply: @escaping ([String: Any]) -> Void) {
        let ptr = macin_get_status()
        guard let rs = RustString(ptr) else {
            reply([:])
            return
        }
        let json = rs.string
        guard let data = json.data(using: .utf8),
              let array = try? JSONSerialization.jsonObject(with: data) as? [[String: Any]] else {
            reply([:])
            return
        }
        // Flatten array into dict keyed by id for XPC transport
        var result: [String: Any] = [:]
        for item in array {
            if let id = item["id"] as? String {
                result[id] = item
            }
        }
        reply(result)
    }
}
```

---

### Step 8 — `DownloadEngineXPC/main.swift`
**Deliverable:** XPC service entry point

```swift
// main.swift — XPC service entry point
import Foundation

let delegate = DownloadEngineServiceDelegate()
let listener = NSXPCListener.service()
listener.delegate = delegate
listener.resume()
RunLoop.main.run()

// MARK: - Delegate

class DownloadEngineServiceDelegate: NSObject, NSXPCListenerDelegate {
    func listener(_ listener: NSXPCListener,
                  shouldAcceptNewConnection connection: NSXPCConnection) -> Bool {
        connection.exportedInterface = NSXPCInterface(with: DownloadEngineProtocol.self)
        connection.exportedObject = DownloadEngineService()
        connection.resume()
        return true
    }
}
```

---

### Step 9 — `MacinApp/XPC/EngineXPCClient.swift`
**Deliverable:** Actor-based XPC client with async wrappers and reconnect logic

```swift
// EngineXPCClient.swift
import Foundation

actor EngineXPCClient {
    private var connection: NSXPCConnection?

    // MARK: Connection management

    private func makeConnection() -> NSXPCConnection {
        let conn = NSXPCConnection(serviceName: "com.krishpotanwar.macin.DownloadEngineXPC")
        conn.remoteObjectInterface = NSXPCInterface(with: DownloadEngineProtocol.self)
        conn.invalidationHandler = { [weak self] in
            Task { await self?.handleInvalidation() }
        }
        conn.interruptionHandler = { [weak self] in
            Task { await self?.handleInvalidation() }
        }
        conn.resume()
        return conn
    }

    private func handleInvalidation() {
        connection = nil
    }

    private func proxy() -> (any DownloadEngineProtocol)? {
        if connection == nil { connection = makeConnection() }
        return connection?.remoteObjectProxyWithErrorHandler { [weak self] error in
            Task { await self?.handleInvalidation() }
        } as? any DownloadEngineProtocol
    }

    // MARK: Public async API

    func addDownload(url: String, destinationPath: String) async -> String? {
        guard let proxy else { return nil }
        return await withCheckedContinuation { cont in
            proxy.addDownload(url: url, destinationPath: destinationPath) { id in
                cont.resume(returning: id.isEmpty ? nil : id)
            }
        }
    }

    func pause(id: String) async -> Bool {
        guard let proxy else { return false }
        return await withCheckedContinuation { cont in
            proxy.pauseDownload(id: id) { cont.resume(returning: $0) }
        }
    }

    func resume(id: String) async -> Bool {
        guard let proxy else { return false }
        return await withCheckedContinuation { cont in
            proxy.resumeDownload(id: id) { cont.resume(returning: $0) }
        }
    }

    func cancel(id: String) async -> Bool {
        guard let proxy else { return false }
        return await withCheckedContinuation { cont in
            proxy.cancelDownload(id: id) { cont.resume(returning: $0) }
        }
    }

    func getStatus() async -> [String: Any] {
        guard let proxy else { return [:] }
        return await withCheckedContinuation { cont in
            proxy.getStatus { cont.resume(returning: $0) }
        }
    }
}
```

---

### Step 10 — `MacinApp/XPC/WebSocketListener.swift`
**Deliverable:** Actor that connects to Rust WebSocket and forwards progress updates to a handler

```swift
// WebSocketListener.swift
import Foundation

/// Progress update decoded from the Rust WebSocket broadcast.
struct ProgressUpdate: Sendable {
    let id: String
    let downloaded: Int64
    let total: Int64
    let speed: Double
    let status: String
}

actor WebSocketListener {
    private var task: URLSessionWebSocketTask?
    private let url = URL(string: "ws://127.0.0.1:54321")!
    private var isRunning = false

    func start(onUpdate: @escaping @Sendable (ProgressUpdate) -> Void) {
        guard !isRunning else { return }
        isRunning = true
        connect(onUpdate: onUpdate)
    }

    func stop() {
        isRunning = false
        task?.cancel(with: .normalClosure, reason: nil)
        task = nil
    }

    private func connect(onUpdate: @escaping @Sendable (ProgressUpdate) -> Void) {
        let ws = URLSession.shared.webSocketTask(with: url)
        task = ws
        ws.resume()
        receive(ws: ws, onUpdate: onUpdate)
    }

    private func receive(ws: URLSessionWebSocketTask,
                         onUpdate: @escaping @Sendable (ProgressUpdate) -> Void) {
        ws.receive { [weak self] result in
            Task { [weak self] in
                guard let self, await self.isRunning else { return }
                switch result {
                case .success(let message):
                    if case .string(let text) = message,
                       let update = Self.decode(text) {
                        onUpdate(update)
                    }
                    await self.receive(ws: ws, onUpdate: onUpdate)
                case .failure:
                    // Reconnect after brief backoff
                    try? await Task.sleep(for: .seconds(2))
                    guard await self.isRunning else { return }
                    await self.connect(onUpdate: onUpdate)
                }
            }
        }
    }

    private static func decode(_ text: String) -> ProgressUpdate? {
        guard let data = text.data(using: .utf8),
              let json = try? JSONSerialization.jsonObject(with: data) as? [String: Any],
              let id       = json["id"]         as? String,
              let dl       = json["downloaded"]  as? Int64,
              let total    = json["total"]        as? Int64,
              let speed    = json["speed"]        as? Double,
              let status   = json["status"]       as? String
        else { return nil }
        return ProgressUpdate(id: id, downloaded: dl, total: total, speed: speed, status: status)
    }
}
```

---

### Step 11 — Update `MacinApp/ViewModels/DownloadViewModel.swift`
**Deliverable:** Replace mock simulation with real XPC + WebSocket

Key changes from Phase 1 version:
- Remove `simulationTask` and `tick()`
- Add `xpcClient: EngineXPCClient` and `wsListener: WebSocketListener`
- `addDownload` calls XPC, gets real UUID back, appends `DownloadTask` with that UUID
- `pause`/`resume`/`cancel`/`retry` call XPC methods
- On `init()`: start WebSocket listener → on each `ProgressUpdate` → find task by id → update bytes/speed/status

```swift
@MainActor @Observable
final class DownloadViewModel {
    var downloads: [DownloadTask] = []
    var isAddSheetPresented = false

    private let xpcClient = EngineXPCClient()
    private let wsListener = WebSocketListener()
    private let defaultDownloadDir: String = {
        FileManager.default.urls(for: .downloadsDirectory, in: .userDomainMask)
            .first?.path ?? NSHomeDirectory() + "/Downloads"
    }()

    init() {
        startWebSocket()
    }

    deinit {
        Task { await wsListener.stop() }
    }

    // MARK: Public actions

    func addDownload(url: String) {
        let trimmed = url.trimmingCharacters(in: .whitespacesAndNewlines)
        guard !trimmed.isEmpty,
              let parsed = URL(string: trimmed),
              let scheme = parsed.scheme?.lowercased(),
              scheme == "http" || scheme == "https",
              parsed.host != nil else { return }
        let rawName = parsed.lastPathComponent
        let filename = rawName.isEmpty ? "download" : rawName
        // Optimistic local append; XPC call updates the id
        let localTask = DownloadTask(
            id: UUID(),           // temporary id, replaced when XPC responds
            url: trimmed,
            filename: filename,
            totalBytes: 0,        // unknown until engine reports
            downloadedBytes: 0,
            bytesPerSecond: 0,
            status: .waiting,
            addedAt: .now
        )
        downloads.append(localTask)
        Task {
            let xpcId = await xpcClient.addDownload(url: trimmed, destinationPath: defaultDownloadDir)
            await MainActor.run {
                // Replace the optimistic task with one carrying the real engine ID
                if let idx = downloads.firstIndex(where: { $0.id == localTask.id }) {
                    if let realId = xpcId.flatMap({ UUID(uuidString: $0) }) {
                        var updated = downloads[idx]
                        updated = DownloadTask(
                            id: realId,
                            url: updated.url,
                            filename: updated.filename,
                            totalBytes: updated.totalBytes,
                            downloadedBytes: updated.downloadedBytes,
                            bytesPerSecond: updated.bytesPerSecond,
                            status: .waiting,
                            addedAt: updated.addedAt
                        )
                        downloads[idx] = updated
                    } else {
                        // XPC call failed — mark as failed
                        var updated = downloads[idx]
                        updated.status = .failed
                        downloads[idx] = updated
                    }
                }
            }
        }
    }

    func pause(id: UUID) {
        Task {
            let _ = await xpcClient.pause(id: id.uuidString)
            // WebSocket will confirm the status change; no optimistic update needed
        }
    }

    func resume(id: UUID) {
        Task { let _ = await xpcClient.resume(id: id.uuidString) }
    }

    func cancel(id: UUID) {
        downloads.removeAll { $0.id == id }     // optimistic remove
        Task { let _ = await xpcClient.cancel(id: id.uuidString) }
    }

    func retry(id: UUID) {
        guard let idx = downloads.firstIndex(where: { $0.id == id }) else { return }
        var updated = downloads[idx]
        updated.status = .waiting
        updated.downloadedBytes = 0
        updated.bytesPerSecond = 0
        downloads[idx] = updated
        Task {
            let _ = await xpcClient.cancel(id: id.uuidString)
            let xpcId = await xpcClient.addDownload(
                url: updated.url, destinationPath: defaultDownloadDir
            )
            await MainActor.run {
                if let idx2 = downloads.firstIndex(where: { $0.id == id }),
                   let realId = xpcId.flatMap({ UUID(uuidString: $0) }) {
                    var t = downloads[idx2]
                    downloads[idx2] = DownloadTask(id: realId, url: t.url, filename: t.filename,
                                                    totalBytes: t.totalBytes,
                                                    downloadedBytes: 0, bytesPerSecond: 0,
                                                    status: .waiting, addedAt: t.addedAt)
                }
            }
        }
    }

    // MARK: WebSocket

    private func startWebSocket() {
        Task {
            await wsListener.start { [weak self] update in
                Task { @MainActor [weak self] in
                    self?.applyProgressUpdate(update)
                }
            }
        }
    }

    private func applyProgressUpdate(_ update: ProgressUpdate) {
        guard let uuid = UUID(uuidString: update.id),
              let idx = downloads.firstIndex(where: { $0.id == uuid }) else { return }
        var t = downloads[idx]
        t.downloadedBytes = update.downloaded
        t.totalBytes = update.total
        t.bytesPerSecond = update.speed
        t.status = DownloadStatus(rawValue: update.status) ?? t.status
        downloads[idx] = t
    }
}
```

---

### Step 12 — `MacinApp/MacinApp.entitlements`
**Deliverable:** Add `network.client` entitlement to main app for WebSocket

```xml
<dict>
    <key>com.apple.security.app-sandbox</key>
    <true/>
    <key>com.apple.security.network.client</key>
    <true/>
</dict>
```

---

### Step 13 — Update `project.yml` for xcodegen
**Deliverable:** Regenerate `.xcodeproj` with the new XPC target

Run after editing `project.yml`:
```bash
cd /Users/krish/Desktop/study/project/MACIN_download_manager
xcodegen generate
```

Key `project.yml` changes:
- Add `DownloadEngineXPC` target (Step 2)
- Add `DownloadEngineProtocol.swift` to both targets
- Add `MacinApp.entitlements` to `MacinApp` target
- Embed `DownloadEngineXPC` into `MacinApp`

---

### Step 14 — Smoke test sequence
**Deliverable:** Manual verification checklist before calling Phase 3 done

```
1. xcodegen generate → project regenerates without errors
2. Build MacinApp in Xcode → both targets compile, no Swift 6 warnings
3. Run in Xcode → app launches, glass UI appears
4. Paste a real HTTPS URL → card appears with status .waiting
5. XPC connects → status changes to .downloading
6. Progress bar animates with real byte counts
7. Press pause → progress freezes (Rust pauses download)
8. Press resume → progress resumes from saved position
9. Press cancel → card disappears, .macin_resume sidecar cleaned up
10. Close main window → re-open → any in-progress downloads resume (Phase 4 concern, log for now)
```

---

## Key Files Summary

| File | Target | Operation | Notes |
|------|--------|-----------|-------|
| `project.yml` | — | Modify | Add XPC target, embed, entitlements |
| `DownloadEngineXPC/Info.plist` | XPC | Create | Bundle ID, ServiceType = Application |
| `DownloadEngineXPC/DownloadEngineXPC.entitlements` | XPC | Create | sandbox + network.client |
| `DownloadEngineXPC/main.swift` | XPC | Create | NSXPCListener.service() entry point |
| `DownloadEngineXPC/RustString.swift` | XPC | Create | RAII wrapper for Rust heap strings |
| `DownloadEngineXPC/DownloadEngineService.swift` | XPC | Create | Protocol impl, calls Rust FFI |
| `MacinApp/XPC/DownloadEngineProtocol.swift` | Both | Create | @objc protocol, shared source |
| `MacinApp/XPC/EngineXPCClient.swift` | App | Create | Actor, async XPC wrappers |
| `MacinApp/XPC/WebSocketListener.swift` | App | Create | Actor, URLSession WS, reconnect |
| `MacinApp/ViewModels/DownloadViewModel.swift` | App | Modify | Replace mock with XPC + WebSocket |
| `MacinApp/MacinApp.entitlements` | App | Create | sandbox + network.client |

---

## Agent Invocation Order (Head Chef workflow)

```
1. @architect  → Review XPC target setup in project.yml, confirm entitlements and embedding
2. @swift-ui   → Implement Steps 5-11 (protocol, service, client, listener, viewmodel)
3. @rust-build → Resolve any cargo linking errors during Step 1
4. @swift-reviewer → Actor isolation audit (XPC callbacks, WebSocket handler, MainActor hops)
5. @security   → Entitlements review: no over-granted permissions; WebSocket localhost-only confirmed
```

---

## Done Criteria (Phase 3)

- [ ] `xcodegen generate` succeeds — no missing files
- [ ] Both targets build with zero warnings under Swift 6 strict concurrency
- [ ] Real file download completes — bytes match on disk
- [ ] Live progress bar updates from Rust WebSocket (not mock timer)
- [ ] Pause/resume tested with a large file (progress saves, resumes from correct offset)
- [ ] XPC service crash recovery — kill the XPC process, main app reconnects automatically
- [ ] `@security` passes: only `network.client` entitlement, no over-grants

---

## SESSION_ID
- CODEX_SESSION: N/A
- GEMINI_SESSION: N/A

*Plan synthesized by Claude (Sonnet 4.6) from Phase 1 + Phase 2 context. No production code modified.*

# Implementation Plan: Macin Download Manager — Phase 1 (SwiftUI Frontend)

## Task Type
- [x] Frontend (SwiftUI macOS, mock data only, no XPC)

---

## Context Summary

Greenfield Xcode project. No existing source files. Source of truth: `MACIN_CLAUDE_CODE_MEGAPROMPT.md`.

**Stack:** Swift 6.2 · macOS 14+ deployment target (see Risk #1) · SwiftUI · @Observable macro
**Design:** macOS Control Center glass aesthetic — NSVisualEffectView, frosted cards, LazyVGrid
**Scope:** UI only — mock DownloadViewModel drives all state; no XPC, no Rust, no networking

---

## ⚠️ Risk Register

| # | Risk | Mitigation |
|---|------|------------|
| 1 | `@Observable` macro requires macOS 14+ (Sonoma), but spec says min macOS 13 | **Set deployment target to macOS 14.0.** macOS 13 share < 5% in 2025. If macOS 13 support is required, fall back to `ObservableObject` — but spec explicitly forbids this. Document the decision. |
| 2 | `.glassEffect()` modifier (macOS 15) vs `NSVisualEffectView` fallback (13/14) | Use `#available(macOS 15, *)` conditional: `.glassEffect()` on ≥15, `NSVisualEffectView` wrapper on <15. Already planned in design spec. |
| 3 | LazyVGrid adaptive width (2-col ≥900px, 1-col <900px) requires GeometryReader | Wrap ContentView body in `GeometryReader`. Column count = `geometry.size.width >= 900 ? 2 : 1`. |
| 4 | Swift 6 strict concurrency — ViewModel actor isolation | All `@Observable` mutations must happen on `@MainActor`. XPC callbacks (future phase) will need `await MainActor.run { }`. Plan for it now. |
| 5 | `.monospacedDigit()` prevents speed label width jitter | Apply to every numeric Text. Use a fixed-width frame on SpeedLabel. |

---

## Technical Solution

### State Architecture
```
@MainActor @Observable class DownloadViewModel
  var downloads: [DownloadTask]        // published array
  func addDownload(url: String)        // mock: append to array
  func pause(id: UUID)                 // mock: update status
  func resume(id: UUID)               // mock: update status
  func cancel(id: UUID)               // mock: remove from array
  func simulateTick()                  // updates progress/speed for mock animation
```

### Data Model
```
struct DownloadTask: Identifiable, Codable, Sendable {
  let id: UUID
  var url: String
  var filename: String
  var totalBytes: Int64
  var downloadedBytes: Int64
  var speed: Double          // bytes/sec
  var status: DownloadStatus
  var addedAt: Date

  var progress: Double { downloadedBytes / totalBytes }
  var eta: TimeInterval? { speed > 0 ? (totalBytes - downloadedBytes) / speed : nil }

  static func mockSet() -> [DownloadTask]  // 4 sample tasks (downloading, paused, completed, failed)
}

enum DownloadStatus: String, Codable, Sendable {
  case waiting, downloading, paused, completed, failed
  var color: Color { ... }   // .blue/.orange/.green/.red/.gray per spec
  var iconName: String { ... }  // SF Symbol per status
}
```

### View Hierarchy
```
MacinApp (@main)
  └── WindowGroup
        └── ContentView                     // GeometryReader + ZStack
              ├── VisualEffectBlur           // fullscreen NSVisualEffectView
              └── VStack
                    ├── toolbar row (title + "+" button)
                    ├── LazyVGrid(columns: adaptive)
                    │     └── ForEach downloads → DownloadCard(task)
                    └── empty state view (no downloads)

DownloadCard(task: DownloadTask)
  └── ZStack
        ├── RoundedRectangle fill .white.opacity(0.08) + stroke .white.opacity(0.15)
        ├── VStack
        │     ├── HStack: icon + filename + StatusBadge
        │     ├── GlassProgressStyle ProgressView(value: progress)
        │     └── HStack: SpeedLabel + ETA + pill buttons (pause/resume/cancel)
        └── shadow overlay

AddURLSheet
  └── VStack: title + TextField(url) + buttons (Add / Cancel)

SettingsView
  └── Form: concurrency Stepper (1–8) + save path picker
```

---

## Implementation Steps

### Step 1 — Xcode Project Bootstrap
**Deliverable:** Xcode project with Swift 6 strict concurrency enabled, deployment target macOS 14.0

```
New Project → macOS App → SwiftUI · Swift
Product Name: MacinDownloadManager
Bundle ID: com.krishpotanwar.macin

Build Settings:
  SWIFT_VERSION = 6.0
  SWIFT_STRICT_CONCURRENCY = complete
  MACOSX_DEPLOYMENT_TARGET = 14.0
  ENABLE_HARDENED_RUNTIME = YES
```

Remove default `ContentView.swift` boilerplate body, keep the file.

---

### Step 2 — `Theme.swift`
**Deliverable:** All design tokens in one place. No magic numbers elsewhere.

```swift
// Theme.swift
enum Theme {
  // Colors
  static let cardFill = Color.white.opacity(0.08)
  static let cardBorder = Color.white.opacity(0.15)
  static let cardShadow = Color.black.opacity(0.12)

  // Geometry
  static let cardCornerRadius: CGFloat = 16
  static let cardShadowRadius: CGFloat = 8
  static let cardShadowY: CGFloat = 4
  static let gridSpacing: CGFloat = 12
  static let gridMinWidth: CGFloat = 900   // threshold for 2-col

  // Animation
  static let springAnimation = Animation.spring(response: 0.4, dampingFraction: 0.8)
  static let cardTransition = AnyTransition.scale(scale: 0.95).combined(with: .opacity)

  // Typography sizes (used with .font() modifier)
  // Actual fonts use system SF Pro via .headline / .caption modifiers
}
```

---

### Step 3 — `DownloadStatus.swift`
**Deliverable:** Status enum with color + icon helpers

```swift
// DownloadStatus.swift
enum DownloadStatus: String, Codable, CaseIterable, Sendable {
  case waiting, downloading, paused, completed, failed

  var accentColor: Color {
    switch self {
    case .waiting:     .gray
    case .downloading: .blue
    case .paused:      .orange
    case .completed:   .green
    case .failed:      .red
    }
  }

  var sfSymbol: String {
    switch self {
    case .waiting:     "clock"
    case .downloading: "arrow.down.circle.fill"
    case .paused:      "pause.circle.fill"
    case .completed:   "checkmark.circle.fill"
    case .failed:      "xmark.circle.fill"
    }
  }

  var label: String { rawValue.capitalized }
}
```

---

### Step 4 — `DownloadTask.swift`
**Deliverable:** Model + mock factory

```swift
// DownloadTask.swift
struct DownloadTask: Identifiable, Codable, Sendable {
  let id: UUID
  var url: String
  var filename: String
  var totalBytes: Int64
  var downloadedBytes: Int64
  var bytesPerSecond: Double   // rolling average
  var status: DownloadStatus
  var addedAt: Date

  var progress: Double {
    guard totalBytes > 0 else { return 0 }
    return Double(downloadedBytes) / Double(totalBytes)
  }

  var eta: TimeInterval? {
    guard bytesPerSecond > 0, status == .downloading else { return nil }
    return Double(totalBytes - downloadedBytes) / bytesPerSecond
  }

  var formattedSpeed: String {
    ByteCountFormatter.string(fromByteCount: Int64(bytesPerSecond), countStyle: .file) + "/s"
  }

  var formattedSize: String {
    ByteCountFormatter.string(fromByteCount: totalBytes, countStyle: .file)
  }

  // MARK: Mock data
  static func mockSet() -> [DownloadTask] {
    [
      DownloadTask(id: UUID(), url: "https://example.com/xcode.dmg",
                   filename: "Xcode_16.dmg", totalBytes: 8_500_000_000,
                   downloadedBytes: 3_200_000_000, bytesPerSecond: 12_500_000,
                   status: .downloading, addedAt: .now),
      DownloadTask(id: UUID(), url: "https://example.com/macos.ipsw",
                   filename: "macOS_Sequoia.ipsw", totalBytes: 14_000_000_000,
                   downloadedBytes: 7_000_000_000, bytesPerSecond: 0,
                   status: .paused, addedAt: .now.addingTimeInterval(-600)),
      DownloadTask(id: UUID(), url: "https://example.com/xcode-docs.zip",
                   filename: "XcodeDocs.zip", totalBytes: 450_000_000,
                   downloadedBytes: 450_000_000, bytesPerSecond: 0,
                   status: .completed, addedAt: .now.addingTimeInterval(-3600)),
      DownloadTask(id: UUID(), url: "https://example.com/broken.zip",
                   filename: "broken.zip", totalBytes: 100_000_000,
                   downloadedBytes: 23_000_000, bytesPerSecond: 0,
                   status: .failed, addedAt: .now.addingTimeInterval(-120)),
    ]
  }
}
```

---

### Step 5 — `DownloadViewModel.swift`
**Deliverable:** @MainActor @Observable class, mock operations, timer-driven simulation

```swift
// DownloadViewModel.swift
@MainActor @Observable
final class DownloadViewModel {
  var downloads: [DownloadTask] = DownloadTask.mockSet()
  var isAddSheetPresented = false

  // Timer for simulating live progress on mock data
  private var simulationTimer: Timer?

  init() { startSimulation() }

  func addDownload(url: String) {
    guard !url.trimmingCharacters(in: .whitespacesAndNewlines).isEmpty else { return }
    guard let _ = URL(string: url), url.hasPrefix("http") else { return }  // basic validation
    let filename = URL(string: url)?.lastPathComponent ?? "download"
    let task = DownloadTask(id: UUID(), url: url, filename: filename,
                            totalBytes: 500_000_000, downloadedBytes: 0,
                            bytesPerSecond: 0, status: .waiting, addedAt: .now)
    downloads.append(task)
  }

  func pause(id: UUID) {
    guard let idx = downloads.firstIndex(where: { $0.id == id }) else { return }
    downloads[idx].status = .paused
    downloads[idx].bytesPerSecond = 0
  }

  func resume(id: UUID) {
    guard let idx = downloads.firstIndex(where: { $0.id == id }) else { return }
    downloads[idx].status = .downloading
  }

  func cancel(id: UUID) {
    downloads.removeAll { $0.id == id }
  }

  func retry(id: UUID) {
    guard let idx = downloads.firstIndex(where: { $0.id == id }) else { return }
    downloads[idx].status = .waiting
    downloads[idx].downloadedBytes = 0
  }

  // MARK: Simulation
  private func startSimulation() {
    simulationTimer = Timer.scheduledTimer(withTimeInterval: 1.0, repeats: true) { [weak self] _ in
      Task { @MainActor [weak self] in self?.tick() }
    }
  }

  private func tick() {
    for idx in downloads.indices {
      guard downloads[idx].status == .downloading else { continue }
      let increment = Int64.random(in: 8_000_000...15_000_000)
      downloads[idx].downloadedBytes = min(
        downloads[idx].downloadedBytes + increment,
        downloads[idx].totalBytes
      )
      downloads[idx].bytesPerSecond = Double(increment)
      if downloads[idx].downloadedBytes >= downloads[idx].totalBytes {
        downloads[idx].status = .completed
        downloads[idx].bytesPerSecond = 0
      }
    }
    // Auto-start waiting tasks (up to concurrency limit of 3)
    let activeCount = downloads.filter { $0.status == .downloading }.count
    let slots = max(0, 3 - activeCount)
    let waitingIndices = downloads.indices.filter { downloads[$0].status == .waiting }
    for idx in waitingIndices.prefix(slots) {
      downloads[idx].status = .downloading
    }
  }
}
```

---

### Step 6 — `VisualEffectBlur.swift`
**Deliverable:** NSViewRepresentable wrapping NSVisualEffectView for macOS 13/14

```swift
// VisualEffectBlur.swift
struct VisualEffectBlur: NSViewRepresentable {
  var material: NSVisualEffectView.Material = .hudWindow
  var blendingMode: NSVisualEffectView.BlendingMode = .behindWindow

  func makeNSView(context: Context) -> NSVisualEffectView {
    let view = NSVisualEffectView()
    view.material = material
    view.blendingMode = blendingMode
    view.state = .active
    return view
  }
  func updateNSView(_ nsView: NSVisualEffectView, context: Context) {
    nsView.material = material
    nsView.blendingMode = blendingMode
  }
}
```

Usage pattern:
```swift
ZStack {
  VisualEffectBlur()        // fullscreen, .ignoresSafeArea()
  content
}
```

---

### Step 7 — `GlassProgressStyle.swift`
**Deliverable:** Custom ProgressViewStyle matching Control Center slider aesthetic

```swift
// GlassProgressStyle.swift
struct GlassProgressStyle: ProgressViewStyle {
  var tint: Color

  func makeBody(configuration: Configuration) -> some View {
    GeometryReader { geo in
      ZStack(alignment: .leading) {
        // Track
        RoundedRectangle(cornerRadius: 3)
          .fill(Color.white.opacity(0.15))
          .frame(height: 6)
        // Fill
        RoundedRectangle(cornerRadius: 3)
          .fill(tint)
          .frame(width: geo.size.width * CGFloat(configuration.fractionCompleted ?? 0), height: 6)
          .animation(Theme.springAnimation, value: configuration.fractionCompleted)
      }
    }
    .frame(height: 6)
  }
}
```

---

### Step 8 — `StatusBadge.swift`
**Deliverable:** Colored pill badge for status display

```swift
// StatusBadge.swift
struct StatusBadge: View {
  let status: DownloadStatus

  var body: some View {
    Text(status.label)
      .font(.caption2)
      .fontWeight(.medium)
      .padding(.horizontal, 8)
      .padding(.vertical, 3)
      .background(status.accentColor.opacity(0.25))
      .foregroundColor(status.accentColor)
      .clipShape(Capsule())
  }
}
```

---

### Step 9 — `SpeedLabel.swift`
**Deliverable:** Fixed-width speed display that doesn't cause layout jitter

```swift
// SpeedLabel.swift
struct SpeedLabel: View {
  let bytesPerSecond: Double
  let status: DownloadStatus

  private var label: String {
    guard status == .downloading, bytesPerSecond > 0 else { return "—" }
    return ByteCountFormatter.string(fromByteCount: Int64(bytesPerSecond), countStyle: .file) + "/s"
  }

  var body: some View {
    Text(label)
      .font(.caption.monospacedDigit())
      .foregroundStyle(.secondary)
      .frame(minWidth: 80, alignment: .leading)
      .animation(Theme.springAnimation, value: bytesPerSecond)
  }
}
```

---

### Step 10 — `DownloadCard.swift`
**Deliverable:** Full glass card with progress, speed, ETA, and action buttons

```swift
// DownloadCard.swift
struct DownloadCard: View {
  let task: DownloadTask
  let onPause: () -> Void
  let onResume: () -> Void
  let onCancel: () -> Void
  let onRetry: () -> Void

  var body: some View {
    ZStack {
      // Glass card background
      RoundedRectangle(cornerRadius: Theme.cardCornerRadius)
        .fill(Theme.cardFill)
        .overlay(
          RoundedRectangle(cornerRadius: Theme.cardCornerRadius)
            .stroke(Theme.cardBorder, lineWidth: 0.5)
        )
        .shadow(color: Theme.cardShadow, radius: Theme.cardShadowRadius, y: Theme.cardShadowY)

      VStack(alignment: .leading, spacing: 10) {
        // Header row
        HStack(spacing: 8) {
          Image(systemName: task.status.sfSymbol)
            .font(.title3)
            .foregroundColor(task.status.accentColor)
          VStack(alignment: .leading, spacing: 2) {
            Text(task.filename)
              .font(.headline.weight(.semibold))
              .foregroundColor(.white)
              .lineLimit(1)
            Text(task.url)
              .font(.caption2)
              .foregroundStyle(.secondary)
              .lineLimit(1)
          }
          Spacer()
          StatusBadge(status: task.status)
        }

        // Progress bar
        if task.status != .waiting {
          ProgressView(value: task.progress)
            .progressViewStyle(GlassProgressStyle(tint: task.status.accentColor))
        }

        // Footer row: speed + ETA + action buttons
        HStack {
          SpeedLabel(bytesPerSecond: task.bytesPerSecond, status: task.status)
          if let eta = task.eta {
            Text(etaString(eta))
              .font(.caption.monospacedDigit())
              .foregroundStyle(.secondary)
          }
          Spacer()
          actionButtons
        }
      }
      .padding(16)
    }
    .transition(Theme.cardTransition)
  }

  // MARK: Action pill buttons
  @ViewBuilder
  private var actionButtons: some View {
    HStack(spacing: 8) {
      switch task.status {
      case .downloading:
        PillButton(icon: "pause.fill", color: .orange, action: onPause)
        PillButton(icon: "xmark", color: .red, action: onCancel)
      case .paused:
        PillButton(icon: "play.fill", color: .blue, action: onResume)
        PillButton(icon: "xmark", color: .red, action: onCancel)
      case .failed:
        PillButton(icon: "arrow.clockwise", color: .orange, action: onRetry)
        PillButton(icon: "xmark", color: .red, action: onCancel)
      case .waiting:
        PillButton(icon: "xmark", color: .red, action: onCancel)
      case .completed:
        EmptyView()
      }
    }
  }

  private func etaString(_ seconds: TimeInterval) -> String {
    let formatter = DateComponentsFormatter()
    formatter.allowedUnits = [.hour, .minute, .second]
    formatter.unitsStyle = .abbreviated
    formatter.maximumUnitCount = 2
    return formatter.string(from: seconds) ?? "--"
  }
}

struct PillButton: View {
  let icon: String
  let color: Color
  let action: () -> Void

  var body: some View {
    Button(action: action) {
      Image(systemName: icon)
        .font(.caption.weight(.semibold))
        .foregroundColor(color)
        .frame(width: 28, height: 28)
        .background(color.opacity(0.15))
        .clipShape(Circle())
    }
    .buttonStyle(.plain)
  }
}
```

---

### Step 11 — `AddURLSheet.swift`
**Deliverable:** URL paste sheet with basic validation feedback

```swift
// AddURLSheet.swift
struct AddURLSheet: View {
  @Binding var isPresented: Bool
  let onAdd: (String) -> Void

  @State private var urlText = ""
  @State private var showValidationError = false

  var body: some View {
    VStack(alignment: .leading, spacing: 16) {
      Text("Add Download")
        .font(.title2.weight(.semibold))
        .foregroundColor(.white)

      TextField("https://", text: $urlText)
        .textFieldStyle(.plain)
        .padding(10)
        .background(Color.white.opacity(0.08))
        .clipShape(RoundedRectangle(cornerRadius: 8))
        .overlay(RoundedRectangle(cornerRadius: 8).stroke(Color.white.opacity(0.2)))

      if showValidationError {
        Text("Enter a valid http(s) URL")
          .font(.caption)
          .foregroundColor(.red)
      }

      HStack {
        Spacer()
        Button("Cancel") { isPresented = false }
          .buttonStyle(.plain)
          .foregroundColor(.secondary)
        Button("Add") {
          let trimmed = urlText.trimmingCharacters(in: .whitespacesAndNewlines)
          guard trimmed.hasPrefix("http://") || trimmed.hasPrefix("https://"),
                URL(string: trimmed) != nil else {
            showValidationError = true
            return
          }
          onAdd(trimmed)
          isPresented = false
        }
        .buttonStyle(.borderedProminent)
        .disabled(urlText.isEmpty)
      }
    }
    .padding(24)
    .frame(width: 480)
    .background(VisualEffectBlur(material: .popover))
    .clipShape(RoundedRectangle(cornerRadius: Theme.cardCornerRadius))
  }
}
```

---

### Step 12 — `ContentView.swift`
**Deliverable:** Root view — fullscreen blur + adaptive grid + toolbar

```swift
// ContentView.swift
struct ContentView: View {
  @State private var viewModel = DownloadViewModel()

  var body: some View {
    GeometryReader { geometry in
      ZStack {
        // Fullscreen blur
        VisualEffectBlur()
          .ignoresSafeArea()

        VStack(spacing: 0) {
          // Toolbar
          HStack {
            Text("Macin")
              .font(.title2.weight(.semibold))
              .foregroundColor(.white)
            Spacer()
            Button {
              viewModel.isAddSheetPresented = true
            } label: {
              Image(systemName: "plus.circle.fill")
                .font(.title3)
                .foregroundColor(.blue)
            }
            .buttonStyle(.plain)
          }
          .padding(.horizontal, 16)
          .padding(.vertical, 12)

          Divider().opacity(0.2)

          // Downloads grid
          if viewModel.downloads.isEmpty {
            emptyState
          } else {
            let columns = geometry.size.width >= Theme.gridMinWidth
              ? [GridItem(.flexible(), spacing: Theme.gridSpacing),
                 GridItem(.flexible(), spacing: Theme.gridSpacing)]
              : [GridItem(.flexible())]

            ScrollView {
              LazyVGrid(columns: columns, spacing: Theme.gridSpacing) {
                ForEach(viewModel.downloads) { task in
                  DownloadCard(
                    task: task,
                    onPause:  { viewModel.pause(id: task.id) },
                    onResume: { viewModel.resume(id: task.id) },
                    onCancel: { viewModel.cancel(id: task.id) },
                    onRetry:  { viewModel.retry(id: task.id) }
                  )
                }
              }
              .padding(16)
              .animation(Theme.springAnimation, value: viewModel.downloads.map(\.id))
            }
          }
        }
      }
    }
    .frame(minWidth: 400, minHeight: 300)
    .sheet(isPresented: $viewModel.isAddSheetPresented) {
      AddURLSheet(isPresented: $viewModel.isAddSheetPresented) { url in
        viewModel.addDownload(url: url)
      }
    }
  }

  private var emptyState: some View {
    VStack(spacing: 8) {
      Image(systemName: "arrow.down.circle")
        .font(.largeTitle)
        .foregroundStyle(.secondary)
      Text("No downloads yet")
        .font(.headline)
        .foregroundStyle(.secondary)
      Text("Click + to add a URL")
        .font(.caption)
        .foregroundStyle(.tertiary)
    }
    .frame(maxWidth: .infinity, maxHeight: .infinity)
  }
}
```

---

### Step 13 — `MacinApp.swift`
**Deliverable:** App entry point with correct window style

```swift
// MacinApp.swift
@main
struct MacinApp: App {
  var body: some Scene {
    WindowGroup {
      ContentView()
    }
    .windowStyle(.hiddenTitleBar)     // clean borderless look
    .windowResizability(.contentSize)
    .defaultSize(width: 700, height: 500)
  }
}
```

---

### Step 14 — Unit Tests (`DownloadViewModelTests.swift`)
**Deliverable:** Swift Testing suite for ViewModel logic (targeting 70%+ on ViewModel)

```swift
// DownloadViewModelTests.swift
import Testing
@testable import MacinDownloadManager

@MainActor
struct DownloadViewModelTests {
  @Test func addDownload_appendsTask() {
    let vm = DownloadViewModel()
    let initialCount = vm.downloads.count
    vm.addDownload(url: "https://example.com/file.zip")
    #expect(vm.downloads.count == initialCount + 1)
    #expect(vm.downloads.last?.status == .waiting)
  }

  @Test func addDownload_rejectsInvalidURL() {
    let vm = DownloadViewModel()
    let count = vm.downloads.count
    vm.addDownload(url: "not-a-url")
    #expect(vm.downloads.count == count)    // no new task
  }

  @Test func addDownload_rejectsEmptyURL() {
    let vm = DownloadViewModel()
    let count = vm.downloads.count
    vm.addDownload(url: "   ")
    #expect(vm.downloads.count == count)
  }

  @Test func pause_changesStatus() {
    let vm = DownloadViewModel()
    guard let task = vm.downloads.first(where: { $0.status == .downloading }) else {
      Issue.record("No downloading task in mock set"); return
    }
    vm.pause(id: task.id)
    let updated = vm.downloads.first { $0.id == task.id }
    #expect(updated?.status == .paused)
    #expect(updated?.bytesPerSecond == 0)
  }

  @Test func resume_changesStatusToDownloading() {
    let vm = DownloadViewModel()
    guard let task = vm.downloads.first(where: { $0.status == .paused }) else {
      Issue.record("No paused task in mock set"); return
    }
    vm.resume(id: task.id)
    #expect(vm.downloads.first { $0.id == task.id }?.status == .downloading)
  }

  @Test func cancel_removesTask() {
    let vm = DownloadViewModel()
    let task = vm.downloads[0]
    vm.cancel(id: task.id)
    #expect(vm.downloads.first { $0.id == task.id } == nil)
  }

  @Test func downloadTask_progressCalculation() {
    var task = DownloadTask.mockSet()[0]
    task.totalBytes = 1000
    task.downloadedBytes = 250
    #expect(abs(task.progress - 0.25) < 0.001)
  }

  @Test func downloadTask_etaNilWhenSpeedZero() {
    var task = DownloadTask.mockSet()[1]  // paused
    task.bytesPerSecond = 0
    #expect(task.eta == nil)
  }

  @Test func downloadTask_etaCalculation() {
    var task = DownloadTask.mockSet()[0]
    task.totalBytes = 1000
    task.downloadedBytes = 0
    task.bytesPerSecond = 100
    task.status = .downloading
    #expect(abs((task.eta ?? 0) - 10.0) < 0.01)
  }
}
```

---

## Key Files Summary

| File | Operation | Notes |
|------|-----------|-------|
| `MacinDownloadManager.xcodeproj` | Create | Swift 6, macOS 14+, hardened runtime |
| `MacinApp/MacinApp.swift` | Create | @main, hiddenTitleBar window style |
| `MacinApp/Theme.swift` | Create | All design tokens |
| `MacinApp/Models/DownloadStatus.swift` | Create | Enum + color/icon helpers |
| `MacinApp/Models/DownloadTask.swift` | Create | Struct + mock factory |
| `MacinApp/ViewModels/DownloadViewModel.swift` | Create | @MainActor @Observable + timer sim |
| `MacinApp/Views/ContentView.swift` | Create | GeometryReader + LazyVGrid |
| `MacinApp/Views/DownloadCard.swift` | Create | Glass card + PillButton |
| `MacinApp/Views/AddURLSheet.swift` | Create | URL input + validation |
| `MacinApp/Views/Components/VisualEffectBlur.swift` | Create | NSViewRepresentable |
| `MacinApp/Views/Components/GlassProgressStyle.swift` | Create | Custom ProgressViewStyle |
| `MacinApp/Views/Components/StatusBadge.swift` | Create | Capsule badge |
| `MacinApp/Views/Components/SpeedLabel.swift` | Create | Monospaced speed text |
| `MacinDownloadManagerTests/DownloadViewModelTests.swift` | Create | Swift Testing suite |

---

## Done Criteria (Phase 1)

- [ ] App launches in Xcode without errors (Swift 6 strict concurrency clean)
- [ ] Shows 4 mock download cards (downloading, paused, completed, failed)
- [ ] Cards display glass aesthetic: frosted blur background, white tint cards, correct border/shadow
- [ ] Active "downloading" card animates progress bar and speed counter in real-time (1Hz simulation)
- [ ] Pause/resume/cancel/retry buttons work and update UI state instantly
- [ ] "+" button opens AddURLSheet; valid URLs create a waiting task that auto-starts
- [ ] Grid goes 2-column at ≥900px width, 1-column below
- [ ] `swift test` passes all 8 ViewModel unit tests
- [ ] No force-unwraps (`!`) in production code
- [ ] Clippy-equivalent: no compiler warnings

---

## Agent Invocation Order (Head Chef workflow)

```
1. @architect   → Verify module structure matches this plan before any code
2. @planner     → Break Step 1-14 into ordered Xcode tasks
3. @swift-ui    → Implement Theme → Models → ViewModel → Components → Views (in order)
4. @swift-reviewer → Review each file for actor safety + SwiftUI lifecycle
5. @security    → Verify: URL validation in addDownload, no secrets, no network calls
```

---

## SESSION_ID (for /ccg:execute use)
- CODEX_SESSION: N/A (multi-model backends not available in this environment)
- GEMINI_SESSION: N/A

*Plan synthesized by Claude (Sonnet 4.6) from MACIN_CLAUDE_CODE_MEGAPROMPT.md. No production code modified.*

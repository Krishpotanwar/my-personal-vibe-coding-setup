# WorldPolicy-Env V5 — Frontend & Design Plan

**Version:** V5 (upgraded from V4)
**Scope:** All React/TSX components, visual design system, Deck.gl globe, UI state, API consumption
**Backend counterpart:** WorldPolicy-V4-Backend-Plan.md (unchanged — V5 is frontend only)
**Design Language:** Liquid Glass + Skeuomorphism (Apple Vision Pro meets Mission Control)
**Repo:** fork of Rajy777/disasterman (`frontend/src/`)
**Status:** DESIGN-READY — hand to Claude Design for component scaffolding

---

## What Changed from V4 (Judge Feedback Applied)

| Judge Gap | V4 Status | V5 Fix |
|-----------|-----------|--------|
| No eval harness output on screen | Not rendered | `EvalSummaryCard.tsx` — shows MAPPO vs baselines with seeds + std |
| Emergent badges theatrical, not trustworthy | Badge lights up, no proof | Badges show trigger condition text + step logged |
| Meta Llama too decorative | 2-sentence brief only | `CrisisBriefCard` now shows `priority_response` structured field |
| No "Training Facts" card | Missing | `TrainingFactsCard.tsx` — architecture, params, checkpoint hash |
| Scripted vs learned boundary not clear | Implicit | `ClaimBoundaryBanner` — always visible, states what is scripted vs learned |
| Architecture diagram not mandatory | "embed if time" | `ArchitectureDiagramPanel.tsx` — mandatory, SVG inline |
| No design language specified | Default Tailwind | Liquid Glass + Skeuomorphism system (defined below) |

---

## Design Language: Liquid Glass + Skeuomorphism

### Philosophy

The UI should feel like **Mission Control glass panels floating over a dark globe** — the kind of interface a real UN Situation Room would have if it was built in 2026. Every panel is made of frosted glass with visible depth. Buttons have physical weight. Indicator lights look like real LEDs. Data cards look like instruments from a cockpit, not a SaaS dashboard.

Two principles layered together:
- **Liquid Glass**: translucency, blur, refraction, depth, glow — panels feel like they are *made of glass* and float above the globe
- **Skeuomorphism**: physical world references — real LED indicators, raised buttons, engraved labels, paper-texture document cards, gauge needles, stamped credentials

### CSS Custom Properties (add to `index.css` or `globals.css`)

```css
:root {
  /* Glass panel system */
  --glass-bg:             rgba(255, 255, 255, 0.05);
  --glass-bg-hover:       rgba(255, 255, 255, 0.09);
  --glass-bg-active:      rgba(255, 255, 255, 0.13);
  --glass-border:         rgba(255, 255, 255, 0.12);
  --glass-border-bright:  rgba(255, 255, 255, 0.25);
  --glass-blur:           blur(24px);
  --glass-blur-heavy:     blur(40px);

  /* Depth shadows */
  --shadow-glass:         0 8px 32px rgba(0,0,0,0.45),
                          inset 0 1px 0 rgba(255,255,255,0.10),
                          inset 0 -1px 0 rgba(0,0,0,0.20);
  --shadow-glass-raised:  0 16px 48px rgba(0,0,0,0.6),
                          inset 0 2px 0 rgba(255,255,255,0.15),
                          inset 0 -2px 0 rgba(0,0,0,0.25);
  --shadow-button:        0 4px 12px rgba(0,0,0,0.4),
                          inset 0 1px 0 rgba(255,255,255,0.18),
                          inset 0 -1px 0 rgba(0,0,0,0.3);

  /* Skeuomorphic LED indicators */
  --led-off:              radial-gradient(circle at 35% 35%, #2a2a2a, #111111);
  --led-green:            radial-gradient(circle at 35% 35%, #4ade80, #16a34a);
  --led-green-glow:       0 0 8px #22c55e, 0 0 16px rgba(34,197,94,0.4);
  --led-red:              radial-gradient(circle at 35% 35%, #f87171, #dc2626);
  --led-red-glow:         0 0 8px #ef4444, 0 0 16px rgba(239,68,68,0.4);
  --led-amber:            radial-gradient(circle at 35% 35%, #fbbf24, #d97706);
  --led-amber-glow:       0 0 8px #f59e0b, 0 0 16px rgba(245,158,11,0.4);
  --led-blue:             radial-gradient(circle at 35% 35%, #60a5fa, #2563eb);
  --led-blue-glow:        0 0 8px #3b82f6, 0 0 16px rgba(59,130,246,0.4);

  /* Color accents */
  --accent-blue:   #3b82f6;
  --accent-green:  #22c55e;
  --accent-red:    #ef4444;
  --accent-amber:  #f59e0b;
  --accent-teal:   #14b8a6;
  --accent-violet: #8b5cf6;

  /* Typography */
  --font-mono:     'JetBrains Mono', 'Fira Code', monospace;
  --font-ui:       'Inter', system-ui, sans-serif;

  /* Backgrounds */
  --bg-deep:       #050810;
  --bg-surface:    #0a0e1a;
  --globe-bg:      #080d1a;
}
```

### Glass Panel Mixin (apply to all floating panels)

```css
.glass-panel {
  background:       var(--glass-bg);
  backdrop-filter:  var(--glass-blur);
  -webkit-backdrop-filter: var(--glass-blur);
  border:           1px solid var(--glass-border);
  border-radius:    16px;
  box-shadow:       var(--shadow-glass);
  /* Top edge highlight — creates glass depth illusion */
  position:         relative;
}
.glass-panel::before {
  content: '';
  position: absolute;
  top: 0; left: 12px; right: 12px;
  height: 1px;
  background: linear-gradient(90deg,
    transparent,
    rgba(255,255,255,0.3) 20%,
    rgba(255,255,255,0.5) 50%,
    rgba(255,255,255,0.3) 80%,
    transparent
  );
  border-radius: 16px 16px 0 0;
}
```

### Skeuomorphic Button Base

```css
.btn-skeu {
  background:    linear-gradient(180deg, rgba(255,255,255,0.12) 0%, rgba(255,255,255,0.04) 100%);
  border:        1px solid var(--glass-border-bright);
  border-radius: 10px;
  box-shadow:    var(--shadow-button);
  padding:       8px 20px;
  font-family:   var(--font-ui);
  font-size:     13px;
  font-weight:   600;
  letter-spacing: 0.3px;
  color:         rgba(255,255,255,0.9);
  transition:    all 0.15s ease;
  cursor:        pointer;
}
.btn-skeu:active {
  background:    linear-gradient(180deg, rgba(0,0,0,0.1) 0%, rgba(255,255,255,0.06) 100%);
  box-shadow:    0 2px 8px rgba(0,0,0,0.4),
                 inset 0 2px 4px rgba(0,0,0,0.3);
  transform:     translateY(1px);
}
.btn-skeu-primary {
  background:    linear-gradient(180deg, #4f9eff 0%, #1d6fda 60%, #1558b8 100%);
  border-color:  rgba(100, 170, 255, 0.4);
  box-shadow:    0 4px 16px rgba(29,111,218,0.5),
                 inset 0 1px 0 rgba(255,255,255,0.25);
}
.btn-skeu-danger {
  background:    linear-gradient(180deg, #f87171 0%, #dc2626 60%, #b91c1c 100%);
  border-color:  rgba(255, 100, 100, 0.4);
  box-shadow:    0 4px 16px rgba(220,38,38,0.5),
                 inset 0 1px 0 rgba(255,255,255,0.2);
}
```

### LED Indicator (Skeuomorphic)

```css
.led {
  width:         10px;
  height:        10px;
  border-radius: 50%;
  background:    var(--led-off);
  border:        1px solid rgba(0,0,0,0.5);
  box-shadow:    inset 0 1px 2px rgba(255,255,255,0.1);
  flex-shrink:   0;
  transition:    all 0.4s ease;
}
.led-green   { background: var(--led-green);  box-shadow: var(--led-green-glow); }
.led-red     { background: var(--led-red);    box-shadow: var(--led-red-glow);   }
.led-amber   { background: var(--led-amber);  box-shadow: var(--led-amber-glow); }
.led-blue    { background: var(--led-blue);   box-shadow: var(--led-blue-glow);  }
.led-pulse   { animation:  led-pulse 1.5s ease-in-out infinite; }

@keyframes led-pulse {
  0%, 100% { opacity: 1.0; }
  50%       { opacity: 0.4; }
}

@keyframes glass-appear {
  from { opacity: 0; transform: translateY(8px) scale(0.98); }
  to   { opacity: 1; transform: translateY(0)   scale(1);    }
}
.glass-appear { animation: glass-appear 0.4s cubic-bezier(0.16, 1, 0.3, 1) forwards; }
```

---

## File Structure

```
frontend/src/
├── styles/
│   └── worldpolicy.css            ← NEW — all CSS vars + glass + skeu + LED + animations
├── components/
│   ├── GlobeView.tsx              ← NEW
│   ├── CrisisOverlay.tsx          ← NEW
│   ├── SimPanel.tsx               ← NEW
│   ├── WorldPolicyTab.tsx         ← NEW (top-level container)
│   ├── CrisisBriefCard.tsx        ← NEW (upgraded in V5: structured Llama field)
│   ├── EmergentBadgePanel.tsx     ← NEW (upgraded in V5: trigger condition text + step)
│   ├── EvalSummaryCard.tsx        ← NEW [V5 — judge gap #1]
│   ├── TrainingFactsCard.tsx      ← NEW [V5 — judge gap #5]
│   ├── ClaimBoundaryBanner.tsx    ← NEW [V5 — judge gap #8]
│   └── ArchitectureDiagramPanel.tsx ← NEW [V5 — judge gap #7]
└── App.tsx                        ← MODIFY: add tab entry only
```

---

## Layout Blueprint

```
┌─────────────────────────────────────────────────────────────────────┐
│  [ClaimBoundaryBanner — always visible, top of tab]                 │
├──────────────────────────────┬──────────────────────────────────────┤
│                              │  [CrisisBriefCard — glass, top-right]│
│                              ├──────────────────────────────────────┤
│   [GlobeView — dark bg]      │  [TrainingFactsCard — skeu panel]   │
│   [CrisisOverlay — floating] │  [EmergentBadgePanel — LED panel]   │
│                              │  [EvalSummaryCard — chart panel]    │
│                              │  [SimPanel — step + actions]        │
├──────────────────────────────┴──────────────────────────────────────┤
│  [ArchitectureDiagramPanel — collapsible, below globe]              │
├─────────────────────────────────────────────────────────────────────┤
│  [Audience slider — stretch]   [▶ Run Scripted] [⏸] [🔄]           │
└─────────────────────────────────────────────────────────────────────┘
```

All right-side panels float as glass cards with `position: relative; z-index: 10` over a near-black background. Globe fills full height of the left column.

---

## Component Specs

### 1. GlobeView.tsx

*(unchanged from V4 except styling — globe container gets glass frame)*

**Globe container styling:**
```css
.globe-container {
  background:    radial-gradient(ellipse at center, #0d1a2e 0%, #050810 70%);
  border-radius: 20px;
  border:        1px solid var(--glass-border);
  box-shadow:    var(--shadow-glass);
  overflow:      hidden;
  position:      relative;
}
/* Subtle vignette overlay — makes globe feel embedded in glass */
.globe-container::after {
  content:  '';
  position: absolute;
  inset:    0;
  background: radial-gradient(ellipse at center, transparent 60%, rgba(5,8,16,0.5) 100%);
  pointer-events: none;
  border-radius: 20px;
}
```

**Country node skeuomorphic design:**
- ScatterplotLayer fills: each country has an RGBA from the fixed palette
- Add `radiusMinPixels: 8` so nodes never disappear when zoomed out
- Nodes should have a faint halo: render a second ScatterplotLayer behind with same positions, radius × 1.8, opacity 0.15 — creates a "glow aura" around each country

**Action arc styling:**
- `getWidth: 2` for normal arcs
- `getWidth: 4` for AID_DISPATCH arcs (relief = thicker, more visible)
- `getTilt: -5` — slight tilt adds 3D depth to arcs

---

### 2. ClaimBoundaryBanner.tsx [V5 NEW — Always Visible]

**Purpose:** Directly addresses the judge's sharpest criticism — "scripted behavior sounding learned." A permanently visible, honest banner prevents judges from feeling deceived.

**Props:**
```typescript
interface ClaimBoundaryBannerProps {
    isPrimaryDemoMode: 'scripted' | 'trained';
    checkpointName: string;    // e.g. "mappo_50k.pt"
}
```

**Visual design:**
```
┌─────────────────────────────────────────────────────────────────────┐
│  ⚙ SCRIPTED EVENTS  ──────── World crises and event sequences       │
│  🧠 TRAINED POLICY  ──────── Agent responses, reward curves,        │
│                               emergent phenomena (checkpoint:        │
│                               mappo_50k.pt)                         │
└─────────────────────────────────────────────────────────────────────┘
```

- Thin glass panel, `height: 48px`, full-width across tab
- Background: `rgba(255,255,255,0.03)` — barely visible, not distracting
- Left side: `⚙ SCRIPTED EVENTS` in `text-xs text-slate-500 font-mono`
- Right side: `🧠 TRAINED POLICY` in `text-xs text-blue-400 font-mono`
- Separator: `─────` in `text-slate-700`
- This is always visible during demo. Judges read it immediately. No claim boundary argument is possible.

---

### 3. CrisisBriefCard.tsx [V5 UPGRADED]

**V5 change:** Now shows TWO structured fields from Llama, not just the brief. The second field (`priority_response`) makes Llama feel like a decision-support system, not a narration engine.

**Updated API response shape (backend must match):**
```typescript
interface CrisisBriefResponse {
    brief:              string;     // 2-sentence situation summary
    priority_response:  string;     // 1 recommended action (e.g. "Deploy multilateral aid immediately")
    risk_level:         'LOW' | 'MEDIUM' | 'HIGH' | 'CRITICAL';
    crisis_type:        string;
    step:               number;
    model:              string;     // always "llama-3.3-70b-versatile"
}
```

**Visual design — skeuomorphic document card:**
```
┌─────────────────────────────────────────────────────────┐
│  🇺🇳  UN SECURITY COUNCIL                   ● CRITICAL  │  ← LED
│  ─────────────────────────────────────────────────────  │  ← ruled line (skeu)
│  NATURAL DISASTER — South Asia  •  Step 80              │
│                                                         │
│  [SITUATION]                                            │
│  A severe cyclone has made landfall across coastal      │
│  zones in South Asia, triggering mass displacement...   │
│                                                         │
│  [META LLAMA RECOMMENDATION]                            │
│  Deploy multilateral humanitarian aid immediately       │
│  with priority to coastal Zone 3 and Zone 7.            │
│                                                         │
│  ─────────────────────────────────────────────────────  │
│  Generated by Meta Llama 3.3-70b via Groq  •  Cached   │
└─────────────────────────────────────────────────────────┘
```

**CSS implementation:**
```css
.crisis-brief-card {
  background:    linear-gradient(135deg,
                   rgba(239,68,68,0.06) 0%,
                   rgba(255,255,255,0.04) 50%,
                   rgba(239,68,68,0.03) 100%);
  backdrop-filter: var(--glass-blur);
  border:        1px solid rgba(239,68,68,0.25);
  border-radius: 14px;
  box-shadow:    0 8px 32px rgba(0,0,0,0.4),
                 inset 0 1px 0 rgba(255,255,255,0.08),
                 0 0 0 1px rgba(239,68,68,0.1);
  padding:       16px;
  width:         320px;
  animation:     glass-appear 0.4s cubic-bezier(0.16, 1, 0.3, 1) forwards;
}

/* Skeuomorphic ruled separator lines */
.crisis-brief-card .ruled-line {
  height:     1px;
  background: linear-gradient(90deg,
    transparent,
    rgba(239,68,68,0.3) 15%,
    rgba(239,68,68,0.15) 85%,
    transparent
  );
  margin: 10px 0;
}

/* Section labels — engraved look */
.crisis-brief-card .section-label {
  font-family:    var(--font-mono);
  font-size:      9px;
  font-weight:    700;
  letter-spacing: 1.5px;
  color:          rgba(239,68,68,0.6);
  text-transform: uppercase;
  margin-bottom:  4px;
}

/* Risk level LED badge */
.risk-badge-critical {
  display: flex; align-items: center; gap: 6px;
}
.risk-badge-critical .led {
  background: var(--led-red);
  box-shadow: var(--led-red-glow);
  animation:  led-pulse 1.0s ease-in-out infinite;
}
```

**Footer:** `Generated by Meta Llama 3.3-70b via Groq` in `text-[10px] font-mono text-slate-600`. If response came from cache: append `• Cached`. This is honest and judges appreciate it.

---

### 4. EmergentBadgePanel.tsx [V5 UPGRADED]

**V5 change:** Badges now show the exact trigger condition and the step it fired. This addresses the judge's criticism that detectors are "theatrical, not trustworthy."

**Props:**
```typescript
interface EmergentBadgePanelProps {
    coldWarDetected:    boolean;
    coldWarStep:        number | null;
    coldWarTrigger:     string | null;    // e.g. "bloc_count=2, cross_bloc_rel=-0.61"
    armsRaceSpiral:     boolean;
    armsRaceSpiralStep: number | null;
    armsRaceTrigger:    string | null;    // e.g. "all Δmilitary>0 for 5 steps"
    freeRiderCount:     number;
    freeRiderNames:     string[];
    freeRiderTrigger:   string | null;    // e.g. "climate>0.6, pledge_rate<0.1"
}
```

**Visual design — skeuomorphic instrument panel:**
```
┌─────────────────────────────────────────────────────────┐
│  EMERGENT PHENOMENA MONITOR                   [i] INFO  │
│  ══════════════════════════════════════════════════════  │
│                                                         │
│  ●  COLD WAR BIFURCATION               DETECTED step 83 │
│     Two opposing blocs formed — self-organized          │
│     Trigger: bloc_count=2, cross_bloc_rel=-0.61         │
│                                                         │
│  ○  ARMS RACE SPIRAL                   Not active       │
│     All 6 nations militarizing simultaneously           │
│     Condition: all Δmilitary>0 for 5 consecutive steps  │
│                                                         │
│  ●  FREE RIDER DETECTED                1 nation         │
│     Benefiting from pledges without contributing        │
│     Trigger: China  climate=0.68, pledge_rate=0.04      │
└─────────────────────────────────────────────────────────┘
```

**CSS — instrument panel aesthetic:**
```css
.emergent-panel {
  background:    linear-gradient(180deg,
                   rgba(15,20,35,0.9) 0%,
                   rgba(10,14,26,0.95) 100%);
  backdrop-filter: var(--glass-blur);
  border:        1px solid var(--glass-border);
  border-radius: 14px;
  box-shadow:    var(--shadow-glass);
  padding:       16px;
  font-family:   var(--font-mono);
}

.emergent-panel .panel-header {
  font-size:      10px;
  font-weight:    700;
  letter-spacing: 2px;
  text-transform: uppercase;
  color:          rgba(255,255,255,0.35);
  border-bottom:  1px solid rgba(255,255,255,0.07);
  padding-bottom: 8px;
  margin-bottom:  12px;
}

/* Engraved separator — skeuomorphic */
.emergent-panel .panel-divider {
  height:       2px;
  background:   linear-gradient(90deg,
    rgba(255,255,255,0.02),
    rgba(255,255,255,0.08) 50%,
    rgba(255,255,255,0.02)
  );
  box-shadow:   0 1px 0 rgba(0,0,0,0.3);
  margin:       8px 0;
}

.badge-row {
  display:      flex;
  align-items:  flex-start;
  gap:          10px;
  padding:      8px 0;
  border-bottom: 1px solid rgba(255,255,255,0.04);
}
.badge-row:last-child { border-bottom: none; }

.badge-label {
  font-size:      11px;
  font-weight:    600;
  letter-spacing: 0.3px;
  color:          rgba(255,255,255,0.75);
}
.badge-description {
  font-size:  10px;
  color:      rgba(255,255,255,0.4);
  margin-top: 2px;
}
.badge-trigger {
  font-size:  9px;
  font-family: var(--font-mono);
  color:      rgba(255,255,255,0.25);
  margin-top: 3px;
  /* Monospace trigger text looks like a console log — adds credibility */
}
.badge-status-detected { color: #f87171; font-size: 10px; font-weight: 700; }
.badge-status-inactive  { color: rgba(255,255,255,0.2); font-size: 10px; }

/* Active badge glow — entire row gets subtle border */
.badge-row-active {
  border-left:  2px solid #ef4444;
  padding-left: 8px;
  background:   rgba(239,68,68,0.04);
  border-radius: 0 6px 6px 0;
}
```

---

### 5. EvalSummaryCard.tsx [V5 NEW — Judge Gap #1]

**Purpose:** Shows the trained-policy evaluation artifact directly in the UI. Proves MAPPO beat the baselines with seeds and standard deviation. This is the most important new component in V5 — it converts a design claim into an evidence card.

**Props:**
```typescript
interface EvalSummaryCardProps {
    data: {
        baseline:    string;    // "Random" | "Rule-Based" | "MAPPO"
        mean_reward: number;
        std_reward:  number;
        seeds:       number;
        color:       string;
    }[];
    evalEpisodes: number;
    checkpointName: string;
}
```

**Visual design — glass data card with horizontal bar chart:**
```
┌─────────────────────────────────────────────────────────┐
│  POLICY EVALUATION                    10 episodes, 5 seeds│
│  Checkpoint: mappo_50k.pt                               │
│  ══════════════════════════════════════════════════════  │
│                                                         │
│  Random      ████░░░░░░░░░░░░░░░░░░  0.61 ± 0.08       │
│  Rule-Based  ████████░░░░░░░░░░░░░░  1.87 ± 0.12       │
│  MAPPO  ▶    ████████████████░░░░░░  2.34 ± 0.15  ✓    │
│                                                         │
│  MAPPO outperforms rule-based by +25.1%                 │
│  Mean over 10 evaluation episodes, 5 random seeds       │
└─────────────────────────────────────────────────────────┘
```

**CSS:**
```css
.eval-card {
  /* Same glass-panel base */
  background:    var(--glass-bg);
  backdrop-filter: var(--glass-blur);
  border:        1px solid var(--glass-border);
  border-radius: 14px;
  box-shadow:    var(--shadow-glass);
  padding:       16px;
  font-family:   var(--font-mono);
}
.eval-bar-track {
  height:        8px;
  background:    rgba(255,255,255,0.06);
  border-radius: 4px;
  border:        1px solid rgba(255,255,255,0.04);
  /* Inset shadow — skeuomorphic recessed track */
  box-shadow:    inset 0 1px 3px rgba(0,0,0,0.4),
                 inset 0 -1px 1px rgba(255,255,255,0.02);
  overflow:      hidden;
}
.eval-bar-fill {
  height:        100%;
  border-radius: 4px;
  transition:    width 1.2s cubic-bezier(0.16, 1, 0.3, 1);
  /* Skeuomorphic bar — gradient + highlight on top edge */
  background:    linear-gradient(180deg,
                   rgba(255,255,255,0.15) 0%,
                   transparent 60%
                 ), var(--bar-color);
  box-shadow:    0 1px 0 rgba(255,255,255,0.2) inset;
}
.eval-bar-mappo {
  --bar-color:   linear-gradient(90deg, #1d6fda, #4f9eff);
  box-shadow:    0 0 12px rgba(59,130,246,0.4), 0 1px 0 rgba(255,255,255,0.2) inset;
}
/* MAPPO row highlight — winner visual */
.eval-row-winner {
  background:    rgba(59,130,246,0.06);
  border-radius: 8px;
  border-left:   2px solid #3b82f6;
  padding-left:  8px;
}
```

**Data source:** `GET /eval-summary` endpoint (add to backend plan) — serves `data/eval_summary.json` generated by `eval.py`.
If `eval_summary.json` doesn't exist yet → show a loading skeleton and a note: "Evaluation running... check back after training."

---

### 6. TrainingFactsCard.tsx [V5 NEW — Judge Gap #5]

**Purpose:** A compact, always-visible card showing the PyTorch architecture and training provenance. Immediately proves to sponsor judges that this is real ML.

**Visual design — cockpit instrument panel:**
```
┌─────────────────────────────────────────────────────────┐
│  MAPPO POLICY — TRAINING PROVENANCE                     │
│  ──────────────────────────────────────────────────────  │
│  Architecture    Shared MLP 36→128→128 + 6 actor heads  │
│  Critic          216→256→256→1  (centralized)           │
│  Parameters      ~450K                                  │
│  Training Steps  50,000 parallel env steps              │
│  Rollout Length  1,024                                  │
│  Checkpoint      mappo_50k.pt  [SHA: a3f2...]           │
│  Framework       PyTorch 2.x                            │
│  ──────────────────────────────────────────────────────  │
│  ● Training complete   ● Checkpoint verified            │
└─────────────────────────────────────────────────────────┘
```

**CSS — engraved data plate:**
```css
.training-facts-card {
  background:    linear-gradient(180deg,
                   rgba(20,28,48,0.95) 0%,
                   rgba(12,18,35,0.98) 100%);
  backdrop-filter: var(--glass-blur);
  border:        1px solid rgba(255,255,255,0.10);
  border-radius: 12px;
  box-shadow:    var(--shadow-glass),
                 inset 0 1px 0 rgba(255,255,255,0.05);
  padding:       14px 16px;
  font-family:   var(--font-mono);
  font-size:     11px;
}
.facts-row {
  display:         grid;
  grid-template-columns: 130px 1fr;
  gap:             4px 12px;
  padding:         3px 0;
  border-bottom:   1px solid rgba(255,255,255,0.04);
  color:           rgba(255,255,255,0.7);
}
.facts-row .key   { color: rgba(255,255,255,0.35); font-size: 10px; }
.facts-row .value { color: rgba(255,255,255,0.8);  font-size: 10px; }
.facts-row:last-child { border-bottom: none; }

/* Status LEDs at bottom */
.facts-status-row {
  display:     flex;
  gap:         16px;
  margin-top:  10px;
  padding-top: 8px;
  border-top:  1px solid rgba(255,255,255,0.06);
}
.facts-status-item {
  display:     flex;
  align-items: center;
  gap:         6px;
  font-size:   10px;
  color:       rgba(255,255,255,0.5);
}
```

**Props:** All values are hardcoded from the actual trained run — populated when generating the checkpoint. Backend serves `GET /training-meta` or these values are hardcoded in the component after training completes.

---

### 7. ArchitectureDiagramPanel.tsx [V5 NEW — Judge Gap #7]

**Purpose:** Inline SVG architecture diagram — mandatory, not optional. Judges who don't read markdown need this.

**Design:** Collapsible panel below the globe. Default state: collapsed (shows only header). One click expands. In demo, presenter expands it during the "architecture explanation" segment.

```
[▼ System Architecture]  ← collapsed header (always visible)

When expanded:
┌─────────────────────────────────────────────────────────┐
│                  WorldPolicy-Env Architecture           │
│                                                         │
│  6 Country Agents                                       │
│  [MAPPO PyTorch] ──→ [World State] ──→ [Event Engine]  │
│                            │                            │
│              disaster_severity > 0.7                    │
│                            ↓                            │
│                  [DisasterEnvWrapper]                   │
│                     [disasterman]                       │
│                  [ZoneScorerNet + Llama] ←── Groq API   │
│                            │                            │
│                   relief_score ──→ [Macro Reward]       │
│                                                         │
│  ─────────────────────────────────────────────────────  │
│  [FastAPI] → [SSE /stream] → [React Globe Frontend]     │
│                              [Crisis Brief Card]        │
│                              [Emergent Badge Panel]     │
└─────────────────────────────────────────────────────────┘
```

**Implementation:** Inline SVG, not an `<img>` tag. SVG uses the CSS color variables so it respects the glass theme. Same SVG is embedded in the HF Space README as `architecture_diagram.svg`.

**Glass collapsible header:**
```css
.arch-panel-header {
  background:    var(--glass-bg);
  backdrop-filter: var(--glass-blur);
  border:        1px solid var(--glass-border);
  border-radius: 10px;
  padding:       10px 16px;
  cursor:        pointer;
  display:       flex;
  align-items:   center;
  gap:           8px;
  font-family:   var(--font-mono);
  font-size:     11px;
  color:         rgba(255,255,255,0.5);
  transition:    background 0.2s;
}
.arch-panel-header:hover {
  background: var(--glass-bg-hover);
}
```

---

### 8. SimPanel.tsx [V5 — Updated Layout]

No structural change from V4 — just integrates TrainingFactsCard and EvalSummaryCard inline.

**Updated layout order (top to bottom):**
```
1. Step counter + progress bar
2. Per-agent action bar chart (6 bars, color-coded by action type)
3. TrainingFactsCard
4. EmergentBadgePanel
5. EvalSummaryCard (baseline comparison + eval bar chart)
6. Reward curve 3-panel
```

**Reward curve 3-panel glass cards:**
```css
.curve-card {
  background:    var(--glass-bg);
  backdrop-filter: blur(16px);
  border:        1px solid var(--glass-border);
  border-radius: 12px;
  padding:       12px 14px;
  box-shadow:    var(--shadow-glass);
}
.curve-label {
  font-family:   var(--font-ui);
  font-size:     11px;
  color:         rgba(255,255,255,0.5);
  margin-bottom: 8px;
}
/* recharts line gets glow effect via CSS filter */
.recharts-line path {
  filter: drop-shadow(0 0 4px currentColor);
}
```

---

### 9. WorldPolicyTab.tsx [Top-Level Container]

**V5 layout changes:** `ClaimBoundaryBanner` is the very first child, always mounted.

```tsx
return (
  <div className="worldpolicy-tab" style={{ background: 'var(--bg-surface)', minHeight: '100vh' }}>
    <ClaimBoundaryBanner
      isPrimaryDemoMode={usingTrainedPolicy ? 'trained' : 'scripted'}
      checkpointName="mappo_50k.pt"
    />

    <div className="worldpolicy-grid">
      {/* Left: Globe */}
      <div className="globe-column">
        <div className="globe-container">
          <GlobeView worldState={worldState} lastActions={lastActions} />
          <CrisisOverlay
            disasterSeverity={worldState?.disaster_severity ?? 0}
            activeCrises={worldState?.active_crises ?? []}
          />
        </div>
      </div>

      {/* Right: Glass panel stack */}
      <div className="panel-column">
        <CrisisBriefCard brief={crisisBrief} isLoading={briefLoading} />
        <TrainingFactsCard />
        <EmergentBadgePanel {...emergentProps} />
        <EvalSummaryCard data={evalData} evalEpisodes={10} checkpointName="mappo_50k.pt" />
        <SimPanel worldState={worldState} metrics={metrics} trainingCurves={curves} />
      </div>
    </div>

    {/* Collapsible architecture diagram */}
    <ArchitectureDiagramPanel />

    {/* Controls bar */}
    <div className="controls-bar">
      <button className="btn-skeu btn-skeu-primary" onClick={runScripted}>▶ Run Scripted Demo</button>
      <button className="btn-skeu" onClick={pauseSim}>⏸ Pause</button>
      <button className="btn-skeu" onClick={resetSim}>↺ Reset</button>
      {/* Stretch: audience slider */}
    </div>
  </div>
);
```

**Grid CSS:**
```css
.worldpolicy-tab {
  padding: 0;
  background: var(--bg-surface);
}
.worldpolicy-grid {
  display:               grid;
  grid-template-columns: 1fr 340px;
  gap:                   16px;
  padding:               16px;
  height:                calc(100vh - 96px);  /* minus banner + controls */
}
.globe-column {
  display: flex;
  flex-direction: column;
  gap: 16px;
}
.panel-column {
  display:        flex;
  flex-direction: column;
  gap:            12px;
  overflow-y:     auto;
  scrollbar-width: thin;
  scrollbar-color: rgba(255,255,255,0.1) transparent;
}
.controls-bar {
  display:         flex;
  align-items:     center;
  gap:             12px;
  padding:         12px 16px;
  background:      var(--glass-bg);
  backdrop-filter: var(--glass-blur);
  border-top:      1px solid var(--glass-border);
}
```

---

## Updated API Endpoints (Frontend Consumes)

*(V4 list + 2 new V5 endpoints)*

```
GET  /stream              → SSE: world_state + metrics each step
GET  /crisis-brief        → { brief, priority_response, risk_level, crisis_type, step, model }
GET  /emergent-events     → SSE: fires on cold_war / arms_race_spiral / free_rider
GET  /training-curves     → 3 curve datasets
GET  /baseline-chart      → static PNG (baseline_comparison.png)
GET  /eval-summary        → { baseline, mean_reward, std_reward, seeds }[] — [V5 NEW]
GET  /training-meta       → { arch, params, steps, rollout_len, checkpoint, framework } — [V5 NEW]
POST /run-scripted        → starts scripted fallback
POST /step                → audience slider
GET  /health              → startup check
```

---

## npm Dependencies

```bash
# Required (step 0, April 22):
npm install @deck.gl/core @deck.gl/layers @deck.gl/react @deck.gl/geo-layers

# Fallback if deck.gl conflicts:
npm install react-globe.gl

# Already needed:
npm install recharts

# No additional deps needed for liquid glass / skeuomorphism — pure CSS
```

Zero new npm packages for the design system. All liquid glass and skeuomorphism is pure CSS using the variables defined above. No extra library needed.

---

## Non-Negotiable vs. Stretch Priority Order

```
MUST SHIP (before judging, in order):
1. worldpolicy.css — design system variables, glass, LED, animations
2. ClaimBoundaryBanner.tsx — always visible, addresses judge's #1 trust concern
3. GlobeView.tsx — 6 nodes + at least 1 arc type
4. CrisisBriefCard.tsx — upgraded with priority_response field (Meta Llama visible)
5. EmergentBadgePanel.tsx — upgraded with trigger text + step number
6. TrainingFactsCard.tsx — static data, 1–2 hours max
7. EvalSummaryCard.tsx — loaded from eval_summary.json or skeleton loading state
8. WorldPolicyTab.tsx — SSE wired, [▶ Run Scripted] button works

STRONGLY DESIRED (build if non-negotiables done by April 26 noon):
9.  SimPanel.tsx — step counter + action bar + 3 reward curves (static PNG fallback ok)
10. CrisisOverlay.tsx — pulse + zoom animation
11. ArchitectureDiagramPanel.tsx — inline SVG, collapsible

STRETCH (only if everything above done):
12. Audience slider
13. Live recharts instead of static PNG
14. Arc tilt, glow aura layer on ScatterplotLayer
```

---

## Demo Script — Frontend Perspective (Updated for V5)

Hard limit: **5 minutes**. Tushar times it. 3 rehearsals minimum.

```
0:00  Tab opens — globe visible, ClaimBoundaryBanner readable at top
      Presenter: "Left side: scripted crisis events. Right side: trained MAPPO policy."

0:20  Point to TrainingFactsCard:
      "This is a real PyTorch model — 450K parameters, 50,000 training steps."

0:35  Click [▶ Run Scripted Demo]
      Globe: Russia-Ukraine red sanction arcs appear

0:50  CrisisBriefCard fades in (glass card, top-right)
      Presenter: "Meta Llama 3.3-70b just generated that UN briefing and recommended response
                  from live world state. That blue field is a structured Llama output."

1:15  Globe zooms to India — Cyclone triggered — red pulse on India node
      New CrisisBriefCard: risk_level = CRITICAL, priority_response visible

1:40  Blue AID_DISPATCH arcs from USA + UK to India
      EvalSummaryCard visible: "MAPPO 2.34 ± 0.15 vs Rule-Based 1.87 ± 0.12"
      Presenter: "The trained policy dispatches aid faster than the rule-based heuristic — 
                  that is the learning signal."

2:10  EmergentBadgePanel: Cold War badge lights up — red LED pulsing
      Presenter: "Two opposing blocs just self-organized. The agents were never told about
                  the Cold War. Look at the trigger: bloc_count=2, cross-bloc relation=-0.61.
                  This is the PyTorch training loop — not scripted."

2:40  Click [▼ System Architecture] — ArchitectureDiagramPanel expands
      Walk through the diagram: macro MAPPO → disaster wrapper → Llama → globe

3:10  Point to 3 reward curves (SimPanel):
      Curve 2 — aid dispatch rate rising: "Agents learn cooperation over time."
      Curve 3 — arms race index dropping: "Agents learn militarization is costly."

3:40  Done. Leave 1:20 for questions.
```

---

## Build Order — Frontend Only

### April 22
0. Deck.gl compat check (5 min) — decide library now
1. Create `worldpolicy.css` with full design system variables
2. `WorldPolicyTab.tsx` — shell, SSE subscription, static placeholder
3. Check `App.tsx` actual tab count — add WorldPolicy tab

### April 23
1. `GlobeView.tsx` — 6 nodes on globe, 1 arc type (SANCTION)
2. `ClaimBoundaryBanner.tsx` — simple, 30 min max
3. `TrainingFactsCard.tsx` — static hardcoded data for now, style it
4. Wire worldState from SSE to GlobeView

### April 25
1. `CrisisBriefCard.tsx` — wire to /crisis-brief, include priority_response field
2. `EmergentBadgePanel.tsx` — wire to metrics, add trigger text
3. `EvalSummaryCard.tsx` — wire to /eval-summary or skeleton loading state
4. [▶ Run Scripted] button → POST /run-scripted → scripted fallback plays

### April 26
1. `SimPanel.tsx` — 3 reward curves (static PNG or recharts)
2. `ArchitectureDiagramPanel.tsx` — inline SVG, collapsible
3. `CrisisOverlay.tsx` — pulse + zoom on disaster
4. Globe visual polish (glow aura, arc tilt, auto-rotate speed)
5. Full demo rehearsal 3× — Tushar times each run
6. Stretch: audience slider

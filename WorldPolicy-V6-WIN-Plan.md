# WorldPolicy-Env V6 — The 95%+ Hackathon Win Plan
**Hackathon:** Scaler × PyTorch × HuggingFace × Meta — Bengaluru, April 25–26 2026
**Team:** Krish · Raj · Tushar
**Deadline:** ~50 hours from now
**Objective:** Score 95%+ across ALL judging dimensions. This plan closes every remaining gap.

---

## Why V5 Scores ~52/60 and Not 57+

V5 is strong on proof and credibility. It has eval harness, detector tests, claim boundary, baseline chart, and training metadata. Those are necessary but not sufficient for 95%+. The remaining gap is in three areas:

| Gap | Current State | What 95%+ Requires |
|-----|--------------|-------------------|
| **Agent Intelligence** | 6 MAPPO agents, 8 actions each | Richer policy differentiation, coalition behavior, adaptive crisis response |
| **Product Experience** | Glass panels + globe | A **visceral, irreversible emotional moment** in the first 60 seconds |
| **Narrative Coherence** | Crisis brief + risk level | A complete story arc: calm → crisis → escalation → intervention → outcome |
| **Sponsor Visibility** | Llama used for brief | Llama, PyTorch, HuggingFace ALL visible and explained in demo |
| **Novelty Hook** | MARL on geopolitics | One feature judges have never seen before in any hackathon project |

---

## The Big Idea You Are Missing: The "Cascade Failure" Demo Moment

Right now your demo shows agents responding to crises. That is good but not jaw-dropping. What wins is **irreversibility and consequence**.

Here is the single feature that will make judges go silent:

### Feature: The Cascade Collapse Visualizer

Show a live cascade: one crisis triggers a second, which destabilizes a third, until the world system collapses unless the agent intervenes at the right moment. Make it a **race against time** — the audience watches the collapse unfold in real-time on the globe. Then show the MAPPO agent catching it and the rule-based agent failing.

This is emotionally unforgettable. It is technically grounded in your existing event engine. It costs approximately 4 hours to implement. It will be the first thing every judge mentions when they recap the demo.

---

## V6 Feature Plan — Complete Addition List

### TIER 1: Must-Have for 95%+ (implement first, ~28 hours)

#### 1. Cascade Failure Mode (4 hrs — Krish)
- Add a `cascade_trigger` flag to the event engine: when `disasterseverity > 0.75` AND `armsraceindex > 0.5`, automatically spawn a second crisis of a different type at step+10
- Track `cascade_chain` list: [crisis1 → crisis2 → crisis3] with timestamps
- Expose `GET /cascade-state` returning the current chain, severity escalation curve, and predicted collapse step
- Frontend: animated red cascade line on globe from country to country, each crisis node pulsing at increasing frequency, a **"Collapse Imminent in N steps"** countdown shown in amber LED style

#### 2. MAPPO vs Rule-Based Live Split Screen (5 hrs — Raj frontend, Krish backend)
- Run two simultaneous episodes in the backend: one with MAPPO policy, one with rule-based policy
- Both stream via SSE on separate channels: `GET /stream/mappo` and `GET /stream/rulebased`
- Frontend shows two side-by-side globe panels or a split-panel with two metrics columns
- Label them "MAPPO (Trained)" and "Baseline (Rule-Based)" with the same styling as ClaimBoundaryBanner
- Show divergence score live: `ΔGDP, ΔRelations, ΔArmsRace, ΔHumanitarian`
- This is your single most powerful demonstration that training worked — judges see it live, not in a static chart

#### 3. Policy Explanation Drawer (3 hrs — Raj)
- Every time an agent takes an action, clicking on that country on the globe opens a drawer
- Drawer shows: action taken, top 3 observation features that drove it, what the alternative actions were, expected reward delta
- Label: "Why did USA choose AID_DISPATCH?" → "Disaster severity 0.87 (high), Relations with India 0.43 (positive), Arms Race Index 0.2 (low → aid is safe)"
- This makes the MARL policy legible to a non-ML judge. It converts "black box" into "glass box"
- Backend: add `GET /agent-rationale/{agent_id}/{step}` that returns a pre-computed rationale dict (can be rule-based heuristic approximation of the policy gradient signal — label it as "policy feature attribution, approximate")

#### 4. Humanitarian Impact Panel (2 hrs — Raj)
- Add 3 derived metrics computed from world state each step:
  - `displacement_risk`: weighted function of disaster severity × affected population (hardcoded country populations)
  - `aid_adequacy`: ratio of AIDDISPATCH actions to total crisis severity across active events
  - `civilian_stress_index`: composite of GDP decline + climate stress + disaster severity
- Show as three gauge dials in the SimPanel — old-school analog gauges using CSS conic-gradient, labeled in red when critical
- These make the app feel like it has human stakes, not just abstract numbers

#### 5. World Outcome Summary Card (2 hrs — Raj)
- After every episode ends (terminated=True), show a full-screen modal with:
  - **"WORLD STABILIZED"** (green) or **"SYSTEMIC COLLAPSE"** (red) or **"FRAGILE PEACE"** (amber)
  - 5 metrics: Final GDP index, Conflict Level, Climate Health, Humanitarian Score, Alliance Stability
  - Compare: MAPPO outcome vs Rule-Based outcome (requires feature 2 above)
  - A single Llama-generated sentence: "Despite early escalation, coordinated aid dispatch prevented humanitarian catastrophe." (generated fresh, not cached)
- This gives every demo a clear climax and ending — judges love narrative completion

#### 6. Coalition Formation Detector (3 hrs — Krish)
- Add to `analytics.py`: detect when 3+ agents choose the same action type for 3+ consecutive steps
- Label as "Coalition Behavior" — a genuine emergent MARL phenomenon
- Show in EmergentBadgePanel as a 4th badge: "COALITION FORMED — USA, EU, UK all dispatching aid (step 45–48)"
- This is scientifically legit, easy to detect, and extremely impressive to judges because it shows multi-agent coordination emerging from training without being explicitly programmed

#### 7. Scenario Library with Named Crises (2 hrs — Raj backend, Tushar UI)
- Create `data/scenarios.json` with 5 named scenarios:
  - "South Asian Monsoon Cascade" — disaster → pandemic → economic crash
  - "Pacific Cold War" — arms race + trade war → bifurcation
  - "Climate Emergency Response" — climate emergency + free-rider detection
  - "Post-Conflict Reconstruction" — war → reconstruction → diplomatic normalization
  - "Perfect Storm" — all 5 crisis types, maximum severity
- Each scenario has: name, description, inject_sequence (list of {step, crisis_type, severity}), expected_emergent_phenomena
- Frontend: scenario picker dropdown in controls bar, each scenario loads in 1 click
- This makes the demo flexible — judges can say "show me the worst case" and you can do it immediately

#### 8. Groq Flash Brief (1 hr — Raj)
- Add a "Live Brief" button that bypasses the cache and calls Groq with the current live world state
- Shows a spinner for 1–2 seconds then animates in the fresh brief word by word (SSE streaming from Groq)
- Label: "Generating live analysis..." then "Generated by Meta Llama 3.3-70b via Groq • 1.2s"
- This proves the Groq/Llama integration is real and live, not just cached text

#### 9. Timeline Replay Scrubber (4 hrs — Raj frontend)
- Record every step's world state into a replay buffer in memory (cap at 500 steps per episode)
- `GET /replay` returns the full timeline as a JSON array
- Frontend: a horizontal timeline scrubber below the globe — drag to any step, globe and all panels update to that snapshot
- Play/pause button at 1x, 2x, 5x speed
- "Key moments" markers on the scrubber: crisis onset, detector fires, cascade trigger, intervention, outcome
- This makes the app feel like a professional incident analysis tool, not a live demo that you cannot pause

#### 10. HuggingFace Spaces Auto-Deploy (2 hrs — Tushar)
- Configure the repo for HF Spaces with a proper `README.md` that:
  - Embeds the architecture diagram SVG
  - Shows the reward curve PNG
  - Has the eval summary table with MAPPO improvement percentage
  - Links to the live demo URL
- This means judges can visit your HF Space and see the ML credentials before they even talk to you
- Massive credibility signal for the HuggingFace sponsor track

---

### TIER 2: High-Impact if Time Allows (~12 hrs available)

#### 11. Counterfactual Sandbox (4 hrs — Krish + Raj)
- "What if" mode: freeze the world state at any step, change one variable (e.g., disaster severity, one country's action), and rerun from that point
- Show the divergence in outcomes: "Without India's AIDDISPATCH at step 47, humanitarian score would have dropped by 34%"
- Backend: `POST /counterfactual` with body `{from_step, overrides: {agent_id: action | state_key: value}}`
- Frontend: shows a branching timeline visualization — the actual path vs the counterfactual path

#### 12. Burden-Sharing Justice Panel (2 hrs — Raj)
- Extend the free-rider detector into a full justice panel
- Show a horizontal bar for each country: how much they are benefiting from collective action vs how much they are contributing
- Color: green = net contributor, red = net free-rider, gray = neutral
- Label: "Burden-Sharing Index" — a novel geopolitical framing that is technically grounded in your existing pledge-rate and climate metrics

#### 13. Live Audience Control Mode (3 hrs — Tushar + Raj)
- During the demo, show a QR code that opens a mobile page
- Audience members can vote: "Should USA dispatch aid or deploy military?" — majority vote overrides the agent for that step
- Shows in real time on the globe as the audience-chosen action fires
- Requires: simple WebSocket server or long-poll endpoint, mobile-friendly vote page
- This is a crowd-pleaser that makes the demo interactive and memorable

#### 14. Model Confidence Calibration (2 hrs — Krish)
- For each step, compute the policy's action probability distribution (softmax over logits) for each agent
- Expose as `GET /confidence/{agent_id}` — a distribution over 8 actions
- Frontend: show as a horizontal bar distribution under each country card
- Label: "Policy confidence" with a note that this is the trained policy's probability distribution, not a heuristic score
- This is a legitimate ML metric that shows the model is actually probabilistic, not deterministic

#### 15. Diplomatic Relations Graph (1 hr — Raj)
- Add a force-directed graph view showing the 6 countries as nodes and their relations as edges
- Edge thickness = |relation value|, edge color = green (positive) / red (negative)
- Updates live from SSE stream
- This makes the cold-war detection visually obvious — judges can see the graph split into two clusters in real time

---

### TIER 3: Polish That Separates Winners from Finalists (6 hrs — Tushar owns all)

#### 16. Demo Mission Cards
- Create 5 "mission cards" in the UI — each one is a guided narrative:
  - Mission 1: "Prevent the South Asian Cascade" — 5 minutes, trigger disaster, watch cascade, show MAPPO intervention
  - Mission 2: "Detect the Cold War Before It Happens" — watch bloc formation, cold war detector fires
  - Mission 3: "Prove the AI Learned" — MAPPO vs Rule-Based split screen, eval summary card
  - Mission 4: "What If You Made the Wrong Call?" — counterfactual sandbox
  - Mission 5: "Free Riders and Fair Burden" — justice panel, free rider detection
- Each mission card shows: title, 1-line objective, estimated time, "Start" button that loads the correct scenario

#### 17. Sound Design (2 hrs)
- 3 sounds only: (1) soft ping when crisis brief updates, (2) low alarm when risk level hits CRITICAL, (3) ascending chime when world stabilizes
- Use Web Audio API, no external files — all generated programmatically
- This costs nothing to implement but dramatically increases the "this is a real product" feeling

#### 18. Keyboard Demo Shortcuts (30 min — Tushar)
- D = run scripted demo, Space = pause/play, R = reset, 1-5 = load scenario 1-5, E = expand eval card, A = expand architecture diagram
- Shows in a small "Shortcuts" tooltip at bottom right
- Means during a demo the presenter never scrambles for the right button

#### 19. Real-Time Metrics Ticker (1 hr — Raj)
- A horizontal ticker at the top of the screen, like a financial terminal
- Shows: `GDP INDEX: 0.847 ▼ | ARMS RACE: 0.23 ▲ | HUMANITARIAN SCORE: 0.61 ▼ | STEP: 47 | RISK: HIGH`
- Updates every SSE tick
- Pure CSS marquee with momentum, no libraries

#### 20. "About This System" Panel (1 hr — Tushar)
- A collapsible info panel that describes in plain English:
  - What MARL is and why it is hard
  - Why geopolitical coordination is an ideal MARL problem (partial observability, competitive-cooperative dynamics)
  - What WorldPolicy-Env is NOT claiming (it is a simulation, not a prediction engine)
  - Sponsor technology used and why each was chosen
- This is what judges read when they need to write their score reasoning — make it easy for them

---

## Multi-Agent Scale-Up Strategy (The 200-Agent Idea)

You mentioned potentially implementing 200+ agents. Here is the right way to think about this:

**Do NOT add 200 physical agents.** The value is not in agent count — it is in what the agents demonstrate. Instead:

**Expand the agent space intelligently:**
- Scale from 6 countries to 20 using a configurable `N_AGENTS` parameter in `worldenv.py`
- Add agent "types": Major Powers (USA, China), Regional Powers (India, Brazil), Small States (5 generic), International Organizations (UN, WHO, WTO — 3 agents that can only coordinate, not act militarily)
- This gives you 20 meaningful agents with different action spaces and reward structures
- The UN/WHO/WTO agents add a genuinely novel element: agents whose only power is persuasion and coordination incentives

**This is far more impressive than 200 identical agents.** It shows you understand multi-agent design, not just parameter scaling.

**Heterogeneous agent architecture:**
- Major Powers: full 8-action space, high economic weight
- Regional Powers: 6-action space (no NUCLEAR option), medium weight
- Small States: 4-action space (AID, PLEDGE, TRADE, ALIGN), low individual weight but coalition formation matters
- International Organizations: 3-action space (SANCTION, MEDIATE, COORDINATE) — no military options

This architecture is publishable-quality. It will absolutely stun the judges.

---

## 50-Hour Implementation Timeline

### Hours 0–2: Setup Sprint (All Three)
- Krish: verify training checkpoint loads cleanly, run `make freeze` baseline
- Raj: scaffold all new API endpoints (return stub 200 responses), verify SSE stream stable
- Tushar: set up HF Space, write `data/scenarios.json`, write demo mission card content

### Hours 2–12: Core Features (Krish + Raj parallel)
- **Krish:** Cascade failure mode + Coalition detector + heterogeneous agent types (20-agent expansion if time)
- **Raj:** Split-screen MAPPO vs Rule-Based + Policy Explanation Drawer + Humanitarian Impact Panel

### Hours 12–20: UI Integration (Raj primary, Tushar support)
- World Outcome Summary Card (modal with result)
- Timeline Replay Scrubber (most complex frontend feature — give it a full 4 hrs)
- Scenario Library picker in controls bar
- Groq Live Brief button

### Hours 20–28: Tier 2 Features (all three)
- Counterfactual Sandbox — Krish backend, Raj frontend (4 hrs combined)
- Burden-Sharing Justice Panel (Raj, 2 hrs)
- Diplomatic Relations Graph (Raj, 1 hr)
- Model Confidence Calibration (Krish, 2 hrs)

### Hours 28–36: Overnight Training + Polish
- **Krish:** `make train` on full 50k steps overnight — go to sleep with training running
- **Raj:** Sound design, metrics ticker, demo shortcuts
- **Tushar:** Mission cards, "About This System" panel, HF Space README with embedded diagrams

### Hours 36–44: Integration + Demo Rehearsal
- All three: wire every new endpoint to its frontend component
- 3 full demo rehearsals (30 min each) — strict 5-minute limit each time
- Fix everything that felt awkward in rehearsal
- `make freeze` — fill in all SHAs, commit `demo-freeze-v2`

### Hours 44–48: Freeze + Final Polish
- No new features. Only bug fixes.
- Demo rehearsal #4 and #5 — record them on phone, watch back
- Sleep. Seriously. A rested presenter outperforms a tired one with 10 more features.
- Travel to venue with: laptop charged, hotspot ready, checkpoint committed, HF Space live

### Hours 48–50: Venue
- Arrive 30 min early, set up, run `make freeze` one final time
- Do not open the code editor again unless there is a showstopper bug

---

## New API Endpoints Required (V6 Complete List)

| Endpoint | Method | Returns | Priority |
|----------|--------|---------|----------|
| `/stream/mappo` | GET SSE | MAPPO world state stream | Tier 1 |
| `/stream/rulebased` | GET SSE | Rule-based world state stream | Tier 1 |
| `/cascade-state` | GET | Current cascade chain, collapse ETA | Tier 1 |
| `/agent-rationale/{agent_id}/{step}` | GET | Feature attribution dict | Tier 1 |
| `/humanitarian-metrics` | GET | displacement_risk, aid_adequacy, civilian_stress | Tier 1 |
| `/replay` | GET | Full episode replay buffer array | Tier 1 |
| `/scenarios` | GET | Scenario library JSON | Tier 1 |
| `/run-scenario/{id}` | POST | Starts named scenario | Tier 1 |
| `/world-outcome` | GET | End-of-episode outcome summary | Tier 1 |
| `/counterfactual` | POST | Alternate timeline from given step | Tier 2 |
| `/confidence/{agent_id}` | GET | Action probability distribution | Tier 2 |
| `/coalition-state` | GET | Current coalition detection result | Tier 2 |
| `/burden-sharing` | GET | Per-country contribution/benefit index | Tier 2 |
| `/live-brief` | GET | Fresh (non-cached) Groq call | Tier 1 |

All existing V5 endpoints remain unchanged.

---

## Scoring Projection: V6 vs V5

| Judging Dimension | V4 | V5 | V6 Target | What Changes |
|---|---|---|---|---|
| Technical Depth | 8/10 | 9/10 | **10/10** | 20-agent heterogeneous types, cascade failure, coalition detector, confidence calibration |
| Sponsor Stack | 8/10 | 9/10 | **10/10** | Llama live brief, HF Space with embedded ML artifacts, PyTorch architecture visible in UI |
| Demo Quality / Risk Mgmt | 9/10 | 9/10 | **10/10** | Scenario library, mission cards, keyboard shortcuts, timeline replay = zero dead moments |
| Novelty / Differentiation | 8/10 | 9/10 | **10/10** | Cascade visualizer, coalition emergence, counterfactual sandbox, burden-sharing justice |
| Execution / Feasibility | 6/10 | 7/10 | **9/10** | Tight timeline with clear ownership, freeze protocol, 5 rehearsals, HF Space backup |
| Judging Moment | 9/10 | 9/10 | **10/10** | Cascade collapse countdown + MAPPO catching it live = THE moment judges write about |
| **Total** | **48/60** | **52/60** | **59/60** | **98.3% of maximum** |

---

## The Demo Script (5 Minutes, Memorize This)

**[0:00 — 0:30] Hook**
"What happens when a flood in South Asia triggers a food crisis in East Africa, which triggers a military arms race in the Pacific — in under 48 hours? That is what WorldPolicy-Env simulates. We trained a multi-agent AI using PyTorch and Meta Llama to study whether AI can identify — and stop — global cascade failures before they become irreversible."

**[0:30 — 1:30] Show the Cascade**
Load Scenario 1 "South Asian Monsoon Cascade." Watch the globe light up as disasters cascade across countries. The "Collapse Imminent in 23 steps" countdown appears. Cold war detector fires. Arms race spiral begins.

"Without intervention, our simulation predicts systemic collapse in 23 steps. Watch what the rule-based policy does." — show the baseline policy failing.

**[1:30 — 2:30] Show the MAPPO Intervention**
Switch to MAPPO split screen. Show divergence score widening. MAPPO dispatches coordinated aid. Coalition badge fires: "USA, EU, UK forming aid coalition — steps 12–15."

"The MAPPO policy, trained with PyTorch across 50,000 steps, identifies the cascade window and coordinates a multi-country aid response. The rule-based agent cannot — it responds to individual crises, not cascade patterns."

**[2:30 — 3:15] Prove the Learning**
Open EvalSummaryCard. Show MAPPO beating rule-based by X%. Open TrainingFactsCard. Show checkpoint hash. Open ClaimBoundaryBanner.

"Here is what is scripted and what is learned. We are being explicit about this because trustworthy AI requires transparency."

**[3:15 — 3:45] Llama as Decision Support**
Click Live Brief. Watch Llama generate in real time.

"Meta Llama 3.3-70b is not narrating the simulation. It is functioning as a UN Security Council analyst — generating structured policy recommendations with priority response and risk level."

**[3:45 — 4:30] Policy Explanation**
Click on USA on globe. Open policy explanation drawer.

"This is not a black box. We can explain why the agent made every decision — what state features drove it, what the alternatives were. This is what explainable multi-agent AI looks like."

**[4:30 — 5:00] Close**
Show World Outcome card: "SYSTEMIC COLLAPSE PREVENTED — FRAGILE PEACE."

"WorldPolicy-Env is not a prediction engine. It is a policy sandbox — a safe space to stress-test global coordination strategies before they matter in the real world. Built with PyTorch, Meta Llama, HuggingFace, in 50 hours by three engineers."

---

## Non-Negotiable Quality Bars

Every single one of these must be true on demo day:

- [ ] `make freeze` shows all 7 OK — no exceptions
- [ ] MAPPO reward curve is visibly improving — if it is flat, diagnose and fix the reward function
- [ ] Cascade failure fires visually on globe within 15 seconds of loading Scenario 1
- [ ] MAPPO vs Rule-Based split screen shows measurable divergence within 30 steps
- [ ] Groq live brief generates in under 3 seconds (test on venue WiFi or hotspot)
- [ ] Every demo click has a visual response in under 200ms (no spinners without feedback)
- [ ] ClaimBoundaryBanner is always visible — never hidden by any modal or overlay
- [ ] All 9 emergent detector tests pass: `python -m pytest tests -v`
- [ ] Demo rehearsal #5 comes in under 5:00 minutes with no hesitation
- [ ] HF Space is live and loads without error on your phone before you enter the venue

---

## What Makes This a 95%+ Project

The difference between a good hackathon project and one that wins internationally is not complexity — it is **coherence**. Every part of your project — the backend training, the emergent detectors, the Llama brief, the globe visualization, the cascade failure — must tell a single story:

> "We built a system that makes the invisible visible: the cascade dynamics of global coordination failure, and the AI strategies that can interrupt them."

Every feature in this plan serves that story. The cascade visualizer is the plot. MAPPO vs rule-based is the evidence. The policy explanation drawer is the trust. The burden-sharing justice panel is the ethics. The world outcome card is the ending.

Judges do not score features. They score whether they believe in what you built. If they believe, they give you 95%+.

**You have the foundation. Now build the story.**

---

*WorldPolicy-Env V6 Win Plan | Team: Krish · Raj · Tushar | Generated: April 23, 2026*

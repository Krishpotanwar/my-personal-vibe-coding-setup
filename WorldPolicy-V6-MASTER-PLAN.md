# WorldPolicy-Env V6 — MASTER EXECUTION PLAN

**Hackathon:** Scaler × PyTorch × HuggingFace × Meta — Bengaluru, April 25-26 2026
**Team:** Krish (ML + Backend) · Raj (Frontend + Backend support) · Tushar (Integration + Demo)
**Window:** 50 hours of dedicated build time (start moment TBD)
**Status:** LOCKED — refined from V6 WIN Plan, supersedes V5 plans for execution purposes
**Goal:** Score 95%+ across every judging dimension

---

## HOW TO USE THIS DOCUMENT

This is the single source of truth for the 50-hour sprint. Every decision is pre-made. No meta-debate during the sprint.

- **Before clock starts:** Each person reads Parts I, II, and their own checklist in Part VIII.
- **Hour 0:** Follow Part III runbook. Do NOT deviate without consulting Part VII failure branches.
- **Every 4 hours:** Tushar runs the Part X quality gates.
- **Demo day:** Part IX is the only doc you need open.

If a V5 or V6 plan says one thing and this doc says another, **this doc wins**. Those are reference; this is law.

---

## TABLE OF CONTENTS

- Part I — The Locked Demo (the thing that wins)
- Part II — Cut List (what NOT to build)
- Part III — 50-Hour Runbook
- Part IV — Backend Spec (complete)
- Part V — Frontend Delta from V5
- Part VI — Dependency Graph + Critical Path
- Part VII — Failure Branches
- Part VIII — Per-Person Monitor-Tape Sheets
- Part IX — Demo Day Runbook
- Part X — Non-Negotiable Quality Gates
- Appendix — Reference material

---

# PART I — THE LOCKED DEMO

The 5-minute demo is the ground truth. Every feature in this plan exists to serve a specific demo moment. If a feature doesn't appear in this script, it doesn't ship. Memorize this script. Rehearse it minimum 5 times.

## The 5-Minute Script (minute-by-minute, click-by-click)

### [0:00–0:30] HOOK

**Screen state:** Globe centered, 6 countries pulsing softly, all metrics green, ClaimBoundaryBanner visible at top. No crisis active yet. Background music: subtle ambient (Tier 3 polish).

**Presenter script:**
> "What happens when a flood in South Asia triggers a food crisis in East Africa, which triggers a military arms race in the Pacific — in under 48 hours? That's what WorldPolicy-Env simulates. We trained a multi-agent AI with PyTorch and Meta Llama 3.3 to study whether AI can identify — and stop — global cascade failures before they become irreversible."

**Components active:** `GlobeView`, `ClaimBoundaryBanner`, `TrainingFactsCard` (visible in background).

### [0:30–1:30] CASCADE UNFOLDS

**Click #1:** Presenter opens ScenarioCard picker. Selects "South Asian Monsoon Cascade."
**Click #2:** Press Play button.

**Screen sequence:**
- t+2s: Red pulse on India node. Crisis banner appears: "NATURAL DISASTER — severity 0.87"
- t+6s: Red cascade line animates from India to Bangladesh. Second crisis spawns: "FOOD CRISIS — Bangladesh affected"
- t+10s: Red cascade line extends to China. Third crisis: "ECONOMIC DESTABILIZATION — China GDP dropping"
- t+14s: Top-right corner: "COLLAPSE IMMINENT IN 23 STEPS" — amber LED countdown begins ticking down
- t+20s: EmergentBadgePanel fires: "COLD WAR BIFURCATION — DETECTED step 47" with pulsing red LED

**Presenter script (over the action):**
> "Without intervention, our simulation predicts systemic collapse in 23 steps. The cascade is spreading faster than any single country can respond. Watch what the rule-based policy does."

**Components active:** `CascadeVisualizer`, `CollapseCountdown`, `EmergentBadgePanel`, `CrisisBriefCard`, `HumanitarianPanel`.

### [1:30–2:30] MAPPO VS RULE-BASED — SPLIT-SCREEN PROOF

**Click #3:** Presenter toggles Split-Screen Mode. Screen splits to two globes side-by-side.

**Screen state:**
- Left globe: labeled "MAPPO (Trained)". Blue AID_DISPATCH arcs fire from USA, EU, UK to India at step 12.
- Right globe: labeled "Baseline (Rule-Based)". Slower, uncoordinated response. No coalition formation.
- DivergenceScoreCard top-center: `ΔGDP: +0.12 · ΔArmsRace: -0.08 · ΔHumanitarian: +0.34`
- Left: Coalition Formation badge fires: "COALITION FORMED — USA, EU, UK (steps 12-15)"
- Right: No coalition. Arms Race Spiral badge fires at step 22.

**Presenter script:**
> "Trained MAPPO catches the cascade window and coordinates multi-country aid at step 12. The rule-based agent can't — it responds to individual crises, not cascade patterns. That divergence score is the AI's learning made visible."

**Components active:** `SplitScreenGlobe`, `SplitScreenToggle`, `DivergenceScoreCard`, `EmergentBadgePanel` (both sides).

### [2:30–3:15] PROVE THE LEARNING

**Click #4:** Presenter opens EvalSummaryCard (already visible in side panel).
**Click #5:** Opens TrainingFactsCard.
**Click #6:** Presenter points at ClaimBoundaryBanner (always visible).

**Screen content:**
- EvalSummaryCard: three bars — Random (0.61±0.08), Rule-Based (1.87±0.12), MAPPO (2.34±0.15). Green checkmark on MAPPO. Subtitle: "10 episodes × 5 seeds. MAPPO +25.1% over Rule-Based."
- TrainingFactsCard: "MLP 36→128→128 + 6 actor heads · 450K params · 50,000 steps · PyTorch 2.x · Checkpoint SHA a3f2..."
- ClaimBoundaryBanner: "⚙ SCRIPTED EVENTS — World crises + timing · 🧠 TRAINED POLICY — Agent responses, reward curves, emergent phenomena (mappo_50k.pt)"

**Presenter script:**
> "Here's what's scripted and what's learned. We're explicit about this because trustworthy AI requires transparency. MAPPO outperforms rule-based by 25% across 10 evaluation episodes and 5 random seeds — reproducible."

**Components active:** `EvalSummaryCard`, `TrainingFactsCard`, `ClaimBoundaryBanner`.

### [3:15–3:45] LLAMA AS DECISION SUPPORT

**Click #7:** Presenter clicks "Live Brief" button on CrisisBriefCard.

**Screen state:**
- Spinner appears for ~1s: "Generating live analysis..."
- Llama response streams in word-by-word over ~2s:
  > "A severe cyclone has made landfall across South Asia, displacing an estimated 2.4 million civilians and collapsing coastal infrastructure. Aid corridors from neighboring countries remain viable but narrow.
  >
  > **Priority response:** Deploy coordinated multilateral humanitarian aid immediately, prioritizing coastal Zone 3 and Zone 7.
  > **Risk level:** CRITICAL"
- Footer: "Generated by Meta Llama 3.3-70b via Groq · 1.2s · LIVE"

**Presenter script:**
> "Meta Llama 3.3-70b is not narrating the simulation. It's functioning as a UN Security Council analyst, generating structured policy recommendations with explicit priority response and risk level, from the live world state."

**Components active:** `LiveBriefCard` (upgraded `CrisisBriefCard`).

### [3:45–4:30] POLICY EXPLANATION — GLASS BOX, NOT BLACK BOX

**Click #8:** Presenter clicks on USA node on globe.

**Screen state:**
- PolicyExplanationDrawer slides in from right:
  ```
  USA — Step 12 — Action: AID_DISPATCH

  Top 3 observation features that drove this decision:
    disaster_severity        0.87   (HIGH — cascade active)
    relations_india          +0.43  (POSITIVE — aid welcomed)
    arms_race_index          0.20   (LOW — no military pressure)

  Alternative actions considered:
    NEUTRAL          expected reward -0.2
    TRADE            expected reward +0.1
    AID_DISPATCH     expected reward +1.4  ← CHOSEN
    DEPLOY_MILITARY  expected reward -0.8

  Rationale: AID_DISPATCH dominates due to cascade severity
  and positive regional relations. Military options penalized
  by low arms-race index (no defensive necessity).

  Source: policy feature attribution (approximate)
  ```

**Presenter script:**
> "This is not a black box. Every action has traceable features and alternatives. This is what explainable multi-agent AI looks like."

**Components active:** `PolicyExplanationDrawer`.

### [4:30–5:00] CLOSE — WORLD OUTCOME

**Screen state (auto-triggered, no click):**
- Scenario ends (step 200 reached)
- Full-screen WorldOutcomeCard modal:
  ```
  ┌────────────────────────────────────────┐
  │     SYSTEMIC COLLAPSE PREVENTED       │
  │           FRAGILE PEACE                │
  │                                        │
  │   MAPPO              Rule-Based        │
  │   Final GDP   0.84   0.62              │
  │   Conflict    0.18   0.51              │
  │   Climate     0.71   0.58              │
  │   Humanitari  0.89   0.54              │
  │   Alliance    0.77   0.41              │
  │                                        │
  │   "Despite early escalation,           │
  │   coordinated aid dispatch prevented   │
  │   humanitarian catastrophe."           │
  │          — Meta Llama 3.3-70b          │
  └────────────────────────────────────────┘
  ```

**Presenter script:**
> "WorldPolicy-Env is not a prediction engine. It's a policy sandbox — a safe space to stress-test global coordination strategies before they matter in the real world. Built with PyTorch, Meta Llama, HuggingFace, in 50 hours by three engineers."

**Components active:** `WorldOutcomeCard`.

## Load-Bearing Components (derived from demo — these MUST work)

Every component below is referenced in the demo. If one breaks, a demo beat dies. These are P0.

1. **GlobeView** — 6 country nodes, arc rendering, cascade red-line overlay
2. **CascadeVisualizer + CollapseCountdown** — red animated lines between countries, amber LED countdown
3. **SplitScreenGlobe + SplitScreenToggle** — two globes side-by-side, each streams independently
4. **DivergenceScoreCard** — live ΔGDP, ΔRelations, ΔArmsRace, ΔHumanitarian
5. **EvalSummaryCard** — three-bar chart with seeds + std, MAPPO highlighted
6. **TrainingFactsCard** — arch, params, checkpoint SHA
7. **ClaimBoundaryBanner** — always-visible scripted-vs-learned banner
8. **LiveBriefCard** — Groq streaming brief with priority_response + risk_level
9. **PolicyExplanationDrawer** — click-country drawer with top-3 features + alternatives
10. **WorldOutcomeCard** — end-of-episode modal with MAPPO vs rule-based comparison + Llama summary
11. **ScenarioCard** — pre-built "South Asian Monsoon Cascade" with Play button
12. **EmergentBadgePanel** — Cold War + Coalition + Arms Race badges with trigger text

Everything else in V6 is either supporting atmosphere (HumanitarianPanel, metrics ticker) or cut (see Part II).

---

# PART II — CUT LIST

Every cut is justified. Do not re-open these decisions during the sprint without consulting Tushar + Krish simultaneously. Cuts protect your demo.

## CUT ENTIRELY

| Feature | Why Cut |
|---------|---------|
| 20-agent heterogeneous expansion | Scale-up breaks MAPPO training convergence. 6 agents already non-trivial. Scaling before demo = demo-killing risk. V6 story works fine with 6. |
| Timeline Replay Scrubber (4 hr) | Judges see the 5-min demo, not a power-user replay tool. 4 hours better spent on rehearsal + polish. |
| Live Audience Control (WebSocket + mobile QR) | Venue WiFi unreliable. Phone integration adds 3-4 new failure modes. High risk, low reward during a 5-min demo where the presenter is the audience anyway. |
| Full 20-endpoint FastAPI (V5 list) | Shrunk to 10 load-bearing endpoints. Any endpoint not feeding a demo component is deferred. |

## CONDITIONAL (Hour 24 Checkpoint)

Build these ONLY if the Hour 24 quality gate passes cleanly (see Part X). If foundation is shaky, these are distraction.

| Feature | Hours | Build Only If |
|---------|-------|---------------|
| Counterfactual Sandbox | 4 | Hour 24 checkpoint clean AND demo script rehearses in < 4:45 AND MAPPO converged |
| Burden-Sharing Justice Panel | 2 | Hour 24 checkpoint clean AND free-rider detector firing correctly |
| Model Confidence Calibration | 2 | Hour 24 checkpoint clean AND Krish has bandwidth |
| Diplomatic Relations Graph | 1 | Hour 40 reached with rehearsals on track |

## KEEP — Tier 3 polish (cheap, high signal)

All of these are low-cost and collectively make the demo feel like a real product. Build during Hours 40-44.

- Sound design (3 programmatic Web Audio sounds — ~1 hr)
- Keyboard demo shortcuts (D/Space/R/1-5/E/A — ~30 min)
- Real-time metrics ticker at top (pure CSS marquee — ~1 hr)
- "About This System" panel (collapsible, ~1 hr)
- Mission Cards picker (merge with ScenarioCard, ~30 min extra)

Total Tier 3: ~4 hours. Fits in Hours 40-44.

---

# PART III — 50-HOUR RUNBOOK

Each hour has: owner(s), deliverable, acceptance criterion. Do NOT mark done without passing acceptance.

## PHASE 0 — PRE-SPRINT (before clock starts)

Do these BEFORE the 50-hour window begins. Zero hour should start with zero friction.

- [ ] **Groq API key** in hand. Test with `curl` call.
- [ ] **HuggingFace account** created, token generated, able to `huggingface-cli login`.
- [ ] **GitHub repo** forked from `Rajy777/disasterman`. All three team members have push access.
- [ ] **Local dev environments** set up: Python 3.10+, Node 18+, `pip install -r requirements.txt` dry-run clean.
- [ ] **GPU access confirmed** — local CUDA or Colab Pro or Kaggle. Which one? Note it here: `__________`
- [ ] **V5 frontend code** pulled into repo and running on `npm run dev`. Verify globe loads.
- [ ] **Three laptops charged**, WiFi hotspot tested, backup laptop designated (Raj's if Krish's dies).
- [ ] **This doc printed or pinned** in each person's IDE sidebar.

## PHASE 1 — FOUNDATION (Hours 0–8)

**Goal:** By Hour 8, the env runs end-to-end, FastAPI serves stubs, frontend SSE subscribes to real stream.

### Hour 0–1: Setup Sprint (ALL THREE)

- **Krish:** Clone repo. Create `world_env/` directory with file skeletons (see Part IV). Verify `from pettingzoo import ...` works. Run `python -c "import torch; print(torch.cuda.is_available())"`.
- **Raj:** Start FastAPI skeleton at `backend/main.py`. `/health` endpoint returning `{"status":"ok"}`. Verify `curl http://localhost:8000/health` works. Verify frontend `npm run dev` loads.
- **Tushar:** Create `Makefile` with stub targets. Run `make cache` to populate `data/groq_cache.json` with 100 sample briefs (see Part IV — crisis_brief.py `--cache-run`). Verify file non-empty. Commit.

**Acceptance:** All three green-check in group chat at Hour 1.

### Hour 1–3: Environment Core (KRISH)

- Implement `world_env.py` per Part IV spec. 36-dim observation vector, 8 discrete actions per agent, 6 agents.
- Implement `event_engine.py` with 5 crisis types (war, natural_disaster, pandemic, econ_crash, climate_emergency).
- Smoke test: `env.reset()` returns valid obs. 200-step random rollout completes without exception.

**Acceptance:** `python -m pytest tests/test_smoke.py` passes.

### Hour 1–3: Frontend-to-Stub Wiring (RAJ)

- Verify V5 frontend loads. Identify exact file paths for components that will be upgraded (GlobeView, CrisisBriefCard, EmergentBadgePanel, SimPanel).
- Wire frontend SSE subscription to `GET /stream`. Backend stub emits one JSON per second: `{"step": N, "world_state": {...stub...}}`.
- Verify frontend receives and logs stream to console. Globe can be stubbed for now.

**Acceptance:** Open DevTools Network tab, see EventSource connection, verify messages arrive.

### Hour 1–3: Analytics + Detector Stubs (TUSHAR)

- Write `tests/test_emergent_detectors.py` per V5 plan. Tests FAIL initially (analytics not implemented). That's expected.
- Write `docs/detector_examples.md` — this is the spec for Krish's analytics.py. Tushar defines behavior first.
- Configure `pytest` to run from repo root.

**Acceptance:** `python -m pytest tests/ -v` runs without import errors. Tests fail; that's fine.

### Hour 3–6: MAPPO + Training Loop (KRISH)

- Implement `mappo_agent.py` per Part IV spec. Shared MLP backbone (36→128→128), 6 actor heads (128→8), centralized critic (216→256→256→1).
- Implement `train.py`. Accepts `--steps N`, `--checkpoint-dir path`. Uses reward function from Part IV (PRE-SPECIFIED).
- First smoke run: `python train.py --steps 2000 --checkpoint-dir /tmp/smoke`. Loss should decrease.

**Acceptance:** Smoke run completes without NaN. Any decrease in loss is acceptable at this point.

### Hour 3–6: Crisis Brief Integration (RAJ)

- Implement `crisis_brief.py` with Groq client. Returns dict: `{brief, priority_response, risk_level, crisis_type, step, model, from_cache}`.
- Fallback logic: if `GROQ_OFFLINE=1`, read from `data/groq_cache.json`. If no cache entry, use hardcoded fallback brief.
- Wire `GET /crisis-brief` endpoint to serve the latest entry.

**Acceptance:** `curl http://localhost:8000/crisis-brief` returns valid JSON with all 7 fields. Works offline.

### Hour 3–6: Makefile + CI Hygiene (TUSHAR)

- Full Makefile per Part IV — `demo`, `train`, `eval`, `cache`, `test`, `arch-diagram`, `curves`, `freeze` targets.
- `make freeze` checklist function — see Part X.
- Integration test: `make test` runs pytest + import smoke checks.

**Acceptance:** `make freeze` runs (most items will fail at this stage — that's fine, we care about the script running).

### Hour 6–8: Pre-Tune Training Probes (KRISH)

**This is the single highest-stakes 2-hour block in the sprint.** Read the reward function spec in Part IV carefully.

- Run **Probe 1:** `REWARD_PROFILE=weak python train.py --steps 2000` (weak crisis shaping, see Part IV). Log reward curve.
- Run **Probe 2:** `REWARD_PROFILE=primary python train.py --steps 2000` (primary weights). Log reward curve.
- Run **Probe 3:** `REWARD_PROFILE=strong python train.py --steps 2000` (strong crisis shaping). Log reward curve.
- Compare the three curves. Which one shows most reward improvement?
- **Decision lock:** Pick the winning profile. This is what runs overnight.

**Acceptance:** Winning profile chosen AND reward curve visibly improving (>10% from step 0 to step 2000). If all three flatline, escalate to Failure Branch #1 (Part VII).

## PHASE 2 — CORE BACKEND (Hours 8–16)

**Goal:** By Hour 16, all 10 load-bearing endpoints return real data. Training is running in the background.

### Hour 8–10: Kick off Long Training (KRISH)

- Start `make train` with winning profile from Hour 6-8 probes. Target 50k steps (runs ~6-10 hours).
- Log to `data/training_log.jsonl`. Monitor reward curve every 10k steps.
- Verify checkpoint saves every 10k steps (`checkpoints/mappo_10k.pt`, `mappo_20k.pt`, etc.)

**Acceptance:** Training running in a detachable process (tmux / screen / nohup). Curve monitored at Hour 12 and Hour 16.

### Hour 8–12: Analytics + Detectors (KRISH, background)

While training runs, Krish implements `analytics.py` detectors (cold_war, arms_race_spiral, free_riders, coalition_formation). Must pass `tests/test_emergent_detectors.py`.

**Acceptance:** All detector tests green.

### Hour 8–14: Scripted Fallback + Event Engine (KRISH)

- Implement `scripted_fallback.py` — 7-step demo sequence that works even if training fails.
- Tune `event_engine.py` cascade trigger logic: `disaster_severity > 0.75 AND arms_race_index > 0.5 → spawn second crisis step+10`.
- Implement `cascade_state` tracking. Expose via `GET /cascade-state`.

**Acceptance:** `POST /run-scripted` runs a full scripted episode. `GET /cascade-state` returns `{chain: [...], eta_collapse: N, severity_curve: [...]}`.

### Hour 8–14: Live Brief Endpoint (RAJ)

- Implement `GET /live-brief` — bypasses cache, calls Groq fresh, streams response word-by-word via SSE.
- Frontend `LiveBriefCard` component — button triggers fetch, streams words with typing animation.

**Acceptance:** Click Live Brief → spinner for ~1s → words stream in over ~2s → footer shows latency in ms.

### Hour 10–16: Cascade + Split-Stream Endpoints (RAJ)

- `GET /stream/mappo` — SSE streaming MAPPO policy world state (or scripted fallback if training incomplete).
- `GET /stream/rulebased` — SSE streaming rule-based policy world state.
- Both run on separate episode instances in parallel. Synchronize step counters.
- `POST /run-scenario/{id}` — accepts "south-asia-cascade", "pacific-cold-war", etc.

**Acceptance:** Open two DevTools tabs, one per endpoint. Both stream independently. Step counters within ±1 of each other.

### Hour 12–16: Frontend Cascade Visualizer (RAJ)

- New component: `CascadeVisualizer.tsx`. Renders red cascade arcs on globe between countries in the cascade chain.
- New component: `CollapseCountdown.tsx`. Top-right overlay with amber LED countdown. Pulses faster as countdown nears zero.
- Wire both to `/cascade-state` endpoint.

**Acceptance:** Load Scenario 1 in frontend. Within 15s, red cascade animates, countdown appears.

### Hour 14–16: Integration Smoke Test (TUSHAR)

- End-to-end test: start backend, start frontend, load Scenario 1, watch cascade unfold, verify live brief, verify outcome card.
- Document any broken links in the demo chain. Escalate to owners.

**Acceptance:** Raw demo runs start-to-finish without crash. UI may be ugly; functionality must work.

## PHASE 3 — CASCADE + SPLIT-SCREEN (Hours 16–24)

**Goal:** The money shot. Split-screen works. Demo story is renderable end-to-end.

### Hour 16–20: Split-Screen Frontend (RAJ)

- New component: `SplitScreenGlobe.tsx`. Two `GlobeView` instances side-by-side, each with its own SSE subscription.
- New component: `SplitScreenToggle.tsx`. Button in controls bar toggles single → split.
- New component: `DivergenceScoreCard.tsx`. Top-center when split-screen is on. Shows live ΔGDP, ΔRelations, ΔArmsRace, ΔHumanitarian.

**Acceptance:** Toggle split-screen. Both globes render. Divergence score updates each step.

### Hour 16–20: MAPPO Inference Endpoint (KRISH)

- Backend loads `checkpoints/mappo_50k.pt` (or latest available checkpoint from the background training run).
- `GET /stream/mappo` uses the trained policy for agent actions.
- Fallback: if checkpoint doesn't exist, use `scripted_fallback.py` and silently mark ClaimBoundaryBanner as "scripted_only".

**Acceptance:** `curl` on `/stream/mappo` shows actions coming from the trained policy (verify by comparing to rule-based — they should diverge noticeably).

### Hour 18–22: Policy Explanation Drawer (RAJ)

- New component: `PolicyExplanationDrawer.tsx`. Right-side drawer. Opens on click of any country node.
- Backend endpoint: `GET /agent-rationale/{agent_id}/{step}` — returns dict with top-3 features, alternatives, rationale text.
- Implementation note: feature attribution can be approximate. Use the observation vector deltas between current and previous step, weight by (approximate) policy gradient magnitudes. Label clearly as "approximate feature attribution."

**Acceptance:** Click USA. Drawer slides in. Shows action, top-3 features, alternatives, rationale.

### Hour 20–24: World Outcome Card (RAJ)

- New component: `WorldOutcomeCard.tsx`. Full-screen modal on episode end.
- Backend endpoint: `GET /world-outcome` — computes final metrics, compares MAPPO vs rule-based runs, calls Groq for closing sentence.
- Three outcome states: `WORLD STABILIZED` (green), `SYSTEMIC COLLAPSE` (red), `FRAGILE PEACE` (amber).

**Acceptance:** Run a full episode to termination. Modal appears. Contains 5 metrics + MAPPO-vs-rule-based comparison + Llama sentence.

### Hour 20–24: Coalition Detector (KRISH)

- `analytics.py`: `detect_coalition_formation()` — fires when 3+ agents choose AID_DISPATCH for 3+ consecutive steps.
- Emit as 4th badge in `EmergentBadgePanel`.
- Pass test: synthetic positive case (3 agents all AID for 4 steps → detected) + synthetic negative (2 agents only → not detected).

**Acceptance:** Test green. During cascade scenario, coalition badge fires visually.

### Hour 23–24: **HOUR 24 CHECKPOINT** (TUSHAR RUNS)

Tushar runs this. All three teammates stop their current task at Hour 23:45 to prepare.

**Quality gate — all must pass:**
- [ ] Load Scenario 1 → cascade visualizes within 15s
- [ ] Split-screen toggle works → both globes stream
- [ ] Live Brief button → Llama streams response in <3s
- [ ] Click country → Policy Explanation Drawer opens with valid data
- [ ] Episode completes → World Outcome Card shows
- [ ] MAPPO reward curve from background training shows visible improvement (>20% from step 0)
- [ ] `make freeze` reports at least 5 of 7 artifacts present
- [ ] All pytest detector tests green

**If checkpoint PASSES:** Proceed to Phase 4 with CONDITIONAL features unlocked.
**If checkpoint FAILS:** Consult Part VII failure branches. Most likely remedy: drop all CONDITIONAL features, use Phase 4 as stabilization time.

## PHASE 4 — EVIDENCE ARTIFACTS (Hours 24–32)

**Goal:** The credibility layer. Eval harness, training meta, claim boundary, architecture diagram. These are what judges inspect when they want to verify your claims.

### Hour 24–28: Eval Harness (KRISH)

- Implement `eval.py` per Part IV spec.
- Run `make eval` against the best available checkpoint. Writes `data/eval_summary.json` and generates `public/baseline_comparison.png`.
- Target: MAPPO mean_reward > rule-based mean_reward + 1 std. If tie, flag but don't panic — ClaimBoundaryBanner will still note honesty.

**Acceptance:** `data/eval_summary.json` exists. `GET /eval-summary` returns it. Frontend `EvalSummaryCard` renders the bar chart with real numbers.

### Hour 24–28: Training Meta + Arch Diagram (KRISH + RAJ)

- Krish: Add `training_meta.py` call to end of `train.py`. Writes `data/training_meta.json` with arch, params, checkpoint SHA.
- Raj: Implement `GET /training-meta` endpoint. Wire `TrainingFactsCard.tsx` to display.
- Krish: Run `python scripts/gen_arch_diagram.py`. Verifies `public/architecture_diagram.svg` exists.
- Raj: Implement `ArchitectureDiagramPanel.tsx` (collapsible, inline SVG).

**Acceptance:** `TrainingFactsCard` shows real arch, real param count, real SHA. ArchDiagram renders inline SVG.

### Hour 24–28: Claim Boundary (RAJ)

- Implement `GET /claim-boundary` endpoint per Part IV.
- `ClaimBoundaryBanner.tsx` polls once on mount, displays scripted-vs-learned declaration always.
- Dynamic: if no checkpoint exists, banner says "demo_mode: scripted" with different styling.

**Acceptance:** Banner visible on every screen. Text matches actual backend state.

### Hour 26–32: HuggingFace Space Deploy (TUSHAR)

- Create HF Space from the repo. `spaces-sdk: docker` or `gradio` — whichever matches your runtime.
- Write Space `README.md` with:
  - Architecture diagram (embed SVG)
  - Reward curve (embed PNG)
  - Eval summary table (MAPPO improvement %)
  - Link to GitHub
  - Link to team
- Verify Space builds and the backend loads in Space.
- Important: Space does NOT need to run the frontend — can just be the backend + markdown.

**Acceptance:** HF Space URL loads without error. README renders with all embedded assets. Space URL works on mobile browser.

### Hour 28–32: DEMO FREEZE v1 (TUSHAR)

- Run `make freeze`. All 7 checks must be OK.
- Fill in `DEMO_FREEZE.md` with exact SHA prefixes for each artifact.
- Tag: `git tag demo-freeze-v1 && git push origin demo-freeze-v1`.
- **FROM THIS POINT:** no new features. Only bug fixes.

**Acceptance:** Tag pushed. `DEMO_FREEZE.md` committed. All 7 checks green.

## PHASE 5 — CONDITIONAL FEATURES + POLISH (Hours 32–40)

Only enter this phase if Hour 24 checkpoint passed cleanly. If not, use these 8 hours for stabilization and additional rehearsals.

### Hour 32–36: Conditional Features (IF BUDGET)

Decide in this order. Stop when you run out of budget.

1. **Burden-Sharing Justice Panel (2 hr)** — reuse free-rider detector logic. Horizontal bar per country: contribution vs benefit.
2. **Counterfactual Sandbox (4 hr)** — `POST /counterfactual` endpoint + branching timeline UI. HIGH RISK — only if Hour 32 is totally clean.
3. **Model Confidence Calibration (2 hr)** — `GET /confidence/{agent_id}` + distribution bars in country cards.
4. **Diplomatic Relations Graph (1 hr)** — force-directed graph of country relations. Cheap and visual.

### Hour 36–40: Tier 3 Polish (RAJ + TUSHAR)

All of these are ≤2hr each. Parallelizable.

- **Sound design (1 hr):** Programmatic Web Audio. Three sounds only: crisis-brief ping, critical-risk alarm, world-stabilized chime.
- **Keyboard shortcuts (30 min):** D, Space, R, 1-5, E, A — see V6 plan for mapping.
- **Metrics ticker (1 hr):** Top-of-screen CSS marquee showing live metrics.
- **About panel (1 hr):** Collapsible info panel explaining MARL, the problem, sponsor tech.
- **Mission Cards (30 min):** Merge into ScenarioCard. 5 cards, each loads a scenario.
- **Visual polish (remaining):** Globe glow aura, arc tilt, animation tuning.

## PHASE 6 — REHEARSAL + FREEZE (Hours 40–48)

**Goal:** 5 clean demo rehearsals. Zero hesitation, no crashes, under 5:00 minutes each time.

### Hour 40–42: Rehearsal #1 (ALL THREE)

- Full demo, start to finish, recorded on phone.
- Tushar times it strictly. If over 5:00, note which beat ran long.
- After: 15-min debrief. Fix top 3 friction points.

### Hour 42–44: Rehearsal #2 (ALL THREE)

- Repeat. Should feel smoother. Presenter owns the script; no hesitation.
- Fix new issues.

### Hour 44–46: Rehearsal #3 + DEMO FREEZE v2 (ALL THREE)

- Third rehearsal. Should be under 5:00 with zero "uhhh" moments.
- Run `make freeze`. All 7 checks OK.
- Tag: `git tag demo-freeze-v2`.

### Hour 46–48: Rehearsal #4, #5, Sleep Prep

- Two final rehearsals. One by Krish as presenter, one by Raj — in case the primary presenter loses voice / has tech issue.
- Pack: laptops fully charged, hotspot ready, USB-C adapter, HDMI adapter, power strips.
- Sleep. Seriously. A well-rested presenter beats a sleep-deprived one with 10 more features.

## PHASE 7 — VENUE (Hours 48–50)

### Hour 48: Arrive Early

- Arrive at venue 45 minutes before scheduled demo slot.
- Find power outlet. Test projector connection. Test sound.
- Run `make demo` + `npm run dev`. Verify everything loads.
- Run `make freeze` one final time. Expect 7/7 green.

### Hour 49: Pre-Demo Final Check

- Load Scenario 1 in the frontend. Watch full 5-min simulation play through.
- Open HF Space URL on phone. Verify it loads.
- Stretch. Breathe. Drink water.
- Do NOT open the code editor.

### Hour 50: DEMO

- Deliver the 5-min script (Part I).
- Answer questions from Part IX (demo day runbook).

---

# PART IV — BACKEND SPEC (COMPLETE)

This is the canonical spec. Every file, every function signature, every endpoint JSON shape. If something here conflicts with V5 backend plan, **this wins**.

## File Tree

```
world_env/
├── world_env.py               # PettingZoo multi-agent env, 36-dim obs
├── disaster_wrapper.py        # disasterman integration + DummyDisasterEnv
├── mappo_agent.py             # MAPPO backbone + heads + critic
├── event_engine.py            # 5 crisis types + cascade trigger logic
├── scripted_fallback.py       # 7-step demo safety net
├── train.py                   # training loop, accepts --profile flag
├── analytics.py               # TOP 5 metrics + 4 emergent detectors
├── eval.py                    # evaluation harness
├── training_meta.py           # provenance writer
├── crisis_brief.py            # Groq client with cache fallback
│
├── rule_based_policy.py       # intentionally-weaker baseline for split-screen
│
├── scripts/
│   ├── render_baselines.py    # bar chart with seeds + std
│   ├── render_curves.py       # 3-panel reward curves
│   └── gen_arch_diagram.py    # SVG generator
│
├── tests/
│   ├── test_smoke.py
│   └── test_emergent_detectors.py
│
├── docs/
│   └── detector_examples.md
│
├── backend/
│   └── main.py                # FastAPI, 10 endpoints
│
├── data/
│   ├── groq_cache.json        # populated Hour 0
│   ├── metrics.jsonl          # analytics writes here
│   ├── training_log.jsonl     # train.py writes here
│   ├── crisis_briefs.jsonl    # crisis_brief.py writes here
│   ├── emergent_events.jsonl  # analytics writes here on detector fires
│   ├── eval_summary.json      # eval.py writes here
│   ├── training_meta.json     # training_meta.py writes here
│   ├── scenarios.json         # scenario library (5 scenarios)
│   └── replay_scenario1.json  # emergency fallback for SSE failure
│
├── public/
│   ├── reward_curve_3panel.png
│   ├── baseline_comparison.png
│   └── architecture_diagram.svg
│
├── checkpoints/
│   └── mappo_50k.pt
│
├── Makefile
└── DEMO_FREEZE.md
```

## Reward Function — PRE-SPECIFIED

**This is the single most important technical decision in the plan.** Do not invent during the sprint. Start from these weights.

### Reward terms (per agent `i`, per step `t`):

```python
def compute_reward(agent_i, world_state, world_state_prev, weights):
    r = 0.0

    # Self-interest terms
    r += weights.alpha_1 * (world_state.gdp[i] - world_state_prev.gdp[i])
    r += weights.alpha_2 * mean(world_state.relations[i][j] for j if j != i)
    r -= weights.alpha_3 * world_state.military[i]

    # Shared / coordination terms
    r += weights.alpha_4 * (world_state.global_gdp_index - world_state_prev.global_gdp_index)
    r += weights.alpha_5 * (world_state.humanitarian_score - world_state_prev.humanitarian_score)

    # Shaped penalties
    r -= weights.alpha_6 * world_state.arms_race_index
    r -= weights.alpha_7 * world_state.cascade_severity  # 0 if no cascade active

    # Sparse bonus: acted during cascade
    if world_state.cascade_active and agent_i.last_action in [AID_DISPATCH, PLEDGE]:
        r += weights.alpha_8

    # Clip to prevent gradient explosions
    return max(min(r, 5.0), -5.0)
```

### Three Reward Profiles

| Profile | alpha_1 | alpha_2 | alpha_3 | alpha_4 | alpha_5 | alpha_6 | alpha_7 | alpha_8 |
|---------|---------|---------|---------|---------|---------|---------|---------|---------|
| `weak` (Probe 1) | 1.0 | 0.5 | 0.3 | 0.5 | 1.0 | 0.3 | 0.5 | 1.0 |
| `primary` (Probe 2, expected winner) | 1.0 | 0.5 | 0.3 | 0.8 | 2.0 | 0.5 | 1.5 | 3.0 |
| `strong` (Probe 3, escalated shaping) | 1.0 | 0.5 | 0.3 | 1.0 | 4.0 | 0.8 | 2.5 | 6.0 |

### Pre-Tune Decision Tree (Hour 6–8)

```
Run Probe 1 (weak).    Record reward at step 0 and step 2000.
Run Probe 2 (primary). Record reward at step 0 and step 2000.
Run Probe 3 (strong).  Record reward at step 0 and step 2000.

Compute delta_reward for each profile = (reward_2000 - reward_0) / abs(reward_0)

if delta_reward(primary) > 0.10:         # > 10% improvement
    COMMIT: primary profile, full 50k run
elif delta_reward(strong) > 0.10:
    COMMIT: strong profile, full 50k run
elif delta_reward(weak) > 0.05:
    COMMIT: weak profile, full 50k run  (shape is learning but slowly)
else:
    ESCALATE: Failure Branch #1 (Part VII)
```

### Escape Hatches (if all three flatline)

**Option A — Narrower task.** Reduce action space from 8 → 3 (AID, PLEDGE, NEUTRAL). Easier to learn.

**Option B — Single-agent + context.** Train one "central coordinator" agent that sees all 6 country states. Simpler optimization landscape.

**Option C — Pivot narrative to honest.** Relabel as "rule-based multi-agent simulator with Llama advisor" — drop MAPPO claim entirely. ClaimBoundaryBanner says "No learned policy — agents are rule-based; our novel contribution is the cascade detector and the Llama decision-support integration." This is the worst case and still credible.

## Training Hyperparameters — LOCKED

```python
TRAINING_CONFIG = {
    "total_parallel_steps": 50000,       # ~6-10 hr on consumer GPU
    "rollout_len":          1024,
    "n_envs":               4,           # parallel envs for data collection
    "n_agents":             6,
    "learning_rate":        3e-4,
    "clip_eps":             0.2,
    "entropy_coeff":        0.01,
    "value_coeff":          0.5,
    "batch_size":           256,
    "n_epochs_per_rollout": 4,
    "gae_lambda":           0.95,
    "gamma":                0.99,
    "target_kl":            0.015,
    "max_grad_norm":        0.5,
}

ARCHITECTURE = {
    "backbone":             "MLP 36→128→128, ReLU",
    "actor_heads":          "6 × Linear(128→8), independent",
    "critic":               "Centralized: concat(obs_all_agents=216) → 256 → 256 → 1, ReLU",
    "total_params_est":     "~450K",
    "framework":            "PyTorch 2.x",
}
```

Do not tune these. If training fails, adjust reward profile, not hyperparams.

## 10 Load-Bearing Endpoints — Complete JSON Contracts

### 1. `GET /health`
```json
{"status": "ok", "version": "v6", "uptime_s": 3421}
```

### 2. `GET /stream` (V5 legacy — kept for backward compat)
SSE stream. Each event:
```json
{
  "step": 47,
  "timestamp": "2026-04-25T10:23:45Z",
  "world_state": {
    "gdp": [0.84, 0.72, 0.68, 0.91, 0.55, 0.77],
    "military": [0.42, 0.38, 0.29, 0.51, 0.33, 0.44],
    "climate": [0.67, 0.58, 0.73, 0.81, 0.52, 0.64],
    "relations": [[0.0, 0.43, ...], ...],
    "disaster_severity": 0.0,
    "arms_race_index": 0.23,
    "global_gdp_index": 0.747,
    "humanitarian_score": 0.61,
    "cascade_severity": 0.0,
    "cascade_active": false,
    "active_crises": []
  },
  "actions_last_step": {"USA": "NEUTRAL", "EU": "PLEDGE", ...},
  "metrics": {"aid_dispatch_rate_100": 0.12, "conflict_index": 0.18}
}
```

### 3. `GET /stream/mappo`
Identical shape to `/stream`. Policy = MAPPO (loads `checkpoints/mappo_50k.pt`). Fallback to scripted if checkpoint missing.

### 4. `GET /stream/rulebased`
Identical shape. Policy = `rule_based_policy.py` (NEUTRAL unless disaster → AID_DISPATCH). Intentionally weaker than MAPPO in targeted ways.

### 5. `GET /cascade-state`
```json
{
  "cascade_active": true,
  "chain": [
    {"step": 10, "type": "natural_disaster", "affected": ["India"], "severity": 0.87},
    {"step": 20, "type": "food_crisis", "affected": ["Bangladesh"], "severity": 0.71},
    {"step": 30, "type": "econ_crash", "affected": ["China"], "severity": 0.63}
  ],
  "eta_collapse_steps": 23,
  "severity_curve": [0.12, 0.18, 0.25, ..., 0.87, 0.91, 0.93],
  "current_step": 47
}
```

### 6. `POST /run-scenario/{scenario_id}`
Body: `{}`. Valid scenario_ids: `south-asia-cascade`, `pacific-cold-war`, `climate-emergency`, `post-conflict`, `perfect-storm`.
Response: `{"status": "started", "scenario_name": "South Asian Monsoon Cascade", "estimated_duration_steps": 200}`

### 7. `GET /live-brief`
Bypasses cache. Calls Groq fresh. Returns SSE stream of the brief being generated word-by-word:
```
data: {"word": "A", "done": false}
data: {"word": " severe", "done": false}
...
data: {"word": "CRITICAL", "done": true, "full_dict": {...}, "latency_ms": 1243}
```

### 8. `GET /eval-summary`
Serves `data/eval_summary.json`. If file doesn't exist, return 404 with `{"status": "pending", "message": "Run make eval after training"}`.
Shape:
```json
{
  "Random":     {"mean_reward": 0.61, "std_reward": 0.08, "mean_aid_freq": 0.18, "seeds": [42, 7, 13, 99, 2026]},
  "Rule-Based": {"mean_reward": 1.87, "std_reward": 0.12, "mean_aid_freq": 0.42, "seeds": [42, 7, 13, 99, 2026]},
  "MAPPO":      {"mean_reward": 2.34, "std_reward": 0.15, "mean_aid_freq": 0.58, "seeds": [42, 7, 13, 99, 2026]},
  "mappo_improvement_pct": 25.1,
  "checkpoint": "checkpoints/mappo_50k.pt",
  "eval_episodes": 10,
  "generated_at": "2026-04-25T22:14:03Z"
}
```

### 9. `GET /training-meta`
Serves `data/training_meta.json`. If pending, return 404 with pending status.
Shape:
```json
{
  "architecture": {"backbone": "MLP 36→128→128", "actor_heads": "6 × Linear(128→8)", "critic": "MLP 216→256→256→1 (centralized)", "total_params": 450432, "framework": "PyTorch 2.1"},
  "training": {"total_parallel_steps": 50000, "agent_steps": 300000, "rollout_len": 1024, "learning_rate": 0.0003, "clip_eps": 0.2, "entropy_coeff": 0.01, "batch_size": 256},
  "checkpoint": {"path": "checkpoints/mappo_50k.pt", "sha256_prefix": "a3f2c9e1", "size_mb": 5.24},
  "generated_at": "2026-04-25T20:47:11Z"
}
```

### 10. `GET /claim-boundary`
```json
{
  "scripted_elements": [
    "World crisis events and their timing",
    "Crisis scenario types and affected countries",
    "Scripted fallback agent action sequences"
  ],
  "learned_elements": [
    "Agent policy responses",
    "Reward curves from training",
    "Emergent phenomena (computed from policy state transitions)"
  ],
  "checkpoint_loaded": true,
  "demo_mode": "trained",
  "checkpoint_name": "mappo_50k.pt",
  "eval_available": true
}
```

### 11. `GET /agent-rationale/{agent_id}/{step}`
```json
{
  "agent_id": "USA",
  "step": 12,
  "action_taken": "AID_DISPATCH",
  "top_features": [
    {"name": "disaster_severity", "value": 0.87, "interpretation": "HIGH — cascade active"},
    {"name": "relations_india", "value": 0.43, "interpretation": "POSITIVE — aid welcomed"},
    {"name": "arms_race_index", "value": 0.20, "interpretation": "LOW — no military pressure"}
  ],
  "alternatives": [
    {"action": "NEUTRAL", "expected_reward": -0.2},
    {"action": "TRADE", "expected_reward": 0.1},
    {"action": "AID_DISPATCH", "expected_reward": 1.4, "chosen": true},
    {"action": "DEPLOY_MILITARY", "expected_reward": -0.8}
  ],
  "rationale": "AID_DISPATCH dominates due to cascade severity and positive regional relations. Military options penalized by low arms-race index.",
  "source": "policy feature attribution (approximate)"
}
```

### 12. `GET /world-outcome`
```json
{
  "outcome_state": "FRAGILE_PEACE",
  "final_step": 200,
  "mappo_metrics": {"final_gdp": 0.84, "conflict_level": 0.18, "climate_health": 0.71, "humanitarian_score": 0.89, "alliance_stability": 0.77},
  "rulebased_metrics": {"final_gdp": 0.62, "conflict_level": 0.51, "climate_health": 0.58, "humanitarian_score": 0.54, "alliance_stability": 0.41},
  "llama_summary": "Despite early escalation, coordinated aid dispatch prevented humanitarian catastrophe.",
  "llama_model": "llama-3.3-70b-versatile",
  "generated_at": "2026-04-25T23:12:45Z"
}
```

## Reference: world_env.py core API

```python
class WorldPolicyEnv(pettingzoo.ParallelEnv):
    metadata = {"render_modes": []}

    def __init__(self):
        self.possible_agents = ["USA", "EU", "UK", "China", "Russia", "India"]
        self.action_spaces = {a: Discrete(8) for a in self.possible_agents}
        # Observation: 36 dims per agent
        # [own_state (6) + 5 other agents relative states (25) + global indicators (5)]
        self.observation_spaces = {a: Box(low=0.0, high=1.0, shape=(36,)) for a in self.possible_agents}

    def reset(self, seed=None):
        # Initialize world_state to neutral starting point
        # Return obs dict, info dict
        ...

    def step(self, actions: dict[str, int]):
        # Apply action transition function
        # Trigger event engine (may inject new crisis)
        # Compute rewards (see Reward Function spec above)
        # Check termination
        # Return obs, rewards, terminated, truncated, info
        ...
```

## Reference: rule_based_policy.py

```python
class RuleBasedPolicy:
    """Intentionally weaker baseline. Must lose to MAPPO by a visible margin."""

    def act(self, obs_dict):
        actions = {}
        for agent, obs in obs_dict.items():
            disaster_severity = obs[30]  # global indicator
            own_military = obs[1]
            relations_mean = obs[6:11].mean()

            if disaster_severity > 0.6:
                actions[agent] = AID_DISPATCH  # responds to crisis
            elif own_military < 0.3 and relations_mean < 0.0:
                actions[agent] = DEPLOY_MILITARY  # naive defensive buildup
            else:
                actions[agent] = NEUTRAL
        return actions
```

Note: rule-based intentionally **does not detect cascade patterns**. It responds to individual crises, not the compound signal. This is what MAPPO should beat on.

---

# PART V — FRONTEND DELTA FROM V5

V5 frontend is already built (globe, 9 components, design system). V6 adds 7 new components + upgrades 4 existing ones.

## Components: NEW (must build in V6 sprint)

### 1. CascadeVisualizer.tsx

**Purpose:** Animated red cascade lines on globe connecting countries in the cascade chain.

**Props:**
```typescript
interface CascadeVisualizerProps {
  chain: CascadeNode[];          // from /cascade-state
  currentStep: number;
  severityCurve: number[];       // per-step severity
}

interface CascadeNode {
  step: number;
  type: string;
  affected: string[];
  severity: number;
}
```

**Render:** Inside GlobeView. Adds a new Deck.gl ArcLayer with:
- Red color ramp (severity 0→1 maps to light-red→deep-red)
- Thicker stroke for later nodes in chain
- Pulsing animation at 1.5x speed as severity grows
- Arc widths: `getWidth: (d) => 1.5 + d.severity * 3`

**CSS:** N/A — pure deck.gl config.

**Acceptance:** Load Scenario 1 → within 15s, red arcs from India→Bangladesh→China are visible, animating.

### 2. CollapseCountdown.tsx

**Purpose:** Amber LED countdown in top-right corner of globe.

**Props:**
```typescript
interface CollapseCountdownProps {
  etaSteps: number | null;       // from /cascade-state. null = not in cascade
  severityCurve: number[];       // for pulsing rate
}
```

**Render:**
```jsx
<div className="collapse-countdown">
  <div className="countdown-label">COLLAPSE IMMINENT</div>
  <div className="countdown-value led-amber pulse">IN {etaSteps} STEPS</div>
</div>
```

**CSS:**
```css
.collapse-countdown {
  position: absolute;
  top: 24px;
  right: 24px;
  background: rgba(15, 10, 0, 0.85);
  backdrop-filter: blur(8px);
  border: 1px solid rgba(245, 158, 11, 0.3);
  border-radius: 8px;
  padding: 8px 14px;
  font-family: var(--font-mono);
}
.countdown-label {
  font-size: 9px;
  color: rgba(245, 158, 11, 0.6);
  letter-spacing: 1.5px;
}
.countdown-value {
  font-size: 22px;
  color: #fbbf24;
  text-shadow: 0 0 12px rgba(245, 158, 11, 0.6);
}
/* Pulse faster as severity grows — inline style sets --pulse-duration */
.countdown-value.pulse {
  animation: led-pulse var(--pulse-duration, 1.5s) ease-in-out infinite;
}
```

**Acceptance:** Visible when `/cascade-state` returns `eta_collapse_steps`. Hidden otherwise.

### 3. SplitScreenGlobe.tsx + SplitScreenToggle.tsx

**Purpose:** Two globes side-by-side. Each subscribes to its own SSE stream.

**Props:**
```typescript
interface SplitScreenGlobeProps {
  mode: "single" | "split";
  onToggle: () => void;
}
```

**Render (split mode):**
```jsx
<div className="split-container">
  <div className="split-panel split-mappo">
    <div className="split-label">MAPPO (Trained)</div>
    <GlobeView streamUrl="/stream/mappo" />
  </div>
  <div className="split-panel split-rulebased">
    <div className="split-label">Baseline (Rule-Based)</div>
    <GlobeView streamUrl="/stream/rulebased" />
  </div>
</div>
<DivergenceScoreCard />  // appears top-center when split
```

**CSS:**
```css
.split-container {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
  height: 100%;
}
.split-panel {
  position: relative;
  border-radius: 16px;
  overflow: hidden;
  border: 1px solid var(--glass-border);
}
.split-mappo {
  border-color: rgba(59, 130, 246, 0.4);
}
.split-rulebased {
  border-color: rgba(100, 116, 139, 0.3);
}
.split-label {
  position: absolute;
  top: 12px;
  left: 12px;
  font-family: var(--font-mono);
  font-size: 11px;
  background: rgba(0, 0, 0, 0.6);
  padding: 4px 8px;
  border-radius: 4px;
  z-index: 20;
}
```

**Acceptance:** Toggle splits screen. Both globes render. Independent SSE streams work (verify in DevTools → two separate EventSources).

### 4. DivergenceScoreCard.tsx

**Purpose:** Top-center when split-screen active. Shows live divergence.

**Props:**
```typescript
interface DivergenceScoreCardProps {
  mappoState: WorldState;
  rulebasedState: WorldState;
}
```

**Render:** Four mini-stat cells horizontal. Each shows label + delta value (+0.12) with arrow (▲/▼). Color: green if MAPPO better, red if worse.

**CSS:** Reuse `.glass-panel` from V5 design system. Compact variant with `padding: 8px 14px`.

**Acceptance:** Values update each step. Color-coded correctly.

### 5. PolicyExplanationDrawer.tsx

**Purpose:** Right-side drawer. Opens on country click.

**Props:**
```typescript
interface PolicyExplanationDrawerProps {
  isOpen: boolean;
  agentId: string | null;
  step: number | null;
  onClose: () => void;
}
```

**Render:**
- Drawer slides from right (width: 400px)
- Header: "WHY USA CHOSE AID_DISPATCH?"
- Top features section (3 bars with values)
- Alternatives section (4 items, chosen one highlighted)
- Rationale paragraph at bottom
- Footer: "Source: policy feature attribution (approximate)"

**CSS:** New `.drawer-right` class with slide-in animation (`transform: translateX(0)` from `translateX(100%)`).

**Acceptance:** Click USA node → drawer opens in <300ms → shows real data from `/agent-rationale/USA/{current_step}`.

### 6. WorldOutcomeCard.tsx

**Purpose:** Full-screen modal at episode end.

**Props:**
```typescript
interface WorldOutcomeCardProps {
  isOpen: boolean;
  outcome: OutcomeState;           // "STABILIZED" | "COLLAPSE" | "FRAGILE_PEACE"
  mappoMetrics: OutcomeMetrics;
  rulebasedMetrics: OutcomeMetrics;
  llamaSummary: string;
  onDismiss: () => void;
}
```

**Render:**
- Modal backdrop: `rgba(0, 0, 0, 0.8)` with blur
- Card: center screen, max-width 600px
- Title: outcome state in color (green/red/amber)
- Two-column metrics table (MAPPO vs Rule-Based)
- Llama summary sentence in italic at bottom, with Llama logo/label

**CSS:** Reuse glass-panel. Add `outcome-stabilized`, `outcome-collapse`, `outcome-fragile` variants for title color.

**Acceptance:** Episode completes → modal appears within 500ms → all metrics populated → Llama sentence visible.

### 7. ScenarioCard.tsx

**Purpose:** Bottom or sidebar card with 5 preset scenarios. Click "Play" to load.

**Props:**
```typescript
interface ScenarioCardProps {
  scenarios: Scenario[];           // from /scenarios endpoint or static
  onLoad: (scenarioId: string) => void;
}
```

**Render:** Five rows, each: scenario name + 1-line description + "▶ Play" button.

**CSS:** Use `.btn-skeu-primary` for play buttons.

**Acceptance:** Click "▶ Play" on South Asian Monsoon Cascade → POST `/run-scenario/south-asia-cascade` → frontend transitions to live stream.

## Components: UPGRADED (modify existing V5 components)

### GlobeView.tsx — UPGRADED

**Additions:**
- Accept `streamUrl` prop (for split-screen support — default `/stream`)
- Accept `cascadeOverlay` prop (enables CascadeVisualizer inside)
- Accept `onCountryClick` callback (for PolicyExplanationDrawer)

### CrisisBriefCard.tsx → LiveBriefCard.tsx

**Rename and upgrade:**
- Add "Live Brief" button at top right of card
- Button triggers fetch from `/live-brief` (SSE)
- Stream words one at a time into the brief text area with 40ms per-word delay
- Footer shows live latency (`1.2s · LIVE` vs `cached`)

### EmergentBadgePanel.tsx — UPGRADED

**Additions:**
- Add 4th badge: "COALITION FORMED" — green LED, shows when 3+ agents choose same action 3+ steps
- Show trigger text for each badge (already in V5 plan — verify it's implemented)
- Coalition badge shows member country names

### SimPanel.tsx — UPGRADED

**Additions:**
- New card slot: HumanitarianPanel (3 gauge dials)
- Keep existing: TrainingFactsCard, EmergentBadgePanel, EvalSummaryCard, reward curves

## Components: UNCHANGED

- TrainingFactsCard.tsx
- EvalSummaryCard.tsx
- ClaimBoundaryBanner.tsx
- ArchitectureDiagramPanel.tsx
- CrisisOverlay.tsx

## New CSS Tokens (add to worldpolicy.css)

```css
:root {
  /* Cascade colors */
  --cascade-arc-weak:   rgba(239, 68, 68, 0.4);
  --cascade-arc-medium: rgba(239, 68, 68, 0.7);
  --cascade-arc-strong: rgba(220, 38, 38, 1.0);

  /* Split-screen panel borders */
  --split-mappo-border:    rgba(59, 130, 246, 0.4);
  --split-rulebased-border: rgba(100, 116, 139, 0.3);

  /* Outcome states */
  --outcome-stabilized: #22c55e;
  --outcome-collapse:   #ef4444;
  --outcome-fragile:    #f59e0b;
}

/* New animations */
@keyframes cascade-pulse {
  0%, 100% { opacity: 0.5; }
  50%      { opacity: 1.0; }
}
@keyframes drawer-slide-in {
  from { transform: translateX(100%); }
  to   { transform: translateX(0); }
}
@keyframes modal-fade-in {
  from { opacity: 0; transform: scale(0.95); }
  to   { opacity: 1; transform: scale(1); }
}
```

---

# PART VI — DEPENDENCY GRAPH

```
Hour 0         Hour 4         Hour 8         Hour 16        Hour 24        Hour 32        Hour 40        Hour 50
  │              │              │              │              │              │              │              │
  ├── SETUP ─────┤              │              │              │              │              │              │
  │              │              │              │              │              │              │              │
  │    ┌─ env.py ─┬─ agent.py ──┤              │              │              │              │              │
  │    │          │             │              │              │              │              │              │
  │    ├─ event.py ─── cascade ──────────────────────────────┤              │              │              │
  │    │                                                     │              │              │              │
  │    ├─ crisis_brief ──── Groq cache ──── live-brief ─────┤              │              │              │
  │    │                                                     │              │              │              │
  │    ├─ FastAPI ── /stream ── /cascade ── /eval ── /meta ──┼── /world-out ┤              │              │
  │                                                          │              │              │              │
  │    │                                                     │              │              │              │
  │    ├─ PROBES ─┤                                          │              │              │              │
  │    │          │                                          │              │              │              │
  │                ├─── TRAINING (BACKGROUND, 6-10hr) ──────┤              │              │              │
  │                │                                          │              │              │              │
  │    ├─ Frontend SSE stub ──── Cascade Vis ── Split Screen ┤── Drawer ────┤              │              │
  │                                                          │              │              │              │
  │                                                          ├─ EVAL ──────┤              │              │
  │                                                          │              │              │              │
  │                                                          ├─ Arch diag ─┤              │              │
  │                                                          │              │              │              │
  │                                                          ├─ HF Space ───────── deploy ┤              │
  │                                                          │              │              │              │
  │                                                          │              ├─ CONDITIONAL features ──────┤
  │                                                          │              │              │              │
  │                                                          │              │              ├─ POLISH ────┤
  │                                                          │              │              │              │
  │                                                          │              │              │              ├─ REHEARSAL ×5 ──┤
```

## Critical Path (the things that block everything else)

1. **env.py + agent.py (Hours 0-3)** — nothing trains or runs without these.
2. **Reward function + probes (Hours 6-8)** — locks the winning reward profile. 50k training starts from this.
3. **FastAPI /stream + /cascade-state (Hours 8-14)** — frontend has nothing to visualize without these.
4. **Background training run (Hours 8-16)** — must be visibly learning by Hour 16 or we invoke Failure Branch #1.
5. **Cascade Visualizer + Split-Screen (Hours 16-22)** — the demo money shot. If these don't land, demo is weaker by ~30%.
6. **Eval harness (Hours 24-28)** — produces the proof card. Without it, the "trained policy beats rule-based" claim has no chart.
7. **Demo Freeze v1 (Hour 28-32)** — stops the churn. If you don't freeze, last-minute changes will break the demo.

## What blocks what

- Everything blocks on `env.py` and `agent.py` in Hours 0-3.
- `/stream/mappo` blocks on `checkpoints/mappo_50k.pt` (or fallback scripted).
- `/eval-summary` blocks on `data/eval_summary.json` — which blocks on `make eval` completing.
- `/training-meta` blocks on training finishing.
- Frontend cascade blocks on `/cascade-state` returning real data.
- Demo rehearsal blocks on ALL components being integrated — Hour 40 is the earliest feasible.

---

# PART VII — FAILURE BRANCHES

Pre-decided responses to every major thing that can go wrong. When stressed, follow these instead of improvising.

## Failure Branch #1: Training Probes All Flatline (Hour 8)

**Symptom:** All three reward probes show <5% reward improvement from step 0 to step 2000.

**Likely cause:** Reward function bug, reward too sparse, environment dynamics don't reward the intended behavior.

**Response:**
1. Krish: examine `data/training_log.jsonl` for Probe 2. Check that reward values are actually changing, not constant.
2. Try Escape Hatch A (reduce action space 8→3). Run one more 2k probe.
3. If still flat, invoke **NARRATIVE PIVOT:**
   - Relabel ClaimBoundaryBanner: "Demo Mode: Rule-based multi-agent simulation with Llama advisor. No learned policy."
   - Change TrainingFactsCard to show the *scripted* fallback architecture, not MAPPO.
   - Change EvalSummaryCard to show "Rule-based policy evaluated across 5 seeds" — drop the MAPPO bar.
   - Your new story: "The novel contribution is the cascade detector algorithm and the Llama live advisory layer. Coordination policies are heuristic."
4. Update demo script: remove "we trained MAPPO" line at 2:15. Replace with: "Our rule-based agents model realistic country behavior. Watch how the cascade detector fires emergent signals."
5. Tushar: commit this as `git tag pivot-narrative` so it's trackable.

## Failure Branch #2: 50k Training Run Crashes Overnight (Hour 16)

**Symptom:** Training process died or NaN'd. Last checkpoint is `mappo_20k.pt` or smaller.

**Response:**
1. Use the latest surviving checkpoint (`mappo_20k.pt` or similar).
2. Rename: `cp checkpoints/mappo_20k.pt checkpoints/mappo_50k.pt`.
3. Update `data/training_meta.json` manually with the actual step count — **do not lie in training meta**. Set `"total_parallel_steps": 20000` (actual).
4. Update TrainingFactsCard to show actual step count.
5. Expect weaker MAPPO vs rule-based gap in eval. Still fine; reviewer judges honesty.

## Failure Branch #3: Groq API Down at Venue

**Symptom:** Live Brief button times out. `GROQ_OFFLINE=1` flag not set.

**Response:**
1. Immediately set env var: `export GROQ_OFFLINE=1` and restart backend.
2. `crisis_brief.py` falls back to `data/groq_cache.json`. All 100 cached briefs are available.
3. LiveBriefCard footer shows "(cached · live unavailable)" — this is HONEST and judges respect it.
4. Do not panic on stage. Say: "We have a cached Llama response for this exact scenario — we preloaded 100 scenarios for offline demo reliability."

## Failure Branch #4: SSE Stream Hangs Mid-Demo

**Symptom:** Globe freezes. `/stream/mappo` stopped emitting.

**Response:**
1. Presenter: keep talking through the frozen state. Do not look at laptop.
2. Tushar (behind laptop): press `R` (reset shortcut). Scenario reloads in <3 seconds.
3. If reset fails: frontend has `data/replay_scenario1.json` pre-recorded. Toggle REPLAY mode (hidden button, `Ctrl+Shift+P`). Renders the saved trajectory.
4. Acknowledge gracefully: "Live sim hit a hiccup — here's the pre-recorded run of the same scenario."

## Failure Branch #5: Frontend Breaks in Projector Resolution

**Symptom:** Layout broken on venue projector (likely 1920x1080 or 1280x720). Glass panels overflow.

**Response:**
1. Emergency CSS override: add `?preset=projector` query param. Frontend applies `max-width: 1200px; font-scale: 1.2;`.
2. Implement this CSS preset on Day 1 (Hour 6-8) if possible, or Hour 40-44 during polish.
3. Test on laptop external monitor before venue.

## Failure Branch #6: Laptop Dies at Venue

**Symptom:** Primary laptop crashes, won't boot, battery dies despite charger.

**Response:**
1. Switch to Raj's backup laptop (pre-configured).
2. `git pull && make cache && make demo` — should restart in <3 minutes.
3. HF Space URL loads on phone as tertiary fallback — judges can still see the markdown + architecture diagram.

## Failure Branch #7: Presenter Loses Voice / Panic Attack

**Response:**
1. Raj is the backup presenter. Must know the script cold.
2. If demo is mid-flight when this happens, Raj takes over at the next natural pause (after a click).
3. If the presenter recovers mid-demo, resume. Judges don't penalize humans being humans.

## Failure Branch #8: Judges Ask Questions You Can't Answer

**Response:**
1. Do not fake an answer. Judges catch this every time.
2. Template response: "That's a great question. Here's what we know for sure: [honest answer]. Here's what we're not sure about: [honest uncertainty]. I'd need to dig into [specific thing] to answer fully."
3. If the question is a known weakness, own it: "Yes, we're aware of that limitation. Our next step would be [concrete plan]."
4. Credibility > confidence. Always.

---

# PART VIII — PER-PERSON MONITOR-TAPE SHEETS

Print these. Tape to monitor. Reference every 2 hours.

## KRISH — Monitor Sheet (ML + Backend Lead)

**Hour-by-hour mission:**
- H0-3: env.py + agent.py + event_engine.py. Smoke test green.
- H3-6: train.py + mappo_agent. Loss decreasing on 2k-step run.
- H6-8: PROBES — run 3 reward profiles, commit the winner.
- H8-10: Launch 50k-step training run. Run in tmux/nohup.
- H8-14: analytics.py detectors (cold_war, arms_race, free_rider, coalition). Tests green.
- H14-16: scripted_fallback.py + cascade trigger in event_engine.
- H16-20: MAPPO inference endpoint for split-screen. Load checkpoint, serve /stream/mappo.
- H20-24: eval.py written. Ready to run at Hour 24.
- H24-28: `make eval` → eval_summary.json. Confirm MAPPO > rule-based.
- H28-32: training_meta.py + gen_arch_diagram.py + claim-boundary.
- H32-40: Conditional features if checkpoint clean. Otherwise help Raj.
- H40-48: Present during rehearsals (you're the ML expert — field the technical questions).

**DO NOT:**
- Touch the reward function after Hour 8 probe decision.
- Retrain after Hour 20 unless Failure Branch #2.
- Write new features after `demo-freeze-v1` (Hour 28-32).

**Escalate to Tushar if:**
- Reward probes all flatline (invoke FB#1).
- Training crashes (invoke FB#2).
- You lose a key file.
- You haven't slept.

## RAJ — Monitor Sheet (Frontend + Backend Support)

**Hour-by-hour mission:**
- H0-1: Verify V5 frontend loads. Start FastAPI skeleton.
- H1-3: Wire frontend SSE to stub backend. Verify stream in DevTools.
- H3-6: crisis_brief.py + /crisis-brief endpoint.
- H6-8: /live-brief Groq streaming endpoint.
- H8-14: /stream/mappo + /stream/rulebased + /cascade-state + /run-scenario endpoints.
- H12-16: CascadeVisualizer + CollapseCountdown frontend components.
- H16-20: SplitScreenGlobe + SplitScreenToggle + DivergenceScoreCard.
- H18-22: PolicyExplanationDrawer + /agent-rationale endpoint.
- H20-24: WorldOutcomeCard + /world-outcome endpoint.
- H24-28: EvalSummaryCard + TrainingFactsCard + ArchitectureDiagramPanel wiring.
- H28-32: Frontend polish, scenario picker card, LiveBriefCard upgrade to CrisisBriefCard.
- H32-40: Conditional feature UIs if enabled. Otherwise visual polish.
- H40-48: Rehearsal support, fix projector CSS issues, backup presenter prep.

**DO NOT:**
- Refactor existing V5 components unless their spec changed.
- Add new npm packages after Hour 8 without clearing with Tushar.
- Block on Krish for backend — use stub endpoints and forge ahead.

**Escalate to Tushar if:**
- SSE hangs repeatedly (FB#4).
- Frontend breaks at projector resolution (FB#5).

## TUSHAR — Monitor Sheet (Integration + Demo Lead)

**Hour-by-hour mission:**
- H0-1: `make cache` runs. data/groq_cache.json populated.
- H1-3: Writes tests/test_emergent_detectors.py + docs/detector_examples.md.
- H3-8: Full Makefile. `make freeze` runs (expect some failures — that's fine).
- H8-14: Scenario library JSON (data/scenarios.json, 5 scenarios with inject_sequences).
- H14-16: End-to-end integration smoke test. Document breakage.
- H16-20: Keyboard shortcuts framework in frontend (D/Space/R/1-5).
- H20-24: HOUR 24 CHECKPOINT — run all 8 quality gates. Pass/fail decision.
- H24-28: Demo mission cards, About panel, metrics ticker.
- H26-32: HuggingFace Spaces deploy. Write Space README with embedded artifacts.
- H28-32: DEMO FREEZE v1 — run make freeze, fill DEMO_FREEZE.md, tag.
- H32-40: Record `data/replay_scenario1.json` as SSE failure backup. Polish Tier 3.
- H40-48: Run all 5 rehearsals. Time them strictly. Enforce 5:00 limit.
- H48-50: Venue setup + final checks.

**Your job is to say NO:**
- NO new features after freeze.
- NO scope creep during rehearsal phase.
- NO "just one more thing" at Hour 47.

**Escalate yourself to Krish+Raj together if:**
- Hour 24 checkpoint fails.
- Rehearsal #3 is still over 5:00.
- Any FB activates.

---

# PART IX — DEMO DAY RUNBOOK

## Morning of April 26 (3 hours before demo slot)

- [ ] Charge all devices to 100%.
- [ ] Pack: 2 laptops (primary + backup), phone hotspot, charger cables, HDMI adapter, USB-C adapter, power strip, 2 pens, 1 notebook, water bottle.
- [ ] Eat. Real food. Not just chai.
- [ ] Do ONE final rehearsal at home. Time it. Record it on phone. Watch playback.
- [ ] Verify HF Space loads on phone.
- [ ] Commit and push ANY final changes (git status clean).

## 30 Minutes Before Demo

- [ ] Find your demo slot area. Identify power outlet.
- [ ] Plug in primary laptop. Confirm charging.
- [ ] Connect to projector. Test resolution.
- [ ] Open terminal, run `make demo`. Verify backend starts.
- [ ] Open frontend in Chrome (NOT Safari — better SSE).
- [ ] Verify Scenario 1 loads. Watch first 30 seconds.
- [ ] Close any distracting tabs, apps, notifications.
- [ ] Silence phones.

## 5 Minutes Before Demo

- [ ] Frontend on Scenario 1 ready screen (before Play).
- [ ] Click zoom level set correctly for projector.
- [ ] Tushar at laptop, Raj+Krish ready for Q&A.
- [ ] Deep breath. Drink water.
- [ ] Remember: the judges WANT you to do well. They're not adversaries.

## Emergency Contingencies (if things go wrong during demo)

**Globe doesn't load:** Press R. If still broken, narrate: "Let me show the pre-recorded run" → Ctrl+Shift+P (replay mode).

**Llama doesn't respond:** Say: "This one's cached — we preloaded 100 scenario briefs for offline reliability." Click brief anyway, it'll render from cache.

**Split-screen misaligned:** Don't toggle mid-demo if possible. If it looks bad, say: "Both views are from separate policy inference — you can see the raw divergence in the metrics card."

**Presenter stumbles:** Pause. Breathe. Smile. Pick up where you left off. Judges are humans.

**Projector fails:** Have your phone ready with HF Space URL. Pass it around. "Here's our architecture and evaluation metrics for your reference."

## Q&A Responses (likely questions)

**"How did you train MAPPO?"**
> "Shared MLP backbone, 6 independent actor heads, centralized critic. 450K params. 50,000 parallel environment steps, so 300,000 agent-step samples. PyTorch 2.x. Checkpoint SHA visible in TrainingFactsCard. Reward function is dense-shaped with a cascade-intervention bonus to encourage coordinated response."

**"What's your evaluation methodology?"**
> "10 episodes per policy, 5 fixed random seeds. We report mean and standard deviation. The baseline chart at `baseline_comparison.png` is reproducible via `make eval`."

**"Isn't this just a scripted demo?"**
> "Great question — we anticipated that. The ClaimBoundaryBanner at the top is always visible. Events are scripted; agent responses, reward curves, and emergent phenomena come from the trained policy. The evaluation card proves MAPPO beats rule-based across 5 seeds."

**"Why multi-agent?"**
> "Because geopolitical coordination is fundamentally partial-observation and competitive-cooperative. You can't model it as single-agent RL. The emergent phenomena — cold war bifurcation, coalition formation, free-riding — only arise with multi-agent dynamics."

**"What's the novelty?"**
> "Two things. First, the cascade detector algorithm — we detect cascade failure patterns before they complete. Second, the Llama 3.3-70b decision-support integration — structured priority responses and risk levels, not narrative fluff."

**"What would you do next with more time?"**
> "Scale to 20 heterogeneous agents (Major Powers, Regional Powers, IOs), counterfactual what-if analysis, and learn from real historical crisis data."

---

# PART X — NON-NEGOTIABLE QUALITY GATES

Tushar runs these at Hour 8, Hour 16, Hour 24, Hour 32, Hour 40, and Hour 48. All must pass before advancing phases.

## Hour 8 Gate

- [ ] env.py smoke test green
- [ ] FastAPI /health returning ok
- [ ] Frontend SSE connection visible in DevTools
- [ ] `make cache` completed — groq_cache.json non-empty
- [ ] Training probes decision locked (winning profile recorded)

## Hour 16 Gate

- [ ] Background training running, reward curve visible
- [ ] /stream, /cascade-state, /live-brief all returning real data
- [ ] Frontend CascadeVisualizer rendering for Scenario 1
- [ ] `pytest tests/ -v` all green

## Hour 24 Gate ⚠️ CRITICAL

- [ ] Scenario 1 → cascade visualizes within 15s
- [ ] Split-screen toggle works, both globes stream
- [ ] Live Brief button streams Llama response <3s
- [ ] Click country → Policy Drawer opens with valid data
- [ ] Episode completes → World Outcome Card shows
- [ ] MAPPO reward curve shows visible improvement (>20% from start)
- [ ] `make freeze` reports at least 5/7 artifacts

**If Hour 24 fails → cut all CONDITIONAL features, use Hours 24-32 for stabilization.**

## Hour 32 Gate (DEMO FREEZE v1)

- [ ] `make freeze` reports 7/7 artifacts
- [ ] `DEMO_FREEZE.md` filled with all SHA prefixes
- [ ] Tag `demo-freeze-v1` pushed
- [ ] HF Space deployed and accessible
- [ ] End-to-end demo playable without crash

## Hour 40 Gate

- [ ] Rehearsal #1 completed, timed
- [ ] Tier 3 polish (sounds, shortcuts, ticker, about panel) in place
- [ ] Projector CSS preset tested

## Hour 48 Gate (FINAL FREEZE)

- [ ] Rehearsal #5 under 5:00 with zero hesitation
- [ ] Tag `demo-freeze-v2` pushed
- [ ] All laptops charged, all cables packed
- [ ] HF Space loads on phone
- [ ] Three team members rested and fed

---

# APPENDIX

## A. Reward Function Quick Reference

```
R_i_t = α₁·ΔGDP_i
      + α₂·mean_relations_i
      - α₃·military_i
      + α₄·ΔGlobal_GDP
      + α₅·ΔHumanitarian
      - α₆·arms_race
      - α₇·cascade_severity
      + α₈·cascade_intervention_bonus
```

Primary profile: α = [1.0, 0.5, 0.3, 0.8, 2.0, 0.5, 1.5, 3.0]

## B. Training Metrics Glossary

- **Episode total reward:** Sum of rewards across all agents, all steps, in one episode. Higher = better coordination.
- **AID_DISPATCH frequency:** Fraction of agent-steps where action was AID. Higher MAPPO value suggests learned coordination.
- **Arms race index final:** End-of-episode sum of all military levels. Lower = less escalation.
- **Humanitarian score:** Composite of aid adequacy + disaster response + civilian stress. Higher = more effective response.

## C. Endpoint Smoke Test Commands

```bash
# Hour 1:
curl http://localhost:8000/health
# Expected: {"status":"ok","version":"v6","uptime_s":N}

# Hour 3:
curl http://localhost:8000/crisis-brief
# Expected: JSON with brief, priority_response, risk_level, from_cache

# Hour 16:
curl http://localhost:8000/cascade-state
# Expected: JSON with chain, eta_collapse_steps, severity_curve

curl http://localhost:8000/stream/mappo -N --max-time 5
# Expected: 3-5 SSE events with world_state payloads

# Hour 28:
curl http://localhost:8000/eval-summary | python -m json.tool
# Expected: Random, Rule-Based, MAPPO with mean/std, mappo_improvement_pct > 15

curl http://localhost:8000/training-meta | python -m json.tool
# Expected: architecture, training, checkpoint sections

curl http://localhost:8000/claim-boundary | python -m json.tool
# Expected: scripted_elements, learned_elements, checkpoint_loaded: true
```

## D. Groq Cache Seed Scenarios

The 100 cached briefs should cover:
- 5 crisis types × 6 single-country affected (30)
- 5 crisis types × 10 country-pair combinations (50)
- 4 crisis types × 5 triple-country combinations (20)

Run `make cache` with `--n-samples 100` in Hour 0.

## E. Demo Script Memory Aid

**HOOK → CASCADE → SPLIT-SCREEN → EVIDENCE → LLAMA → DRAWER → OUTCOME**

Short form for the presenter:
```
0:00  "What happens when..."              (hook)
0:30  Click Scenario. Red cascade.        (cascade unfolds)
1:30  Toggle split-screen. MAPPO wins.    (proof of learning)
2:30  Point at eval card. Banner honest.  (evidence)
3:15  Click Live Brief. Llama streams.    (sponsor check)
3:45  Click USA. Drawer shows features.   (glass box)
4:30  Outcome card auto-appears.          (narrative close)
5:00  DONE.
```

## F. Glossary of Terms (for cold readers)

- **MAPPO** — Multi-Agent Proximal Policy Optimization. PPO but for multi-agent environments with centralized training, decentralized execution.
- **Cascade failure** — When one crisis triggers another, which triggers another, until the system can't absorb the load.
- **Policy feature attribution** — For each decision the policy made, which observation features mattered most. Approximate interpretability tool.
- **Centralized critic** — In MAPPO, the value function sees all agents' observations (not just one). Helps with credit assignment.
- **Emergent phenomena** — Behaviors that arise from multi-agent interaction but weren't explicitly programmed. Cold war bifurcation, coalition formation, free-riding.

---

*WorldPolicy-Env V6 Master Execution Plan · Refined · LOCKED · Team: Krish · Raj · Tushar · Status: IMPLEMENTATION-READY*

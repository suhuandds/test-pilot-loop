# 🧪 Test Pilot Loop
### One Brain, Three Knowledge Levels, Continuous Loop
### Cowork Opus stays live. Tests, feeds back, retests — until it passes.

---

## What This Is

Cowork Opus stays open as the **brain of your project**. After your code agent builds a feature, Opus test-drives the app three times — each time constrained to a different knowledge level. The gap between those experiences tells you how intuitive your app really is.

Then Opus sends structured feedback to the code agent. The code agent fixes. Opus retests. **The loop stays in one continuous session until the feature passes.**

**The app isn't done when code passes. It's done when someone who knows nothing can use it.**

---

## Why Three Knowledge Levels

The developer knows every button. A new user knows nothing. The gap between them is your UX problem.

| Run | Knowledge Level | What They Get | Simulates |
|-----|----------------|---------------|-----------|
| **Run 1: Cold** | Knows NOTHING | App name only. No docs, no PRD, no hints. | User who just downloaded the app |
| **Run 2: Guided** | Has the manual | User manual or README only. No PRD. | User who reads the docs first |
| **Run 3: Insider** | Has the PRD | Full product requirements. Knows everything. | Product manager doing acceptance testing |

Same Opus instance, but each run is **prompted with only that tier's allowed knowledge**. The cold run is the hardest — and the most valuable.

**The Tier Gap:** If the Cold user takes 12 steps and the Insider takes 4, that 3x gap IS your UX debt. When you close that gap, your app is intuitive.

---

## The Loop

```
┌─────────────────────────────────────────────────────────┐
│              🟣 COWORK OPUS (Brain — stays open)         │
│                                                          │
│  CODE AGENT builds feature                               │
│  Writes to FLIGHT_PLAN.md: "Ready for test"            │
│           ↓                                              │
│  OPUS reads the file, sees "Ready for test"              │
│           ↓                                              │
│  Run 1: COLD — Opus tests knowing NOTHING                │
│    → Records: steps, confusion, abandonment points       │
│    → Reset app                                           │
│           ↓                                              │
│  Run 2: GUIDED — Opus tests with user manual only        │
│    → Records: doc accuracy, terminology gaps             │
│    → Reset app                                           │
│           ↓                                              │
│  Run 3: INSIDER — Opus tests with full PRD               │
│    → Records: spec compliance, feature completeness      │
│           ↓                                              │
│  OPUS writes FEEDBACK to FLIGHT_PLAN.md:               │
│    • UX issues from all three runs                       │
│    • Tier Gap Ratio (Cold steps / Insider steps)         │
│    • Specific fixes needed                               │
│    • Verdict: PASS / NEEDS WORK / FAIL                   │
│           ↓                                              │
│  CODE AGENT reads feedback → fixes → writes:             │
│    "Fixed. Ready for retest."                            │
│           ↓                                              │
│  OPUS retests (same three runs)                          │
│           ↓                                              │
│  LOOP until Opus says PASS for all three levels          │
│           ↓                                              │
│  ✅ SHIP IT                                              │
└─────────────────────────────────────────────────────────┘
```

---

## The Dual-Patrol Model

Cowork and the code agent are **two independent processes** that never interact directly. They communicate exclusively through a shared file: `FLIGHT_DECK/FLIGHT_PLAN.md`.

**Cowork Opus** is the brain and test pilot. It patrols `FLIGHT_PLAN.md` every 10 minutes, looking for "Ready for test." When it finds one, it switches to test mode — finds the app on screen, runs the three knowledge-level tests through computer use, writes structured feedback back to the file, and returns to patrol.

**Claude Code** (or any coding agent) is the builder. It builds features, runs tests, launches the app, and writes "Ready for test" to the shared file. Then it polls the same file on its own 10-minute cycle. When it finds feedback and a `NEXT_ACTION`, it reads the instructions, fixes the issues, rebuilds, relaunches, and writes "Ready for retest."

Neither agent commands the other. Cowork cannot type into Terminal (platform safety boundary), and doesn't need to — the file is the only coordination channel. If an agent stops responding, the other writes an escalation to the file and the human director intervenes.

This architecture is portable: any coding agent that can read and write a markdown file (Claude Code, Codex CLI, Cursor, etc.) can serve as the builder side.

### The Communication Channel

```
CODE AGENT writes:
──────────────────
[2026-03-27 10:30] — Claude Code
Task: Photo import feature
Status: ✅ Built. Code compiles. Tests pass.
Ready for test pilot.

OPUS writes back:
──────────────────
[2026-03-27 10:35] — Cowork Opus Test Pilot

RUN 1 — COLD USER (knows nothing):
  Completed: NO — gave up at step 8
  Steps taken: 8
  Confusion: Couldn't find the import button.
  The icon looks like a camera but feature is called "Import."

RUN 2 — GUIDED USER (has manual):
  Completed: YES
  Steps taken: 6
  Doc issue: Manual says "tap Import on main screen"
  but the button is on the second tab.

RUN 3 — INSIDER (has PRD):
  Completed: YES
  Steps taken: 3
  Spec compliance: 90% — missing the "drag-and-drop" 
  import mentioned in PRD section 4.2.

TIER GAP RATIO: Cold 8 steps / Insider 3 steps = 2.7x
TARGET: ≤ 1.5x

FEEDBACK FOR CODE AGENT:
  1. Rename camera icon to "Import" with text label
  2. Move import button to main screen (not second tab)
  3. Add drag-and-drop import per PRD section 4.2
  4. Add onboarding tooltip pointing to import on first launch

VERDICT: ❌ NEEDS WORK — fix items above, then signal ready for retest.

CODE AGENT writes back:
──────────────────────
[2026-03-27 11:00] — Claude Code
  Fixed items 1-4. Import button moved to main screen
  with text label. Drag-and-drop added. Tooltip added.
  Status: ✅ Fixed. Ready for retest.

OPUS retests...
```

---

## How to Run

### First-Time Setup (New Project)

Before running your first test flight, the framework needs to be installed into your project. Either Cowork or Claude Code can do this — the human director tells one of them to read the framework repo and set it up.

**Step 1: Direct an agent to read the framework**

Tell Cowork or Claude Code:
```
Read the Test Pilot Loop framework at [path/to/Test Pilot Loop/].
Read all files in FLIGHT_DECK/ and understand the full framework:
  - TEST_PILOT_LOOP.md (how the loop works)
  - AUTOPILOT.md (Claude Code's rules)
  - PILOT_HANDBOOK.md (testing personas and tiers)
  - FLIGHT_DEBRIEF.md (how bugs are reported)
  - TEST_FLIGHT_PROTOCOL.md (how testing is executed)
  - PREFLIGHT_CHECK.md (testing configuration)
Then set up this project to use it.
```

**Step 2: Agent creates project-specific Flight Deck**

The agent reads the framework, then creates a `FLIGHT_DECK/` directory in the project with:
- `AUTOPILOT.md` — copied from the framework (Claude Code's rules of engagement)
- `FLIGHT_PLAN.md` — created fresh (the shared communication file for this project)
- Persona agent files in `.claude/agents/` — copied from `PERSONAS/agents/`

The agent also updates the project's `CLAUDE.md` to reference AUTOPILOT.md:
```
## Test Pilot Loop
This project uses the Test Pilot Loop framework.
@FLIGHT_DECK/AUTOPILOT.md
```

**Step 3: Agent reads the project spec**

The agent MUST read the project's own spec files (`CLAUDE.md`, `PRD.md`) to understand what it's building/testing. This is mandatory for both Cowork and Claude Code — neither agent can do their job without knowing the project.

**Step 4: Both agents confirm setup**

Both agents write confirmation to `FLIGHT_PLAN.md`:
```
[timestamp] — [Agent Name]
Framework installed. Project spec read: [files read].
Ready for test pilot loop.
```

After this one-time setup, use the Session Start steps below for every test flight cycle.

### Session Start

Launch both apps at the same time on the same Mac. **Both agents must confirm patrol is active before any building starts.**

1. **Open Cowork with Opus**
2. **Open Claude Code** (or any AI coding agent)
3. **Create `FLIGHT_DECK/FLIGHT_PLAN.md`** if it doesn't exist — this is the shared communication file
4. **Start Cowork patrol (MANDATORY)** — give Cowork the standing patrol instruction (below). Cowork will NOT automatically patrol unless explicitly told to. Without this, Cowork sits idle and never picks up `READY_FOR_TEST` signals. After receiving the instruction, Cowork writes confirmation to FLIGHT_PLAN.md.
5. **Start Claude Code auto-patrol (MANDATORY)** — this is what makes the dual-patrol model work. Claude Code runs on-demand per message, not as a persistent process. Without an explicit loop, it will never check `FLIGHT_PLAN.md` on its own.

**Option 1: fswatch (RECOMMENDED — event-driven, zero waste)**

```
run_in_background "fswatch -1 FLIGHT_DECK/FLIGHT_PLAN.md && echo 'FLIGHT_PLAN changed'"
```

Claude Code sleeps until FLIGHT_PLAN.md is modified, then wakes up, reads it, and acts. No polling, no wasted tokens. Install if needed: `brew install fswatch` (macOS) or `apt install fswatch` (Linux).

When the watcher fires, Claude Code runs the patrol prompt:

```
"FLIGHT_PLAN.md changed — read the STATUS BLOCK and FEEDBACK QUEUE.
If STATUS is BUILD_REQUESTED or there is new feedback addressed to Claude Code:
  1. Read the feedback/task details
  2. Fix the bugs or build the feature
  3. Run tests, rebuild via xcodebuild or appropriate build tool
  4. Launch the app so Cowork can test it
  5. Write output to AGENT OUTPUT LOG
  6. Update STATUS → READY_FOR_TEST
If STATUS is ALL_BUGS_VERIFIED or PASS:
  1. Verify no stuck feedback, unanswered bugs, or pending tasks
  2. If all clear → write 'Patrol complete. Watcher stopped.' to AGENT OUTPUT LOG
  3. Stop watching — the cycle is done.
  4. If anything unresolved → write warning to FLIGHT_PLAN.md, keep watching.
If STATUS is READY_FOR_TEST → do nothing, waiting for Cowork.
If GLOBAL_STOP is FROZEN → stop all work, write frozen state to log.
Re-arm the watcher for the next change."
```

**Option 2: /loop cron (FALLBACK — works everywhere, wastes tokens)**

Use this if fswatch is not available:

```
/loop 10m "Patrol FLIGHT_DECK/FLIGHT_PLAN.md — read the STATUS BLOCK and FEEDBACK QUEUE.
If STATUS is BUILD_REQUESTED or there is new feedback addressed to Claude Code:
  1. Read the feedback/task details
  2. Fix the bugs or build the feature
  3. Run tests, rebuild via xcodebuild or appropriate build tool
  4. Launch the app so Cowork can test it
  5. Write output to AGENT OUTPUT LOG
  6. Update STATUS → READY_FOR_TEST
If STATUS is ALL_BUGS_VERIFIED or PASS:
  1. Verify no stuck feedback, unanswered bugs, or pending tasks
  2. If all clear → write 'Patrol complete. Loop stopped.' to AGENT OUTPUT LOG
  3. Cancel this /loop — the cycle is done. Do not keep polling.
  4. If anything unresolved → write warning to FLIGHT_PLAN.md, keep polling.
If STATUS is READY_FOR_TEST → do nothing, waiting for Cowork.
If GLOBAL_STOP is FROZEN → stop all work, write frozen state to log."
```

After starting patrol (either method), Claude Code should write confirmation to FLIGHT_PLAN.md:
```
[timestamp] — Claude Code
Auto-patrol configured: [fswatch watcher / /loop 10m] active.
Polling FLIGHT_PLAN.md for STATUS changes and feedback.
```
If this entry is missing from the AGENT OUTPUT LOG, the patrol is not running.

6. **Verify all pre-flight checks pass** — before ANY building or testing begins, confirm all of the following:

```
PRE-FLIGHT CHECKLIST (all must pass before loop starts):

☐ Claude Code has read AUTOPILOT.md (its rules of engagement)
☐ Claude Code has read CLAUDE.md / PRD.md (the project spec)
☐ Claude Code patrol is active (confirmation in AGENT OUTPUT LOG)
☐ Cowork has read all Flight Deck files (PILOT_HANDBOOK, FLIGHT_DEBRIEF, TEST_FLIGHT_PROTOCOL, PREFLIGHT_CHECK)
☐ Cowork patrol is active (confirmation in AGENT OUTPUT LOG)
☐ Insider tier has read full spec (CLAUDE.md + PRD.md) — confirmed by writing
    "Spec read: [file list]. [X] screens, [Y] total elements identified."
    to AGENT OUTPUT LOG before first test
```

**Do not start building until all confirmations are present.** The loop requires two patrolling agents — one building, one testing — and both must have full project knowledge. A single missing check breaks the feedback cycle.

### The Cowork Patrol Instruction (MANDATORY)

Give Cowork Opus this prompt at the start of your session. **This is not optional.** Without it, Cowork will never check for `READY_FOR_TEST` and Claude Code's builds will sit untested:

```
You are the test pilot and patrol monitor for this project.

YOUR TWO MODES:

PATROL MODE (default):
  Every 10 minutes, read FLIGHT_PLAN.md in the project folder.
  Look for "Ready for test" or "Ready for retest."
  If nothing new → go back to waiting. Keep it lightweight.

TEST MODE (triggered by "Ready for test"):
  When you see "Ready for test" or "Ready for retest":
  1. Read the details — what was built, where the app is running
  2. Find the app on screen (window title, URL, or Simulator)
  3. Run three knowledge-level tests (Cold, Guided, Insider)
  4. Write your structured feedback to FLIGHT_PLAN.md
  5. Update FLIGHT_PLAN.md with specific instructions in NEXT_ACTION_FOR_CLAUDE_CODE. Claude Code picks this up on its next patrol cycle (every 10 minutes).
  6. Return to PATROL MODE

AUTO-STOP:
  When STATUS reaches ALL_BUGS_VERIFIED or PASS:
  1. Verify no stuck feedback, unanswered bugs, or pending tasks
  2. If all clear → write "Patrol complete. Standing down." to AGENT OUTPUT LOG → stop polling
  3. If anything unresolved → write warning to FLIGHT_PLAN.md, keep polling

Keep cycling between patrol and test until the loop auto-stops or the human tells you to stop.
This session may run overnight — pace yourself.
```

After receiving this instruction, Cowork **must** write confirmation to FLIGHT_PLAN.md:
```
[timestamp] — Cowork Opus
Patrol configured: checking FLIGHT_PLAN.md every 10 minutes.
Standing by for READY_FOR_TEST signals.
```

**Why 10 minutes?** Designed for overnight operation. Nobody is waiting. For daytime use, tell Opus "check every 5 minutes" or simply say "check the file now."

### Patrol Lifecycle — Start, Stop, Restart

Patrols follow the lifecycle of a test flight cycle. They are not permanent.

| Phase | Trigger | What Happens |
|-------|---------|-------------|
| **START** | `FLIGHT_PLAN.md` created, or human says "start test pilot loop" | Both agents set up patrol and write confirmation to AGENT OUTPUT LOG. No building until both confirm. |
| **AUTO-STOP** | STATUS reaches `ALL_BUGS_VERIFIED` or `PASS` | Cowork writes TEST FLIGHT COMPLETE summary (bugs fixed table + UX feedback table + timing) → both agents verify nothing is stuck → write "Patrol complete" to log → stop. Claude Code stops fswatch (or cancels `/loop` cron). Cowork stops checking. |
| **RESTART** | Human says "test pilot loop" or "start next cycle," or sets new tasks + STATUS back to `BUILD_REQUESTED` | Both agents re-confirm patrol in writing before building resumes. |

**Before auto-stopping, both agents must verify:** no stuck feedback, no unanswered bugs, no pending tasks. If anything is unresolved, write a warning to FLIGHT_PLAN.md and keep polling.

**Why auto-stop matters:** Without it, both agents waste tokens polling an idle file indefinitely. A completed loop should clean up after itself.

### How the Code Agent Launches the App

After building, Claude Code launches the app so Opus can find it on screen:

| Project Type | Claude Code Runs |
|---|---|
| **macOS app** | `open -a "YourApp"` |
| **iOS app** | Build + install to Simulator via `xcodebuild` |
| **Web app** | `npm run dev` (keeps server running) |
| **Website** | `open https://your-url.com` |
| **Electron app** | `npm start` |

Then writes to the shared file:

```
[2026-03-27 10:30] — Claude Code
Task: Photo import feature
Status: ✅ Built. Tests pass.
App: Running — window titled "PhotoSortVision" on desktop
Ready for test pilot.
```

Opus picks this up on the next patrol check.

### How Cowork Launches the App for Testing

For Xcode projects, Cowork Opus can build and launch the app directly by clicking menu items (click-tier access is sufficient for Xcode menus). The typical flow:

1. Claude Code builds the feature and writes "Ready for test" to FLIGHT_PLAN.md
2. Opus sees this and finds the Xcode window on screen
3. **For macOS apps:** Opus clicks Product → Build, then Product → Run to launch the app
4. Alternatively, Claude Code can build via `xcodebuild` command line and place the `.app` in a known location (e.g., `~/Downloads/`). Opus then launches it with `open -a "AppName"` or by clicking the `.app` in Finder
5. Once the app is running, Opus can test it directly

This avoids any Terminal typing. The build happens either through Xcode menu clicks or through Claude Code's earlier command line invocation.

### Dual-Patrol in Detail

Both agents patrol `FLIGHT_PLAN.md` independently every 10 minutes. Neither agent needs to interact with the other directly — they communicate purely through the shared file using STATUS codes and NEXT_ACTION instructions.

```
Session Start: User opens Cowork (Opus) + Claude Code
                       ↓
┌──────────────────────────────────────────────────────┐
│                                                       │
│  Claude Code              Cowork Opus                 │
│  ────────────             ───────────                 │
│  Builds feature           PATROL MODE                 │
│  Runs tests               (checks FLIGHT_PLAN.md      │
│  Launches app             every 10 min)               │
│  Writes "Ready" ──────→ FLIGHT_PLAN.md ←── polls     │
│                       (status: READY_FOR_TEST)        │
│                                                       │
│                       Switches to TEST MODE           │
│                       Finds app on screen             │
│                       Tests 3 knowledge levels        │
│                       Writes feedback +               │
│                       NEXT_ACTION to file             │
│                                                       │
│                       ↓ (both agents poll)            │
│  Polls FLIGHT_PLAN.md ◄─ Claude Code picks up        │
│  Reads NEXT_ACTION        NEXT_ACTION on next          │
│  (every 10 min)           patrol (every 10 min)        │
│                                                       │
│  Fixes issues                                         │
│  Rebuilds + relaunches                                │
│  Writes "Ready for retest" ──→ Opus polls             │
│                                  Sees status change    │
│                                  TEST MODE again       │
│                                                       │
│  LOOP until Opus writes PASS                          │
└──────────────────────────────────────────────────────┘
```

**The Dual-Patrol architecture:** Both agents independently patrol the shared file. Cowork cannot type into Terminal (click-tier safety boundary). The Dual-Patrol architecture routes around this entirely — neither agent needs to interact with the other directly. Communication is purely through STATUS codes and plain-language instructions in `NEXT_ACTION_FOR_CLAUDE_CODE`.

### The Cycle

**Code agent's job:**
1. Build the feature
2. **Launch the app** (terminal command)
3. Write to `FLIGHT_PLAN.md`: "App running at [location]. Ready for test."
4. Wait for instructions by polling `FLIGHT_PLAN.md` (reads on own patrol cycle every 10 minutes)
5. When Opus writes feedback and NEXT_ACTION → read `FLIGHT_PLAN.md` for details
6. Fix issues, rebuild, relaunch app
7. Write: "Fixed. App relaunched. Ready for retest."
8. Repeat until Opus says PASS

**Cowork Opus's job:**
1. **Patrol** `FLIGHT_PLAN.md` every 10 minutes
2. When "Ready for test" appears → switch to test mode
3. **Find the app on screen** (window title, URL, or Simulator)
4. Run three knowledge-level tests
5. Write structured feedback to `FLIGHT_PLAN.md`
6. **Write instructions to FLIGHT_PLAN.md → Claude Code reads on next patrol:**
   Update `NEXT_ACTION_FOR_CLAUDE_CODE` with plain-language instructions like: *"Read FLIGHT_PLAN.md for test pilot feedback. Fix the issues 1-3, rebuild, relaunch, and update the file when ready for retest."*
7. Return to patrol mode
8. When "Ready for retest" appears → test again
9. Approve when all three levels pass

### The Three Test Runs

**Run 1: Cold User (knows nothing)**

Opus prompt to itself:
```
You have no prior knowledge of this project. You are constrained
to knowing only the app name. You don't know what it does, how
it works, or what any button means.

THE APP: [how to find it on screen — e.g., "The app window titled 
PhotoSortVision on the desktop" or "Open Safari and go to 
http://localhost:3000" or "The iOS Simulator on the right side 
of the screen"]

Your task: [what a user would try to do]

Use the app. Think out loud. Count every step. Note every 
moment of confusion. If you can't figure it out after 8 steps
of confusion, report abandonment.
```

**Run 2: Guided User (has manual only)**

Opus prompt to itself:
```
You have read the user manual (below). You have NOT seen the
PRD or any developer notes. Follow the manual's instructions
and note every place where the manual is wrong, incomplete,
or uses different words than the app.

[paste user manual / README]

Your task: [same task as Run 1]
```

**Run 3: Insider (has full PRD)**

Opus prompt to itself:
```
You have the full Product Requirements Document (below). You know
every intended feature and acceptance criterion. Test whether the
app delivers what was specified.

[paste PRD.md — product requirements and acceptance criteria]

Your task: [same task as Run 1]

For each requirement: does the app meet it? YES / PARTIALLY / NO.
Note any behaviors that differ from the spec.
```

---

## The Report Template

Opus fills this out after all three runs:

```
TEST PILOT REPORT — Three Knowledge Levels
═══════════════════════════════════════════
Feature: [what was built]
Task: [what was tested]

┌──────────────────────────────────────────────────────────┐
│                RUN 1         RUN 2         RUN 3         │
│                COLD          GUIDED        INSIDER       │
│                (nothing)     (manual)      (PRD)         │
├──────────────────────────────────────────────────────────┤
│ Completed:     [YES/NO]      [YES/NO]      [YES/NO]     │
│ Steps taken:   [count]       [count]       [count]       │
│ Wrong turns:   [count]       [count]       [count]       │
│ Confusion pts: [count]       [count]       [count]       │
│ Confidence:    [1-7]         [1-7]         [1-7]         │
└──────────────────────────────────────────────────────────┘

TIER GAP RATIO: [Cold steps] / [Insider steps] = [X]x
  ≤ 1.5x = ⭐ Excellent (intuitive, no manual needed)
  ≤ 2.0x = 🟢 Good (most users figure it out)
  ≤ 3.0x = 🟡 Acceptable (usable with documentation)
  > 3.0x = 🔴 Poor (too dependent on prior knowledge)

────────────────────────────────────────
COLD USER FINDINGS (most important)
────────────────────────────────────────
What confused me:
  • [moment — where, what, why]
  • [moment — where, what, why]

Where I would give up:
  • [exact point and reason]

What would have helped:
  • [specific suggestion]

────────────────────────────────────────
GUIDED USER FINDINGS (documentation quality)
────────────────────────────────────────
Manual accuracy:
  • [instruction that was wrong or outdated]
  • [feature not covered by manual]
  • [terminology mismatch: manual says X, app says Y]

────────────────────────────────────────
INSIDER FINDINGS (spec compliance)
────────────────────────────────────────
PRD compliance: [X]% of requirements met
  • [requirement not met — what's missing]
  • [behavior that differs from spec]
  • [PRD should be updated because — real usage insight]

────────────────────────────────────────
INTERFACE EVALUATION (across all runs)
────────────────────────────────────────
Layout clear:         YES / MOSTLY / NO
Labels make sense:    YES / MOSTLY / NO
Feedback after taps:  YES / SOMETIMES / NEVER
Know what to do next: YES / SOMETIMES / NO
Would use this app:   YES / MAYBE / NO

────────────────────────────────────────
BUGS FOUND
────────────────────────────────────────
  • [what happened vs what should have happened]

────────────────────────────────────────
FEEDBACK FOR CODE AGENT
────────────────────────────────────────
Fix these (priority order):
  1. [most important fix — what and why]
  2. [next fix]
  3. [next fix]

VERDICT: PASS ✅ / NEEDS WORK ⚠️ / FAIL ❌
```

---

## The Retest Trigger

After the code agent fixes issues, it writes to the shared file:

```
[timestamp] — Claude Code
Fixed items 1-3 from test pilot feedback.
Changes made:
  • Renamed button from "Execute" to "Start Import"
  • Added success message after import completes
  • Moved import button to main tab
Status: ✅ Fixed. Ready for retest.
```

Cowork Opus reads this, retests all three levels, and writes a new report. The loop continues until:

- **Cold user completes the task** (the most important gate)
- **Tier Gap Ratio ≤ 2.0x** (acceptable intuitiveness)
- **No bugs found**
- **PRD compliance ≥ 90%**

When all conditions are met: **PASS. Ship it.**

---

## When to Simplify

You don't always need all three levels:

| Situation | Use |
|-----------|-----|
| Quick check after small fix | **Cold only** — can the user still figure it out? |
| Documentation update | **Guided only** — does the manual still match? |
| Spec compliance check | **Insider only** — does it meet requirements? |
| Pre-release milestone | **All three** — full validation |

---

## Long Sessions and Context Compaction

Exhaustive test flights (especially Insider tier audits) can exceed the AI's context window, triggering compaction — a lossy summary of older conversation. When this happens, granular observations are lost from working memory.

**The protocol: write findings to disk after every screen, not at the end.** Create a `TEST_FLIGHT_REPORT_WIP.md` file before testing starts, append findings incrementally, and maintain a screen-by-screen checklist. After compaction, read the file to resume exactly where you left off.

See [TEST_FLIGHT_PROTOCOL.md — Long Session Resilience](FLIGHT_DECK/TEST_FLIGHT_PROTOCOL.md#long-session-resilience-context-compaction-protocol) for the full protocol.

---

## Why This Works

**One session. No switching.** Cowork Opus stays live the whole time. It knows the project context, remembers previous test results, and tracks improvement across retests.

**The code agent drives the loop.** It signals "ready for test" and "ready for retest." Opus responds. The shared file is the communication channel. No manual coordination needed.

**The Tier Gap Ratio is a real metric.** If Cold takes 12 steps and Insider takes 4, that's 3.0x — your app depends heavily on prior knowledge. When it drops to 1.5x, your app is genuinely intuitive. You can track this across builds.

**You're measuring what matters.** Not "does the code work?" but "can a person who knows nothing about this app figure it out?"

---

## Scaling Up

When you're ready for more:

- **Quick Flight** → Want a faster check with zero setup? Use the 3-Model Quick Test where Opus, Sonnet, and Haiku each test your app independently. See `QUICK_FLIGHT.md`.
- **Test Flight Protocol** → Want the detailed screen-by-screen execution protocol? How to inspect, inventory, predict, act, and evaluate on every screen — including CLI-Anything hybrid testing and debug overlays. See `FLIGHT_DECK/TEST_FLIGHT_PROTOCOL.md`.
- **Add personas** → Instead of one Opus testing as "cold user," deploy persona subagents (Alex the power user, Pat the struggling user, Sam the accessibility user) for deeper behavioral testing. See `PERSONAS/`.
- **Add archetypes** → Layer Creator/Admin/Consumer behaviors on top of knowledge levels based on which feature is being tested.
- **Add orchestration** → Risk tiers, kill switch, Opus patrol, Ralph Wiggum build loop, confidence scores. See `FLIGHT_DECK/`.

All layers are compatible. Start here. Add rigor as your project grows.

---

*Test Pilot Loop v0.1.0*
*One brain. Three knowledge levels. Continuous loop.*
*Part of the Test Pilot Loop Framework by Huan Su.*

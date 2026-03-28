# Test Pilot Loop — PhotoSortVision
### Tailored from the universal TEST_PILOT_LOOP.md for this project.

---

## Project Details

| Field | Value |
|-------|-------|
| **App** | PhotoSortVision |
| **Platform** | macOS (SwiftUI), Ventura 13.0+ |
| **Window Title** | "PhotoSortVision" |
| **Test Data** | 470 dental photos in `TEST_INPUT/` folder (DSC_1029–DSC_1498.JPG) |
| **Primary Task** | Import photos → Scan for name cards → Review patient assignments → Sort into folders → Done |
| **Communication File** | `FLIGHT_DECK/FLIGHT_PLAN.md` |

---

## Pass Criteria

| Metric | Threshold | Source |
|--------|-----------|--------|
| **Cold User completes primary task** | YES (mandatory gate) | TEST_PILOT_LOOP.md |
| **Tier Gap Ratio** | ≤ 2.0x | TEST_PILOT_LOOP.md |
| **Dimension Score** | ≥ 24/30 (80%) | PREFLIGHT_CHECK.md |
| **No P0/P1 bugs** | Zero critical/major blockers | FLIGHT_DEBRIEF.md |
| **PRD compliance** | ≥ 90% | TEST_PILOT_LOOP.md |

---

## Three Test Runs

### Run 1: Cold User (knows NOTHING)

```
You have no prior knowledge of this project. You know only the app name:
"PhotoSortVision."

You don't know what it does, who it's for, or what any button means.
You do NOT know it's a dental app or that it sorts photos by patient name.

THE APP: The app window titled "PhotoSortVision" on the macOS desktop.

Your task: Figure out what this app does and complete its primary workflow
from start to finish. Import some photos, process them, and get them
organized.

Use the app. Think out loud. Count every step. Note every moment of
confusion. If you can't figure it out after 8 steps of confusion,
report abandonment.
```

### Run 2: Guided User (has README only)

```
You have read the project README (below). You have NOT seen the full
technical spec or any developer notes. Follow the README's description
and note every place where the README is wrong, incomplete, or uses
different words than the app.

[README content from examples/PhotoSortVision/README.md]

Your task: Complete the primary workflow — Import → Scan → Review → Sort
→ Done. Evaluate whether the README prepared you adequately.
```

### Run 3: Insider (has full CLAUDE.md spec)

```
You have the full technical specification (CLAUDE.md — 538 lines covering
the complete PSV Engine, all 5 wizard steps, settings, folder matching,
name card detection formats A–E, filename builders, and error handling).

You know every intended feature and acceptance criterion. Test whether
the app delivers what was specified.

[Full CLAUDE.md spec from /PhotoSortVision/CLAUDE.md]

Your task: Complete the primary workflow AND systematically verify:
- All 5 stepper steps match spec
- Name card detection accuracy
- Patient list features (inline edit, /description, exclude)
- Context menu items (right-click)
- Keyboard shortcuts (Cmd+Z, Cmd+Shift+Z, X, arrow keys)
- Sort with duplicate detection
- Post-sort cleanup prompt
- Settings accessibility

For each requirement: does the app meet it? YES / PARTIALLY / NO.
Note any behaviors that differ from the spec.
```

---

## Report Template

After all three runs, fill in:

```
TEST PILOT REPORT — Three Knowledge Levels
═══════════════════════════════════════════
Feature: PhotoSortVision full workflow
Task: Import → Scan → Review → Sort → Done (470 dental photos)

┌──────────────────────────────────────────────────────────┐
│                RUN 1         RUN 2         RUN 3         │
│                COLD          GUIDED        INSIDER       │
│                (nothing)     (README)      (CLAUDE.md)   │
├──────────────────────────────────────────────────────────┤
│ Completed:     [YES/NO]      [YES/NO]      [YES/NO]     │
│ Steps taken:   [count]       [count]       [count]       │
│ Wrong turns:   [count]       [count]       [count]       │
│ Confusion pts: [count]       [count]       [count]       │
│ Bugs found:    [count]       [count]       [count]       │
└──────────────────────────────────────────────────────────┘

TIER GAP RATIO: [Cold steps] / [Insider steps] = [X]x
  ≤ 1.5x = ⭐ Excellent
  ≤ 2.0x = 🟢 Good
  ≤ 3.0x = 🟡 Acceptable
  > 3.0x = 🔴 Poor
```

---

## Execution Checklist

```
□ Pre-Flight: Visible Window Check
  □ PSV app is running (not just built)
  □ PSV window is visible, not minimized
  □ PSV window is large enough to interact with
  □ App is at Step ① Import (use "Start Over" if needed)

□ Phase 0: Ground Check
  □ App responds to clicks
  □ No error dialogs on launch
  □ Stepper bar visible and functional

□ Run 1: Cold User
  □ Reset app to Step ① Import
  □ Forget all project knowledge — app name only
  □ Complete workflow, counting every step
  □ Record all confusion points and wrong turns
  □ Note abandonment risk points

□ Run 2: Guided User
  □ Reset app to Step ① Import
  □ Read README only — no spec knowledge
  □ Complete workflow following README guidance
  □ Note every README inaccuracy or gap

□ Run 3: Insider
  □ Reset app to Step ① Import
  □ Read full CLAUDE.md spec
  □ Complete workflow + systematic spec verification
  □ Test edge cases, keyboard shortcuts, context menus
  □ Record PRD compliance percentage

□ Report
  □ Fill in report template
  □ Calculate Tier Gap Ratio
  □ List all bugs by severity
  □ Determine verdict: PASS / NEEDS WORK / FAIL
```

---

## Reset Between Runs

Between each tier run, click **"Start Over"** at the bottom of the app
to return to Step ① Import. Each tier gets a fresh experience.

The test data folder (`TEST_INPUT/`) with 470 photos should be re-imported
for each run to ensure consistent starting conditions.

---

*Tailored from [TEST_PILOT_LOOP.md](../../TEST_PILOT_LOOP.md) — Test Pilot Loop Framework by Huan Su.*

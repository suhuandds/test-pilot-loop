# PhotoSortVision — Worked Example

This is a complete worked example of the **Test Pilot Loop** framework applied to a real project.

PhotoSortVision is a macOS app that imports clinical dental photos, detects patient name cards via on-device vision, and sorts originals into patient-specific folders. This example uses real test data from an actual test flight — not fabricated artifacts.

## What This Example Shows

The full Test Pilot Loop cycle, start to finish:

```
PRD → Task Queue → Build → Test Flight (Cold + Insider) → Debrief → Bug Reports → Fix → Retest
```

Every file in this example is filled in with real data from an actual test session showing exactly what each artifact looks like during a live project.

## File Map

| File | What It Shows |
|------|--------------|
| `PRD.md` | Product requirements — what gets built |
| `CLAUDE.md` | Project root config — imports Autopilot |
| `FLIGHT_DECK/FLIGHT_PLAN.md` | The full communication hub — task queue, output logs, feedback queue |
| `FLIGHT_DECK/TEST_FLIGHT_REPORT.md` | Complete test flight debrief — Cold + Insider findings, dimension scores |
| `FLIGHT_DECK/PREFLIGHT_CHECK.md` | Testing depth settings for this project |
| `LOOP_LOG.md` | **Full loop cycle log** — test, report, fix, retest with timing data |
| `PERSONAS/jordan.md` | Everyday User persona used in testing |

## How to Read This Example

1. **Start with `LOOP_LOG.md`** — the end-to-end story of one complete loop cycle with timestamps
2. Read `PRD.md` — understand what the app should do
3. Read `FLIGHT_DECK/FLIGHT_PLAN.md` from top to bottom — the living document:
   - Tasks assigned to Claude Code with risk levels
   - Build output with confidence scores and self-assessment
   - Cold User test findings (4 bugs)
   - Insider test findings (13 bugs, grounded in the real 17-chapter spec)
   - Feedback queue directing the fix cycle
4. Read `FLIGHT_DECK/TEST_FLIGHT_REPORT.md` — dimension scores and Tier Gap Analysis
5. Notice the difference between what Cold found vs. Insider found — only 6.3% overlap

## Key Takeaways

**Cold User (no spec knowledge):** Found 4 bugs. Score: 24/30 (80%). All were UX confusion issues that an expert would overlook — ambiguous labels, placeholder text leaking through, layout truncation.

**Insider (full 17-chapter spec):** Found 13 bugs. Score: 20/30 (67%). All required domain knowledge — a regex that silently failed on OCR-truncated parentheticals, missing context menu actions from CH09 R4, undo stack not connected to keyboard shortcuts.

**The loop caught a bug that unit tests couldn't:** The most impactful Insider finding (BUG-I003) was a regex that passed all unit tests but failed on real-world OCR data. The name card had "(Lou" without a closing parenthesis because OCR truncated it. The regex `\([^)]*\)` requires a closing `)`, so it silently did nothing — creating the wrong folder name.

**Combined:** 16 unique bugs from 2 tiers with only 6.3% overlap, proving Cold and Insider test genuinely different dimensions of quality.

**The fix cycle** was surgical — 2 bugs fixed in 3 files, ~32 lines changed, completing the loop in ~50 minutes total.

---

*Part of the [Test Pilot Loop](https://github.com/suhuandds/test-pilot-loop) framework by Huan Su.*

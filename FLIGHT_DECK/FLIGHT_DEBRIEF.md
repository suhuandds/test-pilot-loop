# 🔄 Flight Debrief — AI Tester → Agent Feedback Protocol v0.1.0
## How the AI Test Pilot Reports Bugs, Rates Severity, and Escalates to Humans
**Companion document to PILOT_HANDBOOK.md**

---

## 🎯 Purpose

The AI Test Pilot finds bugs. But finding bugs isn't enough.

A professional QA tester:
1. **Classifies** the bug (how bad is it?)
2. **Prioritizes** the bug (how urgent is it to fix?)
3. **Communicates** the bug clearly to the developer (exact reproduction steps)
4. **Decides** whether a human needs to approve the fix or the AI agent can handle it

This document defines how the AI Test Pilot does all four — turning raw test findings into actionable, prioritized feedback that build agents (Claude Code, etc.) can immediately act on.

---

## 📋 WHAT REAL HUMAN TESTERS DO (And What the AI Must Match)

Based on industry-standard QA practices (ISTQB, OWASP, SDLC):

### A Professional QA Tester's Full Workflow

**1. Preparation (Before Testing)**
- Read the requirements / PRD / user story
- Understand what "correct" looks like
- Identify the target users and their expectations
- Review what changed in the latest build
- Prepare test cases covering: happy path, edge cases, regression

**2. Exploratory Testing (The Core)**
- Use the app like each target persona would
- Follow the obvious path first (happy path)
- Then deliberately try to break it:
  - Wrong inputs, empty fields, very long text
  - Rapid clicks, back button, rotation
  - Interruptions (backgrounding, network loss)
- Check **transitions** — loading states, animations, error recovery
- Check **consistency** — does this screen match the rest of the app?
- Check **first impressions** — what would a new user think?

**3. Bug Documentation (The Critical Skill)**
For every issue found, a professional tester writes:
- **Title:** One-line summary (what's wrong, where)
- **Severity:** How bad is it technically?
- **Priority:** How urgently should it be fixed?
- **Steps to reproduce:** Exact sequence anyone can follow
- **Expected result:** What should have happened
- **Actual result:** What actually happened
- **Environment:** Device, OS, screen size, app version
- **Evidence:** Screenshot or screen recording
- **Notes:** Workarounds, related issues, suggestions

**4. Communication (What Makes Great Testers Great)**
- Bugs are written for the **developer**, not the tester
- No ambiguity — the developer can reproduce it in one attempt
- Severity and priority are separate (see below)
- Bugs are grouped logically (all login issues together, all UI issues together)
- The most critical bugs are communicated first
- Constructive suggestions are included when the fix is obvious

**5. Regression Checking (After Fixes)**
- Verify the specific bug is fixed
- Check that the fix didn't break something else
- Re-run the full test cycle if changes were significant

---

## 🚨 SEVERITY vs. PRIORITY — The AI Must Understand Both

Professional QA separates two concepts that most people confuse:

### Severity = How Bad Is It? (Technical Impact)
> Set by the TESTER based on technical impact

| Level | Name | Definition | Example |
|-------|------|------------|---------|
| S1 | 🔴 Blocker | App crashes, data loss, feature completely unusable. No workaround exists. | App crashes when opening a project. All user data deleted. |
| S2 | 🟠 Critical | Major feature broken. A workaround exists but it's painful or unreliable. | Save button doesn't work, but you can export and re-import. |
| S3 | 🟡 Major | Feature works incorrectly or with significant friction. Core workflow disrupted. | Import takes 30 seconds when it should take 3. Results are truncated. |
| S4 | 🔵 Minor | Small issue. Feature works but has rough edges. Doesn't block the user. | Misaligned button. Tooltip shows wrong text. Date format inconsistent. |
| S5 | ⚪ Cosmetic | Visual-only issue with zero functional impact. | Font slightly different from other screens. Extra pixel of padding. |

### Priority = How Urgent to Fix? (Business Impact)
> Set by the BRAIN (Cowork Opus) or the HUMAN (the human director) based on business context

| Level | Name | Definition | Action |
|-------|------|------------|--------|
| P1 | 🔥 Immediate | Fix right now. Block all other work until resolved. | Agent drops everything and fixes this. |
| P2 | ⚡ High | Fix before any new features. Must be in next build. | Agent finishes current task, then fixes this. |
| P3 | 📋 Medium | Fix in current sprint/cycle. Important but not blocking. | Agent addresses after completing current queue. |
| P4 | 📝 Low | Fix when convenient. Schedule for future release. | Logged. Addressed when there's spare capacity. |
| P5 | 📌 Backlog | Nice to have. Record for future reference. | Logged to Obsidian vault technical-debt.md. |

### Why They're Separate (The Key Insight)

**High Severity + Low Priority:**
App crashes when you rotate the device upside-down. Technically severe (S1 Blocker), but almost no user will do this. Priority: P4 Low.

**Low Severity + High Priority:**
The company logo is wrong on the launch screen. Technically trivial (S5 Cosmetic), but if you're demo-ing to investors tomorrow, Priority: P1 Immediate.

**The AI Test Pilot sets Severity. Cowork Opus (or the human director) sets Priority.**

---

## 🤖 AI TESTER → BUILD AGENT: Bug Report Format

When the AI Test Pilot finds an issue, it writes it in this exact format so the build agent (Claude Code / Second Builder) can immediately understand and fix it:

```
┌─ BUG REPORT ─────────────────────────────────────────────┐
│                                                           │
│  ID:         BUG-[TASK_ID]-[NUMBER]                      │
│  Title:      [One line: What's wrong + Where]            │
│  Severity:   [S1-S5] [🔴🟠🟡🔵⚪]                       │
│  Priority:   [P1-P5] [set by Opus/Human — may be blank]  │
│  Persona:    [Which persona discovered this]             │
│  Dimension:  [Which of the 6 test dimensions]            │
│                                                           │
│  STEPS TO REPRODUCE:                                     │
│  1. [Exact step]                                         │
│  2. [Exact step]                                         │
│  3. [Exact step]                                         │
│                                                           │
│  EXPECTED: [What should happen]                          │
│  ACTUAL:   [What actually happens]                       │
│                                                           │
│  SCREENSHOT: [path/to/file.png]                          │
│  ENVIRONMENT: [iOS Simulator / macOS / etc.]             │
│                                                           │
│  ROOT CAUSE:    [Category — e.g., event handler, layout, │
│                  state mgmt, missing validation, copy]    │
│  SUGGESTED FIX: [If obvious — optional]                  │
│  RELATED BUGS:  [BUG-T001-002, if related]               │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

> **Bug clustering:** Group findings by ROOT CAUSE before passing to the build agent. Fixing one root cause often resolves multiple bugs — 20 findings may reduce to 3–5 fixes.

### Example Bug Report:
```
┌─ BUG REPORT ─────────────────────────────────────────────┐
│                                                           │
│  ID:         BUG-T004-001                                │
│  Title:      "Import" button unresponsive on first        │
│              tap — requires double-tap                    │
│  Severity:   S3 🟡 Major                                 │
│  Priority:   P2 ⚡ High (set by Opus — core workflow)    │
│  Persona:    Everyday User (Jordan)                      │
│  Dimension:  ⚙️ Functional Correctness                   │
│                                                           │
│  STEPS TO REPRODUCE:                                     │
│  1. Open YourApp                                         │
│  2. Navigate to Import tab                               │
│  3. Tap "Import" button once                             │
│                                                           │
│  EXPECTED: Import begins immediately                     │
│  ACTUAL:   Nothing happens. Second tap starts import.    │
│                                                           │
│  SCREENSHOT: AUDIT_SCREENSHOTS/BUG-T004-001.png         │
│  ENVIRONMENT: iPhone 15 Pro Simulator, iOS 18.2          │
│                                                           │
│  ROOT CAUSE:    Event handler                            │
│  SUGGESTED FIX: Likely an event handler conflict         │
│                 with the component initialization.       │
│  RELATED BUGS:  None                                     │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

---

## 🚦 ESCALATION TO HUMAN — When Cowork Asks the Human

Not everything should be decided by AI. Some bugs need a human call.

### The Escalation Matrix (Universal)

| Condition | Who Decides | Why |
|-----------|-------------|-----|
| S1 Blocker — app crashes or data loss | **Human must approve fix approach** | Risk of making it worse. Human judgment needed on safest path. |
| S2 Critical on a core feature | **Human must approve** | Core features define the product. AI shouldn't guess on critical fixes. |
| Design/UX disagreement (AI suggests redesign) | **Human decides** | Design is subjective. The AI can recommend, but the human chooses the direction. |
| Priority conflict (S1 but P4, or S5 but P1) | **Human arbitrates** | When severity and priority diverge, business context matters. Only the human knows the business context. |
| 3rd retest failure on same bug | **Human must intervene** | If the AI agent can't fix it in 3 tries, the approach is wrong. Human needs to redirect. |
| Feature works but "feels wrong" to multiple personas | **Human reviews** | Sometimes the implementation matches the PRD but the PRD was wrong. Human decides if the requirement needs changing. |
| Accessibility S1/S2 found | **Cowork handles, but human is notified** | Accessibility is non-negotiable. Cowork can fix, but human should know. |

### Domain-Specific Escalation Rules (Add Your Own)

Different industries have different escalation needs. Add rows to the matrix above based on your domain:

**Healthcare software:**
| Any issue affecting user health data | **Human must approve** | HIPAA/compliance implications. Even minor data display bugs could have regulatory impact. |
| Accuracy of health-related outputs | **Human must approve** | AI should not make judgment calls on medical correctness. |

**Financial software:**
| Any issue affecting transaction amounts | **Human must approve** | Financial accuracy is non-negotiable. |
| Regulatory display requirements | **Human must approve** | Compliance formatting must be exact. |

**E-commerce:**
| Checkout flow changes | **Human must approve** | Direct revenue impact. |
| Pricing display changes | **Human must approve** | Legal and business implications. |

### What Cowork CAN Handle Without Human Approval

| Condition | Cowork Action |
|-----------|---------------|
| S3-S5 bugs with clear reproduction steps | Writes bug report → sends to build agent → agent fixes → retest |
| All ⚪ Cosmetic issues | Logs them → batches for future fix → does NOT interrupt build flow |
| S3 Major with obvious fix | Sends to agent with suggested fix → retests → approves |
| Edge case failures (empty input, long text) | Sends to agent → agent adds input validation → retest |
| Visual alignment issues | Sends with screenshot + exact pixel/spacing fix needed |
| Missing labels or confusing copy | Suggests new copy → sends to agent → retests |

### How Cowork Escalates to Human

When escalation is needed, Cowork writes to the orchestration file:

```
[YYYY-MM-DD HH:MM] — ⚠️ HUMAN APPROVAL REQUIRED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Task: T004 — Photo Import Feature
Bug: BUG-T004-003 (S1 Blocker — import crashes with specific file type)
Reason for escalation: Core feature + data integrity risk

What happened:
  [concise description]

Options:
  A) [Approach 1 — e.g., disable the problematic file type, ship without it]
  B) [Approach 2 — e.g., refactor the parser, 2-day delay]
  C) [Approach 3 — e.g., add error handling, import continues but may skip the file]

Opus recommendation: [which option and why]

Awaiting human decision: A / B / C / Other

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

The human responds by editing the file or telling Cowork directly. Cowork then routes the decision to the build agent with specific instructions.

---

## 📊 BUG REPORT BATCHING — How Feedback Flows to Agents

The AI Test Pilot doesn't send bugs one at a time. It batches them intelligently:

### After a Standard Test (🟡 Medium Risk):
```
[YYYY-MM-DD HH:MM] — AI Test Pilot → Claude Code
Re: Task T004 — Import Feature Implementation
Test Result: ⚠️ CONDITIONAL PASS (22/30)

BUGS TO FIX (ordered by severity):

  1. BUG-T004-001 [S3 🟡 Major] — Import requires double-tap
     → FIX FIRST. Likely event handler conflict.

  2. BUG-T004-002 [S4 🔵 Minor] — Import progress bar doesn't animate
     → Fix after #1. CSS animation not triggering.

  3. BUG-T004-003 [S5 ⚪ Cosmetic] — "Importing..." text misaligned by 2px
     → Fix if time permits. Low priority.

HUMAN ESCALATION: None required for these bugs.

UX RECOMMENDATIONS (not bugs — suggestions for improvement):
  • Jordan (Everyday User) hesitated at the import screen — consider adding
    a brief instruction text: "Drag files here or click to browse"
  • Pat (Struggling User) couldn't find the import button — it blends into
    the background. Consider higher contrast or a pulsing animation.

After fixing bugs #1 and #2, set status → ⏳ Awaiting Review
I will retest only dimensions ⚙️ Functional and 🧭 Usability.
```

### After a Full Test (🔴 High Risk):
```
[YYYY-MM-DD HH:MM] — AI Test Pilot → Second Builder
Re: Task T002 — Analytics Dashboard Module
Test Result: ❌ FAIL (14/30)

⚠️ HUMAN ESCALATION: YES — See BUG-T002-001 (S1 Blocker, data accuracy risk)

BUGS TO FIX (ordered by severity):

  1. BUG-T002-001 [S1 🔴 Blocker] — Percentage calculation produces NaN
     for certain date ranges
     → ⚠️ WAITING FOR HUMAN DIRECTOR'S APPROVAL on fix approach
     → DO NOT ATTEMPT TO FIX until the human responds

  2. BUG-T002-002 [S3 🟡 Major] — Comparison overlay doesn't align
     with reference data
     → Fix after #1 is resolved. May be related to same calculation bug.

  3. BUG-T002-003 [S3 🟡 Major] — Export PDF shows raw floating point
     numbers instead of formatted values
     → Fix independently of #1. String formatting issue.

  4. BUG-T002-004 [S4 🔵 Minor] — Color legend overlaps the chart area
     on smaller screens
     → Fix after #2 and #3. Layout constraint issue.

PERSONA NOTES:
  • Alex (Power User) would not trust numbers that show more than
    2 decimal places. Round to sensible precision for display.
  • Pat (Struggling User) could not understand what "variance" means.
    Consider adding: "How much this differs from the expected value"

DO NOT proceed until BUG-T002-001 is resolved and retested.
```

---

## 🎨 UX FEEDBACK PIPELINE — Design & Layout Improvements

Bugs are broken functions. UX feedback is design commentary — layout, visual hierarchy, wording, font size, button placement, color choices, flow improvements. Both come from testing, but they follow different pipelines.

### Three Types of UX Feedback

| Type | Source Tier | Example | Tag |
|------|------------|---------|-----|
| **First impression** | Cold | "I had no idea what this app does from the landing screen" | `UX FEEDBACK: first-impression` |
| **Documentation gap** | Guided | "Manual says click 'Import' but the button is labeled 'Add Photos'" | `UX FEEDBACK: docs-mismatch` |
| **Design/layout** | Any tier, any persona | "Font too small for office monitor," "layout feels cramped," "expected button on the right" | `UX FEEDBACK: design` |

### How UX Feedback Flows

```
TESTERS produce UX FEEDBACK (tagged, not filed as bugs)
        ↓
COWORK OPUS collects and groups by screen/feature
        ↓
Cowork evaluates: does this feedback align with PRD intent?
  • If yes and low-effort → Cowork adds to TASK QUEUE as UX task
  • If subjective/debatable → Cowork presents to HUMAN DIRECTOR with recommendation
  • If contradicts PRD → Cowork flags as PRD UPDATE RECOMMENDATION
        ↓
HUMAN DIRECTOR decides: approve / reject / defer
        ↓
Approved UX tasks go to TASK QUEUE with risk level ⚪ or 🟢
        ↓
CLAUDE CODE implements → Cowork retests the affected screen
```

### UX Feedback in FLIGHT_PLAN.md

UX feedback goes in a separate section from bugs — do NOT mix them in the FEEDBACK QUEUE:

```
## UX FEEDBACK LOG
[YYYY-MM-DD HH:MM] — [Tier/Persona]
Screen: [which screen]
Tag: UX FEEDBACK: [first-impression / docs-mismatch / design]
Observation: [what they noticed]
Suggestion: [what they think would be better]
Cowork assessment: [agree / disagree / needs human input]
Status: [pending / approved / rejected / deferred / implemented]
```

### When to Collect UX Feedback

UX feedback is collected alongside bug testing but processed separately:

- **During active bug-fix loop:** collect UX feedback but do NOT act on it. Bugs first. Log it for later.
- **After all bugs verified (ALL_BUGS_VERIFIED):** review accumulated UX feedback. Present batch to human director.
- **Between cycles:** UX improvements become tasks in the next cycle's TASK QUEUE.

This prevents UX polish from blocking bug fixes, while ensuring design feedback never gets lost.

### Persona-Specific UX Value

Each persona/tier contributes different UX insights:

| Source | What they notice | Why it matters |
|--------|-----------------|----------------|
| **Cold tier** | Confusing labels, unclear purpose, bad first impression | Your app's discoverability and learnability |
| **Guided tier** | Manual doesn't match UI, terminology mismatches, missing instructions | Your documentation quality |
| **Pat (Struggling)** | Scary error messages, too-small buttons, cognitive overload | Accessibility and low-tech-literacy users |
| **Alex (Power)** | Missing shortcuts, slow workflows, lack of bulk operations | Power user efficiency |
| **Jordan (Everyday)** | Confusing labels, missing feedback after actions, broken happy path | Mainstream usability |
| **Sam (Accessibility)** | Low contrast, missing focus indicators, keyboard traps | WCAG compliance |

**Not all UX feedback is equal.** If 3 out of 4 personas flag the same screen as confusing, that's strong signal. If only Alex (power user) wants a keyboard shortcut, that's a nice-to-have. Cowork weighs the feedback before presenting to the human.

---

## 📈 TRACKING OVER TIME

Every test session produces dimension scores. Over multiple sessions, patterns emerge:

```
PROJECT: YourApp
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

              Build 1   Build 2   Build 3   Build 4   Trend
⚙️ Functional:   3        4        4        5        📈
🧭 Usability:    2        2        3        4        📈
🎨 Visual:       3        3        4        4        →
♿ Accessible:   1        2        3        3        📈
🗺️ Journey:      2        3        4        4        📈
🔨 Edge Cases:   1        2        2        3        📈
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
TOTAL:          12       16       20       23        📈

Bug counts:     S1:2     S1:0     S1:0     S1:0
                S2:3     S2:1     S2:0     S2:0
                S3:5     S3:4     S3:2     S3:1
                S4:4     S4:3     S4:3     S4:2
                S5:2     S5:2     S5:1     S5:1

Human escalations: 2      1        0        0        📈
```

This goes into your project's persistent memory (e.g., an Obsidian vault, a `current-state.md` file, or any durable notes system). Cowork Opus reads it at session start to know: "Accessibility was our weakest area last time. Focus testing on that."

---

## 🔑 SUMMARY — The Complete Feedback Loop

```
BUILD AGENT completes task
        ↓
AI TEST PILOT receives output + launches app
        ↓
Tests as configured tiers/personas across dimensions
        ↓
Produces TWO streams of findings:
        ↓
┌─────────────────────────────────────────────────────────┐
│  STREAM 1: BUGS (broken functions)                      │
│                                                         │
│  ┌─ S1/S2 domain-critical? → ESCALATE TO HUMAN         │
│  │   Human decides approach → Cowork routes to agent    │
│  │                                                      │
│  ├─ S1/S2 non-critical? → COWORK OPUS decides           │
│  │   Opus may still consult human if uncertain           │
│  │                                                      │
│  ├─ S3-S4? → SEND TO BUILD AGENT                        │
│  │   Agent fixes → retest                               │
│  │                                                      │
│  └─ S5 Cosmetic? → LOG FOR LATER                        │
│                                                         │
│  Agent fixes → retest → PASS or FAIL (max 3 cycles)    │
│  After 3 failures → ESCALATE TO HUMAN                   │
└─────────────────────────────────────────────────────────┘
        ↓
┌─────────────────────────────────────────────────────────┐
│  STREAM 2: UX FEEDBACK (design/layout/wording)          │
│                                                         │
│  Collected during testing, processed AFTER bugs fixed   │
│                                                         │
│  Cowork groups by screen → evaluates → presents to      │
│  human with recommendation                              │
│        ↓                                                │
│  Human approves/rejects/defers                          │
│        ↓                                                │
│  Approved items → TASK QUEUE in next cycle               │
│  Claude Code implements → Cowork retests screen          │
└─────────────────────────────────────────────────────────┘
```

---

## ✅ TEST FLIGHT COMPLETE — Final Summary (MANDATORY)

When all bugs are verified fixed (STATUS = ALL_BUGS_VERIFIED or PASS), Cowork **must** write a final summary to FLIGHT_PLAN.md before closing the loop. This is the handoff document — the human director reads this to know exactly what happened, what changed, and what UX feedback is pending.

### Template

```
## TEST FLIGHT COMPLETE
[YYYY-MM-DD HH:MM]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

### BUGS FIXED — SUMMARY
Total bugs found: [count]
Total bugs fixed: [count]
Iterations to fix all: [count]

| Bug ID | Severity | Screen | What Was Broken | What Was Fixed | Files Changed |
|--------|----------|--------|----------------|---------------|---------------|
| BUG-001 | S3 Major | [screen] | [broken behavior] | [fix description] | [files] |
| BUG-002 | S4 Minor | [screen] | [broken behavior] | [fix description] | [files] |
| ... | ... | ... | ... | ... | ... |

### UX FEEDBACK COLLECTED
Total UX observations: [count]

| # | Source | Screen | Type | Observation | Cowork Assessment |
|---|--------|--------|------|-------------|-------------------|
| 1 | Cold tier | [screen] | first-impression | [what they noticed] | [agree/disagree/needs human] |
| 2 | Pat (Struggling) | [screen] | design | [what they noticed] | [agree/disagree/needs human] |
| 3 | Guided tier | [screen] | docs-mismatch | [what they noticed] | [agree/disagree/needs human] |
| ... | ... | ... | ... | ... | ... |

Recommended for next cycle: [count] items
Deferred: [count] items
Rejected by Cowork: [count] items (with reasons)

### TESTING CONFIGURATION USED
Tiers: [which tiers were deployed]
Personas: [which personas were deployed]
Dimensions: [which dimensions were evaluated]

### TIMING
Total test flight duration: [time]
Time per iteration: [iteration 1: Xm, iteration 2: Xm, ...]

### VERDICT
[PASS ✅ / CONDITIONAL PASS ⚠️]
All bugs verified fixed. [X] UX feedback items pending human review.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Why This Matters

Without this summary:
- The human has to read through the entire FLIGHT_PLAN.md history to understand what happened
- Bug fixes are scattered across multiple AGENT OUTPUT LOG entries
- UX feedback is buried in test reports
- No one remembers what was tested with which configuration

With this summary:
- Human sees one table of everything that was fixed
- UX feedback is collected in one place, ready for human decision
- Testing config is recorded so the next cycle knows what was covered
- Timing data helps estimate future test flights

### When to Write It

Cowork writes this summary as the **last action before auto-stopping patrol**. The sequence is:

1. All bugs verified → STATUS = ALL_BUGS_VERIFIED
2. Cowork writes TEST FLIGHT COMPLETE summary to FLIGHT_PLAN.md
3. Cowork writes "Patrol complete. Standing down." to AGENT OUTPUT LOG
4. Patrol stops

---

*AI Tester Feedback Protocol v0.1.0*
*Part of the Test Pilot Loop — Flight Deck by Huan Su.*
*Professional QA rigor, applied by AI, with human judgment where it matters.*

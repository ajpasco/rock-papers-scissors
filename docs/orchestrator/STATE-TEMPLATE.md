# Preparation state: <project name>

Copy this file to the project as `PREPARATION-STATE.md`. The orchestrator updates it after every
step. Any session can resume from this file alone.

- **Project:** <name>
- **Runtime:** claude-code | codex
- **Current step:** 1 of 7
- **Status:** in progress | waiting on the user | blocked | gate passed
- **Updated:** <date, timezone>

## Gate log

| Gate | Passed | By what evidence | Question budget | Notes |
|---|---|---|---|---|
| 1 Your idea |  |  | about 8; used  |  |
| 2 Clarify the scope |  |  | about 6; used  |  |
| 3 PRD and mockup |  |  | about 6; used  |  |
| 4 Technical blueprint |  |  |  |  |
| 5 Evals and stories |  |  |  |  |
| 6 Planning review |  |  |  |  |
| 7 Ready to build |  |  |  |  |

`Question budget` is the number of stops the orchestrator stated at the gate's first stop and the
number used so far. Where the orchestrator set a number other than the default, or the estimate
changed, the reason follows in a few words (ORCHESTRATOR.md section 3.4, rules 4 and 6).

## Artifacts

| Artifact | Path | SHA-256 | Revision | Valid |
|---|---|---|---|---|
| PRD |  |  |  |  |
| Mockup |  |  |  |  |
| Blueprint |  |  |  |  |
| Eval plan |  |  |  |  |
| Story list |  |  |  |  |
| Review findings |  |  |  |  |

`Valid` is `no` once something upstream of it changed. An invalid artifact is never carried
forward; the run returns to the earliest invalidated gate.

## Questions asked, and the answers

| Stop | Question or item | Class | Options, each with its cost and an example | Recommended | Answer | Kind |
|---|---|---|---|---|---|---|

One row per stop. A stop that is a list has one row per item, numbered under the stop: `12.1`,
`12.2`, `12.3`. A rule is one row: it names the rule and the two or three instances given as
examples, and its answer applies to every instance (ORCHESTRATOR.md section 3.4, rules 1 and 3).

`Class` is `idea` (a question that shapes the idea), `happy path`, `likely error`, `edge case`
or `technical` (section 3.4, rule 6). An edge case is never asked; it has a row only so that it
is carried, with `none` in `Stop` and `not handled` in `Kind`.

`Kind` is `product` (the user answered), `default` (a recommended default the user accepted),
`open` (deferred, still waiting), `look` (the direction at gate 3, or a decision after the
look-only revisions: about look, approving no behaviour) or `not handled` (an edge case, or a gap
the user did not pick at the budget; the PRD lists it in its section 8, "Edge cases not handled
in version one"). There is no kind for an answer the user did not give. An answer worked out from
earlier replies is never recorded here: it is either an application, in the Applied table, or a
question, asked (section 3.4, rule 5).

Every option has its cost in the same detail as the others;
`Recommended` names the orchestrator's recommendation and why for a technical question, and is
`none: product question` for a product one (ORCHESTRATOR.md section 3.1, gates 2 and 4). On the
list a gate's budget brings, it is `carry as not handled` on each item (section 3.4, rule 6). When
the user answers "go with your suggestion", the `Answer` ends with the words "taken on the
session's suggestion" and the `Kind` is `default` (ORCHESTRATOR.md section 3.2).

## Standard behaviours

- **Standard behaviours:** shown at gate 2, stop <n>; the user's reply, verbatim: "<reply>";
  recorded as decision <number>; changed or struck: <SB-n, now decision <number>> | none

The list is `STANDARD-BEHAVIOURS.md`. It is shown once and never asked again; an item the user
changed is a decision of its own, and criteria cite that decision instead of the item.

## Open questions

| # | Question | Waiting on | Blocks |
|---|---|---|---|

## Decisions

| # | Decision | Who decided | Where it is recorded |
|---|---|---|---|

`Who decided` is the user, a recorded default, or a cited PRD clause — never the orchestrator.
Every answer the user gives at a stop, the reply to the standard behaviours, each item the user
changed and each decision about look is a numbered decision here, and drafters cite it by number.

## Applied

| # | What was applied | Where | From | Told at |
|---|---|---|---|---|

An application is an answer that follows from a numbered decision or an SB item. `Where` is the
requirement, criterion or screen it went into; `From` is the decision's number or the `SB-n`;
`Told at` is the gate whose acceptance stop listed it, as information, not as a question. If the
user disagrees with one, that is a new numbered decision changing its source, and every row
applied from that source is revisited (ORCHESTRATOR.md section 3.4, rule 5).

## Findings

| ID | Severity | Class | Resolution class | Status | Closed by |
|---|---|---|---|---|---|

A blocker says how the application would break, or which decided behaviour would be left
unbuilt, if it were built as written; anything else is an advisory, fixed the next time the
artifact is touched (ORCHESTRATOR.md section 6). `Class` is the gap's class where the finding
raises one: `happy path`, `likely error` or `edge case`.

## Corrections

| Review | Correction used | What changed | Re-review session |
|---|---|---|---|

One automatic plan-only correction per review, then a new review in a new session.

## Freeze

- **Frozen:** no
- **Pull request:** <number>, carrying `docs/` and `eval/` and nothing under `app/`
- **Head commit approved:** <full sha>, `pod/approval` = success, set by <the user's login, time>
- **Preview:** `pod/preview` = <success, as expected | success | failure> on <sha>; is that what
  the plan predicts?
- **Gate:** `pod-gate` = <conclusion> on that head commit
- **Merged into main:** <merge commit, time>
- **Read from GitHub by the orchestrator:** <date>

Nothing may be frozen before gate 6 passes, and only the user may freeze: the user runs
`pod-works approve <pr>`, and the orchestrator fills these lines from the pull request's statuses
and merge on GitHub, never from the user's word.

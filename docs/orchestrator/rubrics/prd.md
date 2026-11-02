# Rubric: the PRD

Standard to match: `examples/equipment-checkout/prep/PRD.md` in the pod-works repository (observable acceptance criteria), absent from a founder's repository, where this rubric's rules apply on their own. A drafter receives this file, the confirmed scope, the recorded answers,
the open questions, the numbered decisions, [STANDARD-BEHAVIOURS.md](../STANDARD-BEHAVIOURS.md) with
the user's reply to it, and the gaps carried as not handled. It receives no other agent's
reasoning, and nothing as decided beyond these (ORCHESTRATOR.md section 3.4, rule 5).

## Required sections

1. **Problem statement.** Who has the problem, in one or two paragraphs. No solution language.
2. **The product.** What it is, in the user's terms.
3. **Users and roles.** Each role, what it may do, and what it may not do.
4. **Definitions.** Any term the acceptance criteria rely on, defined once. A criterion that uses
   an undefined term is a finding, judged as the checker's verdict says.
5. **Functional requirements.** `REQ-01…REQ-nn`, each with **User**, **Flow** (numbered steps) and
   **Acceptance** (a list of observable criteria).
6. **Non-functional requirements.** Only those the user stated or accepted as a default.
7. **Non-goals.** What version one deliberately does not do.
8. **Open questions.** Every deferred question, with who it is waiting on. Then, under the heading
   **Edge cases not handled in version one**, every edge case found and every gap the user left at
   a gate's budget, one line each, with no criterion. The user may promote any of them by naming
   it; it is then asked, and only the answer puts it in a requirement.

## Requirement rules

- **This drafter assigns the requirement IDs.** No other artifact may create one. Leaving the PRD
  without IDs so that the blueprint or the eval plan numbers the requirements instead is the
  defect this rule exists to prevent: it inverts traceability and leaves the contract pointing at
  the design.
- IDs are stable and never reused or renumbered. Any fixed width is fine (`REQ-01` or `REQ-001`)
  as long as one width is used throughout.
- **One requirement is one capability**, judged when the IDs are first assigned. Where IDs were
  adopted from an earlier artifact and a requirement bundles several capabilities, do not split or
  renumber it: the citations that carry the traceability would break. Record the bundling, and rely
  on criterion-level coverage in the eval plan to keep the protection the rule exists to give. Adding a requirement appends; removing one leaves
  the number retired with a line saying so.
- When assigning IDs for the first time: "and" in a requirement title usually means two
  requirements.
- Every requirement traces to something the user said. A drafter that cannot cite the scope,
  an answer, a numbered decision, an SB item or a feedback round for a requirement must list it
  under open questions instead. An answer worked out from the user's earlier replies is not a
  source: what follows from a decision or an SB item cites it, and what needs judgement is an
  open question (ORCHESTRATOR.md section 3.4, rule 5).
- **A rule is stated once.** A behaviour several requirements share (what happens on an impossible
  value, what back does over a dialog) is stated in one home requirement, with its criteria. Every
  other requirement it covers cites the home requirement by id ("Unusable input: as REQ-04") and
  does not restate it.
- Every role's prohibitions are stated as acceptance criteria, not implied by the absence of a
  permission. "Admins cannot borrow" is a criterion; silence is not.

## Acceptance criterion rules

**A criterion states a business outcome, never a technical choice.** No HTTP status code, no table
or column shape, no module, function or file name, no library, framework or build tool, no log
format. Those are the blueprint's, and the acceptance tests do not verify them. Where the user has
decided a technical fact, it stays the user's decision and is recorded in the blueprint's decision
list; the criterion says what a person would see, be shown or be refused. "The request is refused,
nothing changes and the Access denied modal appears" is a criterion; "the server answers 403" is a
blueprint entry.

A criterion is acceptable only if someone other than its author can tell whether it holds:

- It names what is visible, recorded or refused — not how it is implemented.
- Bad: "the UI is professional and clean." Good: "every row has identical height regardless of
  status, icons or loader."
- Bad: "handles errors." Good: "a failed request shows the failure modal with Close and Retry, and
  no row changes state."
- Each criterion covers one case. Where behaviour differs by role or state, there is a criterion
  per role or state. Where it is the same, it is one rule, stated once and cited; a criterion is
  never restated per surface.
- The home requirement for unusable input (missing, malformed, out of range) states the rule
  once, citing SB-1 and SB-2 or the decision that changed them, and each requirement that takes
  input cites that home requirement. Unauthorized input is a role prohibition, a criterion under
  the requirement rules above.
- A criterion that comes from a standard behaviour cites it by id, as one from a numbered decision
  cites the decision: "A count of 0 is refused with the reason, and nothing changes (SB-1)."
- An edge case gets no criterion: a corner a user rarely meets, two rare states interacting, input
  nobody gives, a display at an extreme. It is a line in section 8, unless the user promoted it.

## Change rounds

Each round of user feedback appends to the PRD's own change list with the user's words, and
updates the requirements it touches. A mockup change that alters behaviour with no PRD change is a
gate-3 failure, not a cosmetic difference.

## Checker's verdict

First, the numbering: every `REQ-nn` appears exactly once, the sequence has no holes, every
acceptance criterion sits under exactly one requirement, and no criterion is left outside the
requirements. A failure in any of those is a blocker: a criterion with no single requirement is
built and tested by nobody.

Then, with the state file's decisions and the standard behaviours beside the PRD: per numbered
decision that decides a behaviour (not a look or a technical choice), and per standard behaviour
the user changed, the criterion that carries it, or none. None is a blocker: a decided behaviour
left unbuilt.

Then, per requirement: traced to a user statement, a decision or an SB item, and cited (yes/no),
criteria all observable (yes/no), role prohibitions present (yes/no), the unusable-input rule
cited where the requirement takes input (yes/no, or not applicable). Then list every criterion you
judged unobservable, quoting it; every rule stated in more than one requirement; and every
criterion that specifies an edge case the user did not promote.

A blocker is only a finding that, built as written, would break the application or leave a
decided behaviour unbuilt, and you say how: the step of the journey that fails, or the decision
with no criterion (ORCHESTRATOR.md section 6). An undefined term, an untraced requirement, an
unobservable criterion, a restated rule, a missing citation and a specified edge case are
advisories unless they do one of those two things; report each, because the orchestrator still
removes or asks about an untraced requirement at gate 3. Where a finding raises a gap, class it
happy path, likely error or edge case; an edge case is never a blocker and is reported only as a
line for section 8.

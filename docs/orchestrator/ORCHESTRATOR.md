# The preparation orchestrator

The canonical definition. It is plain markdown on purpose: any coding agent in any runtime can
read this file and run the process without a service, a plugin or a network call. The runtimes
(Claude Code CLI, Codex CLI) only change who plays each role and how a fresh session is started;
see [RUNTIMES.md](RUNTIMES.md).

## 1. What it produces, and for whom

One idea in; three documents out, good enough for a build team to build from without asking the
author what was meant:

| Output | Rubric | Standard it must match |
|---|---|---|
| `PRD.md` | [rubrics/prd.md](rubrics/prd.md) | `examples/equipment-checkout/prep/PRD.md` |
| `TECH-SPEC.md` (the blueprint) | [rubrics/blueprint.md](rubrics/blueprint.md) | `examples/equipment-checkout/prep/TECH-SPEC.md` |
| `eval-plan.md` and the story list | [rubrics/eval-plan.md](rubrics/eval-plan.md) | `examples/equipment-checkout/prep/eval-plan.md` |

The exemplars live in the pod-works repository, not in a founder's; when they are absent, the
rubric's rules apply on their own, and the orchestrator says so once rather than reporting them
missing. A long-form reference PRD — definitions, non-functional requirements, non-goals, known
limitations, glossary, changelog — is produced only
when the application will have readers outside the team. A small trial application does not need
one, and the orchestrator does not invent one to look thorough.

## 2. Why the gates exist

Agents produce confident, plausible, defective artifacts. This project's own record shows it: a
build plan whose evals did not link to the requirements they claimed, verification scheduled
before the tests it verifies existed, a restraint check that flagged a meeting title as an
unauthorized feature, and a session that reported "no blocker" when there was one. None of that
was caught by the agent that wrote it. It was caught by a human reading the output, or by a
separate agent checking it against a fixed standard.

So the orchestrator's value is not generation. It is the refusal to advance a step whose exit
conditions are unmet, and the separation between the agent that drafts and the agent that checks.

## 3. Roles

**Orchestrator.** Runs the steps, asks the user one stop at a time (section 3.4), keeps the
state file, dispatches drafters, designers and checkers, reports blockers. It writes no artifact
and approves nothing.
One narrow exception: it may apply a **mechanical amendment** — a change that implements a numbered
user decision verbatim, touching only the sentences that decision governs, where dispatching a
drafter for two lines would be waste. It records every such amendment in the state file, names the
decision, and the checker reviews the result like any other content. Anything calling for judgement
is a drafter's work, not the orchestrator's.

Two rules learned by getting this wrong:

- **An amendment is mechanical only in the sentences it changes.** Everything downstream — a
  definition that pointed at the question now closed, the change list, the register row, the tests
  that assert the old wording — is drafting work and goes to a drafter. After any amendment the
  orchestrator sweeps the artifacts for **every phrasing** of what it closed, not for the phrases
  it happens to think of, and it states which phrasings it searched.
- **An accepted decision is scope even when it matches what an agent already proposed.** It needs
  its criterion, its register row and its test like any other scope. "It confirms the default, so
  nothing changes" is how decided behaviour ends up with no test at all.
It manages every agent at every gate — including the reviewer at gate 6 — so no gate is ever run
by an agent reporting to itself, and the handoff to the build side is the orchestrator's report
rather than any drafter's summary.

**Drafter.** Writes or revises exactly one artifact against its rubric. It receives the artifact's
inputs and its rubric, never the previous drafter's reasoning or the checker's chat.

**Designer.** Draws the mockup against its rubric, [rubrics/mockup.md](rubrics/mockup.md), in
passes: a direction of three to five key screens, which the user sees before the PRD is drafted;
then the full page; then revisions. It researches the platform's own guideline and comparable
products itself, in writing and with its sources, and designs an original look from them. It
decides look, layout and the quality of each interaction. It never decides behaviour: anything a
better design would need that the confirmed scope or the PRD does not state comes back as a
question for the user, classed as section 3.4 says, and is drawn as the scope stands. Like a
drafter, it receives its inputs and its rubric and no other agent's reasoning, and it never
checks its own mockup.

**Checker.** Reviews one artifact against its rubric in a session that started for this task, with
the artifacts, the rubric and, where the rubric needs them, the state file's decisions and the
standard behaviours as its only inputs. Its output is a findings file, not an opinion in
conversation. A drafter never checks its own work, and no session plays both roles for the same
artifact.

**User.** Answers product questions, decides between options, dispositions findings, and accepts
each step. The only approver. Nothing in this process can be approved on the user's behalf.

### 3.1 Whose side the orchestrator is on

The orchestrator is not a neutral referee standing between the user and the agents. It is the
user's manager of those agents. It works for the user, and at every gate it is the user's
advocate against the ways this kind of work goes wrong.

- **An agent's report is a claim, not a result.** The orchestrator checks the decisive claims
  against the artifact itself — the count, the missing row, the hash, the test that is supposed to
  fail — before it records a gate as passed. "The drafter says it is complete" is not evidence.
- **Defective work goes back to the agent, not up to the user.** A drafter whose output misses its
  rubric is sent back with the specific rule it missed, twice at most. Only then does the
  orchestrator report that the agent cannot produce it. The user's attention is not the place
  where agent failures get resolved.
- **It brings decisions, not homework.** A question reaching the user carries the options, what
  each one costs, the orchestrator's recommendation and the evidence behind it, for a technical
  question; a product question carries its options and their costs and no recommendation
  (gate 2). Handing over a raw findings file and asking "what do you want to do?" is a failure of
  this role. How the decision is said, in plain words and in a fixed order, is section 3.2.
- **It guards the user's words.** Anything in an artifact that does not trace to something the
  user said is challenged and removed, or raised as a question. Scope that arrives by inference,
  by an agent's preference, by a reviewer's suggestion or by silence does not survive a gate.
- **It holds the agents to the user's standard, not to their own.** The rubrics are the user's
  interests written down. An agent that wants one relaxed is refused, and the request is reported.
- **It names the user's own contradictions.** Where two of the user's instructions conflict, the
  orchestrator quotes both, says what each implies, and asks which wins. It never silently picks
  the more recent one.
- **It warns before an expensive step, not after.** Before a freeze, before accepting a gate with
  an open question attached, the orchestrator states plainly what is about to be locked in and
  what is still unanswered.
- **It sides with the evidence, even against the user.** If the user is about to lock in something
  the artifacts show is wrong, the orchestrator says so once, in plain terms, with the evidence.
  Then it does what the user decides and records who decided. Agreeing with the user against the
  evidence is not loyalty; it is the failure this role exists to prevent.
- **It reports its own mistakes first.** When the orchestrator gets something wrong, it says so
  directly, corrects it, and carries on. It never repairs its own error quietly and it never lets
  a stale claim of its own stand in the record.

What it absorbs, and what it escalates:

| The orchestrator handles | The user decides |
|---|---|
| A rubric rule an agent missed | A product choice, or a conflict between the user's own instructions |
| A sequencing, ownership or traceability defect inside the agreed contract | A change to the chosen technical approach |
| A finding an agent raised with no basis | A finding whose resolution would add or cut scope |
| Re-dispatching, re-checking, bookkeeping, hashes, state | Accepting each gate, and the freeze |

### 3.2 How every session speaks to the user

The user is often new to this process and is not assumed to be an engineer. Every Claude Code
session in pod-works that talks to the user follows this rule: the preparation orchestrator, the
build orchestrator, and the `/pod-works-prepare`, `/pod-works-build`, `/pod-works-increment`,
`/pod-works-qa` and `/pod-works-accept` skills. Section 3.1 says what a question must carry; this
section says how it is said. The skills and agents apply it and do not restate it.

**1. Every stop speaks plainly.** Whenever the session stops for the user (a question, a gate's
acceptance, an approval, a decision list, the acceptance walk), it says, in this order and in
everyday words:

1. **Where we are**, in one line: the stage and the step, named as the README's "The journey"
   names them, for example "Preparation, gate 2 of 7 (clarifying the scope), stop 4 of about 6".
2. **What is being asked of you, and why it matters**: what the answer changes in the application
   or in what happens next, not in the process's own terms.
3. **The options**, each in the same detail as the others, each with what it costs.
4. **The session's suggestion and its reason, where the rules allow one.** A technical question,
   a defect (fix now or defer) and a rubric, sequencing or traceability matter carry one. A product
   question carries none: the session explains the trade-off and says plainly that this one is
   the user's call (gate 2).
5. **Exactly what to type or click to answer**: the words to reply with, the option to pick, the
   command with its real numbers filled in, or the console's section and button.

A technical term is defined the first time it appears in a session, in one clause, in the wording
of the README's "The words" ("a row, one acceptance test", "a packet, a pull request that changes
the plan or the tests and waits for your approval"). No step is ever a bare command: a command
comes with what it does and what the user will see after it.

**2. Help when the user is lost.** If the user replies "what now", "where are we", "what does this
mean", "I'm not sure", "help", or anything else that is not an answer to what was asked, the
session does not repeat the question. It explains the current stop again in simpler words, gives
an example of an answer, says what happens next for each option, and offers its suggestion where
rule 1 allows one.

The user may reply "go with your suggestion". The session records that as the user's decision,
with the words "taken on the session's suggestion", so the record shows whose idea it was: in the
state file the answer ends with those words and its kind is `default`, the record gates 2 and 4
call an accepted default; in a decision list or a run record, the same words follow the decision.
Where there is no suggestion, because the question is a product one, the session says so,
explains the trade-off once more, and waits; it does not pick.

At any point the user may ask for **the plan** ("what's the plan", "where are we in all this").
The session lists the stages done, the stop it is at now and the stops still to come, with the
command or skill for each, from the README's "The journey" and this project's own record (the
state file, `pod-works status`). Then it puts the current question again, in full, as rule 1 says.

**3. Nothing new is decided by the session.** This rule changes how things are explained, never
who decides. A suggestion is not a default the session may apply: a product question stays the
user's, with no suggestion and no default; every approval, acceptance and freeze stays the user's
own act, typed or clicked by them; silence, or a question back, is not an answer; and a suggestion
the user takes is recorded as taken on the session's suggestion, never as the session's decision
and never as the user's own idea. Plain words never blur a fact: a command, a commit, a row id or
a count is still given exactly, after the plain sentence that explains it.

### 3.3 An approval stop

Some stops end in a yes that only the user can give: the freeze at gate 7, an increment's packet,
the acceptance note's pull request, a merge over a reader's block, and the release. At each of
them the session does three things, after the order of section 3.2 and inside it: it tells the
user what to check in this pull request, with what it found; it says what sending it back means;
and it ends with the one line that approves it, which the user types themselves.

**1. The user types the approval, in their own session, with `!`.** In Claude Code, a line that
begins with `!` runs the rest as a shell command in the user's own session, under their own login,
and puts what it printed into the conversation, so the session can read the result without having
run the command. Every approval stop ends with one line of this form, the real number filled in:

> To approve, type `! pod-works approve 7`. To send it back, tell me what is wrong.

The release ends the same way: "To release, type `! pod-works release <version>`." with the
version the user chose, or a version to choose. A separate terminal in the repository works just as
well, without the `!`; the session says so once, as the alternative, and does not repeat it at
every stop.

The session itself never runs `pod-works approve`, `pod-works release` or any merge, and the
command is never put in a session's allow-list (the `permissions.allow` of any settings file, a
`--allowedTools` flag or a headless allowlist). The reason is `docs/THREAT-MODEL.md`'s first
assumption: the user's `gh` login is the user, and whoever holds that login's session holds the
approval. A session runs on the user's machine as the user, so nothing in the tool can tell its
`pod-works approve` from theirs; the yes must be a keystroke of the user's, not a decision of the
model's. Approving a permission prompt for a command the session proposed is not the same act,
and a session does not propose it. This adds to section 7.1 and changes none of it.

**2. What to check, from this pull request, before the approve line.** Three to five items, each
a question with the answer the session found in this pull request, in this order:

1. **Your words.** The decision the pull request rests on, quoted from the state file, the decision
   list or the user's reply. "If that is not what you said, send it back."
2. **What it changes.** The files under `eval/` and `docs/`, the rows added and any deferred, and
   that nothing under `app/` changed. "If it touches the application, send it back."
3. **What the reader found.** No block, and the notes that matter in one line each; or the block,
   quoted. "A block means it must not merge as it is."
4. **What the preview says.** "As expected: only the new rows fail", or what else failed. "If other
   rows fail, something is wrong."
5. **For a story's packet, its scope.** The files the story may change, from
   `eval/stories/<n>.txt`.

The session fills each from the records it has: the pull request (`gh pr view <pr>`, its files),
the reader's record, the preview's line (`gh pr checks <pr>`, or `.pod/runs/preview-<pr>-<ms>.json`);
`pod-works approve` prints the same pull request, head commit, files by protected path, gate
verdict and preview line before it sets the status, so the user sees them again as they approve.
Where the session has no record for an item it says so ("I have no reader's record for this
head") rather than guessing. A stop that is not a packet uses the items that apply: the freeze has
all five, S-1's scope being the fifth; the acceptance note's pull request has the first two
(the verdict is the user's own words, and it changes only `docs/acceptance/<date>.md`) and the
reader and preview where they exist; a merge over a block has the third first.

**3. What happens when you send it back, every time.** After the checks and before the approve
line, the session says what sending it back sets off, as a numbered sequence in plain words, so the
user knows a "no" is cheap and normal. For the freeze and a packet:

1. Nothing merges; main stays as it is.
2. You say what is wrong in your own words; one sentence is enough.
3. The session redrafts the packet, has a fresh reader read it again (`pod-works review --packet`)
   and runs the preview again. In the lab that cost a few dollars and a few minutes, not a build.
4. The same stop comes back, on the new head commit, with the checks and the approve line.
5. Until a packet you approve merges, nothing else can merge, and that is normal: main's tests
   fail on purpose until it does, so the gate stays red. Sending it back is the process working,
   not a failure.

Each other stop says its own variant of the sequence:

- **The acceptance note**: (1) nothing merges, and nothing is accepted; (2) you rewrite the
  verdict or the limitations in the approval console's Acceptance section; (3) a new note's pull
  request opens; (4) the stop comes back. The verdict stays the user's own words throughout, and
  `pod-works release` will not publish until a note that accepts the commit has merged.
- **A reader's block on a story**: (1) nothing merges; (2) `pod-works fix S-<n>` sends the reader's
  words to the builder in the same worktree; (3) the gate and a fresh reader judge the new push;
  (4) the stop comes back only if the reader blocks again. Here the choice is (a) send it back,
  which the session may run on "go with your suggestion", costing another builder round, the
  reader and the gate again; or (b) merge it anyway, the user's own act through
  `! pod-works approve <pr>`, landing it with the findings as known risks, the gate still required
  green. Each comes with its cost and the session's suggestion, as section 3.2 requires, and with
  why to send it back in the reader's own words, quoted from its finding.
- **The release** has nothing to send back: not typing it is the answer, and nothing changes.

### 3.4 How questions are asked

Section 3.2 says how a stop is said. This section says what is asked, in what shape, and how much.
It governs every question the orchestrator puts to the user. The standard behaviours and the
budgets belong to gates 1 to 3, where most questions are asked.

It exists because of a founder's real run. To the end of gate 3 he answered 116 questions over two
days. Many were the same rule asked once per screen, an answer the orchestrator had worked out and
then asked him to confirm, or a corner no user would meet. His bar was the right one: the happy
paths work, and the errors a user is likely to meet are handled; anything else is fixed later.

The words this section uses:

- A **stop** is any point where the session stops for the user (section 3.2).
- A **gap** is anything a pass found that the confirmed scope, the numbered decisions and the
  standard behaviours do not answer. A pass is one round of work by the orchestrator, a drafter, a
  checker or a designer.
- A **surface** is one place a user meets the application: a screen, a dialog, a field, a command.
- A **rule** is one behaviour that several surfaces share: what happens on an impossible value,
  what back does over a dialog.
- A **numbered decision** is an entry in the state file's Decisions table: the user's answer at a
  stop, or a recommended default the user took, recorded as a default.
- An **SB item** is one of the standard behaviours in
  [STANDARD-BEHAVIOURS.md](STANDARD-BEHAVIOURS.md), `SB-1` onward.

**1. Ask the rule, once, with examples.** Before any question is put, the orchestrator groups the
gaps a pass found into rules: its own, a drafter's open questions, a checker's `user-decision`
findings and a designer's questions for the founder alike. It asks one question per rule, with two
or three of the rule's instances given as examples. The answer is recorded once and applies to
every instance, present and future: a surface found later that the rule covers is an application of
it (rule 5), never a new question. The PRD states the rule once, in a home requirement, and the
other requirements cite it ([rubrics/prd.md](rubrics/prd.md)); a criterion is never restated per
surface.

**2. Standard behaviours are shown once and never asked.**
[STANDARD-BEHAVIOURS.md](STANDARD-BEHAVIOURS.md) lists the behaviours that have the same answer in
every application, each with its id, one sentence and one example. At gate 2 the orchestrator shows
the whole list once, as one stop: "These are the standard behaviours; they apply unless you strike
or change one. Reply 'all', or the numbers to change with what you want instead." The reply is one
numbered decision. From then on each SB item is a cited source for a criterion, exactly like a
numbered decision, and no SB item is ever asked about again, for any surface. An item the user
changed becomes a numbered decision of its own. A drafter or checker that opens a question an SB
item answers has not read the list; the orchestrator applies the item and does not ask. The list is
not a default the session applies on its own: nothing on it holds until the user has replied.

**3. One stop at a time, and a stop may be a list.** A stop is one of two things:

- **One question that shapes the idea**: one whose answer changes what the product is, who it is
  for, or what it does at its centre, such as whether a routine is kept on one phone or shared
  with others. It is asked alone, with its options, what each costs and an example of each, and
  with no recommendation and no default (gate 2).
- **One numbered list of related items**, each of which a letter or a word can answer: a rule's
  instances, the standard-behaviour list, technical defaults with the recommendation on each, the
  gaps one pass found. A list never mixes a question that shapes the idea with small ones. The
  user answers item by item ("1 a, 2 b, 3 all correct") or in one word for the lot ("all").

Every option, in a question or in a list item, carries a concrete example: the same moment, shown
under that option. "If a call comes in during a round: (a) the timer pauses and shows Resume; (b)
the timer keeps running and the next beep still sounds."

**4. Every stop says how many are left.** Every stop says how many stops are left in this gate, as
an estimate, in its "where we are" line (section 3.2): "stop 3 of about 6 for this gate". When the
estimate changes, the next stop says why, in one sentence.

**5. Nothing derived is recorded as decided.** The orchestrator never works out an answer from the
user's earlier replies and records it as told, read, assumed or inferred. An answer the process
needs is one of two kinds, and there is no third:

- **An application.** The answer follows from a numbered decision or an SB item. The drafter or
  designer cites that decision or item where it applies it, the state file's Applied table records
  it, and the gate's acceptance stop lists the applications ("applied from your decisions: ...") as
  information, not as questions. If the user disagrees with one, that changes the decision or the
  item it came from: the change is a new numbered decision, and it applies everywhere the old
  answer did.
- **A question.** The answer needs judgement, so it is asked, under rules 1 and 3.

A drafter or designer is given as decided only the confirmed scope, the numbered decisions, the SB
items and the artifacts the user has accepted. Nothing else.

**6. The bar and the budget.** The bar, in the founder's words, is that happy paths work and that
the errors a user is likely to meet are handled. Every gap is classed by whoever finds it, and the
orchestrator checks the class as it checks any claim (section 3.1):

- **happy path**: the journey the product exists for, done as intended;
- **likely error**: a mistake or a failure a user will meet in ordinary use, such as a wrong value,
  a lost connection or a cancelled dialog;
- **edge case**: neither. A corner a user rarely meets, two rare states interacting, input nobody
  gives, a display at an extreme: a save name of only spaces, a tap on Resume while a read-back
  waits, back over a box on top of a box.

Only the first two may become questions. Edge cases are not asked, not specified and not drawn. The
PRD lists them in its section 8 under "Edge cases not handled in version one", one line each, with
no criterion. The user may promote any of them by naming it, and it is then asked like any other
gap. Until the PRD exists, the state file records them, kind `not handled`. A question that shapes
the idea is not a gap and takes no class.

Each gate's question phase has a **budget**, which the orchestrator states at the gate's first
stop: about 8 stops at gate 1, about 6 at gate 2 and about 6 at gate 3. These are defaults; the
orchestrator may set another number for a gate, and says why. When a gate's stops reach its budget,
the remaining gaps come as one list, one line each, with the recommendation to carry each as not
handled in version one, and the user picks which of them to answer. That recommendation is about
whether to spend more questions, not about any answer: an item the user picks is then asked as
rule 3 says, and a product question among them still carries no recommendation. Questions that
shape the idea come first in a gate, so the budget is not spent before them.

A checker's **blocker** is only a finding that, built as written, would break the application or
leave a decided behaviour unbuilt, and the checker says how. Everything else is an advisory, fixed
the next time the artifact is touched. A gate whose exit conditions hold is accepted with its
advisories open (section 6).

**7. The stops of gates 1 and 3.**

- **Gate 1 is a thinking partner, not a scribe.** The orchestrator captures the idea verbatim. Then
  it organises it: it groups the points; names every gap, contradiction and ambiguity; and names
  the decisions a product of this kind usually has to make that the idea does not mention, as
  questions, never as scope. It asks those as stops under rules 1 and 3, within gate 1's budget.
  Then it restates the scope, once, and the user confirms the restatement. "Nothing the user did
  not say" holds for the restatement, not for pointing at what is missing.
- **Gate 3 draws first, then writes.** It begins with a direction pass by the designer: research,
  design notes, a screen map, and three to five key screens drawn from the confirmed scope and
  gate 2's answers, before the PRD is drafted. The orchestrator renders the screens itself and
  brings one stop in two parts: the direction, a decision about look, recorded as such, which
  approves no behaviour; and, as one list, the behaviour questions the screens raised, grouped
  into rules. Then the PRD is drafted, then the full mockup is drawn, then the checkers run. After
  the user has seen the full page, at most two look-only revisions run on the user's words; then
  the user decides: accept the look as it stands, with the advisories listed, or name the one
  thing the next revision changes.

## 4. The seven gates

Each gate lists what must be true to advance. The orchestrator states which condition failed and
stops; it never advances on a partial pass.

### Gate 1 — Your idea
- The idea is captured in the user's own words.
- The orchestrator organised it before restating it (section 3.4, rule 7): the points grouped,
  every gap, contradiction and ambiguity named, and the decisions a product of this kind usually
  has to make that the idea does not mention put as questions, never added as scope. They were
  asked as stops within gate 1's budget, and each answer is recorded.
- The orchestrator restates the scope, once, and the user confirms the restatement.
- Nothing is in the restatement that the user did not say. A question that pointed at what was
  missing added nothing to it; only the user's answer did.

### Gate 2 — Clarify the scope
- The standard behaviours were shown once, as one stop, and the user's reply is recorded as one
  numbered decision (section 3.4, rule 2). No SB item was asked about on its own.
- Every question that shapes the idea, and every gap classed happy path or likely error, was
  asked as section 3.4 says: one stop at a time, gaps grouped into rules and each rule asked
  once, an example with every option, the count at every stop. At the budget, the rest came as
  the one list of rule 6, and the items the user picked were asked. Each answer is recorded.
- No product question is answered by assumption, and no answer is worked out from earlier
  replies and recorded as told (section 3.4, rule 5). A technical question may carry a
  recommended default, and an accepted default is recorded as a default, not as a user decision.
- Questions the user defers are recorded as open questions and carried forward, not resolved
  quietly later. Edge cases, and the gaps the user left at the budget, are recorded as not
  handled, for the PRD's section 8.

### Gate 3 — PRD and mockup
- **The direction comes first.** The gate opens with the designer's direction pass, before the
  PRD is drafted, and the one direction stop (section 3.4, rule 7). The direction is recorded as
  a decision about look; it approves no behaviour. Then the PRD draft, then the full mockup,
  then the checkers, whose gaps are grouped into rules, classed, and asked within gate 3's
  budget.
- **The requirement IDs are created here, by the drafter that writes the PRD, and nowhere else.**
  Requirements are numbered `REQ-01…REQ-nn`, stable and never renumbered, each with its user, its
  flow, and its acceptance criteria. The orchestrator checks the numbering itself before
  advancing: every ID appears exactly once, the sequence has no holes, and every acceptance
  criterion sits under exactly one requirement.
- No later artifact may create, renumber or extend a requirement ID. A blueprint or an eval plan
  that needs an ID the PRD does not have is a **gate-3 failure**: the orchestrator returns to gate
  3, has the PRD amended, and re-runs the gates that depend on it. It does not let the downstream
  artifact number the requirements for the PRD.
- Every requirement traces to something the user said, a numbered decision or an SB item, and
  cites it. A requirement that traces to none of them is scope nobody decided: the orchestrator
  has it removed or raises it as a question, whatever its severity (section 3.1). Every
  acceptance criterion is observable (someone other than the author can tell whether it holds);
  one that is not is a finding, a blocker only as section 6 says.
- A rule shared by several requirements is stated once, in its home requirement, and cited by
  the others. Edge cases are listed in the PRD's section 8, "Edge cases not handled in version
  one", one line each; none has a criterion or a drawing unless the user promoted it.
- **The mockup is `docs/MOCKUP.html`**: one self-contained page, a page of terminal transcripts
  for a command-line tool, drawn by the `mockup-designer` and checked in a fresh session by the
  `mockup-checker` against [rubrics/mockup.md](rubrics/mockup.md) and the PRD. The
  `acceptance-walker` reads it at that path.
- The mockup demonstrates every journey the PRD claims, and claims no journey the PRD lacks.
- After each change round, the PRD is re-confirmed alongside the mockup. A mockup change that
  alters behaviour and leaves the PRD untouched fails this gate.
- **Look revisions are bounded.** After the user has seen the full page, at most two look-only
  revisions run on the user's words; then the user decides, as section 3.4 rule 7 says.
- No checker reports a blocker. The acceptance stop lists, as information and not as questions,
  what was applied from the user's decisions and the SB items, and the edge cases not handled.

### Gate 4 — Technical blueprint
- The user chose between stated options; the orchestrator did not choose for them.
- **Every question put to the user here meets section 3.1.** Each option is stated in the same
  detail as the others: what it is, and what it costs (the build work, what it adds to install
  or run, what it rules out later, and the risk). A technical question carries the
  orchestrator's recommendation and the reason for it; a product question carries none, as gate 2
  says, and its options and their costs are still stated. The state file records each question
  with its options, each option's cost and, for a technical question, the recommendation. A
  question recorded with an option of one word, an option with no cost, or a technical question
  with no recommendation fails this gate, and the question is put again. The recommendation never
  becomes the answer: the user chooses, and a default the user takes is recorded as a default.
- Every requirement appears in exactly one layer or phase, cited by its PRD ID. The blueprint
  inherits the IDs; it never mints one.
- A verification register maps every acceptance criterion to the mechanism that will produce
  evidence for it, and names what that evidence is.
- Security controls are named per requirement that needs one, including what happens on missing,
  malformed or unauthorized input.
- Every technical choice the build needs is recorded here — status codes, schemas, names, libraries,
  log formats, configuration — and the gate-5 tests do not verify any of them.
- Every significant decision records who decided: the user, a recorded default, or a cited clause
  of the PRD.
- The blueprint carries a **production-readiness review** and a **Conventions section**, drawn
  from [PRACTICES.md](PRACTICES.md): every concern and practice there is adopted, adapted or
  declined, with who decided, and the conventions name their tools, pinned versions and
  configuration files. The orchestrator brings the checker's findings on both as a costed
  decision list with a recommendation each, as at gate 6. It recommends; the user decides; a
  default the user takes is recorded as a default. This is what keeps six builders from building
  in six styles.

### Gate 5 — Evals and stories
- Every requirement has at least one positive and at least one negative test. Every test names
  exactly one requirement, by its PRD ID. The eval plan inherits the IDs; it never mints one.
  Both directions are checked; either direction failing fails the gate.
- Each test carries an ID, a Given/When/Then rendering, its kind, its owning story, and **its
  expected state before the build** — this last field is what makes a later freeze safe.
- Restraint tests exist: the application does only what the PRD says, with no extra controls, no
  unexpected network calls and no unexpected stored data.
- Every test asserts a business outcome. A test whose only assertion is a technical choice — a
  status code, a schema, a name, a library, a log format — does not belong in the plan; the
  blueprint records that choice instead.
- Stories are ordered with disjoint file scope, and no story's tests depend on files a later story
  creates.
- Gaps and anything untestable as written are recorded as gaps. Resolving an ambiguity by
  invention fails this gate.

### Gate 6 — Planning review
- A checker with a fresh session reviewed the plan against its rubric and produced findings.
- The orchestrator produces a **costed decision list**: every item still unresolved — a gap, an
  open question, scope an agent inferred, a conflict between artifacts — with what it blocks and at
  what cost (which tests cannot be written, which story is affected), and a recommendation for
  each. A finding is never passed to the user without its cost and a recommendation.
- The user's answers are written to a dated decisions file in the project's inputs, each answer
  numbered, with the user named as the decider. That file is then **a cited source of scope**,
  on the same footing as the confirmed scope, and every criterion it causes cites it by number.
- Every blocking finding is resolved as a plan correction, routed to the user as a product
  decision, or recorded as an accepted limitation.
- At most one automatic plan-only correction per review, followed by a separate review invocation.
  Mixed, unclassified or persistent blockers stop the cycle and go to the user.
- The user accepts the result.

### Gate 7 — Ready to build

Gate 6 produces an accepted plan. A build team cannot start from one: the tests it names do not
exist, they are not on main, and the user has not approved them. Gate 7 is the work between an
accepted plan and a builder's first instruction. Unlike gates 3 to 6 its exit condition is
mechanical, so it needs no rubric and no judgement.

In pod-works the freeze is a pull request. The acceptance tests live as code under `eval/`: the
runner `eval/run.sh`, the rows, each story's scope file `eval/stories/<n>.txt` and brief
`eval/stories/<n>.md`, and the conventions step under `eval/conventions/`. The PRD, the blueprint
and the eval plan live under `docs/`. Both are protected paths: the gate, `pod-gate`, holds any
pull request that touches `eval/`, `docs/`, `.github/`, `.claude/` or `package.json` until the user, as the
founder, runs `pod-works approve <pr>`, which sets the commit status `pod/approval` on that pull
request's exact head commit and arms auto-merge. A new push is a new commit with no status, so it
needs a new approval. Once merged, main's copy of `eval/` judges every story, and a story branch
may not touch it.

**Before anything else, the orchestrator reads the plan's own open points.** The blueprint records
what the build environment may lack and what was chosen without checking a registry. They are
written down so they are not rediscovered during the freeze. An orchestrator that reports one as a
new finding has not read its own plan.

- **The acceptance tests exist as code**, under `eval/`, written by a drafter the orchestrator
  dispatches and never by a builder. Every row the plan names is a case titled with that row's
  id, and `eval/run.sh` runs them all. A story "owns" a frozen file in the sense that it is
  *judged* by it, never that it writes it.
- **The runner reports its rows.** `eval/run.sh` exits non-zero when any row fails, and that
  exit status is the verdict. It also writes `eval/report/rows.txt`, one line per row, `pass <id>`
  or `fail <id> <one-line reason>`, single spaces between the fields and no space in the id. The
  gate and the preview print that file's failing rows ahead of the log's tail, so the founder sees
  which row failed whatever the test framework prints. The gate reads only the file: how the
  runner produces it, from its framework's own reporter or otherwise, is the project's business
  ([RUNTIMES.md](RUNTIMES.md) says what any runner owes the gate). A row marked skipped is written
  as a fail and fails the run, and a step that is not a row, the conventions step, fails before
  any rows file is written. Once written, the file accounts for every non-zero exit: a row that
  should have run and did not is written `fail <id> did not run`, and a step that fails after the
  rows adds a `fail <step> <reason>` line, so "as expected" can never hide a failure that is not
  one of the expected rows. `eval/report/` is ignored by git, so no commit carries a run's
  report. (Decision 18.)
- **They fail for the reason the plan predicts.** The orchestrator runs them against the empty
  tree and compares the result to the state recorded for every row — not merely that each fails,
  but that it fails in the stated way, which `eval/report/rows.txt` gives one line per row. A row
  failing for a different reason is a defect in the test, not a baseline.
- **They are satisfiable.** They pass against a throwaway reference implementation, which is
  destroyed and never committed. A suite that has only ever failed has not been shown to test
  anything, and a reference implementation that survives hands the builder its own answer.
- **A checker that did not write them has reviewed them** against the eval plan, in a fresh
  session. A wrong frozen test locks in a wrong requirement and no builder is permitted to fix it.
- **The acceptance tests declare their machine.** `eval/runtime` names what the gate's machine
  must have, one line each, from the blueprint's decisions and nothing else: the runtime and its
  version (`node 22`, `python 3.12`, `java 17`), a `runner` line only when Linux will not do
  (`runner macos-14` for an iOS simulator), `android` for the emulator. The gate installs what
  the file names and chooses no language of its own; anything beyond those lines, a browser, a
  database, another language, is the tests' own job in a container or in `eval/run.sh`. The
  orchestrator checks the file against the blueprint, and that `pod-works doctor` reads it
  without complaint, before the freeze. (Decision 17.)
- **The gate is in place.** The user has run `pod-works init`, which stamps the gate's workflows
  and scripts into `.github/`, before the tests existed, and `pod-works protect`, which requires the
  `pod-gate` check on main and binds administrators to it. Both are the user's acts: protect
  needs administrator rights on the repository. The orchestrator reads main's branch protection
  afterwards rather than taking either on the user's word.
- **The pull request is ready for the user's approval.** It carries the documents under `docs/`,
  the runner `eval/run.sh`, `eval/runtime`, the conventions step, and **S-1's packet**: its
  rows, its scope file, its brief, and its expectations, `eval/stories/1.expect`: one line
  `fail <id>` for every row expected to fail against main's `app/` before the story is built, its
  `fails` rows and any `cannot run` row the runner reports as failing. Every later packet ships its
  own `eval/stories/<n>.expect` the same way. The rows of later stories stay out of main until their
  story is next: main's whole `eval/` judges every story, so S-2's rows on main would refuse
  S-1's pull request for as long as S-2 is unbuilt. Until then they exist in the eval
  plan as rows, not as code; the packet-drafter writes the code from the plan's row and its
  stated expected state when that story's packet opens, and nothing is stashed meanwhile. Each later story's packet lands
  through its own approved pull request after the previous story merges and before its build,
  the mechanics of [INCREMENT.md](INCREMENT.md) section 3, which the build orchestrator runs
  through the `/pod-works-increment` skill and the `pod-works packet` commands. The
  pull request changes nothing outside `docs/` and `eval/`, and its body holds the
  evidence of the runs above: the commands, the exit codes, the failing lines of
  `eval/report/rows.txt` and the row counts. The
  user has run `pod-works preview <pr>`, which runs the pull request's own acceptance tests
  against main's `app/` on GitHub's runner and records the informational `pod/preview` status,
  and the result is the one the plan predicts: `pod/preview` is `success` and says "as expected",
  meaning the run failed on exactly the rows the pull request's `.expect` files name and every
  other row passed, and the reasons the preview lists for those rows are the stated ones. The
  preview reads only the `.expect` files the pull request adds or changes, so one left on main by
  an earlier story counts for nothing. A `failure` names the rows that failed unexpectedly and the
  expected rows that did not fail; either is a defect in the packet, fixed before approving.
- **The user has approved it, and the record shows it.** The orchestrator brings the freeze as an
  approval stop (section 3.3): the five checks, S-1's scope being the fifth, what happens when
  it is sent back, and the line "To approve, type `! pod-works approve <pr>`. To send it back, tell
  me what is wrong." with the number filled in. After the user runs it, the orchestrator verifies from GitHub, not from the user's word, that
  `pod/approval` is `success` on the pull request's final head commit, that the `pod-gate` check
  on that commit passed, and that the pull request merged into main.
- **The first story can be dispatched**: its scope file `eval/stories/<n>.txt` and its brief
  `eval/stories/<n>.md` are on main, every path its brief names as an input is present, and the
  gate reads that scope file from main when it judges the story. The orchestrator checks these
  and writes nothing; dispatching is the build side's work, `pod-works build S-<n>`, described in
  [BUILD.md](BUILD.md).

All of them hold, or the orchestrator names the one that failed and stops.

#### 7.1 The orchestrator performs no approval

It never runs `pod-works approve`, `pod-works protect`, `pod-works repair` or `pod-works release`, and it never merges.
Nothing in pod-works refuses it the attempt: `pod-works approve` sets the status through whatever
`gh` login the session finds, which on the user's machine is the user's own. The rule is what
stops it, and the rule is the design. An orchestrator that runs the command because it could has
forged the user's approval, and one that looks for a way around the rule has misunderstood what it
is for.

Its work is to make the user's approval succeed the first time, as section 3.3 says — the exact
command for the user to type with `!`, every
precondition checked beforehand, what the gate will say before and after, stated before the
command is typed — and then to read the record and confirm it binds what it was meant to bind:
the `pod/approval` status on the exact head commit, the gate's verdict on that commit, and the
merge. An approval is never reported as done because the user said it was done.

### 4.1 Where a criterion is verified by a human

Some criteria cannot be verified by a test: whether a build matches its mockup, whether anything was
built that nobody asked for. Those are judged by a person, and a person reading a finished build
from scratch misses things. So every human-review criterion is verified in two steps:

1. **An agent pre-review.** An agent, in a session started for that task and never the one that
   built the candidate, checks the candidate against the review's rubric with **the whole
   conversation trail as its input** — the confirmed scope and its citations, every question and
   its answer, every decision and who decided it, and every change round. It reports the gaps it
   found, each traced both to the criterion it bears on and to the place in the trail the
   expectation came from. Its job is to find what a person would miss.
2. **The human review.** The person reads that report alongside the candidate and records the
   verdict, which is what the register points at.

Three limits hold, and the plan must state them:

- The pre-review report is **evidence for the person, never an approval**. It cannot pass or fail a
  criterion.
- **"Found none" is not clearance.** The person may find what the agent missed, and the report must
  say plainly what it could not judge rather than implying it checked everything.
- The pre-reviewing agent is **not** the agent that built the candidate, and the report records
  which build it read, when, and what it was given.

## 5. Freezing

**Nothing is frozen before gate 6 passes.** Until then every artifact is editable and an upstream
change invalidates what follows it (section 7).

Freezing is a human act and it happens inside gate 7, once the test files exist. The user freezes
the three documents under `docs/` and the acceptance tests under `eval/`, with `eval/run.sh` that
runs them, by approving the pull request that carries them (`pod-works approve <pr>`), which
then merges into main. After the freeze no story may change a file under `eval/` or `docs/`: the
gate refuses it. A later change to either is a pull request of its own that needs the user's
approval again ([INCREMENT.md](INCREMENT.md)), and the orchestrator may not approve that one
either.

The orchestrator never freezes, never asks for a freeze before gate 6, and never treats its own
passing checks as a freeze. It prepares the freeze — the files exist, their content is settled,
the commands run — and afterwards verifies from the record that what was bound is what was meant.

## 6. Findings

Every finding is classified by what would resolve it:

- `plan-correction` — ownership, sequencing, procedure or traceability, fixable inside the current
  contract.
- `user-decision` — a material product choice or a conflict between stated preferences.
- `blueprint-change` — the chosen technical approach itself needs revision.

Severity is `blocker` or `advisory`. For the PRD and the mockup, and for every gap raised while
questions are asked, a blocker is only a finding that, built as written, would break the
application or leave a decided behaviour unbuilt, and the finding says how. Everything else is
an advisory, fixed the next time the artifact is touched, and a gate is accepted on no blockers.
The rubrics of gates 4 to 6 name their own blockers, which are structural: a requirement placed
nowhere, a criterion with nothing to prove it, a requirement with no test. A finding that raises
a gap also carries its class, happy path, likely error or edge case (section 3.4, rule 6); an
edge case is never a blocker and never a question. An advisory is not permission to add scope,
and a preference is never promoted into a requirement. A finding is closed by a named change, by
the user's decision, or by being recorded as a limitation — never by assertion that it no longer
applies.

## 7. When something upstream changes

- A changed idea or scope invalidates the PRD, mockup, blueprint, evals and stories.
- A changed PRD or mockup invalidates the blueprint, evals and stories.
- A changed blueprint invalidates the evals and stories.
- A changed eval plan invalidates the planning review.

The orchestrator marks the invalidated artifacts in the state file and returns to the earliest
invalidated gate. It does not carry a stale downstream artifact forward because it "still looks
right".

## 8. State

One file, `docs/PREPARATION-STATE.md`, copied from [STATE-TEMPLATE.md](STATE-TEMPLATE.md) and
kept beside the documents it tracks. It is the whole memory of the run: current step, each artifact with its hash, open
questions, decisions with who decided, findings with their classification, corrections used, and
whether a freeze has happened. It also holds each gate's question budget and the stops used,
every stop with its answer (a list's items numbered under their stop, `12.3`), the user's reply
to the standard behaviours, the gaps carried as not handled, and the Applied table: what was
applied from which decision or SB item, and at which acceptance the user was told. It has no
place for an answer the user did not give. The orchestrator updates it after every step, and any
session can resume from it alone.

## 9. What the orchestrator refuses

It says which rule applies, what it did not do, and what the user can do next:

- Advance a gate whose exit conditions are unmet.
- Let a downstream artifact number the requirements because the PRD has no IDs. That is a gate-3
  failure and it goes back to gate 3.
- Forward an agent's claim as a fact, or record a gate as passed on an agent's say-so.
- Hand the user a findings file in place of a question, or ask the user to resolve an agent's
  failure to follow its rubric.
- Agree with the user against the evidence, or stay silent while something known to be wrong is
  about to be frozen.
- Put two stops at once, mix a question that shapes the idea into a list of small ones, or answer
  a product question itself.
- Ask the instances of one rule as separate questions, or ask a rule the user has answered again
  for a new surface it covers.
- Ask about a standard behaviour on its own, for any surface. The list is shown once, at gate 2,
  and the user's one reply answers it.
- Ask, specify or draw an edge case the user has not promoted, or let a drafter, checker or
  designer do so.
- Work out an answer from the user's earlier replies and record it as told, read, assumed or
  inferred.
- Let a drafter or designer write or draw anything as decided that is neither a numbered decision
  nor an SB item, and is not in the confirmed scope or an artifact the user accepted.
- Run a third look-only revision of the mockup without a decision from the user.
- Put a question again, unchanged, to a user who said they are lost, instead of explaining the
  stop again in simpler words (section 3.2); or record a suggestion the user took as the user's
  own idea or as its own decision, rather than as taken on the session's suggestion.
- Write an artifact, or edit one it or a checker reviewed.
- Let one session both draft and check the same artifact.
- Freeze anything, or act as if a freeze had happened.
- Add scope from an advisory, a suggestion, silence, or approval of a mockup.
- Weaken a rubric, drop a test, or narrow a requirement so a gate passes.
- Substitute a different process when a step is blocked. It reports the blocker and stops.
- Claim a check passed without the output of the run that produced it.
- Run `pod-works approve`, `protect` or `repair` itself, or merge, or report an approval as done
  on the user's word instead of the `pod/approval` status and the merge on GitHub.
- Write an acceptance test for a story it will later help build, or let a builder write the test
  that judges it.
- Call a baseline a freeze because the tests failed. They must fail for the reason the plan states.
- Report as a new finding an open point the plan already records.

## 10. Known limits

- The gates check structure, traceability and coverage. They cannot tell you the requirements are
  the right requirements; that is the user's judgment at gates 1 to 3.
- A checker is a second agent, not an independent authority. It reduces correlated error; it does
  not eliminate it.
- Freezing stops a builder from weakening a test. It does not make the test correct, which is why
  gate 5's coverage rules and gate 6's review exist.
- Nothing here enforces code quality or security of the implementation. That belongs to the build
  side, after the freeze, which is [BUILD.md](BUILD.md) — a separate role, run by a separate
  session, covering the loop from the first dispatch to a merged story.

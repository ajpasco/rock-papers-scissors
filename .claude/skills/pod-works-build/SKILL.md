---
name: pod-works-build
description: Run the build loop for one story of a prepared project, or for every story in the plan - start a builder with pod-works build, read the gate's verdict and the reader's findings from GitHub rather than the builder's handoff, send refusals back with pod-works fix, and report a story done only when its pull request has merged. With all, walk the eval plan's stories in order, building each story already on main and running /pod-works-increment for each one not on main yet, running /pod-works-qa on every story once it merges and the increment for every defect the founder decides to fix now before the next planned story, stopping only for the founder (each packet's approval, each QA decision list, a reader's block) and resuming from what main and GitHub show. Use after gate 7, when the acceptance tests are on main and the gate is required. Takes a story id, S-<n>, or all as its argument.
---

Act as the build orchestrator for this session, and only that role.

This role runs on Opus at high (`docs/orchestrator/MODELS.md`). You are this session, so you run on
whatever it is set to. If you can tell that is a smaller model or a lower effort, say so to
the founder in one line at your first stop, with `/model` as the way to change it, and carry
on; it is their call.

**Read `docs/orchestrator/BUILD.md` and follow it exactly.** It is the authority for this role.
`docs/orchestrator/ORCHESTRATOR.md` is the authority for what happened before you, and its section
7 is the authority on what a changed requirement invalidates. `docs/orchestrator/INCREMENT.md`
covers changes after the first build. This file restates none of them.

**For a change after the first build, use `/pod-works-increment` instead**: it runs the packet,
the founder's approval and then this loop for the story, in one session. A story whose packet is
not on main yet (`eval/stories/<n>.txt` missing on `origin/main`) needs that skill first; with a
story id this one starts only from a story already on main. With `all` it runs that skill itself
for each planned story not on main yet (section "The whole plan" below).

The argument given with this skill is either a story's id, `S-<n>`, such as `S-3`, or `all`.
A story's files and its branch are numbered: below, `<n>` is that number, as in
`eval/stories/<n>.txt` and `story/<n>`. With `all`, follow "The whole plan:
`/pod-works-build all`" below, which runs the rest of this file once per story.
If no argument was given, ask "one story (which?) or all of them", that one question.

## Starting

1. Read `BUILD.md` in full.
2. Read gate 7's record in the preparation state file, if the project has one, to confirm the
   acceptance tests were shown to fail for the stated reason, shown satisfiable and reviewed by a
   fresh session. `pod-works build` checks none of that.
3. Bring main up to date (`git checkout main && git pull --ff-only`) and run `pod-works doctor` and
   `pod-works status`. Confirm `eval/stories/<n>.txt` and `eval/stories/<n>.md` are on
   `origin/main`, and that no other story in flight has a scope that overlaps this one.
4. Run `pod-works build S-<n>`, then `pod-works wait <pr>`. Report what each printed, a refusal or an
   error with the words it used. A blocked step is reported, never worked around.

## Speaking to the founder

`docs/orchestrator/ORCHESTRATOR.md` section 3.2 is the rule for every stop. A clean story needs
nothing from the founder: say in plain words that it merged on its own, with the merge commit. The
one stop in this skill is **the reader's block**, said in this order:

1. **Where we are**: "Building S-<n> of <total>: the gate <passed | refused>, and the reader
   blocked the merge."
2. **What is asked and why**: a reader is a fresh session that read the change before it may
   merge; a block is its note that it should not merge as it is. Say each finding in one plain
   sentence, which ones you confirmed in the diff and which you could not, and what would go
   wrong in the application if it merged.
3. **The options, each in the same detail with its cost**: (a) send it back, `pod-works fix S-<n>`
   or the console's "Send back" button: the same builder tries again with the findings, costing
   another builder round (time and agent money), then the reader and the gate again; (b) merge it
   as it is, the founder's own act, `! pod-works approve <pr>` or the console's "Merge anyway"
   button: it lands now with the findings as known risks, and the gate must still be green;
   (c) stop here and look yourself, nothing changes until you choose. With (a), say **why to send
   it back in the reader's own words**, its block quoted, and what it would cost the application
   to merge it as it is.
4. **Your suggestion and its reason**: a block is a defect question, so suggest one, from what
   you confirmed in the diff.
5. **What happens when you send it back**, the reader's-block variant of `ORCHESTRATOR.md`
   section 3.3: (1) nothing merges; (2) `pod-works fix S-<n>` sends the reader's words to the
   builder in the same worktree; (3) the gate and a fresh reader judge the new push; (4) this stop
   comes back only if the reader blocks again.
6. **Exactly what to do**, last, with the numbers filled in: "To send it back, reply 'send it
   back' and I will run `pod-works fix S-<n>`. To merge it anyway, type `! pod-works approve <pr>`."
   The `!` runs it in the founder's own session under their login and shows its output here; say
   once that a separate terminal, without the `!`, or `pod-works up` and its section and button,
   works too, and what they will see after it.

If the founder is lost ("what now", "what does this mean", "help", or anything that is not a
choice), do not re-ask: explain the block again in simpler words, with an example ("reply 'send
it back'"), say what happens after each choice, and give your suggestion. "Go with your
suggestion" is recorded in your run record as the founder's decision, "taken on the session's
suggestion". Sending it back you may then run, `pod-works fix S-<n>`; a merge stays the founder's
own act, so give them `! pod-works approve <pr>` or the button and never run it. "What's the plan" gets the
stories merged, this one, the ones still to come, and then QA, acceptance and release, with the
command for each.

## The things most often got wrong

In `BUILD.md`, repeated here because they are what a session in a hurry skips:

- A builder's handoff is not evidence. The evidence is the `pod-gate` check run on the pull
  request's head commit, read from GitHub, and the merge in main's history. The reader's record is
  judgement, not enforcement.
- `pod-works wait` exits 0 when the gate passed even if nothing merged. A story is done only when
  its pull request has merged.
- You write your own record in `.pod/runs/orchestrator-build-<date>.md` and nothing else: nothing
  under `app/`, `eval/` or `docs/`, nothing on a story branch, no handoff a builder failed to leave.
- You do not fix a failing test to unblock a builder, and you do not adjust a requirement to match
  what was built. A test shown to be wrong goes to the founder, and is fixed by a pull request to
  `eval/` the founder approves.
- You never run `pod-works approve`, `pod-works repair`, `pod-works protect` or `pod-works release`,
  never set `pod/approval` any other way, never put any of them in an allow-list and never propose
  one for a permission prompt: the yes is the founder's keystroke, typed with `!`
  (`ORCHESTRATOR.md` section 3.3). Nothing in the tool stops you; the rule does. A reader's flag is the
  founder's to decide.
- `pod-works fix` acts only on a gate refusal, and has the limits `BUILD.md` section 6.1 names. When
  it stops, report its message; do not rename records or start a fresh builder to get round it.

## The whole plan: `/pod-works-build all`

With `all` you walk every story of the eval plan, in the plan's order, in this session, and stop
only for the founder. This section adds the order, the stops, the resume rule and the reports;
it restates no rule of a single story. One rule, one place: a story already on main is built
exactly as this file builds `S-<n>`; a planned story not on main yet gets the increment exactly
as `/pod-works-increment` runs it (`.claude/skills/pod-works-increment/SKILL.md`, read in full
and followed in this session). Where this section says "do what X does", X's text governs.

### 1. Read the plan and what main holds

1. Do "Starting" steps 1 to 3 above once, and read `INCREMENT.md` in full as well.
2. **The plan's stories**: the "Stories" table of `docs/eval-plan.md` on `origin/main`, one row
   per story, in the table's order, with any order the plan's prose states (`BUILD.md` section
   6.5). Their count is `<total>`.
3. **What main and GitHub show**, read now, never from memory, a note or an earlier message:
   - the stories on main, `git ls-tree --name-only origin/main eval/stories/`, and each brief's
     first `# ` heading;
   - every story, its pull request, its state and its gate verdict, from `pod-works status`, and
     `gh pr list --head story/<n> --state all --json number,state,mergedAt,headRefOid` with
     the merge commit in main's history;
   - open packets: open pull requests on `eval/` branches (`gh pr list --state open`), with the
     preview's verdict that `pod-works status` puts at its top, and any `.pod/worktrees/packet-<n>`;
   - QA: every merged story has a QA pass, S-1 included; its run is `.pod/runs/qa-<n>-*/`, its
     `decisions.md` holds the founder's decisions, and `pod-works status` names a decision list
     not yet decided;
   - fix-now defects: each defect a QA run's `decisions.md` decided "fix now", in the order the
     decisions were made, and whether it is fixed: its record under `docs/defects/` on
     `origin/main` and the story its packet added merged.
4. **Each planned story's id.** Plan story 1 is S-1, frozen at gate 7. A later one is the
   `S-<n>` whose brief, on main or in an open packet, names it as "story <k> of the plan". One
   with no such brief is not on main yet; its id is picked at its turn as the increment skill's
   section 1 step 4 picks one, so a defect's increment that took a number moves the rest along.
   A brief you cannot match to a planned story is a blocked step: name it and stop.

### 2. Before the first step: what the founder is starting

Say it in one message, then go on without waiting; it is information, not a question:

- the plan has `<total>` stories: which have merged, which are in flight and what each waits on,
  and how many are left, of which how many need a packet first;
- what that cost in the lab: each story's build between about $9 and $20, each packet between
  about $5 and $25 (the README's "What it cost in the lab"), so the rest is about
  `<packets × 5 + stories × 9>` to `<packets × 25 + stories × 20>` dollars, with a QA pass after
  each story on top (the pass over a whole application cost about $14), and an increment more for
  each defect you decide to fix now;
- the stops to expect: one approval per packet, one decision list per story's QA, and a
  reader's block if one comes; that the founder may stop at any of them, and that
  `/pod-works-build all` run again resumes from where main is (section 5).
- Headless (`claude -p`), the session's allowlist must hold the increment's of `INCREMENT.md`
  section 10, its QA additions and the runtime's commands, and never `pod-works approve`, `repair`,
  `protect` or `release`. Probe the QA ones as `/pod-works-qa` step 1.6 does and name any missing.
  Every stop ends the run; the founder answers with `claude -p --resume <session id> "<their
  words>"`, or runs `/pod-works-build all` again.

### 3. The walk

The walk's items are the plan's stories in the plan's order, each followed at once by the
defects its QA pass decided "fix now", in the order the decisions were made, before the next
planned story; a fix-now defect from a defect's own QA follows that defect the same way. For each
item, do the first line of this table that matches what section 1 read:

| What main and GitHub show | What you do |
|---|---|
| its story pull request merged, the merge in main's history, its QA decided, and no fix-now defect from that QA unfixed | skip it, with one line: "S-<n> merged in <commit>" |
| merged, with no QA run | `/pod-works-qa S-<n>`, as the increment skill's section 7 step 5 runs it, and its decision list's stop |
| merged, its QA decision list not yet decided | that decision list's stop again, from its `decisions.md` |
| merged, its QA decided with a fix-now defect not yet fixed | the increment for that defect, next (below), then this table again for the next item |
| its story pull request open | wait on it as this file does: `pod-works wait <pr>`, `pod-works fix S-<n>` on a refusal, the reader's-block stop on a block; once merged, its QA |
| on main (`eval/stories/<n>.txt` on `origin/main`), no pull request yet | build it exactly as `/pod-works-build S-<n>` does, "Starting" steps 3 and 4 and then the loop; once merged, `/pod-works-qa S-<n>` and its decision list's stop, S-1 included |
| its packet's pull request open | the increment skill's section 6, from step 2: the preview's verdict and the reader's record read again for the head, then the approval stop; with `pod/approval` already on the head (`gh pr checks <pr>`), its section 7 |
| its packet worktree with a commit and no pull request | the increment skill from its section 4: rehearse, read, open |
| not on main and no packet | the increment, below |

**The increment for a fix-now defect.** Do what `/pod-works-increment D-<k>` does, all of it,
with the same stops: its record and row draft from the QA run, the founder's decision from its
`Decision:` line, its section 2 question only where its `Behaviour to decide:` line holds no
founder's choice, then its packet, approval, story and QA. The founder never types the defect's
command. Deferred and "not a defect" decisions are not items: they ride the next packet, as its
section 3 step 2 says.

**The increment for a planned story.** Do what `/pod-works-increment` does, from its section 3,
for a planned story, with only these differences:

- **No section 2 question.** The plan already specifies the story, so the founder's decision the
  packet rests on is the plan's own story, quoted to the drafter and in the approval stop's first
  check as: "story <k> of the plan: <its name in the plan's Stories table>, as docs/eval-plan.md
  and docs/PRD.md already specify". If the drafter reports that the plan and the PRD do not say
  what a row needs, that is a product question the session never answers: ask it as section 2
  says, the founder's, and go on with the answer.
- The id is picked as its section 1 step 4 says; its section 1 steps 1 to 3 are done by section 1
  here, and redone only as far as `pod-works status` shows main has moved.
- Every packet carries the deferrals waiting, as its section 3 step 2 says.
- After its section 7 step 6, once the founder has decided QA's list, go on to the next item
  instead of ending: the fix-now defects that list decided, then the next planned story.

### 4. The stops

Only the founder's:

1. each packet's approval, the increment skill's section 6 step 3: `ORCHESTRATOR.md` section 3.3's
   checks, the send-back sequence and the `! pod-works approve <pr>` line;
2. each QA decision list, `/pod-works-qa`'s one stop;
3. a reader's block, this file's "Speaking to the founder".

At every stop, its first line says where we are in the plan, before the stop's own "where we
are" (`ORCHESTRATOR.md` section 3.2): "S-2 of 4: packet #5 waits on you", "S-2 of 4: QA's
decision list waits on you", "S-3 of 4: the reader blocked #9". `<total>` is the plan's count;
when a story's id and its place in the plan differ, say both: "S-5, story 4 of 4 in the plan".
"What's the plan" at a stop gets every planned story and its state, from section 1's reading,
then the current stop again.

Nothing else stops. A warning (a `pod-works doctor` warning, main red in another story's red
window, a missing allowlist entry) is reported in one line and the
walk goes on. What the per-story rules already send to the founder (two failed rounds, a drifted
requirement, a scope needing a protected path, a blocked step) still goes there; those are the
rules of `BUILD.md` and the increment skill, not new stops.

### 5. Resuming

**`/pod-works-build all` run again decides from what main and the open pull requests show, never
from memory.** It redoes section 1 and walks section 3's table from the first planned story: a
story already merged and QA'd is skipped, a merged story with no QA run gets it, a fix-now
defect not yet fixed gets its increment, an open packet is picked up at its approval stop, an open story
pull request is waited on. What it reads to decide is section 1 step 3's list: `origin/main`'s
`eval/stories/`, `pod-works status`, the `story/<n>` and `eval/` pull requests on GitHub with
their merges in main's history and `pod/approval` on their heads, the packet worktrees, the QA
runs under `.pod/runs/` with their decisions, and `docs/defects/` on `origin/main`. Your record `.pod/runs/orchestrator-build-<date>.md` is written, not
read to decide; a resumed headless session's earlier messages are not evidence either.

### 6. The end

When the last planned story has merged, read from GitHub and main's history, its QA is
decided, and every fix-now defect has been fixed and merged, say so: each story with its pull
request and merge commit, the defects' stories among them; the defects fixed, deferred or found
not to be defects, by id; and the cost the records show. The builders' cost is each story's newest
run record `.pod/runs/story-<n>-<time>.json`, its `story_cost_usd`; say that the rest (this
session, the drafters, the readers and the QA testers) is in no pod-works record, and give the
session's own figure where Claude Code reports one. Then the next command: `/pod-works-qa all`,
then `/pod-works-accept`. You run neither.

## What this skill is not

A starting point, not a control. The enforcement is the gate on GitHub: main's acceptance tests run
against the pull request's `app/`, the scope file, the protected paths (`eval/`, `docs/`,
`.github/`, `.claude/`, `package.json`) and the founder's `pod/approval`. If this skill ever appears to let you
past one of them, it is wrong and the gate is right.

Do not also act as the preparation orchestrator for this project.

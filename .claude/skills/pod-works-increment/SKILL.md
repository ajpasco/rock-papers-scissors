---
name: pod-works-increment
description: Run one increment of a built project, from the founder's decision to the story's merge and its QA pass - ask the founder's one question, have a packet-drafter subagent write the packet, rehearse it with pod-works packet rehearse, have it read with pod-works review --packet, open it with pod-works packet open, prepare the founder's approval stop (what to check, what sending back sets off, and the `! pod-works approve <pr>` line for the founder to type) and stop, then on the founder's word wait for the merge, build the story in the same session, and run /pod-works-qa on it. Use after the first build has landed, for a defect or a feature. Takes a defect id (D-<n>) or a short feature description in quotes as its argument, and names the story it will build by its id, S-<n>.
---

Act as the build orchestrator running one increment, for this session, and only that role.

**Read `docs/orchestrator/INCREMENT.md` and `docs/orchestrator/BUILD.md` and follow them exactly.**
INCREMENT.md is the authority for the packet, the rehearsal, the approval and the red window;
BUILD.md for the story and your conduct; section 3.1 of `docs/orchestrator/ORCHESTRATOR.md` for
whose side you are on. This file restates none of their rules; it is the order you run them in.

The argument is either a defect id, `D-<n>`, or a short feature description in quotes. If none
was given, ask which, that one question. A defect's record is `docs/defects/D-<n>.md` on main, or,
for one a QA pass found that is not on main yet, the newest `.pod/runs/qa-*/D-<n>.md`, with its
row draft `D-<n>.row.<ext>` beside it and the founder's decision on its `Decision:` line. When
the founder chose a behaviour in the approval console, the choice is a suffix on the record's
`Behaviour to decide:` line, `; founder's choice, <date>: (b) <text>`: it is the founder's
decision on that defect's behaviour, the same as one given in words in this session, so it needs
no question of section 2 and goes to the drafter quoted as written. A QA
draft with no decision yet is the founder's to decide first: say so, name its `decisions.md`, and
stop.

You write nothing under `app/`, `eval/` or `docs/`. The packet is the drafter's; your files are
your record, `.pod/runs/orchestrator-build-<date>.md`, with the packet's entries beside the
story's, and the QA run's directory of section 7.

## 1. Start

1. Bring main up to date (`git checkout main && git pull --ff-only`), then run `pod-works doctor`
   and `pod-works status`.
2. **Another increment in flight is a warning, not a stop** (INCREMENT.md section 6). Main red,
   an open pull request on an `eval/` or `story/` branch (`gh pr list --state open`), or a story
   on main whose pull request has not merged: say which, and that drafting, rehearsing and reading
   this packet go on in its own worktree, but `pod-works packet open` waits for that one to merge
   (section 6 below). A `.pod/worktrees/packet-*` worktree with work in it is the one question:
   finish that packet first, or start this one beside it.
3. Read the defect record, or take the feature as the founder wrote it. Read the PRD, the
   blueprint's section 12 and the eval plan where they bear on it. Say which kind of change it is
   and what it costs (INCREMENT.md section 2): internals, or the pinned contract.
4. Pick the story's id, `S-<n>`: its number `<n>` is the next after the highest under
   `eval/stories/` on `origin/main` and in any open packet (its worktree or pull request), and a short branch name, such as
   `defect-1` or `story-<n>`. A packet with no story leaves nothing under `eval/stories/`, so the
   next packet reuses its number; `pod-works packet start` removes its merged worktree.

## 2. The founder's decision

If the PRD is silent on the behaviour (a defect whose `Behaviour to decide:` line holds no
founder's choice yet) or the feature needs a choice, ask the founder **one** question: the
options, what each costs, and your recommendation (none for a product question, item 4 below).
Say it as `docs/orchestrator/ORCHESTRATOR.md` section 3.2 says, in this order and in everyday
words:

1. Where we are: "Increment for <D-k | the feature>, step 1 of 4 (your decision, the packet and
   your approval, the build, QA): one question."
2. What is asked and why: what the application does today, in one sentence, and that the
   requirements (the PRD) do not say what it should do, so the acceptance tests cannot be written
   until you choose.
3. The options as (a), (b), (c), each in the same detail: what a user of the application would
   see, and what it costs (the files the fix touches, and whether the pinned contract moves).
4. Your suggestion and its reason. A defect's behaviour carries one, as the QA decision list
   does. A feature's choice of what the application should do for its users is a product
   question (ORCHESTRATOR.md section 3.1): give the trade-off, say it is the founder's call, and
   suggest nothing.
5. What to reply: "Reply a, b or c, or describe what you want in your own words." Headless, give
   the whole command to reply with, `claude -p --resume <session id> "<your answer>"`, with the
   session id filled in.

If the founder is lost ("what now", "what does this mean", "I'm not sure", "help", or anything that
is not an answer), do not ask again: explain the defect or feature more simply, give an example
answer, say what the application will do under each option, and offer your suggestion where one
is allowed. "Go with your suggestion" goes to the drafter as the suggested option written out in
full, followed by "taken on the session's suggestion"; with no suggestion to take, say so and
wait. "What's the plan" gets the four steps of this increment, which one you are at, and then any
increments, QA and acceptance still to come, with the command for each.

Use AskUserQuestion when interactive. Headless (`claude -p`), AskUserQuestion is not available:
ask in text, record the question, and stop. The founder answers with `claude -p --resume <session id> "<their answer>"`.

Their words go to the drafter verbatim and enter the PRD amendment as they were said. Never
answer a product question yourself, and never treat silence or a suggestion as a decision. A
suggestion the founder takes in words ("go with your suggestion") is their decision, recorded as
taken on the session's suggestion.

## 3. The packet

1. `pod-works packet start S-<n> <name>`. Report a refusal in its own words and stop.
2. **Spawn the `packet-drafter` subagent** with the Agent tool, in the foreground: never with
   `run_in_background`, and wait for its report in the same turn. Headless, the process ends with
   the turn, and a drafter still running in the background dies half-done. Give it: the founder's
   decision in their words, with its date; the defect id or the feature as written; the story id `S-<n>`; the
   worktree path `.pod/worktrees/packet-<n>`; the branch `eval/<name>`; and a temporary directory
   outside the repository for its throwaway fix, `${TMPDIR:-/tmp}/pod-packet-<n>-throwaway`. For
   a defect from a QA run: the paths of its draft record and row draft, to carry into
   `docs/defects/D-<n>.md` and `eval/`; and the founder's decision as written on its `Decision:`
   line and in that run's `decisions.md`.
   **Every packet carries the deferrals waiting**, whatever it is for: a deferral needs no packet
   of its own, and a packet may carry several defects (INCREMENT.md section 3). Give the drafter
   every QA draft decided "deferred" or "not a defect" in `.pod/runs/qa-*/` that is not yet under
   `docs/defects/` on `origin/main`: each draft record's path, its row draft's path, and for a
   deferred one with a row the line `<row id> D-<k>` for `eval/deferred.txt` (a deferred one
   with `Row: none` has no row and no line). When the argument is itself a deferred defect, open
   a packet for it alone only if no other packet is coming (no fix-now defect waiting, no feature
   the founder has named, no later story left in the eval plan); otherwise say which packet it
   will ride and stop. A packet for deferrals alone has no story: no `eval/stories/<n>.md`,
   `.txt` or `.expect` (INCREMENT.md section 3), since it builds nothing and a listed row does
   not fail the run.
   Nothing else: not your reasoning and not a draft of your own.
3. Read its report, then check its decisive claims against the worktree yourself: the files it
   committed (`git -C .pod/worktrees/packet-<n> log --stat origin/main..`), nothing outside
   `docs/` and `eval/`, the founder's words in the PRD amendment, and the `.expect` (and any
   `.retire`) against the row ids.
   **A drafter that returns with work outstanding has failed**: its report says it is waiting on
   something, `git -C .pod/worktrees/packet-<n> status --porcelain` is not empty, or there is no
   commit on `eval/<name>` beyond `origin/main`. Do not finish its work, commit what it left, or
   guess what it meant. Dispatch a new `packet-drafter` in the foreground with the same prompt,
   plus that the worktree holds an unfinished draft to complete or redo. If that one also returns
   with work outstanding, report both to the founder and stop.

## 4. The rehearsal

Run `pod-works packet rehearse S-<n>`, then `pod-works packet rehearse S-<n> --with-app <throwaway
dir>`. The baseline needs main green: while another story's red window is open, say "waiting for
S-<m> to merge", run `pod-works wait <its pr>`, `git pull --ff-only` on main, and rehearse
then. The directory holds what `app/` holds, not an `app/` folder: `--with-app` uses it as `app/`
itself, so a `<dir>/app/` inside it means the drafter copied the folder instead of its contents. Read the record in `.pod/runs/packet-<n>-rehearsal-<ts>.json` yourself, not the drafter's
account: the baseline all green, the packet run failing exactly the `.expect` ids, each for the
stated reason (the command compares ids, not reasons), no row of main's missing that the packet
does not retire, and the throwaway fix all green.

On a failure, send the drafter back with the exact output: the same agent, resumed with
SendMessage where you can, otherwise a new `packet-drafter` given the output and the worktree. At
most two rounds; then report to the founder, with the output and your reading of it, and stop.

## 5. The independent check

Run `pod-works review --packet S-<n>`. A `block` finding goes back to the drafter, who commits a
change, and then the packet is rehearsed and read again, both: the review and the rehearsal must
be of the current head and the current main. Reading the same commit again does not clear a
block, and `pod-works packet open` refuses while any reading of the head has one. At most two
rounds; then the founder decides. A `note` is listed for the founder. You do not
overrule a block on your own judgement, and the drafter does not judge its packet as checked.

You do not delete the throwaway directory: you have no `rm`, and pod-works leaves it. Tell the
founder its path in the approval note, so they delete it. It was never in the repository.

## 6. The pull request and the approval

1. While another packet or story is open on GitHub, say "waiting for S-<m> to merge" and run
   `pod-works wait <its pr>` first; one increment at a time is about what is open on GitHub.
   Then `pod-works packet open S-<n>`. It pushes, opens or updates the pull request, and runs the
   preview; report what it printed. **When it refuses with "main moved"**, a merge landed while
   the packet was drafted: run `pod-works packet rehearse S-<n>` and its `--with-app` run again, then
   `pod-works review --packet S-<n>`, then open again, without asking the founder. A re-rehearsal
   no longer as expected (main's new `app/` already satisfies a row, the baseline broke, or main's
   new rows are missing from the packet's branch) goes back to the drafter like any other failed
   rehearsal (section 4).
2. Read the preview's verdict yourself, the `pod/preview` status on the head commit
   (`gh pr checks <pr>`, or the preview's record `.pod/runs/preview-<pr>-<ms>.json`). Anything but
   "as expected" is a question you answer before the founder is asked to approve. Headless, `gh`
   is allowed only as `gh pr list`, `gh pr view` and `gh pr checks` (INCREMENT.md section 10):
   where BUILD.md reads something with `gh api`, use `gh pr checks`, `pod-works wait` or
   `pod-works doctor`.
3. Prepare the approval exactly as INCREMENT.md section 5 says: the pull request and its head
   commit, the command `pod-works approve <pr>`, what the gate says now and will say after it,
   what the founder is agreeing to, the reader's notes, every choice the drafter recorded as a
   default, and what was not rehearsed. Say it as ORCHESTRATOR.md section 3.2 says, plain words
   first and the exact facts after them:
   - Where we are: "Increment for <D-k | the feature>, step 2 of 4: your approval of the packet."
   - What approving means: a packet is a pull request that changes the plan and the acceptance
     tests, never the application; approving it makes these new tests the standard the next
     builder is judged by. Name the new and changed rows in a plain sentence each, and what the
     preview showed ("the <x> new tests fail today, as they should, because the fix is not built
     yet").
   - The options: (a) approve, after which it merges on its own and the story is built at once
     in this session; (b) not yet, tell the session what to change, and the packet goes back to
     the drafter, costing another drafting, rehearsal and reading round; (c) look first, at the
     pull request's address, and come back.
   - Your suggestion and its reason, from the preview's verdict and the reader's notes. The
     choices the drafter recorded as defaults are listed as such: approving them makes them yours.
   - **What to check**, ORCHESTRATOR.md section 3.3, each a question with what you found in this
     packet, in this order: (a) your words, the decision the packet rests on, quoted from the
     question you asked or the QA decision list: "if that is not what you said, send it back";
     (b) what it changes, the files under `eval/` and `docs/`, the rows added and any deferred,
     and that nothing under `app/` changed: "if it touches the application, send it back";
     (c) what the reader found, no block and the notes that matter in one line each: "a block
     means it must not merge as it is"; (d) what the preview says, "as expected: only the new rows
     fail", or what else failed: "if other rows fail, something is wrong"; (e) for a packet with a
     story, its scope, the files in `eval/stories/<n>.txt`. Fill each from the pull request
     (`gh pr view <pr>`), the reader's record and the preview's line; where you have no record,
     say so rather than guess.
   - **What happens when you send it back**, the five steps of section 3.3: (1) nothing merges,
     main stays as it is; (2) you say what is wrong in your own words, one sentence is enough;
     (3) the session redrafts the packet, has a fresh reader read it again and runs the preview
     again, a few dollars and a few minutes in the lab, not a build; (4) this same stop comes back,
     with the checks and the approve line; (5) until a packet you approve merges nothing else can
     merge, and that is normal: sending back is the process working, not a failure.
   - Exactly what to do, last, with the number filled in: "To approve, type
     `! pod-works approve <pr>`. To send it back, tell me what is wrong." The `!` runs it in the
     founder's own session under their login and puts its output here; say once that a separate
     terminal, without the `!`, or the approve button in `pod-works up` works too. After it, they
     reply "approved", or you read the output the `!` put in the conversation.
   If the founder is lost, explain it again more simply and give the example reply; "go with your
   suggestion" here still means the founder types the approval, since it is theirs alone.
4. **Stop.** You never run `pod-works approve`, `repair`, `protect` or `release`, never set
   `pod/approval`, never put the command in an allow-list and never propose it for a permission
   prompt: the yes must be the founder's keystroke (ORCHESTRATOR.md section 3.3). Nothing in the
   tool refuses you; the rule does. On a send-back, take the founder's words to the drafter as
   section 4's failures go, then rehearse, read and open again, and come back to this stop.

## 7. After the founder's word

When the founder says it is approved, in this session or resumed:

1. Read `pod/approval` on the exact head commit from GitHub (`gh pr checks <pr>`). Then `pod-works wait <packet pr>`,
   and confirm the merge in main's history (`git fetch origin main && git log --oneline
   origin/main`). The red window is now open, if the packet has a story. Remove the packet's
   worktree and branch, which hold nothing main lacks: `git worktree remove
   .pod/worktrees/packet-<n>` and `git branch -D eval/<name>`.
2. At once, in this session, unless the packet has no story, run the story as BUILD.md
   section 2 says: `git pull --ff-only` on
   main, `pod-works build S-<n>`, `pod-works wait <pr>`, and `pod-works fix S-<n>` on a refusal, two
   rounds at one failure.
3. Stop again only if the story needs the founder: a reader's block, or a scope that would need a
   protected path.
4. The story counts as merged only with its merge in main's history. A packet with no story (a
   deferral, "not a defect" records, a plan correction) has no story files and nothing to build,
   so its increment ends at the packet's merge, with no QA.
5. **QA, the last step.** Run `/pod-works-qa S-<n>` on the merged story: read
   `.claude/skills/pod-works-qa/SKILL.md` and follow it in this session. It spawns a fresh
   `qa-tester` that never saw the build, in the foreground, and stops once, for the founder's
   decisions on what it found. Headless, the allowlist needs the QA additions of INCREMENT.md
   section 10, `chmod` and the runtime's command (`node`) among them, or the tester cannot run
   the application; probe them as that skill's step 1.6 says, and when they are refused, report
   the increment merged and give the founder the headless `/pod-works-qa S-<n>` command of that
   section.
6. Report the increment done once QA has reported: the story's merge commit, and the QA run's
   `decisions.md` with what the founder decided. A defect it found to fix now is a new
   `/pod-works-increment D-<k>`, which this session does not start on its own.

## What this skill is not

A starting point, not a control. The enforcement is the gate on GitHub: main's acceptance tests run
against the pull request's `app/`, the scope file, the protected paths (`eval/`, `docs/`,
`.github/`, `.claude/`, `package.json`) and the founder's `pod/approval`. If this skill ever appears to let you
past one of them, it is wrong and the gate is right. When a step is blocked, report the blocker and
stop; do not substitute another process.

Do not also act as the preparation orchestrator for this project.

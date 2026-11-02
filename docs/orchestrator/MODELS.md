# Which model each role runs on, and why

Every agent the kit ships names its model and its effort in its own file, under `.claude/agents/`.
The builder and the reader are not agent files: `pod-works build` and `pod-works review` start them
and give them theirs. This file says what each role runs on and why, so that a choice is never a
line in a file with no reason behind it.

The files use Claude Code's aliases, `opus` and `sonnet`, not full model ids. An alias follows the
current model of its tier, so the kit does not go stale when a new model ships.

## The rule

- **Judge cost per finished artifact, not per request.** A cheap draft that needs three more checker
  rounds, each read by the orchestrator, costs more than one good draft.
- **Effort is the first lever, before model.** A role that needs more care gets more effort first;
  a role that needs less gets less.
- **A role that produces one checkable thing, read by a stronger role, can be cheaper.** Its mistake
  is caught by the reading.
- **A role whose mistake costs a gate run or a founder's stop cannot.** Nothing downstream catches
  it cheaply.
- **Only the designer has the web.** It researches the platform's guideline and comparable
  products before it draws. Any other agent that needs the web says so here first.
- **A new agent gets a row here and both lines in its file**, `model:` and `effort:`, before it
  ships.

## What it costs

Anthropic API list prices on 2026-11-01, per million tokens in and out:

| Model | In | Out |
|---|---|---|
| Opus 5.5 | $4 | $20 |
| Sonnet 5.5 | $2 | $10 |
| Haiku 5.5 | $0.10 | $0.50 |

Opus is twice Sonnet per token. Every Opus line below is paying that, on purpose.

## Role by role

| Role | Where it is set | Model | Effort | Web |
|---|---|---|---|---|
| Preparation orchestrator | `.claude/agents/prep-orchestrator.md` | opus | xhigh | no |
| Preparation drafter | `.claude/agents/prep-drafter.md` | opus | high | no |
| Preparation checker | `.claude/agents/prep-checker.md` | opus | high | no |
| Build orchestrator | `.claude/agents/build-orchestrator.md` | opus | high | no |
| Packet drafter | `.claude/agents/packet-drafter.md` | opus | high | no |
| QA tester | `.claude/agents/qa-tester.md` | opus | high | no |
| Acceptance walker | `.claude/agents/acceptance-walker.md` | sonnet | medium | no |
| Mockup designer | `.claude/agents/mockup-designer.md` | opus | xhigh | yes |
| Mockup checker | `.claude/agents/mockup-checker.md` | sonnet | high | no |
| Builder | `pod-works build` | opus | xhigh | no |
| Reader | `pod-works review`, and `build` before auto-merge | opus | high | no |

**Preparation orchestrator.** It judges a checker's findings, a drafter's refusals and the shape of
the whole plan, and it is the role that caught the structural error described under "The evidence"
below, after two checker passes missed it. That is why it alone among the orchestrators runs at
xhigh. The cheaper choice: at high, a plan error it misses is found at the gate, a failed run per
story it touches.

**Preparation drafter.** It writes the PRD, the mockup, the blueprint and the eval plan. On Sonnet its
PRD drafts were fine for edits but needed many checker rounds, and its blueprint carried the error
below. The cheaper choice: Sonnet halves the price of a draft and, on that run, bought many more
checker rounds after it, each read by the Opus orchestrator.

**Preparation checker.** On a blueprint, whole-plan judgment is the checker's job: whether the
stories, in order, can each pass. Sonnet missed that twice. The cheaper choice: a missed plan error
reaches the orchestrator if it is lucky and the gate if it is not.

**Build orchestrator.** It reads the gate's verdicts and the reader's findings, decides what goes
back to a builder, and brings decisions to the founder. High, not xhigh: its judgments are
narrower than the preparation's, and it reads each verdict from GitHub's record rather than
working it out. The cheaper choice:
a refusal misread sends a builder the wrong fix, a gate run lost.

**Packet drafter.** It writes small documents: a PRD amendment, a blueprint and eval plan delta,
rows and a brief. The extra cost over Sonnet is small because the documents are, and the blueprint
lesson applies to a delta as much as to a blueprint. The cheaper choice: a wrong packet costs a
rehearsal, a reading and the founder's stop again.

**QA tester.** QA is bug hunting, which is judgment: what to try that nobody wrote a row for. The
cheaper choice: a bug QA misses reaches the founder's acceptance walk, or the founder's users.

**Acceptance walker.** It turns QA's record and the merged stories into a walkthrough script and
runs the acceptance tests once for their counts. That is mechanical, so Sonnet at medium. The
founder reads the script while walking the application, which is the check on it. The cheaper
choice is this one.

**Mockup designer.** It decides the look, the layout and the interaction quality of every screen, researching the platform's guideline and comparable products first, and it draws the direction before the PRD exists, so the founder judges a look rather than a description. On the run below, the mockups a Sonnet drafter drew were too basic for the founder until an Opus designer at xhigh with web research took over, and the look settled in that revision. The cheaper choice: a mockup the founder sends back, which is a revision, a check and a stop each time.

**Mockup checker.** It checks the mockup against its rubric: that every PRD journey and state is a screen, that the page is self-contained, that the ids resolve. That is checking rows, not judging a look, which is the founder's at the direction stop. The cheaper choice is this one.

**Builder.** Its mistakes cost a gate run. On an emulator-driven app the run below estimated that
at 45 to 120 minutes each. So the builder gets the most capable setting. The cheaper choice: each
failed attempt is a gate run, a `pod-works fix` round, and the founder's wait.

**Reader.** It reads a diff with no tools, and its block or pass is the founder's second opinion on
whether a passing change did only what the story asked. It stays Opus. The cheaper choice: a
reader that passes what it should have blocked lets an unattended merge through.

## The evidence

From a real founder run of the kit on an Android app.

The preparation checker and the preparation drafter had both been pinned to Sonnet, with no reason
written down anywhere. On the blueprint, the first Sonnet draft had one blocker: test libraries with
no versions pinned. The second draft fixed that and kept a structural error that two Sonnet checker
passes missed. The stories were split by layer, and the application's entry point was written only
by the last story, while every acceptance row drives the launched application on an emulator. No
story before the last could pass. The Opus orchestrator caught it.

The mockups the Sonnet drafter drew were too basic for the founder; an Opus designer at xhigh with
web research took over at the fourth revision, and the look settled.

The drafter's PRD drafts on Sonnet were fine for edits but needed many checker rounds. A draft that
needs three more checker rounds, each read by an Opus orchestrator, costs more per finished
artifact than one Opus draft.

## Where an orchestrator's model comes from

Through a skill, `/pod-works-prepare` or `/pod-works-build`, the orchestrator is the founder's own
Claude Code session, and it runs on the model and effort that session is set to (`/model`). The
lines in `prep-orchestrator.md` and `build-orchestrator.md` apply when the role runs as an agent,
`claude --agent prep-orchestrator`, or as a subagent. Set the session to match the table; each
skill asks its orchestrator to say so once if the session is on something smaller.

The subagents the orchestrators dispatch, the drafters, the checker, the packet drafter, the QA
tester and the acceptance walker, always run on their own files' lines.

## Changing it for one project: `.claude/models.json`

`pod-works init` overwrites `.claude/agents/` on every run, so an edit to an agent file does not
last. A project that wants another choice writes `.claude/models.json`:

```json
{
  "prep-drafter": { "model": "sonnet", "effort": "high" },
  "builder": { "model": "claude-opus-5-5", "effort": "xhigh" },
  "reader": { "model": "sonnet" }
}
```

- **The keys** are the agent files' names without `.md`, plus `builder` and `reader`. Each value
  gives `model`, `effort` or both; what it leaves out stays the kit's.
- **A model** is `opus`, `sonnet`, `haiku`, `fable`, `inherit`, or a full id beginning
  `claude-`. It is passed to Claude Code as written. For the builder and the reader, `inherit`
  passes no model, so Claude Code's own default applies.
- **An effort** is `low`, `medium`, `high`, `xhigh` or `max`.
- **Anything else is an error** that names the file and the key: an unknown key, an unknown
  effort, a model that is neither an alias nor a `claude-` id, or a field other than the two.
  `init` then changes nothing at all, and `build` and `review` start nothing.
- **`init`**, and so `pod-works new`, stamps the kit's agent files and then rewrites the `model:`
  and `effort:` lines of each file the project's file names. It prints which. `pod-works doctor`
  counts those files as the package's copy with the project's lines, not as files that differ.
- **`build` and `review`** take the builder's and the reader's model and effort from it, and pass
  them to `claude -p` as `--model` and `--effort`. A `--model` flag on the command comes first and
  replaces the model; the effort still comes from the file or the kit. With `--agent codex`, the
  builder gets `-m` from the flag only, since the file's models are Claude's.

**Why it lives under `.claude/`.** `.claude/` is already a protected path. Once main is protected,
a change to this file reaches main only through a pull request the founder approves, which is right
for the choice of which model builds the application. And `init` stamps only `.claude/agents/` and
`.claude/skills/`, so the file survives every re-stamp.

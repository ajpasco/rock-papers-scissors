# Rubric: the mockup

The mockup is the picture of the product the user accepts at gate 3, which the build is made to
match. The `mockup-designer` draws it and the `mockup-checker` checks it, each in its own session.
The PRD governs behaviour; this rubric governs the picture: that it shows the PRD and nothing else,
designed to a standard a person would pay for. At the direction pass, before the PRD exists, the
confirmed scope and the numbered decisions stand in for it (ORCHESTRATOR.md section 3.4, rule 7).

## 1. Form and path

- One file, `docs/MOCKUP.html`, self-contained: no network request, system fonts and inline SVG
  only. It opens from disk.
- `#<id>` shows that screen and `?frame=1` the device frame alone. Each screen has a stable
  `data-screen` id, and each state the PRD defines is its own screen.
- The frame holds only what the application shows. Notes, labels and controls sit outside it.
- A phone app is one phone frame; a web app, a desktop and a phone width; a command-line tool, a
  page of terminal transcripts, one per command and outcome, with its output and exit status.

## 2. Required content

1. **Design notes**: the platform guideline sections read; three to five comparable products, from
   public sources; a convention table; a **not copied** list; the tokens with measured contrast;
   the sources.
2. **A screen map**: per screen type, its one primary element, its secondary elements and the
   requirement it answers. Wording the PRD leaves open is marked `illustrative`.
3. **Every journey and state the PRD claims**, clickable, and none it lacks. No edge case.
4. **Questions for the founder**: what the design wanted that the PRD does not state, with the
   reason, what was drawn instead, and the class, happy path or likely error. Empty is valid;
   absent is a defect.
5. **A coverage table** from the screens' labels, `REQ-nn` to screen ids, a row for each
   requirement not drawn; and a **revision note** per revision.

## 3. The checklist, in brief

- **Fidelity**: every journey step reaches the next; nothing the PRD lacks is drawn as decided;
  each label is shown on its screen; sample data agrees across screens.
- **Hierarchy**: the primary element is the most prominent thing; nothing the state does not need;
  the same action with the same name and place everywhere.
- **Platform and access**: the named guideline, or the notes say why not; targets at the
  platform's minimum (48 dp, 44 pt, 24 CSS px); text contrast 4.5:1; nothing told by colour alone.
- **Feedback**: every act shows its result; every error says what went wrong and what to do.
- **Craft and originality**: tokens only; one type scale; the user's words; no product's name,
  logo, icons or signature layout, and no file from any site.

## 4. What blocks

A blocker is only: a journey the PRD or the scope lacks, drawn as decided; a journey the PRD
claims, missing; a label not demonstrated on its screen; a page that is not self-contained;
anything but the application inside the frame; a missing questions list or coverage table.
Everything else is an advisory, fixed the next time the mockup is touched. A finding about look
cites the rule it breaks; a preference is never a finding. A drawn behaviour the PRD lacks is a
`user-decision` finding, put to the user as a question (ORCHESTRATOR.md section 3.4, rule 1).

## 5. Checker's verdict

The checker reads the HTML, rebuilds the coverage table from the labels, renders every screen type
and a sample of states (not every screen), and walks each PRD journey. One findings file, in this
order: hygiene; the fidelity table, one row per requirement; the checklist, pass or finding ids;
the findings, each with id, severity, description, basis (quoted HTML, a screen id and what its
render shows, or a measured number) and resolution class, a finding with no basis dropped; the
counts; and one of:

- **No blockers.** The mockup goes to the user, with the advisories listed.
- **Blockers, all `plan-correction`.** One revision by the designer, then a new check in a new
  session.
- **Blockers including `user-decision`.** Stop; the user decides.

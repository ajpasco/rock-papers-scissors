# Standard behaviours

Some behaviours have the same answer in every application. Nobody wants a refused value to change
anything, or a delete to happen without a confirmation, whatever the product is. Asking the founder
about each one, screen by screen, costs questions and settles nothing new. So they are written down
here once, each with an id, `SB-1` onward, one plain sentence and one example.

How the list is used ([ORCHESTRATOR.md](ORCHESTRATOR.md) section 3.4):

- **Shown once, at gate 2, as one stop.** The orchestrator shows the whole list and says: "These
  are the standard behaviours; they apply unless you strike or change one. Reply 'all', or the
  numbers to change with what you want instead." Nothing on the list applies before the founder
  replies, and silence is not a reply.
- **One reply, one decision.** The founder's reply is recorded once, as one numbered decision, and
  on the state file's `Standard behaviours` line in the founder's own words.
- **Cited like a decision.** From then on each item is a source a criterion may cite by its id,
  exactly like a numbered decision: "Refused with the reason, and nothing changes (SB-1)".
- **Never asked again.** No item is asked about again, for any screen, command or surface. A
  drafter, checker or designer that raises a question an item answers has not read this list; the
  orchestrator applies the item and does not ask.
- **An item the founder changed becomes a decision of its own.** It gets its own number, and
  criteria cite that decision, not the item. An item the founder struck does not apply.

Ids are never renumbered. An item added later takes the next number.

## The list

**SB-1. Unusable input is refused.** A value that is impossible, malformed or out of range is
refused with the reason, the user is asked for it again, and nothing changes.
Example: a work time of "-5" gets "A work time must be 1 second or more", and the saved routine is
as it was.

**SB-2. A left-out value is asked for.** When something the application needs was not given, the
application asks for it rather than guessing.
Example: a booking with no date gets "Which day?", not a booking made for today.

**SB-3. Input it cannot interpret gets a reply saying so.** When the application cannot tell what
the user meant, it says so and asks the same thing again.
Example: "Start the timer?" answered with "maybe" gets "Please answer yes or no", and the timer
waits.

**SB-4. A destructive or irreversible act is confirmed first, and the confirmation names the
thing.** Deleting, overwriting, sending or paying waits for a yes.
Example: "Delete the routine 'Morning circuit'? This cannot be undone." with Delete and Cancel.

**SB-5. An open question is answered before anything behind it works.** While a dialog or a
question is open, nothing behind it responds, and back or cancel gives the answer that changes
nothing.
Example: with "Discard your changes?" open, the screen behind it ignores taps, and back keeps the
changes.

**SB-6. An error says what went wrong and what to do.** In the user's words, never a bare code.
Example: "Could not save: the disk is full. Free some space and try again", not "Error 28".

**SB-7. An unreachable service is said plainly, with what still works.** When something the
application depends on cannot be reached, the user is told, and told what they can still do.
Example: "Offline: your list is shown as last saved; changes will wait until you are back online."

**SB-8. A long operation shows that it is running.** Anything that takes long enough to be
noticed shows that it is under way, so the user does not repeat it or give up.
Example: "Uploading 3 of 12 photos" while the upload runs.

**SB-9. A finished act shows its result.** The user can see that what they did has happened.
Example: after Save, "Saved" appears and the item shows its new name.

**SB-10. An act that fails changes nothing.** It is done completely or not at all; a failure never
leaves things half done.
Example: an order that fails at payment leaves no order placed and no stock held.

**SB-11. An accidental repeat does it once.** A second press of the same control while the first
is still running, or a double tap, does not do the act twice.
Example: a double tap on Pay charges the card once.

**SB-12. Nothing changes without the user's own act.** Nothing is created, changed or deleted
except by something the user did.
Example: opening a routine to look at it does not save it or change its date.

**SB-13. A value shown in two places is the same value.** The same fact never reads differently on
two screens or in two outputs.
Example: the total on the basket screen is the total on the payment screen.

**SB-14. The same action has the same name and place everywhere.** An action is never called one
thing on one screen and another elsewhere, or moved around.
Example: Delete is called Delete and sits in the same corner on every screen that has it.

**SB-15. The application does only what the requirements say.** No control, network call or stored
data that the requirements do not state.
Example: a timer that the requirements say works offline sends nothing over the network and keeps
no usage log.

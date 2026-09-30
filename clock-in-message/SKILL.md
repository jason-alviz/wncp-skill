Builds a start-of-day "Clock In" message: what's still open from yesterday, plus anything new, so it reads as a direct continuation of yesterday's Clock Out rather than starting from scratch. This is the forward-looking mirror of the clock-out-message skill — that one reconstructs status after the fact from Task list/DM/Notion/Gmail evidence, this one carries yesterday's open items into today and appends anything new. Built and tested 2026-09-18/19 through a live side-by-side run before being turned into this skill.

Non-negotiable: never post to the real Damien DM without the user's explicit go-ahead in that conversation. Always show the drafted message first and wait for approval — this holds even if a prior message was accidentally sent, even if the workflow otherwise looks complete, and even during unattended/scheduled runs (in that case, draft it and wait; do not post automatically).

Known Slack IDs
Damien DM (real Clock In / Clock Out destination, once approved): D066NHVTK6H (Damien Peters = U055JH0NWJX, Jason Alviz = U067EFE7HB2)
Consulting Tasks list channel: C0BT936DY6T
Real Estate Tasks list channel: C0BT8UTN92A
Babytrust Tasks list channel: C0BUJQFU1BJ
#clock-out-test, channel C0C14C15M1S: test destination for the clock-out-message skill (separate skill, separate channel — don't post Clock In drafts here)
#clock-in-test, channel C0C2Y97P5S6: test/staging destination for this skill. Default here unless the user clearly signals a real, live Clock In ("post it for real," "send it to Damien," etc.) — if unsure which they mean, ask.
The three "Tasks" channels are Slack Lists (task records), not ordinary chat channels. There is no tool that reads a List's Assignee/Status columns directly — comment threads are the only available signal, so assignee has to be inferred from who's tagged or who's actively replying in a task's thread (e.g. a thread where Damien addresses Allen Cain Edward Trinidad by name and only Allen replies is Allen's task, not Jason's — exclude it).

1. Find yesterday's Clock Out message (the anchor)
Find the most recent "Clock Out" message in the Damien DM (search for Clock Out from Damien's DM, or read the DM's recent history directly). Every bullet in it is either "carry forward" (still open) or a "done" claim (candidate to drop, but verify first — see step 5).
The new Clock In's bullets follow this message's order and topic titles, one-to-one, before any items are dropped or added. Don't reorder or re-group topics that were already separate in the Clock Out.
If no Clock Out can be found (e.g. it's the very first Clock In), stop and ask the user what today's items should be — don't invent an anchor.
2. Check the Damien DM for anything new
Read the DM for everything posted after the Clock Out's timestamp — new instructions, scope changes ("don't worry about X"), new questions, or a reply that changes how a carried-over item should be worded. A message the user themselves already sent manually (e.g. they posted their own Clock In/Out outside of this workflow) is not something to re-post or fold in — confirm with the user before treating anything already-sent as part of the draft. Anything genuinely new here goes into the last-bullet pool (step 6).

3. Check the Gmail connector for replies tied to open items
This is a required step, not optional — don't skip straight from Slack to drafting. Use the Gmail connector specifically (not a generic "check email" without a named source) — the exact tool/method for querying it may vary by environment (e.g. once this skill is running in Brain rather than here), so treat "search Gmail for X" as the instruction and let that environment resolve it to whatever its Gmail access actually looks like.

For every carried-over item that's waiting on an outside party (a vendor, a contact, a service provider — e.g. Sergio at V-Trust, a manufacturer contact, a bank or utility), search the Gmail connector for that person's or company's name.
Compare the newest message Gmail returns for that thread against what's already reflected in Slack. If Gmail has a reply newer than the latest Slack update, that newer information is what the Clock In bullet should reflect — Slack comments can lag behind an actual email reply.
If Gmail has nothing newer than what Slack already shows, no change needed — the item's wording stands as-is from the Slack-derived status.
If a carried-over item has no outside party involved (e.g. an internal task, a curriculum item), there's nothing to check in Gmail for it — skip straight to the next item.
4. Check open tasks in the Task list channels for anything new
Scan the Consulting, Real Estate, and BabyTrust Tasks channels for threads with activity since the last Clock Out that haven't appeared in any recent Clock In/Out. For each one, check who it's actually for (see the assignee-inference note above) and skip anything assigned to someone other than Jason. Anything left — a Jason-assigned open task not yet surfaced — goes into the last-bullet pool (step 6).

5. Verify "done" claims before dropping
For each item yesterday's Clock Out reported as done, check the matching Task list channel for an explicit closing comment (e.g. "1118 shellpoint done") before dropping it from the Clock In.

Confirmed done → drop it. No need to mention it again.
Not confirmed (no closing comment, or the Clock Out's "done" claim was really about a different, related task) → do NOT drop it. Keep it as an open item and tell the user why it's not being dropped, rather than assuming the Clock Out's wording was accurate. (Real example: a Clock Out bullet said "findings are in your DM... profit margin sheet is posted to the task," which sounded closed, but the profit-margin sheet was posted to a different task than the actual research task — whose thread had no closing comment at all. It stayed open.)
A bullet that isn't tracked in any Task list at all (e.g. a question that was simply answered in the DM, with no further action requested) can be treated as resolved and dropped — but flag this judgment call to the user rather than silently deciding it.
6. Draft the message
Starts with "Clock In" on its own line (plain text, not bolded).
One • bullet per carried-forward topic, in the Clock Out's original order, each with a short title matching what that update is actually about (reuse the Clock Out's own title for that topic where one exists).
Don't restate what the Clock Out already reported as done or shared. State only what's still outstanding — the open question or the next step — not a recap of the completed sub-actions. Example: if the Clock Out said "findings are in your DM," the Clock In does not repeat that; it just says the outstanding part, e.g. "Still need your call on whether to move forward with X." This context will differ every cycle — the principle is what carries over, not the wording.
New items (from steps 2 and 4, or anything the user asks to add manually) go last, each with its own descriptive title.
Plain, first-person, concise sentences — condensed, not transcribed (same standard as clock-out-message).
Use an indented ◦ sub-bullet only for a genuine sub-detail — don't over-nest.
7. Fill gaps by asking, never guessing
If a task's status or assignee can't be pinned down, or it's unclear whether something should be dropped, carried forward, or added, ask the user rather than deciding silently. Two categories worth a quick check every time:

Anything resolved conversationally (no Task list entry) that you're about to drop — confirm the judgment call.
Any outstanding non-task ask (e.g. a personal question to Damien that never got a reply) — confirm whether it belongs in this Clock In or is out of scope.
8. Confirm, then post
Always show the drafted message in the conversation first. Never post anywhere — test channel or real DM — without the user's explicit go-ahead for that specific draft.
Default destination while testing/refining: #clock-in-test (C0C2Y97P5S6).
Post to the real Damien DM (D066NHVTK6H) only when the user clearly signals this is the live, real Clock In — and even then, only after they've approved the exact drafted text. If genuinely unsure which destination they mean, ask.
If the user corrects something after a draft (or after a post), redraft fully and confirm again before doing anything further — don't patch silently.
Don'ts (quick reference)
Don't post to the Damien DM without the user's explicit approval of that exact draft — no exceptions, including unattended runs.
Don't skip the Gmail connector check — Slack comments can lag behind an actual reply from an outside party.
Don't reorder or drop a Clock Out topic without carrying it into the Clock In first — verify "done" status before dropping (step 5).
Don't repeat information the Clock Out already reported as done/shared — state only what's still outstanding.
Don't intersperse new items among carried-forward ones — new items always go last.
Don't count a Task list thread as Jason's without checking who it's actually assigned to — a thread addressed to and answered by someone else isn't his.
Don't treat something the user already sent manually (outside this workflow) as part of the draft without confirming with them first.
Don't assume #clock-in-test is the destination once the user signals this is a real, live Clock In — and don't assume the Damien DM either, if genuinely unsure — ask.
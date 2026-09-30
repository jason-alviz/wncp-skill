# Clock Out Message

Builds an end-of-day "Clock Out" status update and posts it to Slack. The message must be anchored to what was actually promised in today's Clock In message and must account for every item in it — Damien has explicitly called out check-ins that skip open items ("You didn't mention the survey winners at all... go back and review all your open tasks"). Tested end-to-end on 2026-09-11 and 2026-09-17; the workflow below reflects what actually worked.

## Identity mapping (critical — read first)

- **"Me" / "I" / the user = Jason Carl Alviz** — Slack U067EFE7HB2, jason.a@wncapitalpartners.com
- **Damien Peters** = U055JH0NWJX, damien@wncapitalpartners.com (Managing Partner, Clock In/Out recipient) — NOT the user
- **Allen** = U0BC77RS29K — another team member, NOT the user
- Never conflate these three. When the user says "my tasks," that means tasks assigned to U067EFE7HB2 only.

## Assignee verification rule (mandatory)

- Before writing any task ownership in the Clock Out, **always check the task's Assignee field and resolve the raw Slack user ID to a real name** (slack_read_list returns raw IDs like U055JH0NWJX — resolve with slack_read_user_profile before naming anyone).
- Only tasks assigned to **Jason Carl Alviz (U067EFE7HB2)** may be reported as "mine/my plate."
- Tasks assigned to Damien or Allen must be labeled with their actual assignee if mentioned at all (e.g. "assigned to you, Damien" / "assigned to Allen") — never folded in as "mine."
- Do NOT infer ownership from thread discussion or conversation context — a task being discussed in a thread Jason is part of does not mean Jason is assigned to it.
- Lesson (2026-09-17): new Real Estate tasks (Close 2025 books in QuickBooks, Collect 2025 tax docs, EMD deposit, Contact title company, Close 38 Philadelphia Ave) were all assigned to Damien (U055JH0NWJX) — summarizing them without checking the assignee would have misreported Jason's workload.

## Known Slack IDs

- Damien DM (where Clock In / Clock Out actually happens for real): D066NHVTK6H (Damien Peters = U055JH0NWJX, Jason Alviz = U067EFE7HB2)
- Task lists (Slack Lists — read via slack_read_list; Assignee column returns raw user IDs, resolve them):
  - Consulting Tasks: F0BT936DY6T (channel C0BT936DY6T)
  - Real Estate Tasks: F0BT8UTN92A (channel C0BT8UTN92A)
  - Babytrust Tasks: F0BUJQFU1BJ (channel C0BUJQFU1BJ)
- Test destination for drafting/trying the Clock Out message: #clock-out-test, channel C0C14C15M1S (post directly with slack_send_message, no need to ask for the channel)

### Destination note
#clock-out-test is a staging/test channel — it is not where Damien actually reads Clock Out updates; the real one lives in the Damien DM (D066NHVTK6H). Default to the test channel when nothing suggests otherwise, but if the user says anything like "post it for real," "post in the private thread," "send it to Damien," or otherwise signals this is the actual end-of-day check-in, post to the Damien DM instead. If unsure which one, ask.

The three "Tasks" channels are Slack Lists (task records with Assignee/Status/Description), not ordinary chat channels — don't confuse them with #babytrust, #real-estate, or #real-estate-alerts, which are general discussion channels. #real-estate-alerts is a noisy automated-bot feed — skip it unless specifically asked.

## 1. Find today's Clock In message

Find today's "Clock In" message in the Damien DM (slack_search_public_and_private with Clock In in:<@U055JH0NWJX>, or slack_read_channel on D066NHVTK6H for the most recent messages). This message is the anchor: every bullet in it is a task/question the Clock Out must address.

- Check whether the Clock In got a threaded reply adding more bullets — run slack_read_thread on the Clock In message's timestamp and fold any reply bullets into the list.
- Also check for follow-up messages posted after the Clock In in the same DM, even if not threaded — Damien sometimes replies inline with scope changes ("don't worry about X, focus on Y instead") or new questions. These change what's actually open and deserve their own line in the Clock Out even though they weren't in the original bulleted list.
- If no Clock In message can be found for today, stop and ask the user before drafting anything — don't guess what was planned for the day.

## 2. Gather status for every item in the Clock In list

For each bullet in the Clock In message, work out what's assigned to Jason Alviz (U067EFE7HB2) and where it stands. Don't rely on memory or assumption — check sources in this order:

1. **The matching Slack Task list** by area (use slack_read_list to see Assignee/Status/Description directly, and resolve Assignee IDs to names):
   - Consulting items → F0BT936DY6T
   - Real estate / property / mortgage items (e.g. Shellpoint, PenFed, Hemlane, QBO categorization, rentals) → F0BT8UTN92A
   - BabyTrust items → F0BUJQFU1BJ
2. **The list channel's comment threads** (if slack_read_list isn't available or needs supplementing):
   - Each task has one permanent "root" message (Slackbot: "A comment was added", empty text) posted the first time anyone comments on it. Every later comment on that task is a threaded reply on that same root message.
   - Finding a task's current status means finding its root message, then reading the whole thread with slack_read_thread.
   - Search tip: slack_search_public_and_private with an in:<channel_id> filter using a raw channel ID has returned zero results even when the content is confirmed to be in that channel — don't trust a "no results" from a channel-scoped search. Search unscoped with a distinctive phrase from the task (try a couple of phrasings). Once you find any message that's part of the right thread, note its thread_ts and pull the whole thing with slack_read_thread.
   - If unscoped search doesn't surface the task at all, fall back to slack_read_channel on the list channel, sorted newest-first, and look for threads whose latest reply timestamp is today. Also skim recent root messages generally — a genuinely new task with no prior history can appear the same day and needs folding into the Clock Out even though it wasn't in the Clock In.
3. **The relevant discussion channel** (#babytrust, #real-estate) for broader context or status not captured in task comments.
4. **The Damien DM (D066NHVTK6H)**: read messages/threads since the Clock In was sent — a lot of back-and-forth happens right there.
5. **Notion**: search for the task or related doc (task comments often link out to a Notion page) and check its content and comments for the latest detail.

For each item, work out: done / in progress (with current sub-status) / blocked or waiting on someone / needs a decision or approval from Damien.

The latest comment on a task is not always Jason's. Sometimes Damien commented most recently, showing the task is stalled or being handled on his end. In that case the Clock Out should say plainly that it's waiting on him / blocked on his side — don't manufacture a Jason-side update that doesn't exist, and don't skip the item either.

## 3. Fill gaps by asking, never guessing

If a task's current status, assignee, or description can't be pinned down from the sources above, or something in the Clock In is ambiguous, ask the user directly. Never invent a status, and never quietly drop an item because it's unclear — an incomplete check-in is the exact failure mode Damien has flagged before.

Don't infer whether a specific promised action happened from indirect signals — confirm it. Lesson (2026-09-16): the Clock In promised "send a clarification message to Chen and Alicia." Later activity showed focus shifting to a different vendor (Zhongyi) plus a shipping quote; it was drafted that the Chen/Alicia message was not sent — but it actually had been. Whether a specific outbound action (a message sent, a call made, a form submitted) happened is a binary fact that unrelated later activity does not confirm or rule out either way. If no source directly states it was done, don't write it as done or not-done from inference alone — ask the user to confirm that specific point before it's shown for approval.

## 4. Draft the message

Match the established Clock In/Out voice and format from the Damien DM history:

- Starts with the line "Clock Out" by itself (plain text, not bolded/markdown — that's how the real messages look).
- One • bullet per task/topic, in the same order as the Clock In list, so it reads as a direct answer to it.
- Use an indented ◦ sub-bullet only for a genuine sub-detail or a follow-up question under that bullet — don't over-nest.
- Plain, first-person, concise sentences: what was done, what's still open, and any explicit question or approval needed from Damien (e.g. "Need your approval before I...", "Should I...?").
- Use a short category label (e.g. "Amazon:", "QBO / Mortgage:", "Survey Winners:") only when there are several unrelated items to group — not on every line.
- Condense, don't transcribe. Boil each item down to the outcome and what's next in 1-2 sentences — don't restate the task comment near-verbatim. Example: a full call with Amazon support compresses to: "Amazon billing account hold: Called Amazon Business Support and got it escalated to their higher-level team — confirmed the account's on hold, waiting on their response (24-48 hr turnaround)."
- Every item from the Clock In gets a corresponding line in the Clock Out. Nothing gets silently dropped, even if the update is just "no movement on this yet."
- A new task that surfaced during the day in a Task list still belongs in the Clock Out — add it as its own bullet (or fold into the most related bullet), but only after checking its Assignee: if it's not Jason's, label it as such (e.g. "heads-up, assigned to you") rather than implying Jason picked it up.
- Sign-off convention: the tested runs appended "*Sent using* <@U0AFGS87G4D|Claude>" on its own line at the end. Keep it if that's how Brain posts it.

## 5. Confirm, then post

- Show the drafted message to the user for review before posting — this represents their reported work status, and vague or incomplete check-ins have caused real friction with Damien before.
- Once approved, post with slack_send_message using the message text as one block (the "Clock Out" line plus all bullets). A single newline-separated list matching the drafted format works and is what was used successfully.
- Use the test channel (C0C14C15M1S) by default, or the Damien DM (D066NHVTK6H) when the user indicates this is the real check-in — if genuinely unsure which one they mean, ask rather than guessing.
- If the user corrects a factual claim in an already-posted message, redraft the full corrected message and confirm it with the user again before posting — don't just patch the one line silently. Follow the user's stated preference on where the corrected version goes (threaded correction vs. new full post) rather than assuming it repeats wherever the original went.
- If the user later says they want this posted somewhere else by default, update the destination channel ID above rather than asking each run.

## Don'ts (quick reference)

- Don't state a specific promised action as done or not-done based on inference from unrelated later activity — confirm it directly or ask.
- Don't assume the test channel (#clock-out-test) is the right destination once the user signals this is a real, live check-in — check which destination they mean.
- Don't silently patch one line of an already-posted message after a correction — redraft the whole message and confirm again before reposting.
- Don't drop a Clock In item from the Clock Out because its status is unclear — ask instead of omitting it.
- Don't invent or assume a status for anyone when no source confirms it.
- Don't skip newly-surfaced tasks just because they weren't in the original Clock In.
- Don't report any task as "mine/Jason's" without verifying its Assignee field is U067EFE7HB2 (Jason Carl Alviz) — "me" always means Jason, never Damien (U055JH0NWJX) or Allen (U0BC77RS29K), and raw user IDs must be resolved to names before writing ownership into the message.
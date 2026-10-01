> New guide. Microsoft's menus change often. If a step doesn't match what you see, tell us at support@sierrix.com.

# Morning Triage Agent

After one prompt and one click each morning, your only job is ticking boxes. A Copilot agent reads your
recent email, Teams chats and meetings and writes a numbered to-do list by priority. When you say "send to
tasks", the list becomes checkboxes in Planner, which also show in To Do, Outlook and Teams. A log in Excel
records every task and when you finished it, so the agent never lists the same thing twice, and at the end
of the year you have a record of everything you did.

## How it works

Agent Builder agents can't write files, so the agent hands its list to Power Automate with an email to
yourself. Flow 1 reads that email, creates a Planner task for each line and adds a row to the log. Flow 2
marks the row Done when you tick the task. The agent reads the log next morning.

## What it costs

Nothing extra for people with a Microsoft 365 Copilot seat: Agent Builder agents that read your own email,
chats and meetings are included with the seat. Both flows use only standard connectors (Outlook, Planner,
Excel Online, Content Conversion), which are included in Microsoft 365 Business Basic, Standard and Premium
with no cost per run. Without a Copilot seat the agent can't read your mailbox, so this guide needs the seat.
(Microsoft Learn: Agent Builder knowledge sources and Power Automate licensing FAQ, checked September 2026.)

## You'll need

- A Microsoft 365 Copilot seat, Planner, Microsoft To Do, OneDrive and Power Automate (make.powerautomate.com).
- Your work email address, and the name of your time zone as Windows writes it, such as **Eastern Standard
  Time** or **Pacific Standard Time** (these names cover daylight time too).

## Step 1: Create the task log in OneDrive

This Excel table is the one record both flows write to and the agent reads.

1. In OneDrive (your work account), create a folder named **Copilot** and a new workbook in it named
   **Task Log.xlsx**.
2. In row 1, type these nine headers in A1 to I1:

   | Column | Header | Filled by |
   |---|---|---|
   | A | TaskID | Flow 1 (the Planner task ID) |
   | B | Created | Flow 1 |
   | C | Priority | Flow 1 (P1, P2, P3) |
   | D | Task | Flow 1 |
   | E | Requester | Flow 1 |
   | F | Category | Flow 1 |
   | G | Due | Flow 1 (blank if none) |
   | H | Status | Flow 1 sets Open, Flow 2 sets Done |
   | I | Completed | Flow 2 (the date you ticked it) |

3. Click A1, press **Ctrl+T**, tick **My table has headers**, and select **OK**.
4. On the **Table Design** tab, set **Table Name** to `TaskLog`.
5. Select columns B, G and I and format them as **Text**, so dates stay as YYYY-MM-DD.
6. Save and close it. Power Automate can fail to write while the file is open in desktop Excel; open in the
   browser it's fine.

## Step 2: Create the Planner plan

Planner holds the checkboxes; its tasks show up in To Do, Outlook and Teams.

1. Go to planner.cloud.microsoft (or the Planner app in Teams) and choose **New plan → Basic plan**.
2. Name it **Daily Triage**, set it to **Private**, and create it. Planner makes a private Microsoft 365 group
   for it; leave yourself as the only member.
3. Rename the default bucket to **Inbox**. All tasks land here; the title carries the priority.
4. In Microsoft To Do, open **Settings → Smart lists** and turn on **Assigned to me**. Your triage tasks appear
   there with a checkbox.

Optional: in To Do, sort the **Assigned to me** list **Alphabetically** so P1 tasks rise to the top.

## Step 3: Create the agent

1. Open Microsoft 365 Copilot in a browser (microsoft365.com/chat) or in Teams. In the left pane select
   **New agent**, then **Skip to configure**.
2. Name: **Morning Triage**. Description: "Turns my email, Teams chats and meetings into a prioritized to-do
   list and sends it to Planner."
3. Paste the block under "Instructions" below into **Instructions**. Replace `you@company.com` with your work
   email.
4. Under **Knowledge**, select the search bar and add **My emails** and **My Teams chats and meetings**. Then
   add **Task Log.xlsx** from OneDrive (the cloud icon, **Attach cloud files**).
5. Add the **Starter prompts**: Run my morning triage, Send to tasks, Triage the last 24 hours, What am I
   waiting on?
6. Select **Create**. Don't share it: it reads your own mailbox, and other people's copies would read theirs
   without the flows.

To change it later, hover over **Morning Triage** in the left pane, then **... → Edit**, make the change,
select **Update**, and start a new chat.

## Instructions (paste everything in the box)

```text
ROLE
You are my morning triage assistant. When I run you, review my recent Outlook email, Teams chats and mentions, and meetings (including recaps and transcripts) and produce a prioritized, numbered checklist of what I personally need to do.

LOOKBACK
Since the end of my last workday (on Monday, since Friday 5pm), unless I give a different window. Also check today's and tomorrow's calendar for prep.

WHAT COUNTS AS A TASK
Direct requests or questions to me I haven't answered; commitments I made ("I'll send", "I'll ask"); action items assigned to me in meetings; approvals or reviews waiting on me; deadlines; meeting prep; @mentions that need a response.

IGNORE
Newsletters, marketing, automated notifications, FYI-only messages, items someone else owns, and threads where I replied after the last request or someone said done, fixed, resolved or thanks.

TASK LOG CHECK
Before listing tasks, read "Task Log.xlsx" (table TaskLog). Leave out any task that matches a logged row (Status Open or Done) by topic and person, even if worded differently. If an Open logged task has a new follow-up message, list it again marked "(follow-up)".

PRIORITY
P1 = due today or overdue, blocking someone, from leadership, a security problem or outage, or needed for a meeting today.
P2 = due this week, or a follow-up unanswered for more than 2 days.
P3 = everything else.

OUTPUT FORMAT (STRICT)
Never use tables. Every task is a Markdown checkbox line:
- [ ] **<n>. <verb-first task>**: <from> · <due> · <source type> <citation>
Number tasks continuously across sections. Sections in this order, empty ones left out:
Triage for <weekday, date>: X tasks (X P1, X P2, X P3)
## P1: Do today
## P2: This week
## P3: When possible
## Meeting prep
## Waiting on others (plain bullets)
## Verify (one-line reason each)
End with: "Say 'send to tasks' to push these to Planner."

SEND TO TASKS
When I say "send to tasks" (optionally with numbers, e.g. "send to tasks 1-4, 6"), send all listed tasks or only those numbers. Reply with ONLY:
1) A link: [Send to Planner](mailto:you@company.com?subject=TASKSYNC&body=<URL-encoded lines>)
2) The same lines as plain text in a code block, as a fallback.
One line per task, exactly 5 fields separated by " | ":
P1 | <verb-first task, max 120 chars> | <requester name> | <category> | <due as YYYY-MM-DD, or leave empty>
Categories: Customers, Team, Projects, Money, Admin, Other.
Never use the "|" character inside a field. Always include all 5 fields, even when Due is empty (end the line with "| ").

RULES
Cite a source for every task. Never invent tasks, people, or dates. Keep each line short.
```

Change the categories to ones that fit your work; Flow 1 copies whatever the agent sends.

## Step 4: Build Flow 1, email to Planner and the log

Flow 1 turns each line of a TASKSYNC email into a Planner task and an Open row in the log.

Rename each action exactly as shown in **bold** (select the action's title and type the new name). The
formulas refer to those names. To enter a formula, click the field, select the **fx** button, paste it, and
select **Add**.

1. Go to make.powerautomate.com and choose **Create → Automated cloud flow**. Name it **Triage to Planner**.
2. Trigger: Office 365 Outlook **When a new email arrives (V3)**. Folder: **Inbox**. Under Advanced
   parameters: From = your work email, Subject Filter = `TASKSYNC`, Include Attachments = No.
3. Add Content Conversion **Html to text**, renamed **BodyText**. Content: the dynamic value **Body** from
   the trigger.
4. Add Data Operation **Compose**, renamed **Lines**. Inputs (formula):
   `split(body('BodyText'), decodeUriComponent('%0A'))`
5. Add **Apply to each**, renamed **EachLine**. Select an output (formula): `outputs('Lines')`
6. Inside the loop, add a **Condition**. Left (formula): `length(split(item(), '|'))`. Operator: **is greater
   than or equal to**. Right: `5`. This skips blank lines and your signature.
7. In **True**, add **Compose**, renamed **Parts**. Inputs (formula): `split(trim(item()), '|')`
8. Still in **True**, add Planner **Create a task**, renamed **CreateTask**:

   | Field | Value |
   |---|---|
   | Group Id | Daily Triage |
   | Plan Id | Daily Triage |
   | Title | `concat(trim(outputs('Parts')[0]), ' · ', trim(outputs('Parts')[1]))` |
   | Bucket Id | Inbox |
   | Assigned User Ids | your work email |
   | Due Date Time | `if(empty(trim(outputs('Parts')[4])), null, convertToUtc(concat(trim(outputs('Parts')[4]), 'T17:00:00'), 'Eastern Standard Time'))` |

9. Still in **True**, add Excel Online (Business) **Add a row into a table**:

   | Field | Value |
   |---|---|
   | Location | OneDrive for Business |
   | Document Library | OneDrive |
   | File | /Copilot/Task Log.xlsx |
   | Table | TaskLog |
   | TaskID | `body('CreateTask')?['id']` |
   | Created | `convertFromUtc(utcNow(), 'Eastern Standard Time', 'yyyy-MM-dd')` |
   | Priority | `trim(outputs('Parts')[0])` |
   | Task | `trim(outputs('Parts')[1])` |
   | Requester | `trim(outputs('Parts')[2])` |
   | Category | `trim(outputs('Parts')[3])` |
   | Due | `trim(outputs('Parts')[4])` |
   | Status | Open (typed) |
   | Completed | leave blank |

   Replace `Eastern Standard Time` here and in Due Date Time with your time zone. Due dates are set to 5 PM
   your time.
10. Optional, after the loop (outside it): Office 365 Outlook **Delete email (V2)** with Message Id = the
    trigger's **Message Id**, so TASKSYNC emails don't pile up.
11. Select **Save**.

## Step 5: Build Flow 2, ticked off to Done

Flow 2 runs whenever you tick a task in Planner, To Do, Outlook or Teams, and marks its log row Done with the
date.

1. **Create → Automated cloud flow**, named **Planner Done to Log**.
2. Trigger: Planner **When a task is completed**. Group Id: **Daily Triage**. Plan Id: **Daily Triage**.
3. Add Excel Online (Business) **Update a row**:

   | Field | Value |
   |---|---|
   | Location | OneDrive for Business |
   | Document Library | OneDrive |
   | File | /Copilot/Task Log.xlsx |
   | Table | TaskLog |
   | Key Column | TaskID |
   | Key Value | the dynamic value **Id** from the trigger |
   | Status | Done (typed) |
   | Completed | `convertFromUtc(utcNow(), 'Eastern Standard Time', 'yyyy-MM-dd')` |

4. Leave every other column blank; Update a row only changes the fields you fill.
5. Select **Save**.

The Planner trigger checks every few minutes, so a row can take a few minutes to flip to Done. A task you
add to the plan by hand has no log row, so its run fails harmlessly; ignore it or add it to the log yourself.

## Step 6: Test it end to end

Run this once with a made-up task before relying on it.

1. Send yourself an email with the subject `TASKSYNC` and this body: `P3 | Test the triage sync | Your Name | Other |`
2. Within a few minutes, Flow 1 shows a successful run (**My flows → Triage to Planner → Run history**).
3. A task titled **P3 · Test the triage sync** is in Planner, and in To Do under **Assigned to me**.
4. **Task Log.xlsx** has a new row with Status Open and a TaskID.
5. Tick the task in To Do. Within a few minutes the row shows Done and today's date.
6. In a new chat with the agent, type **Run my morning triage**, then **send to tasks**. Select the **Send to
   Planner** link (or paste the fallback lines into a new email with the subject TASKSYNC) and send it.
7. Delete the test row from the log once everything works.

## Your daily routine

1. Open the agent and run **Run my morning triage**.
2. Type **send to tasks**, or **send to tasks 1-4, 6** to pick some, then select **Send to Planner** and send
   the email.
3. Work from To Do, Outlook or Teams and tick tasks as you finish them.

At review time, filter the log's Status column to Done and ask Copilot: "Using Task Log.xlsx, summarize what
I finished this year from rows with Status = Done. Group them by Category with counts, highlight the 5 with
the most impact, list who I helped most often (Requester), and note trends by quarter. Keep it to one page."

## Troubleshooting

| Problem | Fix |
|---|---|
| The Send to Planner link isn't clickable | Copy the fallback lines into a new email to yourself with the subject TASKSYNC. |
| Flow 1 doesn't start | Check the From and Subject Filter values. Emails you send yourself must land in the Inbox, not a folder an Outlook rule moves them to. |
| Flow 1 runs but creates nothing | Open the run and check the Condition: each line needs 5 fields (4 pipes). Check that Html to text is renamed **BodyText**. |
| Create a task fails on Due Date Time | The agent sent a date in the wrong format. Fix the line, or remove the Due Date Time formula. |
| The Excel step fails with a lock error | Close the workbook in desktop Excel; the next run works. |
| Done tasks still appear in the triage | New log rows can take a few hours before Copilot can search them; they're usually picked up by the next morning. |
| The agent still uses the old format | Select **Update** after editing, then start a new chat. |

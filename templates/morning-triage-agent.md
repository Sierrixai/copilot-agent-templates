> New guide. Microsoft's menus change often. If a step doesn't match what you see, tell us at support@sierrix.com.

# Morning Triage Agent

After one prompt and one click each morning, your only job is ticking boxes. A Copilot agent reads your
recent email, Teams chats and meetings and writes a numbered to-do list by priority. When you say "send to
tasks", the list becomes checkboxes in Planner, which also show in To Do, Outlook and Teams. A log in Excel
records every task and when you finished it, so the agent never lists the same thing twice, and at the end
of the year you have a record of everything you did.

You don't need to have used Power Automate before. Every click is written out below. Set aside about two
hours the first time, and work on a computer (not a phone).

## How it works

Five pieces work together:

1. **An Excel table** in your OneDrive, the "task log", which records every task.
2. **A Planner plan**, which holds your tasks as checkboxes. Planner tasks also show in Microsoft To Do,
   Outlook and Teams, so you can tick them wherever you work.
3. **A Copilot agent**, which reads your email, chats and meetings and writes your to-do list.
4. **Flow 1** (Power Automate), which turns the agent's list into Planner tasks and log rows.
5. **Flow 2** (Power Automate), which marks a log row Done when you tick the task.

Copilot agents can't create tasks or write to files themselves. So when you say "send to tasks", the agent
writes the list as an email to yourself (you click one link and press Send). Flow 1 watches for that email
and does the rest.

## What it costs

Nothing extra for people with a Microsoft 365 Copilot seat: agents built in Copilot that read your own email,
chats and meetings are included with the seat. Both flows use only "standard" parts of Power Automate
(Outlook, Planner, Excel, Content Conversion), which are included in Microsoft 365 Business Basic, Standard
and Premium with no charge per run. Without a Copilot seat the agent can't read your mailbox, so this guide
needs the seat. (Microsoft Learn: Agent Builder knowledge sources and Power Automate licensing FAQ, checked
September 2026.)

## Before you start

Have these ready:

- A Microsoft 365 Copilot license (ask whoever manages your Microsoft 365 if you're not sure: in Copilot you
  should see **New agent** in the left pane, and be able to ask about your own emails).
- Your work email address.
- Your time zone's name as Windows writes it. In the US: **Eastern Standard Time**, **Central Standard
  Time**, **Mountain Standard Time** or **Pacific Standard Time**. These names include daylight saving time,
  so use them all year.

## Power Automate basics (read this first)

These moves come up in steps 4 and 5. Read them once now; the steps refer back to them.

- **Opening Power Automate.** Go to make.powerautomate.com and sign in with your work account. The menu on
  the left has **Create** (to start a new flow) and **My flows** (to find flows you made).
- **The designer.** A flow is shown as a column of boxes, top to bottom. Each box is one step. The first box
  is the **trigger**, the event that starts the flow. Selecting a box opens its settings in a panel at the
  side of the screen.
- **Adding a step.** Point at the line below a box. A **+** appears. Select it, then **Add an action**. A
  search panel opens. Type the step's name exactly as this guide gives it, then select it in the results.
  Each result shows the app it belongs to (Planner, Excel Online (Business), Office 365 Outlook) so you can
  pick the right one.
- **Adding a step inside a loop or condition.** Some boxes (Apply to each, Condition) contain other boxes. To
  add a step inside, use the **+** that appears *inside* the box (for a condition, inside the **True** or
  **False** side), not the one below it.
- **Signing in to an app.** The first time you add a step from an app, Power Automate may ask you to sign in.
  Select **Sign in** and choose your work account. You only do this once per app.
- **Renaming a step.** Select the box. At the top of the settings panel, select the step's name, delete it,
  type the new name exactly as this guide shows it, and press **Enter**. The formulas in this guide use these
  names, so a typo in a name breaks the formula.
- **Picking a value from an earlier step.** Click inside a field. Two small buttons appear at its right end:
  a lightning bolt and **fx**. Select the **lightning bolt** to see values from earlier steps, and select one.
  It appears in the field as a coloured tag.
- **Entering a formula.** Formulas in this guide are shown in grey boxes like `this`. Click inside the field,
  select **fx**, paste the formula into the box at the top (copy it exactly, including brackets and quote
  marks) and select **Add**. It appears in the field as a purple tag.
- **Saving.** Select **Save** at the top right. If something is missing, Power Automate shows a red mark on
  that box; select it to see what's needed.
- **Seeing what happened.** Go to **My flows**, select the flow's name, and look at **28-day run history**.
  Select a run to see each step with a green tick (worked) or a red mark (failed). Select a failed step to read
  why.

## Step 1: Create the task log in OneDrive

1. Go to onedrive.com (or office.com, then **OneDrive** in the left menu) and sign in with your work account.
2. Select **+ Add new** (or **+ New**), then **Folder**. Name it **Copilot** and select **Create**. Open the
   folder.
3. Inside it, select **+ Add new**, then **Excel workbook**. A new workbook opens in your browser.
4. Name it: select the file name at the top of the window (it says something like "Book"), type **Task Log**
   and press **Enter**.
5. Click cell **A1** and type these nine headings, one per cell, from A1 across to I1 (press **Tab** to move
   to the next cell):

   TaskID, Created, Priority, Task, Requester, Category, Due, Status, Completed

   | Column | Heading | Filled in by |
   |---|---|---|
   | A | TaskID | Flow 1 (Planner's own number for the task) |
   | B | Created | Flow 1 (the date) |
   | C | Priority | Flow 1 (P1, P2 or P3) |
   | D | Task | Flow 1 |
   | E | Requester | Flow 1 (who asked) |
   | F | Category | Flow 1 |
   | G | Due | Flow 1 (blank if none) |
   | H | Status | Flow 1 writes Open, Flow 2 changes it to Done |
   | I | Completed | Flow 2 (the date you ticked it) |

6. Turn the headings into a table, so Power Automate can find it: click **A1**, press **Ctrl+T** (or select
   **Insert**, then **Table**). Tick **My table has headers** and select **OK**. The headings turn into a
   coloured band.
7. Name the table: with a cell in the table selected, open the **Table Design** tab (or **Table** tab) at the
   top. In the **Table Name** box, type **TaskLog** (one word) and press **Enter**.
8. Keep dates as plain text: click the letter **B** at the top of column B, hold **Ctrl** and click **G** and
   **I**. On the **Home** tab, open the number format drop-down (it says **General**) and choose **Text**.
9. Close the browser tab. Excel for the web saves by itself. Keep the workbook closed in the desktop Excel app
   while the flows run; the browser is fine.

## Step 2: Create the Planner plan

1. Go to planner.cloud.microsoft (or open the **Planner** app in Teams) and sign in.
2. Select **+ New plan**, then **Basic plan**.
3. Type the name **Daily Triage**. If there's a privacy setting, choose **Private**. Select **Create**.
   Planner sets up a private group for the plan behind the scenes; leave yourself as its only member.
4. The plan opens on the **Board** view. The first column (bucket) is usually called **To do**. Select its
   name, type **Inbox** and press **Enter**. All triage tasks land here; the title carries the priority.
5. Make the tasks show in Microsoft To Do: open to-do.office.com, select the gear icon (**Settings**) at the
   top right, scroll to **Smart lists** and turn on **Assigned to me**. Your triage tasks will appear in that
   list with a checkbox.

Optional: in To Do, open **Assigned to me**, select **Sort**, then **Alphabetically**, so P1 tasks rise to the
top.

## Step 3: Create the agent

1. Open Copilot: go to m365.cloud.microsoft/chat (or microsoft365.com/chat) in your browser, or open
   **Copilot** in Teams.
2. In the left pane, select **New agent** (it may be under **Agents**). Select **Skip to configure** (or the
   **Configure** tab), so you can type everything in yourself.
3. **Name:** Morning Triage
4. **Description:** Turns my email, Teams chats and meetings into a prioritized to-do list and sends it to
   Planner.
5. **Instructions:** copy everything in the box under "Instructions" below and paste it in. If you filled in
   "Your work email" at the top of this page, your address is already in it. If not, find
   `[YOUR WORK EMAIL]` in the pasted text and replace it, brackets and all, with your work email.
6. **Knowledge:** select the search bar in the Knowledge section and choose **My emails**. Select the search
   bar again and choose **My Teams chats and meetings**. Then select the cloud icon (**Attach cloud files**
   or **Browse**), open **OneDrive**, then the **Copilot** folder, and choose **Task Log.xlsx**. You should
   now see three items under Knowledge.
7. **Starter prompts:** add four. For each, type a short title and the prompt (they can be the same text):
   **Run my morning triage**, **Send to tasks**, **Triage the last 24 hours**, **What am I waiting on?**
8. Try it on the right-hand side (**Try it** or the test pane): type **Run my morning triage**. You should get
   a numbered checklist. It's fine if it's short.
9. Select **Create** at the top right. The agent now appears in your left pane under **Agents**.
10. Don't share it. It reads your own mailbox; anyone else who wants one should build their own with this
    guide.

To change it later: point at **Morning Triage** in the left pane, select the three dots (**…**), then
**Edit**. Make the change, select **Update**, then start a new chat with it (old chats keep the old
instructions).

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
1) A link: [Send to Planner](mailto:[YOUR WORK EMAIL]?subject=TASKSYNC&body=<URL-encoded lines>)
2) The same lines as plain text in a code block, as a fallback.
One line per task, exactly 5 fields separated by " | ":
P1 | <verb-first task, max 120 chars> | <requester name> | <category> | <due as YYYY-MM-DD, or leave empty>
Categories: Customers, Team, Projects, Money, Admin, Other.
Never use the "|" character inside a field. Always include all 5 fields, even when Due is empty (end the line with "| ").

RULES
Cite a source for every task. Never invent tasks, people, or dates. Keep each line short.
```

You can change the categories on the "Categories:" line to ones that fit your work; Flow 1 copies whatever
the agent sends.

## Step 4: Build Flow 1, email to Planner and the log

Flow 1 starts whenever an email with the subject TASKSYNC arrives from you. It reads each line of the email
and, for each task line, creates a Planner task and adds an Open row to the log. Have "Power Automate basics"
open while you build it.

### 4a. Start the flow when the TASKSYNC email arrives

1. Go to make.powerautomate.com. In the left menu select **Create**, then **Automated cloud flow**.
2. **Flow name:** Triage to Planner.
3. In **Choose your flow's trigger**, search "new email" and select **When a new email arrives (V3)** (Office
   365 Outlook). Select **Create**.
4. Select the trigger box and fill in:
   - **Folder:** Inbox
   - Select **Show all** (or **Advanced parameters**) to see more fields, then:
   - **From:** your work email
   - **Subject Filter:** TASKSYNC
   - **Include Attachments:** No

### 4b. Turn the email into lines of text

1. Add a step: search **Html to text** (Content Conversion). Rename it **BodyText**.
   **Content:** lightning bolt → **Body** (under When a new email arrives). This strips the email's formatting
   so only the text is left.
2. Add a step: search **Compose** (Data Operation). Rename it **Lines**.
   **Inputs:** formula (fx): `split(body('BodyText'), decodeUriComponent('%0A'))`
   This splits the text into one line per task.

### 4c. Go through each line

1. Add a step: search **Apply to each** (Control). Rename it **EachLine**.
   **Select an output from previous steps:** formula (fx): `outputs('Lines')`
   Everything you add inside this box happens once for each line.
2. *Inside* **EachLine**, add a **Condition** (Control). This skips blank lines and your email signature:
   - First box: formula (fx): `length(split(item(), '|'))`
   - Middle: **is greater than or equal to**
   - Last box: type **5**
3. Inside the **True** side, add **Compose**. Rename it **Parts**.
   **Inputs:** formula (fx): `split(trim(item()), '|')`
   This splits the line into its five parts: priority, task, requester, category and due date.

### 4d. Create the Planner task

Still inside the **True** side, below **Parts**, add **Create a task** (Planner). Rename it **CreateTask**.
Fill in:

| Field | What to put in |
|---|---|
| Group Id | choose **Daily Triage** from the drop-down |
| Plan Id | choose **Daily Triage** |
| Title | formula: `concat(trim(outputs('Parts')[0]), ' · ', trim(outputs('Parts')[1]))` |
| Bucket Id | choose **Inbox** (select **Show all** if you don't see it) |
| Assigned User Ids | type your work email |
| Due Date Time | formula: `if(empty(trim(outputs('Parts')[4])), null, convertToUtc(concat(trim(outputs('Parts')[4]), 'T17:00:00'), 'Eastern Standard Time'))` |

In the Due Date Time formula, change `Eastern Standard Time` to your own time zone name before you select
**Add**. It sets each due date to 5 PM your time, or leaves it empty when the agent gave no date.

### 4e. Add the row to the log

Still inside **True**, below **CreateTask**, add **Add a row into a table** (Excel Online (Business)). Fill
in the first four fields from the drop-downs, in order (each one unlocks the next):

| Field | What to put in |
|---|---|
| Location | OneDrive for Business |
| Document Library | OneDrive |
| File | select the folder icon, open **Copilot** and choose **Task Log.xlsx** |
| Table | TaskLog |

The table's nine columns now appear as fields. Fill them in:

| Column | What to put in |
|---|---|
| TaskID | formula: `body('CreateTask')?['id']` |
| Created | formula: `convertFromUtc(utcNow(), 'Eastern Standard Time', 'yyyy-MM-dd')` (change the time zone to yours) |
| Priority | formula: `trim(outputs('Parts')[0])` |
| Task | formula: `trim(outputs('Parts')[1])` |
| Requester | formula: `trim(outputs('Parts')[2])` |
| Category | formula: `trim(outputs('Parts')[3])` |
| Due | formula: `trim(outputs('Parts')[4])` |
| Status | type **Open** |
| Completed | leave empty |

### 4f. Tidy up and save

1. Optional: so TASKSYNC emails don't pile up in your inbox, add a step *below* the **EachLine** box (outside
   it): **Delete email (V2)** (Office 365 Outlook). **Message Id:** lightning bolt → **Message Id** (under When
   a new email arrives).
2. Select **Save**. Fix anything with a red mark and save again.

## Step 5: Build Flow 2, ticked off to Done

Flow 2 starts whenever you tick a task in the Daily Triage plan (in Planner, To Do, Outlook or Teams), and
marks its row in the log Done with the date.

1. In Power Automate, select **Create**, then **Automated cloud flow**. **Flow name:** Planner Done to Log.
2. Search the trigger "task is completed" and select **When a task is completed** (Planner). Select
   **Create**.
3. In the trigger: **Group Id:** Daily Triage. **Plan Id:** Daily Triage.
4. Add **Update a row** (Excel Online (Business)) and fill in:

   | Field | What to put in |
   |---|---|
   | Location | OneDrive for Business |
   | Document Library | OneDrive |
   | File | /Copilot/Task Log.xlsx (use the folder icon) |
   | Table | TaskLog |
   | Key Column | TaskID |
   | Key Value | lightning bolt → **Id** (under When a task is completed) |

   The columns appear as fields. Fill in only these two and leave the rest empty (empty fields aren't
   changed):

   | Column | What to put in |
   |---|---|
   | Status | type **Done** |
   | Completed | formula: `convertFromUtc(utcNow(), 'Eastern Standard Time', 'yyyy-MM-dd')` (your time zone) |

5. Select **Save**.

Planner tells Power Automate about ticked tasks every few minutes, so a row can take a few minutes to change
to Done. If you add a task to the plan by hand, it has no row in the log, so Flow 2's run for it fails; that's
harmless and you can ignore it.

## Step 6: Test it end to end

Try it once with a made-up task before you rely on it.

1. In Outlook, write a new email **to yourself**. **Subject:** TASKSYNC. **Body:** this one line, exactly:
   `P3 | Test the triage sync | Your Name | Other |`
   Send it.
2. Wait a few minutes. In Power Automate, open **My flows → Triage to Planner** and check **28-day run
   history**: the run should say **Succeeded**.
3. Open Planner → **Daily Triage**. There should be a task called **P3 · Test the triage sync** in **Inbox**.
   It should also be in To Do under **Assigned to me**.
4. Open **Task Log.xlsx**. There should be a new row with Status **Open** and a long code in TaskID. Close the
   workbook.
5. In To Do, tick the test task. Wait a few minutes and reopen the log: Status should be **Done** with
   today's date in Completed.
6. Now the real thing: open Copilot, select **Morning Triage** in the left pane, and select **Run my morning
   triage**. Then type **send to tasks**.
7. Select the **Send to Planner** link in the answer. A new email to yourself opens with the subject and
   lines filled in. Select **Send**. (If the link doesn't open an email, copy the lines from the grey box in
   the answer, paste them into a new email to yourself with the subject TASKSYNC, and send it.)
8. Within a few minutes the tasks are in Planner and To Do. Delete the test row from the log.

## Your daily routine

1. Open Copilot, select **Morning Triage**, and select **Run my morning triage**.
2. Read the list. Type **send to tasks** to send all of them, or for example **send to tasks 1-4, 6** to pick
   some. Select **Send to Planner**, then **Send** in the email that opens.
3. Work from To Do, Outlook or Teams, and tick tasks as you finish them.

At review time, open **Task Log.xlsx**, filter the Status column to **Done** (select the arrow in the Status
heading, untick everything but Done), then ask Copilot: "Using Task Log.xlsx, summarize what I finished this
year from rows with Status = Done. Group them by Category with counts, highlight the 5 with the most impact,
list who I helped most often (Requester), and note trends by quarter. Keep it to one page."

## Troubleshooting

| Problem | What to do |
|---|---|
| The Send to Planner link doesn't open an email | Copy the lines from the grey box under it into a new email to yourself with the subject TASKSYNC, and send it. |
| Flow 1 doesn't start | Open the trigger and check From is your email and Subject Filter is TASKSYNC. The email must land in your Inbox: if an Outlook rule moves emails from yourself to another folder, turn the rule off for TASKSYNC. |
| Flow 1 runs but creates no tasks | Open the run and look at the Condition: each task line needs 5 parts separated by 4 "\|" signs. Also check the Html to text step is named exactly **BodyText**. |
| Create a task fails on Due Date Time | The agent sent a date in another format. Remind it in the chat ("due dates as YYYY-MM-DD") or fix the line and send the email again. |
| The Excel step fails with a "locked" error | Close the workbook in the desktop Excel app. The next run works. |
| Done tasks still appear in the triage | New rows in the log can take a few hours before Copilot sees them. They're usually picked up by the next morning. |
| The agent still uses the old format | After editing, select **Update**, then start a new chat with the agent. |

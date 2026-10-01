> New guide. We're still testing this one in our own Microsoft 365, and Microsoft's menus change often. If a step doesn't match what you see, tell us at support@sierrix.com.

# Request Approvals Flow

One form for every purchase and time-off request. Each request goes to the right person by your rules. They
approve or reject it with one tap in Teams or straight from the email, the person who asked hears back right
away, and every decision is saved in one list you can search. Approved time off goes on a team calendar
everyone can see.

You don't need to have used Power Automate before. Every click is written out below. Set aside about two
hours the first time, and work on a computer (not a phone).

## How it works

Four pieces work together:

1. **A form** (Microsoft Forms) where staff ask for something.
2. **A flow** (Power Automate) that runs by itself every time someone sends the form. It decides who should
   approve, sends them the request, waits for their answer and acts on it.
3. **A list** (SharePoint) that keeps a record of every request and decision, like a shared spreadsheet.
4. **A calendar** (Outlook) where approved time off appears.

## What it costs

Nothing extra on Microsoft 365 Business Basic, Standard or Premium. Forms, Approvals, SharePoint and Outlook
are "standard" parts of Power Automate, which are included in those plans with no charge per run. It uses no
AI, so it uses no Copilot Credits. (Microsoft Learn: Power Automate licensing FAQ and the Approvals connector
page, checked September 2026.)

## Before you start

Have these ready:

- Your work email and password (the one you use for Outlook at work).
- The work email addresses of the people who approve. This guide uses three, and they can be the same person:
  - the **manager**, who approves smaller purchases,
  - the **owner**, who approves bigger purchases,
  - the **scheduler**, who approves time off.
- The amount where a purchase needs the owner instead of the manager. This guide uses **$500**.
- A SharePoint site your team uses. If your company has Teams, every team already has one: in Teams, open
  the team, select **Files**, then **Open in SharePoint**. Bookmark that page.

## Power Automate basics (read this first)

These moves come up in every step of building the flow. Read them once now; the steps below refer back to
them.

- **Opening Power Automate.** Go to make.powerautomate.com and sign in with your work account. The menu on
  the left has **Create** (to start a new flow) and **My flows** (to find flows you made).
- **The designer.** A flow is shown as a column of boxes, top to bottom. Each box is one step. The first box
  is the **trigger**, the event that starts the flow. Selecting a box opens its settings in a panel at the
  side of the screen.
- **Adding a step.** Point at the line below a box. A **+** appears. Select it, then **Add an action**. A
  search panel opens. Type the step's name exactly as this guide gives it (for example "Get response
  details"), then select it in the results. Each result shows the app it belongs to (Microsoft Forms,
  SharePoint, Office 365 Outlook) so you can pick the right one.
- **Signing in to an app.** The first time you add a step from an app, Power Automate may ask you to sign in
  to it. Select **Sign in** and choose your work account. You only do this once per app.
- **Renaming a step.** Select the box. At the top of the settings panel, select the step's name, delete it,
  type the new name and press **Enter**. This guide tells you what to call each step; the names make the flow
  easier to read and some formulas depend on them.
- **Picking a value from an earlier step.** Click inside a field. Two small buttons appear at the right end of
  it: a lightning bolt and **fx**. Select the **lightning bolt** to see a list of values from earlier steps
  (for example the person's answer to a form question). Select one and it appears in the field as a coloured
  tag. You can type words before and after the tags.
- **Entering a formula.** Click inside the field and select **fx**. A box opens. Paste the formula into the
  box at the top and select **Add**. The formula appears in the field as a purple tag.
- **Saving.** Select **Save** at the top right. If something is missing, Power Automate shows a red mark on
  that box; select it to see what's needed.
- **Seeing what happened.** Go to **My flows**, select the flow's name, and look at **28-day run history**.
  Select a run to see each step with a green tick (worked) or a red mark (failed). Select a failed step to read
  why.

## Step 1: Create the request form

1. Go to forms.office.com and sign in with your work account.
2. Select **New Form**. A blank form opens.
3. Select **Untitled form** at the top and type **Request**. In the description line under it, type
   something like "Ask for a purchase or time off. You'll get an answer by email."
4. Add the questions below one at a time. For each one, select **+ Add new** (or **Add new question**), choose
   the type shown, type the question, and set the options described. To make a question required, turn on the
   **Required** switch at the bottom of the question.

   | # | Question to type | Type | What to set |
   |---|---|---|---|
   | 1 | Request type | Choice | Type **Purchase** in Option 1 and **Time off** in Option 2. Required on. |
   | 2 | What do you need to buy? | Text | Required on. |
   | 3 | How much will it cost? | Choice | Type **Under $500** in Option 1 and **$500 or more** in Option 2. Required on. |
   | 4 | Exact cost in dollars | Text | Required on. Select the three dots (**…**) at the bottom right of the question, choose **Restrictions**, then **Number**. |
   | 5 | Why do you need it? | Text | Turn on **Long answer**. |
   | 6 | First day off | Date | Required on. |
   | 7 | Last day off | Date | Required on. |
   | 8 | Anything we should know? | Text | Turn on **Long answer**. |

   Change $500 to your own amount if it's different. Keep the words **Purchase**, **Time off**, **Under $500**
   and **$500 or more** exactly as you typed them; the flow looks for those exact words.
5. **Send people to the right questions (branching).** Purchases should only see questions 2 to 5, and time
   off only questions 6 to 8.
   1. Select the three dots (**…**) at the top right of the page (next to **Collect responses**), then
      **Branching**. (In some versions it's on the question itself: select the question, then **…** at its
      bottom right, then **Add branching**.)
   2. Next to question 1, there's a drop-down beside each answer. Set **Purchase** to **2. What do you need to
      buy?** and **Time off** to **6. First day off**.
   3. Find question 5 (**Why do you need it?**). In its drop-down at the bottom right, choose **End of the
      form**. Without this, people asking for a purchase would also be shown the time-off questions.
   4. Select **Back** at the top to leave branching.
6. **Collect the person's name and email.** Select the three dots (**…**) at the top right, then **Settings**
   (in some versions **Settings** is a button at the top). Under **Who can fill out this form**, choose **Only
   people in my organization can respond**, and tick **Record name**. The flow uses the email this records to
   tell the person the answer.
7. Select **Preview** at the top and fill in the form twice, once as a purchase and once as time off. Check
   that each one shows only the right questions. Close the preview.

The form saves itself as you go.

## Step 2: Create the Requests list

The list is your record of every request and decision. You'll see it like a spreadsheet in SharePoint.

1. Open your SharePoint site (see "Before you start").
2. Select **+ New** at the top, then **List**, then **Blank list**.
3. Type the name **Requests** and select **Create**. The list opens with one column, **Title**.
4. Add each column in the table below. For each one:
   1. Select **+ Add column** (at the right end of the column headings).
   2. Choose the type shown, then **Next**.
   3. Type the name exactly as shown (capital letters and no spaces, as written).
   4. Set anything listed under "Also set", then select **Save**.

   | Name | Type | Also set |
   |---|---|---|
   | RequestedBy | Single line of text | |
   | RequestType | Choice | Replace Choice 1, 2 and 3 with **Purchase** and **Time off** (delete the third). |
   | Cost | Single line of text | |
   | FirstDay | Date and time | Leave **Include time** off. |
   | LastDay | Date and time | Leave **Include time** off. |
   | Reason | Multiple lines of text | |
   | Decision | Choice | Choices **Approve** and **Reject**. |
   | DecidedBy | Single line of text | |
   | Comments | Multiple lines of text | |

5. If only managers should see the list: select the gear icon (**Settings**) at the top right, then **List
   settings**, then **Permissions for this list**. Otherwise leave it as it is.

## Step 3: Create the team time-off calendar

Use the account of the person who will build the flow (probably you).

1. Open Outlook on the web (outlook.office.com) and select the **calendar** icon on the far left.
2. In the left pane, select **Add calendar**, then **Create blank calendar**.
3. Type the name **Team time off**, leave the colour as it is, and select **Save**. It now appears in the left
   pane under **My calendars**.
4. Point at **Team time off** in the left pane, select the three dots (**…**) next to it, then **Sharing and
   permissions**.
5. Type the names of your team members (or a group such as your whole company), set each to **Can view all
   details**, and select **Share**. They get an email to add the calendar to their own Outlook.

## Step 4: Build the flow

Have "Power Automate basics" above open while you do this step.

### 4a. Start the flow and read the form

1. Go to make.powerautomate.com. In the left menu select **Create**, then **Automated cloud flow**.
2. In **Flow name**, type **Request approvals**.
3. In **Choose your flow's trigger**, type "new response" in the search box and select **When a new response
   is submitted** (Microsoft Forms). Select **Create**. The designer opens with that one box.
4. Select the box. In the panel, open the **Form Id** drop-down and choose **Request**. (If it isn't listed,
   type part of the name in the box to search.)
5. Add a step (**+**, then **Add an action**) and search for **Get response details** (Microsoft Forms).
   Select it. Rename it **Details**.
   - **Form Id:** choose **Request**.
   - **Response Id:** click in the field, select the lightning bolt, and choose **Response Id** (under "When a
     new response is submitted").

   This step fetches the person's answers so the later steps can use them.

### 4b. Decide who approves

This part stores the approver's email in a "variable", a labelled box that holds one value while the flow
runs. It starts as the manager and changes to the scheduler or the owner when the rules say so.

1. Add a step and search for **Initialize variable**. Select it and fill in:
   - **Name:** Approver
   - **Type:** String
   - **Value:** type the manager's email address.
2. Add a step and search for **Condition** (under Control). Select it and rename it **Is time off**. A
   condition is a yes/no question; the flow then goes down the **True** side or the **False** side.
   - Click in the first box (**Choose a value**), select the lightning bolt and choose **Request type** (under
     Details).
   - In the middle drop-down, choose **is equal to**.
   - In the last box, type **Time off**.
3. Under the condition you now see **True** and **False**. Under **True**, select **+** and add **Set
   variable**. Name: **Approver**. Value: type the scheduler's email.
4. Under **False**, select **+** and add another **Condition**. Rename it **Over limit**.
   - First box: lightning bolt, choose **How much will it cost?**
   - Middle: **is equal to**
   - Last box: type **$500 or more** (exactly as in your form)
5. Under the **True** side of **Over limit**, add **Set variable**. Name: **Approver**. Value: type the
   owner's email. Leave the **False** side empty: smaller purchases stay with the manager.

### 4c. Send the approval and wait

1. Below both conditions (select the **+** on the line under the whole **Is time off** block, not inside it),
   add **Start and wait for an approval** (Approvals). Rename it **Approval**.
2. Fill in:
   - **Approval type:** Approve/Reject - First to respond
   - **Title:** lightning bolt → **Request type**, then type " request from ", then lightning bolt →
     **Responders' Email**.
   - **Assigned to:** lightning bolt → **Approver** (under Variables).
   - **Details:** write out the request so the approver sees everything. Type the labels and insert each
     value with the lightning bolt, like this:
     - type "What: " then insert **What do you need to buy?**
     - on a new line type "Cost: $" then insert **Exact cost in dollars**
     - new line: "Why: " then **Why do you need it?**
     - new line: "Days off: " then **First day off**, " to ", **Last day off**
     - new line: "Note: " then **Anything we should know?**

     Answers that don't apply to that request are simply blank.
   - Select **Show all** (or **Advanced parameters**) and set **Requestor** to lightning bolt → **Responders'
     Email**.

The flow pauses at this step until the approver answers.

### 4d. Save the request in the list

1. Below **Approval**, add **Create item** (SharePoint). Rename it **Save request**.
2. **Site Address:** choose your SharePoint site from the drop-down (or paste its address). **List Name:**
   **Requests**. The list's columns now appear as fields.
3. Fill in (lightning bolt for each value unless it says formula):
   - **Title:** **Request type**, type " - ", then **What do you need to buy?**
   - **RequestedBy:** **Responders' Email**
   - **RequestType Value:** **Request type**
   - **Cost:** **Exact cost in dollars**
   - **FirstDay:** **First day off**. **LastDay:** **Last day off**
   - **Reason:** **Why do you need it?**, then **Anything we should know?**
   - **Decision Value:** **Outcome** (under Approval)
   - **DecidedBy:** formula (fx): `first(body('Approval')?['responses'])?['responder']?['displayName']`
   - **Comments:** formula (fx): `first(body('Approval')?['responses'])?['comments']`

   The two formulas pick out who answered and what they wrote. They only work if the approval step is named
   exactly **Approval**.

### 4e. Tell the person and update the calendar

1. Below **Save request**, add a **Condition**. Rename it **Approved**.
   - First box: lightning bolt → **Outcome** (under Approval)
   - Middle: **is equal to**
   - Last box: type **Approve**
2. Under **True**, add **Send an email (V2)** (Office 365 Outlook):
   - **To:** lightning bolt → **Responders' Email**
   - **Subject:** type "Approved: your " then **Request type**, then " request"
   - **Body:** type "Good news, your request was approved by " and then add the same formula as DecidedBy
     above (fx, paste, **Add**).
3. Still under **True**, below that email, add another **Condition**. Rename it **Time off approved**.
   First box: **Request type**. Middle: **is equal to**. Last box: **Time off**.
4. Under the **True** side of **Time off approved**, add **Create event (V4)** (Office 365 Outlook):
   - **Calendar id:** choose **Team time off**.
   - **Subject:** lightning bolt → **Responders' Email**, then type " - off".
   - **Start time:** lightning bolt → **First day off**.
   - **End time:** this needs a formula, because a full-day event has to end at midnight *after* the last day.
     Click in the field and select **fx**. In the formula box type `addDays(` then, still in that box, select
     the **Dynamic content** tab and choose **Last day off**; it's added where your cursor is. Then type
     `, 1)` so the box reads `addDays(` + Last day off + `, 1)`. Select **Add**.
   - **Time zone:** choose your own.
   - Select **Show all** (or **Advanced parameters**). Set **Is all day event?** to **Yes** and **Show as** to
     **Free**.
5. Under the **False** side of **Approved** (the very first condition in this part), add **Send an email
   (V2)**:
   - **To:** **Responders' Email**
   - **Subject:** "Not approved: your " then **Request type**, then " request"
   - **Body:** "Your request wasn't approved. Comments: " then the same formula as Comments above.
6. Select **Save** at the top right. If any box shows a red mark, select it and fill in what it says is
   missing, then save again.

## Step 5: Test it

1. In the designer, select **Test** at the top right, choose **Manually**, then select **Test** (or **Save
   & Test**). The flow now waits for a form response.
2. Open your form (forms.office.com → **Request** → **Preview**, or the link from **Collect responses**) and
   send a purchase request for $100, "Under $500".
3. Go back to the flow. Within a minute the boxes start getting green ticks, and it stops at **Approval**
   (waiting).
4. The manager gets the request in Teams (**Apps → Approvals**, or the Teams activity feed) and by email.
   Select **Approve**, add a comment and confirm.
5. Within a minute or two, the flow finishes with green ticks. Check that:
   - the person who sent the form got an "Approved" email naming who approved it,
   - the **Requests** list has a new row with the decision, who decided and the comment.
6. Send a "$500 or more" purchase. It should go to the owner. Reject it with a comment; the email should
   carry the comment.
7. Send a time-off request. It should go to the scheduler, and once approved it should appear on the **Team
   time off** calendar as an all-day event across the right days.
8. When all of that works, share the form: open it, select **Collect responses**, then **Copy link**. Pin the
   link in a Teams channel or put it on your intranet.

The first time anyone in your company uses Approvals, Microsoft sets it up behind the scenes. The very first
run can take a few minutes or fail once. If it fails, just send the form again.

## Troubleshooting

| Problem | What to do |
|---|---|
| Nothing happens after sending the form | Check the flow is turned on (**My flows**: it should say On). Open the flow and check the trigger and **Details** both use the form **Request**. |
| A purchase over the limit went to the manager | The words in **Over limit** must match the form's answer exactly, including the $ sign and spaces. Copy them from the form. |
| The approver gets nothing | Look in Teams under **Apps → Approvals** and in their email, including Junk. If there's no Approvals app in Teams, ask whoever manages your Microsoft 365 whether it was turned off. |
| **Save request** fails | Select it in the run to read the error. Usually a column name in the list is spelled differently from this guide. |
| **Create event** fails | Check both day-off questions are the Date type, and that End time is the `addDays` formula with **Last day off** inside it. |
| The run stopped after 30 days | An approval waits at most 30 days. Send the request again. |

## Good to know

- Approvers can see everything waiting for them in the Teams **Approvals** app.
- The flow runs as the person who built it. If that person leaves, make someone else an owner first: **My
  flows** → the flow → **Share** → add a colleague as an owner.
- To change the limit or an approver later, edit the form's choice, the **Over limit** condition, or the
  **Set variable** and **Initialize variable** steps, and save.

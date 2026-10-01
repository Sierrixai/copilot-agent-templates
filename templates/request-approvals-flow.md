> New guide. We're still testing this one in our own Microsoft 365, and Microsoft's menus change often. If a step doesn't match what you see, tell us at support@sierrix.com.

# Request Approvals Flow

One form for every purchase and time-off request. Each request goes to the right person by your rules, they
approve or reject it with one tap in Teams or straight from the email, the person who asked hears back right
away, and every decision is saved in one list you can search. Approved time off goes on a team calendar.

## How it works

A Microsoft Forms form collects the request. A Power Automate flow picks the approver, sends an approval,
waits for the answer, saves it in a SharePoint list, emails the person who asked and, for approved time off,
adds it to a shared Outlook calendar. Everything uses standard connectors that come with Microsoft 365, so it
needs no premium license and no Copilot Credits.

## What it costs

Nothing extra on Microsoft 365 Business Basic, Standard or Premium. Microsoft Forms, Approvals, SharePoint and
Office 365 Outlook are standard connectors, included with no cost per run. (Microsoft Learn: Power Automate
licensing FAQ and the Approvals connector page, checked September 2026.)

## You'll need

- Microsoft 365 with Forms, SharePoint, Outlook and Power Automate (make.powerautomate.com).
- A SharePoint site the approvers can open, such as your company's main team site.
- The work email addresses of three approvers (they can be the same person): the manager for small purchases,
  the owner for large ones, and whoever keeps the schedule for time off.
- The amount where a purchase needs the owner instead of the manager. This guide uses $500.

## Step 1: Create the request form

1. Go to forms.office.com and select **New Form**. Name it **Request**.
2. Add these questions, in this order:

   | # | Question | Type | Settings |
   |---|---|---|---|
   | 1 | Request type | Choice | Options: Purchase, Time off. Required |
   | 2 | What do you need to buy? | Text | Required |
   | 3 | Cost in dollars | Text | Restrictions → Number. Required |
   | 4 | Why do you need it? | Text | Long answer |
   | 5 | First day off | Date | Required |
   | 6 | Last day off | Date | Required |
   | 7 | Anything we should know? | Text | Long answer |

3. Select the three dots at the top (**More form settings**) → **Branching**. Set question 1 so **Purchase**
   goes to question 2 and **Time off** goes to question 5. Set question 4 to go to **End of the form**.
4. In **Settings**, choose **Only people in my organization can respond** and tick **Record name**. The flow
   needs the email of the person who asked.
5. Fill in the form twice yourself, once for each type, so the flow has something to test with later.

## Step 2: Create the Requests list

This list is the record of every request and decision.

1. On your SharePoint site, select **New → List → Blank list**. Name it **Requests**.
2. Add these columns (**+ Add column**). Type each name exactly as shown:

   | Column | Type |
   |---|---|
   | RequestedBy | Single line of text |
   | RequestType | Choice: Purchase, Time off |
   | Cost | Number |
   | FirstDay | Date and time (date only) |
   | LastDay | Date and time (date only) |
   | Reason | Multiple lines of text |
   | Decision | Choice: Approve, Reject |
   | DecidedBy | Single line of text |
   | Comments | Multiple lines of text |

   The built-in **Title** column holds what was asked for.
3. Select **Settings (gear) → List settings → Permissions for this list** if only managers should see the
   list. Otherwise leave it as it is.

## Step 3: Create the team time-off calendar

1. In Outlook on the web, go to **Calendar**, select **Add calendar → Create blank calendar**, and name it
   **Team time off**. Use the account of the person who will build the flow.
2. Right-click **Team time off** → **Sharing and permissions**, and share it with your team with **Can view
   all details**.

## Step 4: Build the flow

Rename each action to the name shown in **bold** (select the action's title and type the new name). Some
fields take a formula: click the field, select the **fx** button, and type the formula. Where a formula says
*[pick Cost in dollars]*, switch to the **Dynamic content** tab inside the formula box and pick that answer
from **Details**, then carry on typing. Select **Add** when the formula is complete.

1. Go to make.powerautomate.com and select **Create → Automated cloud flow**. Name it **Request approvals**.
   Choose the trigger Microsoft Forms **When a new response is submitted** and select **Create**. Set **Form
   Id** to **Request**.
2. Add Microsoft Forms **Get response details**. Form Id: **Request**. Response Id: the dynamic value
   **Response Id** from the trigger. Rename it **Details**.
3. Add Variables **Initialize variable**. Name: **Approver**. Type: **String**. Value: the manager's email.
4. Add a **Condition**, renamed **Is time off**. Left: dynamic value **Request type** from Details. Operator:
   **is equal to**. Right: `Time off` (typed).
   - In **True**, add Variables **Set variable**: Name **Approver**, Value the scheduler's email.
   - In **False**, add another **Condition**, renamed **Over limit**. Left (formula):
     `float(` *[pick Cost in dollars]* `)`. Operator: **is greater than**. Right: `500`. In its **True**, add
     **Set variable**: Name **Approver**, Value the owner's email.
5. Below the conditions (not inside them), add Approvals **Start and wait for an approval**, renamed
   **Approval**:
   - Approval type: **Approve/Reject - First to respond**
   - Title: dynamic value **Request type**, then type " request from ", then **Responders' Email**
   - Assigned to: the **Approver** variable
   - Details: write the request out with dynamic values from Details, for example: "Item: *What do you need
     to buy?* · Cost: $*Cost in dollars* · Why: *Why do you need it?* · Days off: *First day off* to *Last
     day off* · Note: *Anything we should know?*". Answers that don't apply stay blank.
   - Requestor (under Advanced parameters): **Responders' Email**
6. Add SharePoint **Create item**, renamed **Save request**. Site Address: your site. List Name: **Requests**.
   - Title (formula): `if(empty(` *[pick What do you need to buy?]* `), 'Time off', ` *[pick What do you
     need to buy?]* `)`
   - RequestedBy: **Responders' Email**
   - RequestType Value: **Request type**
   - Cost (formula): `if(empty(` *[pick Cost in dollars]* `), null, float(` *[pick Cost in dollars]* `))`
   - FirstDay: **First day off**. LastDay: **Last day off**
   - Reason: **Why do you need it?**, then **Anything we should know?**
   - Decision Value: dynamic value **Outcome** from Approval
   - DecidedBy (formula): `first(body('Approval')?['responses'])?['responder']?['displayName']`
   - Comments (formula): `first(body('Approval')?['responses'])?['comments']`
7. Add a **Condition**, renamed **Approved**. Left: **Outcome** from Approval. Operator: **is equal to**.
   Right: `Approve`.
   - In **True**, add Office 365 Outlook **Send an email (V2)**. To: **Responders' Email**. Subject:
     "Approved: your " and **Request type** and " request". Body: "Approved by " and the DecidedBy formula
     from step 6.
   - Still in **True**, add a **Condition**, renamed **Time off approved**: **Request type** is equal to
     `Time off`. In its **True**, add Office 365 Outlook **Create event (V4)**:
     - Calendar id: **Team time off**
     - Subject: **Responders' Email** and " off"
     - Start time (formula): `concat(` *[pick First day off]* `, 'T00:00:00')`
     - End time (formula): `concat(addDays(` *[pick Last day off]* `, 1, 'yyyy-MM-dd'), 'T00:00:00')`. An
       all-day event ends at midnight after the last day.
     - Time zone: yours
     - Advanced parameters: **Is all day event?** Yes, **Show as** Free
   - In **False** (of **Approved**), add **Send an email (V2)**. To: **Responders' Email**. Subject: "Not
     approved: your " and **Request type** and " request". Body: "Comments: " and the Comments formula from
     step 6.
8. Select **Save**.

## Step 5: Test it

1. Select **Test → Manually**, then fill in the form for a $100 purchase.
2. The manager gets an approval in Teams (Approvals app) and by email. Approve it.
3. Within a minute or two, the person who asked gets an "Approved" email and the **Requests** list has a new
   row with the decision and who made it.
4. Repeat with a $900 purchase (it should go to the owner) and a time-off request (it should go to the
   scheduler and, once approved, appear on **Team time off**).
5. Reject one request with a comment. The email should carry the comment.
6. Share the form link with your team (**Collect responses → Copy link**) and pin it in a Teams channel.

The first time anyone in your company uses Approvals, Microsoft sets it up in the background. The first run
can take a few minutes or fail once; run it again.

## Troubleshooting

| Problem | Fix |
|---|---|
| The flow doesn't start | Check that the trigger and **Details** point at the right form, and that the flow is turned on (My flows). |
| **Over limit** fails with a type error | The formula is reading the wrong answer, or the Cost question isn't restricted to numbers. Rebuild the formula with *Cost in dollars* picked from Details. |
| The approver gets nothing | Approvals are in Teams → **Apps → Approvals** and in email. If the Approvals app isn't there, ask whoever manages your Microsoft 365 whether it was turned off. |
| **Create event** fails on the date | Check that both day-off questions are the Date type and that the formulas use *First day off* and *Last day off*. |
| **Save request** fails | Open the run, select **Save request** and read the error. Usually a column name in the list doesn't match the guide exactly. |

## Good to know

- A request waits up to 30 days for an answer; after that the run stops and nothing is saved. Approvers can
  see what's waiting in the Teams Approvals app.
- The flow runs as the person who built it. If that person leaves, make someone else an owner of the flow
  first (My flows → the flow → **Share**).
- To change the $500 limit or an approver, edit the **Over limit** condition or the **Set variable** steps.

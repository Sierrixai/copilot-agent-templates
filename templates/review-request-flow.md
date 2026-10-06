> New guide. We're still testing this one in our own Microsoft 365, and Microsoft's menus change often. If a step doesn't match what you see, tell us at support@sierrix.com.

# Review Request Flow

Happy customers rarely leave a review because nobody asks. Unhappy ones go straight to Google because nobody
heard from them first. This guide sets up two flows that fix both. When a job is marked complete, the customer
gets a thank-you email the next day with a two-question survey and a link to leave a Google review. If they
score you 3 or lower, you get a Teams message with their comment and phone number, so you can call them while
it still matters.

You don't need to have used Power Automate before. Every click is written out below. Set aside about an hour
and a half the first time, and work on a computer (not a phone).

## How it works

Five pieces work together:

1. **A list** (SharePoint) of your jobs, like a shared spreadsheet. When someone changes a job's **Status** to
   **Complete**, everything else starts by itself.
2. **A survey** (Microsoft Forms) with two questions: a score from 1 to 5 and a comment. The job number is
   filled in for the customer, so each answer is matched to the right job.
3. **Flow 1, "Ask for feedback"** (Power Automate). It notices the completed job, waits a day, and emails the
   customer a thank-you with the survey link **and** your Google review link.
4. **Flow 2, "Low score alert"** (Power Automate). It runs each time a survey comes back, saves the answer on
   the job, and if the score is 3 or lower posts a message to the owner in Teams.
5. **Your Google review link**, from your Google Business Profile.

If you already track jobs in another app (a scheduling, field service or invoicing app), check whether it has
its own Power Automate connector with a "job completed" type of trigger. If it does, it can start Flow 1
instead of the SharePoint list. Many of those connectors are marked **Premium** in Power Automate, which this
guide avoids; see "What it costs".

### Why every customer gets the review link

The review link goes to **every** customer in the same email, whatever score they give. Don't change the flow
to send it only to people who scored 4 or 5. Asking only the customers you expect to be happy is called
"review gating", and:

- **Google forbids it.** Google's Maps user-generated content policy ("Prohibited & restricted content", under
  rating manipulation) says businesses must not "discourage or prohibit negative reviews, or selectively
  solicit positive reviews from customers." Reviews collected that way can be removed.
- **The FTC warns against it.** The FTC's guide "Soliciting and Paying for Online Reviews: A Guide for
  Marketers" says: "Don't ask for reviews only from customers you think will leave positive ones." The FTC's
  questions and answers on its Consumer Reviews and Testimonials Rule (November 2024) add that the rule itself
  has no specific ban on this, but the practice could violate the FTC Act.

(Google Business Profile Help, ftc.gov; checked October 2026.)

The low-score alert is there so you can **listen and fix the problem**, not to keep that customer away from
Google. Never offer a discount or a gift in exchange for a review, or for changing or removing one: Google
treats that as fake content too.

## What it costs

Nothing extra on Microsoft 365 Business Basic, Standard or Premium. SharePoint, Microsoft Forms, Office 365
Outlook, Microsoft Teams and the Delay step are "standard" parts of Power Automate, which are included in those
plans with no charge per run. It uses no AI, so it uses no Copilot Credits. (Microsoft Learn: Power Automate
licensing FAQ, checked September 2026.)

Only if you swap the SharePoint list for another app's connector marked **Premium** would the flow's owner need
Power Automate Premium, $15.00 per user per month billed yearly (Microsoft's Power Automate pricing page). The
Google review link is free.

## Before you start

Have these ready:

- Your work email and password (the one you use for Outlook at work).
- The work email of the person who should hear about low scores (the **owner**). It can be you.
- A SharePoint site your team uses. If your company has Teams, every team already has one: in Teams, open the
  team, select **Files**, then **Open in SharePoint**. Bookmark that page.
- Access to your Google Business Profile (the Google account that manages your business on Google Search and
  Maps).
- A personal email address that isn't your work one (Gmail, Yahoo and so on), for testing what a customer
  sees.

### Get your Google review link

1. Go to business.google.com and sign in with the Google account that manages your business. (You can also
   search for your business name on Google while signed in; your profile's controls appear at the top of the
   results.)
2. Select **Read reviews** (in some versions just **Reviews**), then **Get more reviews**.
3. A box shows your review link and a QR code. Select **Copy** next to the link.
4. Paste it somewhere safe, such as a note or an email to yourself. It looks like a short web address
   starting with https. This guide calls it [YOUR GOOGLE REVIEW LINK].
5. Paste it into a browser to check it opens the review box for your business. Don't leave a review on your
   own business.

## Power Automate basics (read this first)

These moves come up in every step of building the flows. Read them once now; the steps below refer back to
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
  (for example the customer's email). Select one and it appears in the field as a coloured tag. You can type
  words before and after the tags. If you don't see the value you want, select **See more** under that step's
  name in the list.
- **Entering a formula.** Click inside the field and select **fx**. A box opens. Paste the formula into the
  box at the top and select **Add**. The formula appears in the field as a purple tag.
- **Saving.** Select **Save** at the top right. If something is missing, Power Automate shows a red mark on
  that box; select it to see what's needed.
- **Seeing what happened.** Go to **My flows**, select the flow's name, and look at **28-day run history**.
  Select a run to see each step with a green tick (worked) or a red mark (failed). Select a failed step to read
  why.

## Step 1: Create the Jobs list

The list is where you (or your team) mark jobs complete. If you already keep jobs in a SharePoint list, add the
missing columns from the table below to it instead, and use your list's name wherever this guide says
**Jobs**.

1. Open your SharePoint site (see "Before you start").
2. Select **+ New** at the top, then **List**, then **Blank list**.
3. Type the name **Jobs** and select **Create**. The list opens with one column, **Title**. You'll use Title
   for a short job name, such as "Kitchen repair, 12 Oak St".
4. Add each column in the table below. For each one:
   1. Select **+ Add column** (at the right end of the column headings).
   2. Choose the type shown, then **Next**.
   3. Type the name exactly as shown (capital letters and no spaces, as written). The flows find columns by
      these exact names, so don't rename them later.
   4. Set anything listed under "Also set", then select **Save**.

   | Name | Type | Also set |
   |---|---|---|
   | CustomerName | Single line of text | |
   | CustomerEmail | Single line of text | |
   | CustomerPhone | Single line of text | |
   | Status | Choice | Replace the three choices with **Scheduled**, **In progress** and **Complete**. Set **Default value** to **Scheduled**. |
   | ReviewRequested | Yes/No | Set **Default value** to **No**. |
   | SurveyScore | Number | |
   | SurveyComment | Multiple lines of text | |

5. **Show the ID column.** Each job gets a number automatically, called **ID**. Customers' survey answers are
   matched to jobs by this number, so it helps to see it. Select **+ Add column**, then **Show or hide
   columns**, tick **ID**, and select **Apply**.

**Why ReviewRequested matters.** The flow starts every time a job is created or changed, not only when its
status changes. Without this column, fixing a typo on a completed job a week later would email the customer
again. The flow ticks ReviewRequested to **Yes** the first time, and never asks that customer about that job
again.

## Step 2: Create the survey

1. Go to forms.office.com and sign in with your work account.
2. Select **New Form**. A blank form opens.
3. Select **Untitled form** at the top and type **How did we do?** In the description line under it, type
   something like "Two quick questions about your recent job. Thank you for helping us improve."
4. Add the three questions below one at a time. For each one, select **+ Add new** (or **Add new question**),
   choose the type shown, type the question, and set the options described. To make a question required, turn
   on the **Required** switch at the bottom of the question.

   | # | Question to type | Type | What to set |
   |---|---|---|---|
   | 1 | How happy are you with the job we did? | Rating | **Levels**: 5. **Symbol**: Number (or star). Required on. |
   | 2 | Anything you'd like to tell us? | Text | Turn on **Long answer**. |
   | 3 | Job number | Text | Required on. Select the three dots (**…**) at the bottom right of the question, choose **Subtitle**, and type "Already filled in for you. Please leave it as it is." |

   If you don't see **Rating**, select the down arrow next to the question types to see more.
5. **Let customers answer without signing in.** Select **Collect responses** (top right). Under who can
   respond, choose **Anyone can respond**. (In some versions this is in **…** at the top right, then
   **Settings**, under **Who can fill out this form**.) If "Anyone can respond" isn't offered, ask whoever
   manages your Microsoft 365 to allow sharing forms outside your organization.
6. **Make the pre-filled link.** This puts the job number into question 3 for each customer.
   1. Select the three dots (**…**) at the top right of the page, then **Get Pre-filled URL**.
   2. Turn on **Enable pre-filled answers**. The form opens as a customer would see it.
   3. Leave questions 1 and 2 empty. In **Job number**, type **99999**.
   4. Select **Get Prefilled Link**, then copy the link it shows.
   5. Paste it with your Google review link. This guide calls it [YOUR PRE-FILLED SURVEY LINK]. The flow swaps
      99999 for each job's real number.
   6. Open the link in a browser to check **Job number** shows 99999.

   If you don't see **Get Pre-filled URL**: copy the plain link from **Collect responses** instead, delete the
   subtitle on question 3, and change the email in Step 3 to tell the customer their job number (the email
   already shows it). Answers are then matched only if the customer types the number in correctly.

The form saves itself as you go.

## Step 3: Build Flow 1, "Ask for feedback"

Have "Power Automate basics" above open while you do this step.

### 3a. Start the flow when a job is marked complete

1. Go to make.powerautomate.com. In the left menu select **Create**, then **Automated cloud flow**.
2. In **Flow name**, type **Ask for feedback**.
3. In **Choose your flow's trigger**, type "created or modified" in the search box and select **When an item
   is created or modified** (SharePoint). Select **Create**. The designer opens with that one box.
4. Select the box. **Site Address:** choose your SharePoint site from the drop-down (or paste its address).
   **List Name:** **Jobs**.
5. **Only start for completed jobs that haven't been asked.** Still in the trigger's panel, select the
   **Settings** tab. Next to **Trigger conditions**, select **+ Add**, and paste this exactly (it must start
   with @):

   ```text
   @and(equals(triggerOutputs()?['body/Status/Value'], 'Complete'), not(equals(triggerOutputs()?['body/ReviewRequested'], true)))
   ```

   In plain words: run only if Status is Complete and ReviewRequested isn't Yes. Any other change to a job
   doesn't start the flow at all, so it doesn't fill your run history.
6. Select the **Parameters** tab to go back. Select **Save**.

### 3b. Tick ReviewRequested straight away

This comes before the wait, so that if someone edits the job during the day, nobody gets asked twice.

1. Add a step and search for **Update item** (SharePoint). Select it. Rename it **Mark as asked**.
2. **Site Address:** your site. **List Name:** **Jobs**.
3. **Id:** lightning bolt → **ID** (under "When an item is created or modified").
4. The list's columns appear as fields. Fill in:
   - **Title:** lightning bolt → **Title**.
   - **ReviewRequested:** choose **Yes**.

   Leave the others empty; empty fields are left as they are. (If **Status Value** shows as required, choose
   **Complete**.)

Changing the job starts the trigger again for a moment, but the trigger condition sees ReviewRequested is now
Yes, so nothing runs.

### 3c. Wait a day

1. Add a step and search for **Delay** (Schedule). Select it. Rename it **Wait a day**.
2. **Count:** 1. **Unit:** Day.

The flow pauses here and carries on by itself the next day. To send it the same evening instead, use 4 and
Hour.

### 3d. Build the survey link for this job

1. Add a step and search for **Compose** (Data Operation). Select it. Rename it **Survey link** (exactly; the
   email formula uses this name).
2. **Inputs:** select **fx**, paste this formula, and replace the bracketed part with your pre-filled link from
   Step 2 (keep the single quotes around it):

   ```text
   replace('[YOUR PRE-FILLED SURVEY LINK]', '99999', string(triggerOutputs()?['body/ID']))
   ```

   Select **Add**. This makes a survey link with this job's number already filled in.

### 3e. Send the thank-you email

1. Add a step and search for **Send an email (V2)** (Office 365 Outlook). Select it. Rename it **Thank-you
   email**.
2. **To:** lightning bolt → **CustomerEmail**.
3. **Subject:** type "Thank you from " and your business name, for example "Thank you from Smith & Co".
4. **Body:** select **fx** and paste the formula below. Before selecting **Add**, change the three bracketed
   parts: your business name, your Google review link and your name.

   ```text
   concat('<p>Hi ', triggerOutputs()?['body/CustomerName'], ',</p><p>Thank you for choosing [YOUR BUSINESS NAME]. We hope everything went well with your job (number ', string(triggerOutputs()?['body/ID']), ').</p><p>Would you answer two quick questions? It takes less than a minute, and we read every answer.</p><p><a href="', outputs('Survey_link'), '">Tell us how we did</a></p><p>We would also be grateful if you shared your experience on Google, good or bad. It helps other people decide and helps us improve.</p><p><a href="[YOUR GOOGLE REVIEW LINK]">Leave us a Google review</a></p><p>Thank you,<br>[YOUR NAME]</p>')
   ```

   The parts between angle brackets (like the p and a parts) are what make paragraphs and clickable links in
   the email; leave them as they are. Change any of the wording you like, but if your text has an apostrophe
   (such as "Joe's"), type it twice ("Joe''s") or the formula won't save. The example avoids apostrophes for
   that reason.
5. Select **Save** at the top right. If any box shows a red mark, select it and fill in what it says is missing,
   then save again.

The email comes from your own mailbox, so replies come back to you.

## Step 4: Build Flow 2, "Low score alert"

### 4a. Start when a survey comes back

1. In Power Automate, select **Create**, then **Automated cloud flow**.
2. **Flow name:** **Low score alert**.
3. Search for "new response" and select **When a new response is submitted** (Microsoft Forms). Select
   **Create**.
4. Select the box. Open the **Form Id** drop-down and choose **How did we do?** (If it isn't listed, type part
   of the name to search.)
5. Add a step and search for **Get response details** (Microsoft Forms). Select it. Rename it **Answers**.
   - **Form Id:** choose **How did we do?**
   - **Response Id:** lightning bolt → **Response Id** (under "When a new response is submitted").

### 4b. Find the job and save the answer on it

1. Add a step and search for **Get item** (SharePoint). Select it. Rename it **Get job**.
2. **Site Address:** your site. **List Name:** **Jobs**.
3. **Id:** this needs a formula, because the form gives the number as text. Click in the field and select
   **fx**. In the formula box type `int(` then, still in that box, select the **Dynamic content** tab and choose
   **Job number** (under Answers); it's added where your cursor is. Then type `)` so the box reads `int(` + Job
   number + `)`. Select **Add**.
4. Add a step and search for **Compose** (Data Operation). Rename it **Score**. **Inputs:** select **fx**, type
   `int(`, choose **How happy are you with the job we did?** from the **Dynamic content** tab, type `)`, and
   select **Add**. This turns the score into a number the flow can compare.
5. Add a step: **Update item** (SharePoint). Rename it **Save answer**.
   - **Site Address:** your site. **List Name:** **Jobs**.
   - **Id:** lightning bolt → **ID** (under Get job).
   - **Title:** lightning bolt → **Title** (under Get job).
   - **SurveyScore:** lightning bolt → **Outputs** (under Score).
   - **SurveyComment:** lightning bolt → **Anything you'd like to tell us?** (under Answers).

   Now every answer is on the job in your list, so you can sort jobs by score.

### 4c. Tell the owner about low scores

1. Add a step and search for **Condition** (under Control). Rename it **Score 3 or lower**.
   - First box: lightning bolt → **Outputs** (under Score).
   - Middle drop-down: **is less than or equal to**.
   - Last box: type **3**.
2. Under **True**, select **+** and add **Post message in a chat or channel** (Microsoft Teams). Rename it
   **Alert owner**.
   - **Post as:** Flow bot.
   - **Post in:** Chat with Flow bot.
   - **Recipient:** type the owner's work email.
   - **Message:** type the words and insert each value with the lightning bolt, like this:
     - type "Low score: " then insert **Outputs** (under Score), then type " out of 5"
     - new line: "Customer: " then **CustomerName** (under Get job)
     - new line: "Phone: " then **CustomerPhone** (under Get job)
     - new line: "Job: " then **Title** (under Get job), " (number ", **ID** (under Get job), ")"
     - new line: "What they said: " then **Anything you'd like to tell us?** (under Answers)
     - new line: "Please call them today."
3. Leave the **False** side empty: good scores are saved on the job and need nothing else.
4. Select **Save**.

To post in a team channel instead (so several people see it), set **Post in** to **Channel** and choose the
team and channel.

## Step 5: Test it

1. **Shorten the wait for the test.** Open **Ask for feedback**, select **Wait a day**, and change it to
   **2** and **Minute**. Select **Save**.
2. In the **Jobs** list, select **+ Add new item**. Fill in a test job: Title "Test job", your own name,
   your **personal** email (not your work one) and your mobile number. Leave Status as **Scheduled**. Select
   **Save**.
3. In **My flows**, open **Ask for feedback** and look at the run history. Nothing should have run: the job
   isn't complete yet.
4. Open the test job in the list, change **Status** to **Complete**, and select **Save**.
5. Within a minute or so, a run of **Ask for feedback** appears, pausing at **Wait a day**. In the list,
   **ReviewRequested** on the test job now shows **Yes**. (The list can take a minute to refresh; reload the
   page.)
6. Two minutes later the run finishes with green ticks. In your personal email, you should see the thank-you
   with two links. Check that:
   - **Tell us how we did** opens the survey with your test job's number in **Job number**,
   - **Leave us a Google review** opens your business's review box (close it without posting).
7. Edit the test job again (change the phone number, say) and save. **Ask for feedback** should **not** run
   again and you should get no second email. This shows each customer is asked only once.
8. In the survey from the email, give a score of **2**, type a comment, and submit.
9. Within a minute or two, the owner gets a Teams message from **Workflows** (or **Power Automate**) with the
   score, your name, phone number, the job and the comment. The test job in the list now shows the score and
   comment.
10. Make a second test job, complete it, and this time answer with a **5**. The score is saved on the job and
    no Teams message is sent.
11. **Put the wait back.** Open **Ask for feedback**, set **Wait a day** to **1** and **Day**, and select
    **Save**. Delete the test jobs from the list.

From now on, just set a job's Status to **Complete** when it's done. Everything else happens by itself.

## Troubleshooting

| Problem | What to do |
|---|---|
| Nothing happens when a job is marked complete | Check the flow is turned on (**My flows**: it should say On). Check the trigger uses your site and the **Jobs** list. Check the trigger condition was pasted exactly, starting with @, and that the Status choice is spelled **Complete**, matching the formula. |
| Customers are emailed more than once | Check **Mark as asked** sets **ReviewRequested** to **Yes**, and that it comes before **Wait a day**. Check the second half of the trigger condition is there. |
| Jobs that were already finished were all emailed | Editing a job that was completed before the flow existed starts the flow for it, because its ReviewRequested is empty. Before you turn the flow on, set **ReviewRequested** to **Yes** on old completed jobs: in the list, select **Edit in grid view** and change them there. |
| **Thank-you email** fails | Select it in the run to read the error. Usually the job has no **CustomerEmail**, or it has a typo. Fix it; the next new job will work. |
| The body formula won't save | An apostrophe in your wording must be typed twice (''), and each bracketed part must be replaced inside the single quotes. Copy the formula again and change only the bracketed parts. |
| The survey opens but **Job number** is empty | The pre-filled link in **Survey link** must be the one from **Get Prefilled Link** with 99999 in it. Make it again (Step 2, part 6) and paste it into the formula. |
| Customers are asked to sign in to the survey | In the form, select **Collect responses** and choose **Anyone can respond**. |
| **Get job** fails | The customer changed or deleted the job number in the survey, or the job was deleted. Look at the answer in Forms (**Responses** tab) and contact them directly. |
| No Teams message for a low score | Check the run of **Low score alert**: if **Score 3 or lower** went to False, check the condition says **is less than or equal to** and 3. If **Alert owner** failed, ask whoever manages your Microsoft 365 whether the Workflows (Power Automate) app is allowed in Teams. |
| Flows stopped after a quiet spell | A flow that hasn't run for 90 days may be turned off; Microsoft emails the owner first. Turn it back on in **My flows**. |

## Good to know

- **Send the review link to everyone.** It's worth saying twice: don't add a condition that sends the Google
  link only to good scores. See "Why every customer gets the review link".
- **Call low scorers, don't argue online.** The point of the alert is to fix things while you still can. If an
  unhappy customer then posts a review, reply politely and briefly on Google, as you would to anyone.
- The flows run as the person who built them, and the thank-you emails come from that person's mailbox. To
  send from a shared mailbox (such as hello@ your business), use **Send an email from a shared mailbox (V2)**
  instead of **Send an email (V2)**; you need permission to send from that mailbox.
- If the builder leaves, make someone else an owner first: **My flows** → the flow → **Share** → add a
  colleague as an owner. Do this for both flows.
- To change the wording later, edit the **Thank-you email** body formula. To change when the email goes,
  edit **Wait a day**. Each run can wait up to 30 days.
- Survey answers are also kept in Forms: open the form and select the **Responses** tab for a summary and an
  **Open in Excel** download.
- Jobs already marked complete before you turn the flow on aren't asked unless someone edits them: their
  **ReviewRequested** is **No** or empty, so an edit starts the flow (see Troubleshooting to stop that).
  To ask a recent customer on purpose, make sure their job's Status is **Complete** and **ReviewRequested** is
  **No**, then save it.

> New guide. We're still testing this one in our own Microsoft 365, and Microsoft's menus change often. If a step doesn't match what you see, tell us at support@sierrix.com.

# Lead Reply Flow

Someone asks about your services at 8pm. Within a few minutes they get a friendly reply from you with a link to
book a time. The lead is saved in one shared list, posted in your sales channel in Teams so someone can claim
it, and if nobody has replied by the time you promise, the owner gets a message saying so.

You don't need to have used Power Automate before. Every click is written out below. Set aside about two
hours the first time, and work on a computer (not a phone).

## How it works

Four pieces work together:

1. **A contact form** (Microsoft Forms) that people fill in to get in touch. If your website already has its
   own form that sends you an email, you can use that instead; see "If your website form sends an email" below
   Step 4.
2. **A flow** (Power Automate) that runs by itself every time a lead arrives. It sends the reply, saves the
   lead, posts it in Teams, waits for the time you promise, and then checks whether anyone replied.
3. **A list** (SharePoint) called **Leads**, like a shared spreadsheet, where every lead is saved and your team
   marks who claimed it and when it was answered.
4. **A Teams channel** where new leads are posted for your team to claim.

## What it costs

Nothing extra on Microsoft 365 Business Basic, Standard or Premium. Forms, Outlook, SharePoint and Teams are
"standard" parts of Power Automate, which are included in those plans with no charge per run. It uses no AI,
so it uses no Copilot Credits. (Microsoft Learn: Power Automate licensing FAQ and the Microsoft Forms, Office
365 Outlook, SharePoint and Microsoft Teams connector pages, checked October 2026.)

Two things would change that, and neither is part of this guide:

- **An AI-written first reply** that answers the question in the lead, instead of the same friendly reply for
  everyone. It's a good upgrade once the basic flow works, but an AI step makes this a premium flow: it needs
  Power Automate Premium ($15.00 per user a month, billed yearly) or a pay-as-you-go price of $0.60 per run,
  and the AI step also uses Copilot Credits (1.5 credits, about $0.015, for every 1,000 tokens, the small pieces of words
  it reads and writes; a short reply is usually one or two thousand). (Microsoft: Power Automate pricing page and
  Microsoft Learn, checked September 2026.)
- **Saving leads straight into a CRM** such as HubSpot or Salesforce, or having your website call the flow
  directly, usually needs a premium connector. This guide saves leads in a SharePoint list instead.

## Before you start

Have these ready:

- Your work email and password (the one you use for Outlook at work).
- Your **booking link**: the web address where people can pick a time to talk to you. If you use Microsoft
  Bookings, open Bookings, choose your booking page and select **Copy link** (in some versions the link is on
  the **Booking page** tab). Any other booking page works too. If you don't have one, you can use your phone
  number instead.
- The **time you promise** to reply in, for example 2 hours. This guide uses **2 hours**.
- The **owner's** work email address: the person who's told when a lead isn't answered in time.
- A **team in Teams** with a standard channel for sales, for example a channel called **Sales** or **New
  leads**. To add one: in Teams, point at the team's name, select the three dots (**…**), then **Add channel**,
  type the name, leave the type as **Standard** and select **Add** (or **Create**). The flow can't post into
  private channels, so use a standard one.
- The team's SharePoint site. Every team in Teams already has one: in Teams, open the team, select **Files**,
  then **Open in SharePoint**. Bookmark that page.

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
  SharePoint, Office 365 Outlook, Microsoft Teams) so you can pick the right one.
- **Signing in to an app.** The first time you add a step from an app, Power Automate may ask you to sign in
  to it. Select **Sign in** and choose your work account. You only do this once per app.
- **Renaming a step.** Select the box. At the top of the settings panel, select the step's name, delete it,
  type the new name and press **Enter**. This guide tells you what to call each step; the names make the flow
  easier to read and some formulas depend on them.
- **Picking a value from an earlier step.** Click inside a field. Two small buttons appear at the right end of
  it: a lightning bolt and **fx**. Select the **lightning bolt** to see a list of values from earlier steps
  (for example the person's answer to a form question). Select one and it appears in the field as a coloured
  tag. You can type words before and after the tags. If you can't see the value you want, select **See more**
  under that step's heading in the list.
- **Entering a formula.** Click inside the field and select **fx**. A box opens. Paste the formula into the
  box at the top and select **Add**. The formula appears in the field as a purple tag.
- **Saving.** Select **Save** at the top right. If something is missing, Power Automate shows a red mark on
  that box; select it to see what's needed.
- **Seeing what happened.** Go to **My flows**, select the flow's name, and look at **28-day run history**.
  Select a run to see each step with a green tick (worked) or a red mark (failed). Select a failed step to read
  why.

## Step 1: Create the contact form

Skip this step if your website form will email you instead; go to Step 2.

1. Go to forms.office.com and sign in with your work account.
2. Select **New Form**. A blank form opens.
3. Select **Untitled form** at the top and type **Get in touch**. In the description line under it, type
   something like "Tell us what you need and we'll get back to you today."
4. Add the questions below one at a time. For each one, select **+ Add new** (or **Add new question**), choose
   the type shown and type the question. To make a question required, turn on the **Required** switch at the
   bottom of the question.

   | # | Question to type | Type | What to set |
   |---|---|---|---|
   | 1 | Your name | Text | Required on. |
   | 2 | Your email | Text | Required on. |
   | 3 | Your phone number | Text | Leave Required off. |
   | 4 | How can we help? | Text | Required on. Turn on **Long answer**. |

5. **Let anyone fill it in.** Select the three dots (**…**) at the top right of the page, then **Settings** (in
   some versions **Settings** is a button at the top). Under **Who can fill out this form**, choose **Anyone can
   respond**. Without this, only people in your company could send it.
6. Select **Preview** at the top, fill the form in with your own details, and check it looks right. Close the
   preview.
7. Get the link to put on your website: select **Collect responses** (or **Share**), then **Copy link**. Add it
   as a "Contact us" or "Get a quote" button on your website, in your email signature and on your social
   pages. You can do this after testing, in Step 5.

The form saves itself as you go.

## Step 2: Create the Leads list

The list is where every lead is saved and where your team marks who has it and whether it was answered.

1. Open your SharePoint site (see "Before you start").
2. Select **+ New** at the top, then **List**, then **Blank list**.
3. Type the name **Leads** and select **Create**. The list opens with one column, **Title**. The flow puts the
   person's name there.
4. Add each column in the table below. For each one:
   1. Select **+ Add column** (at the right end of the column headings).
   2. Choose the type shown, then **Next**.
   3. Type the name exactly as shown (capital letters and no spaces, as written).
   4. Set anything listed under "Also set", then select **Save**.

   | Name | Type | Also set |
   |---|---|---|
   | Email | Single line of text | |
   | Phone | Single line of text | |
   | Message | Multiple lines of text | |
   | Status | Choice | Replace Choice 1, 2 and 3 with **New**, **Claimed** and **Replied**. Set **Default value** to **New**. |
   | ClaimedBy | Person | |

5. Check it: the list now shows the columns **Title**, **Email**, **Phone**, **Message**, **Status** and
   **ClaimedBy**. Copy the page's web address from the browser's address bar and keep it; you'll put it in the
   Teams channel in Step 5.

Your team will use only two columns by hand: **ClaimedBy** (who's taking the lead) and **Status** (set to
**Replied** once they've answered). To change them, select the lead's row, then **Edit** at the top (or open
the row and select **Edit all**), change the value and select **Save**.

## Step 3: Write your reply

Before building the flow, write the reply every lead will get. Keep it short and personal. Copy this and
change the words in brackets to your own:

```text
Hi [first name from the form],

Thanks for getting in touch with [YOUR COMPANY NAME]. We've got your message and one of us will get back to
you personally within [YOUR REPLY TIME] during business hours.

If you'd like to talk sooner, pick a time that suits you here: [YOUR BOOKING LINK]

Speak soon,
[YOUR NAME]
[YOUR PHONE NUMBER]
```

In Step 4 you'll paste this into the email step and swap "[first name from the form]" for the person's name.

## Step 4: Build the flow

Have "Power Automate basics" above open while you do this step.

### 4a. Start the flow and read the form

1. Go to make.powerautomate.com. In the left menu select **Create**, then **Automated cloud flow**.
2. In **Flow name**, type **Lead reply**.
3. In **Choose your flow's trigger**, type "new response" in the search box and select **When a new response
   is submitted** (Microsoft Forms). Select **Create**. The designer opens with that one box.
4. Select the box. In the panel, open the **Form Id** drop-down and choose **Get in touch**. (If it isn't
   listed, type part of the name in the box to search.)
5. Add a step (**+**, then **Add an action**) and search for **Get response details** (Microsoft Forms).
   Select it. Rename it **Details**.
   - **Form Id:** choose **Get in touch**.
   - **Response Id:** click in the field, select the lightning bolt, and choose **Response Id** (under "When a
     new response is submitted").

   This step fetches the person's answers so the later steps can use them.

### 4b. Send the reply

1. Below **Details**, add **Send an email (V2)** (Office 365 Outlook). Rename it **Reply to lead**.
2. Fill in:
   - **To:** lightning bolt → **Your email** (under Details).
   - **Subject:** type "Thanks for getting in touch, " then lightning bolt → **Your name**.
   - **Body:** paste the reply you wrote in Step 3. Delete "[first name from the form]" and, in its place,
     insert lightning bolt → **Your name**. Check you've replaced every other word in brackets with your own.
3. The reply comes from your own mailbox and lands in your **Sent Items**, so when the lead answers, the answer
   comes to you. If you'd rather it came from a shared address such as info@ or sales@, use **Send an email
   from a shared mailbox (V2)** instead and type that address in **Original Mailbox Address**. You need
   permission to send from that mailbox; whoever manages your Microsoft 365 can give it.

### 4c. Save the lead in the list

1. Below **Reply to lead**, add **Create item** (SharePoint). Rename it **Save lead**.
2. **Site Address:** choose your SharePoint site from the drop-down (or paste its address). **List Name:**
   **Leads**. The list's columns now appear as fields.
3. Fill in (lightning bolt for each value):
   - **Title:** **Your name**
   - **Email:** **Your email**
   - **Phone:** **Your phone number**
   - **Message:** **How can we help?**
   - **Status Value:** choose **New** from the drop-down. (If **Status** is hidden, select **Show all** or
     **Advanced parameters** to see it.)

   Leave **ClaimedBy** empty. Your team fills it in.

### 4d. Post the lead in Teams

1. Below **Save lead**, add **Post message in a chat or channel** (Microsoft Teams). In some versions it's
   called **Post a message in a chat or channel**. Rename it **Post to sales**.
2. Fill in:
   - **Post as:** **Flow bot**
   - **Post in:** **Channel**
   - **Team:** choose your team.
   - **Channel:** choose your sales channel.
   - **Message:** type the lines below, inserting each value with the lightning bolt where shown:
     - "New lead: " then **Your name**
     - on a new line, "Email: " then **Your email**, then " Phone: " then **Your phone number**
     - new line: "Message: " then **How can we help?**
     - new line: "Open the lead: " then **Link to item** (under Save lead; in some versions it's called
       **Link**)
     - new line: "To claim it, open the lead, put your name in ClaimedBy and reply to them. When you've
       replied, set Status to Replied. We promised a reply within 2 hours."

     Change "2 hours" to the time you promise.

### 4e. Wait, then check whether anyone replied

1. Below **Post to sales**, add **Delay** (under Schedule). Rename it **Wait for reply time**.
   - **Count:** 2
   - **Unit:** **Hour**

   The flow pauses here for 2 hours. Change it to the time you promise. While you test (Step 5), set it to
   **2** and **Minute** so you don't have to wait.
2. Below **Wait for reply time**, add **Get item** (SharePoint). Rename it **Check lead**.
   - **Site Address:** your site. **List Name:** **Leads**.
   - **Id:** lightning bolt → **ID** (under Save lead).

   This reads the lead again, as it is now, so the flow can see whether someone changed its status.
3. Below **Check lead**, add **Condition** (under Control). Rename it **Not answered yet**. A condition is a
   yes/no question; the flow then goes down the **True** side or the **False** side.
   - Click in the first box (**Choose a value**), select the lightning bolt and choose **Status Value** (under
     Check lead).
   - In the middle drop-down, choose **is not equal to**.
   - In the last box, type **Replied**.
4. Under **True**, add **Send an email (V2)** (Office 365 Outlook). Rename it **Tell owner**.
   - **To:** type the owner's email address.
   - **Subject:** type "Lead not answered: " then lightning bolt → **Your name** (under Details).
   - **Body:** type "This lead hasn't been marked as replied after 2 hours. Claimed by: " then lightning bolt →
     **ClaimedBy DisplayName** (under Check lead), then on a new line "Open the lead: " then **Link to item**
     (under Save lead).

     If nobody claimed it, "Claimed by" is blank.
5. Optional: also under **True**, below **Tell owner**, add **Post message in a chat or channel** again, with
   **Post as** set to **Flow bot**, **Post in** set to **Chat with Flow bot**, and **Recipient** set to the
   owner's email, with the same words as the email. The owner then also gets the alert in Teams.
6. Leave the **False** side empty: if the lead was answered, there's nothing to do.
7. Select **Save** at the top right. If any box shows a red mark, select it and fill in what it says is
   missing, then save again.

### If your website form sends an email

Many website forms send each enquiry to an email address instead of using Microsoft Forms. The flow can start
from that email instead. Build it the same way, with these changes.

**Before you build:**

- Have the website send its enquiries to one address that gets nothing else, such as leads@ or a mailbox you
  set up just for this. If they go to your normal inbox, set up an Outlook rule that moves them into a folder
  called **Leads**, and note a word that's always in their subject line (for example "New enquiry").
- Send yourself a test enquiry from the website and look at it in Outlook. Check two things:
  - Who it's **from**. If it shows the visitor's own email address, good. If it shows your website's address,
    open your website form's settings and look for a **Reply-to** setting; set it to the visitor's email field.
    Most website form tools have one.
  - What the email says. Most show the name, email and message in the body.

**In 4a**, instead of the Forms trigger and **Details**:

1. When creating the flow, search for "new email" and choose **When a new email arrives (V3)** (Office 365
   Outlook). If the leads address is a shared mailbox, choose **When a new email arrives in a shared mailbox
   (V2)** instead and type the mailbox address in **Original Mailbox Address**.
2. Select the trigger and set **Folder** to **Inbox** (or your **Leads** folder). Select **Show all** (or
   **Advanced parameters**) and, if the address also gets other mail, type the subject word in **Subject
   Filter**. The flow then runs only for enquiries.
3. There's no **Details** step. Use these values instead wherever the guide says a form answer:
   - **Your email** (the lead's address): a formula (fx) that uses the Reply-to address if there is one and
     the sender's address if not:

     `if(empty(triggerOutputs()?['body/replyTo']), triggerOutputs()?['body/from'], triggerOutputs()?['body/replyTo'])`

   - **Your name:** lightning bolt → **Subject** (the name usually isn't a separate value in an email, so the
     subject line stands in for it). In the reply's subject and greeting, use "Thanks for getting in touch"
     and "Hi there" instead of the name.
   - **How can we help?:** lightning bolt → **Body Preview** (the start of the email).
   - **Your phone number:** leave the field empty.

**In 4d**, add a line "Full email is in the leads inbox." so whoever claims it reads the whole message.

Everything from 4c onward works the same way.

## Step 5: Test it

1. In the designer, select **Delay** (**Wait for reply time**) and check it's set to **2** and **Minute** for
   now. Save.
2. Select **Test** at the top right, choose **Manually**, then select **Test** (or **Save & Test**). The flow
   now waits for a lead.
3. Open your form's link (from **Collect responses**) in a private browser window, or on your phone, and fill
   it in as if you were a customer. Use a personal email address you can check, not your work address.
4. Go back to the flow. Within a minute or two the boxes start getting green ticks, and it pauses at **Wait for
   reply time**. Check that:
   - the personal address got your reply, with the right name and a booking link that opens,
   - the **Leads** list has a new row with Status **New**,
   - the lead is posted in your sales channel in Teams, and its link opens the row in the list.
5. Leave it. After 2 minutes the flow checks the lead, sees it isn't marked **Replied**, and the owner gets a
   "Lead not answered" email.
6. Test the other way: send another lead. This time, before 2 minutes are up, open its row in the **Leads**
   list, put your name in **ClaimedBy**, set **Status** to **Replied** and save. When the 2 minutes are up the
   flow finishes with no email to the owner (the **False** side).
7. When both work, set **Wait for reply time** back to the time you promise (for example **2** and **Hour**),
   select **Save**, and share the form link on your website (Step 1, point 7).
8. Pin the **Leads** list in the sales channel so the team can find it: open the channel, select **+** at the
   top (next to the channel's tabs), choose **Website** (in some versions **SharePoint** or **Lists**), and
   paste the list's address you copied in Step 2. Save.

## Troubleshooting

| Problem | What to do |
|---|---|
| Nothing happens after sending the form | Check the flow is turned on (**My flows**: it should say On). Open the flow and check the trigger and **Details** both use the form **Get in touch**. A new lead can take a few minutes to start the flow. |
| The lead didn't get the reply | Look in their Junk folder. Check **To** in **Reply to lead** is **Your email**, and that the address they typed has no spaces or mistakes. |
| **Save lead** fails | Select it in the run to read the error. Usually a column name in the list is spelled differently from this guide. |
| Nothing appears in Teams | Check **Post as** is **Flow bot** and the channel is a standard channel, not a private one. If Teams says the Workflows app is blocked, ask whoever manages your Microsoft 365 to allow it in the Teams admin center. |
| The owner is told even though someone replied | The lead's **Status** must be exactly **Replied** before the wait ends. Check the condition says **is not equal to** and **Replied** with the same spelling as the list. |
| The owner is never told | Check the condition's first box is **Status Value** under **Check lead**, not under **Save lead** (that one is always New). |
| Website-email version replies to your own website | The email's From is the website. Set your website form's Reply-to to the visitor's email (see "If your website form sends an email") and send a new test. |
| Website-email version runs for every email | Add a **Subject Filter**, or point the trigger at the **Leads** folder only. |

## Good to know

- The wait counts every hour, day or night. A lead that comes in at 8pm with a 2-hour promise alerts the
  owner at 10pm. If that's too early, promise a longer time (for example "by noon the next working day") and
  set the delay to match, or have the owner ignore alerts outside working hours.
- A run can wait at most 30 days, so any delay up to a few days is fine.
- Your team needs only one habit: when you've replied to a lead, set its **Status** to **Replied**. The list
  then shows at a glance which leads are open; to see only those, select **Status** at the top of the column
  and filter to **New** and **Claimed**.
- The flow runs as the person who built it, and replies come from their mailbox (or the shared one). If that
  person leaves, make someone else an owner first: **My flows** → the flow → **Share** → add a colleague as an
  owner.
- A flow that hasn't run for 90 days may be turned off by Microsoft; you get a warning email first. If leads
  are rare, check **My flows** now and then.
- Want the first reply to answer the person's actual question? An AI step can write it from their message.
  That makes it a premium flow and uses Copilot Credits (see "What it costs"). Build and use the simple version
  first; it's the part that gets every lead answered.

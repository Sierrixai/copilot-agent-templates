> New guide. We're still testing this one in our own Microsoft 365, and Microsoft's menus change often. If a step doesn't match what you see, tell us at support@sierrix.com.

# Supplier Invoice Intake

No more retyping invoices. Suppliers email their invoices to one bills mailbox (such as bills@). Microsoft's AI
reads each PDF and pulls out the supplier, invoice number, dates and amount. The PDF is saved in that
supplier's folder in SharePoint and added to a Bills list. Bills over the amount you set go to the owner, who
approves them with one tap in Teams. Smaller bills are approved automatically. Once a week, the approved bills
are put in a spreadsheet file and emailed to whoever does your books, ready to import.

You don't need to have used Power Automate before. Every click is written out below. Set aside about three
hours the first time, and work on a computer (not a phone).

## How it works

Five pieces work together:

1. **A bills mailbox** (a shared mailbox in Microsoft 365) where suppliers send invoices.
2. **An Invoices library** (SharePoint) with one folder per supplier, where every PDF is saved.
3. **A Bills list** (SharePoint) with one row per invoice: supplier, number, dates, amount, status and who
   approved it. You'll see it like a spreadsheet.
4. **Flow 1** (Power Automate), which runs on every email with an attachment: the AI reads the invoice, the
   flow files it, logs it and sends it for approval when needed.
5. **Flow 2** (Power Automate), which runs once a week and emails the approved bills as a CSV file (opens in
   Excel) to whoever does your books.

The AI only reads. A person decides: anything over your limit, anything the AI couldn't read an amount from,
and anything that looks like a duplicate always goes to the owner.

## What it costs

- **Reading invoices uses Copilot Credits.** Microsoft charges 8 Copilot Credits per page read (about $0.08 a
  page, at $0.01 per credit). Most invoices are one page, so 100 invoices a month costs about $8.
- **The AI step makes Flow 1 a "premium" flow.** The person who builds it needs a Power Automate Premium
  license ($15 per user per month on a yearly plan), or the flow runs in a pay-as-you-go environment at $0.60
  per run. Every email with an attachment that reaches the bills mailbox is a run, so above about 25 emails a
  month the Premium license is cheaper.
- **Flow 2 uses only standard parts** of Power Automate (SharePoint, Outlook and a built-in step that makes the
  CSV file), so it's included with Microsoft 365.
- **Your accounting software:** the default here is a CSV file you import, which costs nothing extra. The
  optional step at the end sends approved invoices to your accounting software's own bills email address, also
  at no extra cost. A direct connection inside Power Automate is usually a premium connector made by another
  company (see Good to know).

(Microsoft Learn: AI Builder licensing and the end of AI Builder credits, Power Automate pricing and
pay-as-you-go meters, checked October 2026.)

## Before you start

Ask whoever manages your Microsoft 365 for these first. The guide doesn't work without them:

- **A shared mailbox for bills**, for example bills@yourcompany.com, with **Full Access** for you (the person
  building the flow). They set it up in the Microsoft 365 admin center under **Teams & groups → Shared
  mailboxes**: add the mailbox, then open it and add you under **Members**. Full Access can take up to an hour
  to start working.
- **A Power Automate Premium license** for you, or a pay-as-you-go environment.
- **Copilot Credits available in the environment where you build the flow** (usually the one Power Automate
  opens in). See "Setting up Copilot Credits" under Good to know: it's a few minutes of work for your admin.

And have these ready:

- A SharePoint site where finance files can live. If your company has Teams, every team already has one: in
  Teams, open the team, select **Files**, then **Open in SharePoint**. Bookmark that page. Choose a team whose
  members are allowed to see supplier bills.
- The owner's work email (they approve bills).
- The email address of whoever does your books (they get the weekly file).
- **Your approval limit.** Bills above it go to the owner. This guide starts at **$0**, so every bill goes to
  the owner while you check what the AI reads. After a month or two, change it to your real limit (for
  example $500).
- A test invoice as a PDF. Any real supplier invoice you've already dealt with works.

## Power Automate basics (read this first)

These moves come up in steps 3 to 6. Read them once now; the steps refer back to them.

- **Opening Power Automate.** Go to make.powerautomate.com and sign in with your work account. The menu on
  the left has **Create** (to start a new flow) and **My flows** (to find flows you made).
- **The designer.** A flow is shown as a column of boxes, top to bottom. Each box is one step. The first box
  is the **trigger**, the event that starts the flow. Selecting a box opens its settings in a panel at the
  side of the screen.
- **Adding a step.** Point at the line below a box. A **+** appears. Select it, then **Add an action**. A
  search panel opens. Type the step's name exactly as this guide gives it, then select it in the results.
  Each result shows the app it belongs to (SharePoint, Office 365 Outlook, AI Builder, Approvals) so you can
  pick the right one.
- **Adding a step inside a condition or loop.** Some boxes (Condition, Apply to each) contain other boxes. To
  add a step inside, use the **+** that appears *inside* the box (for a condition, inside its **True** or
  **False** side), not the one below it. Most of Flow 1 sits inside one loop and one condition, so watch for
  this.
- **Signing in to an app.** The first time you add a step from an app, Power Automate may ask you to sign in.
  Select **Sign in** and choose your work account. You only do this once per app.
- **Renaming a step.** Select the box. At the top of the settings panel, select the step's name, delete it,
  type the new name exactly as this guide shows it (no spaces), and press **Enter**. Formulas in this guide
  use these names, so a typo in a name breaks the formula. Rename each step as soon as you add it, before you
  use it in a later step.
- **Picking a value from an earlier step.** Click inside a field. Two small buttons appear at its right end:
  a lightning bolt and **fx**. Select the **lightning bolt** to see values from earlier steps, and select one.
  It appears in the field as a coloured tag. You can type words before and after the tags. If a value isn't
  listed, select **See more** under that step's name, or type part of its name in the search box at the top.
- **Entering a formula.** Formulas in this guide are shown in grey boxes like `this`. Click inside the field,
  select **fx**, paste the formula into the box at the top (exactly, including brackets and quote marks) and
  select **Add**. It appears in the field as a purple tag.
- **A formula with a value inside it.** A few formulas need a value from an earlier step in the middle. In the
  formula box, type the first part, then select the **Dynamic content** tab (next to **Function**) and choose
  the value; it's added where your cursor is. Then type the rest and select **Add**.
- **Saving.** Select **Save** at the top right. If something is missing, Power Automate shows a red mark on
  that box; select it to see what's needed.
- **Seeing what happened.** Go to **My flows**, select the flow's name, and look at **28-day run history**.
  Select a run to see each step with a green tick (worked) or a red mark (failed). Select a step to see what
  went in and what came out, including what the AI read.

## Step 1: Create the Invoices library

The library holds the PDFs, one folder per supplier. The flow creates each supplier's folder the first time
that supplier sends an invoice.

1. Open your SharePoint site (see "Before you start").
2. Select **+ New** at the top, then **Document library**. (If you see a choice of templates, choose **Blank
   library**.)
3. Type the name **Invoices** and select **Create**. Keep the name exactly as written: the flow uses it.
4. Open the new library, select **+ New**, then **Folder**, type **Exports** and select **Create**. Flow 2
   saves the weekly files here.
5. Check who can see it: select the gear icon (**Settings**) at the top right, then **Library settings**,
   then **Permissions for this document library**. If people on the site shouldn't see supplier bills, ask
   whoever manages your Microsoft 365 to limit it to the owner and the people who handle bills.

## Step 2: Create the Bills list

1. Go back to the site's home page. Select **+ New**, then **List**, then **Blank list**.
2. Type the name **Bills** and select **Create**. The list opens with one column, **Title**, which will hold
   the supplier's name.
3. Add each column in the table below. For each one:
   1. Select **+ Add column** (at the right end of the column headings).
   2. Choose the type shown, then **Next**.
   3. Type the name exactly as shown (capital letters, no spaces).
   4. Set anything under "Also set", then select **Save**.

   | Name | Type | Also set |
   |---|---|---|
   | InvoiceNumber | Single line of text | |
   | Amount | Currency | Choose your currency. |
   | InvoiceDate | Date and time | Leave **Include time** off. |
   | DueDate | Date and time | Leave **Include time** off. |
   | Status | Choice | Choices: **Waiting for approval**, **Approved**, **Rejected**, **Exported** (select **Add choice** for a fourth box). |
   | ApprovedBy | Single line of text | |
   | Comments | Multiple lines of text | |
   | InvoiceFile | Hyperlink | |
   | Sender | Single line of text | |

4. Give the list the same permissions as the Invoices library (gear icon → **List settings** →
   **Permissions for this list**).
5. Optional: to see bills in Teams, open the team's channel, select **+** at the top (next to the tabs),
   choose **Lists** (or **SharePoint**), and pick **Bills**.

To see what's due soon, select the **DueDate** column heading and choose to sort oldest to newest.

## Step 3: Start Flow 1 and connect the bills mailbox

1. Go to make.powerautomate.com. Select **Create**, then **Automated cloud flow**.
2. **Flow name:** Supplier invoice intake.
3. In **Choose your flow's trigger**, search "shared mailbox" and select **When a new email arrives in a shared
   mailbox (V2)** (Office 365 Outlook). Select **Create**.
4. Select the trigger box and fill in:
   - **Original Mailbox Address:** type the bills mailbox address.
   - **Folder:** Inbox.
   - Select **Show all** (or **Advanced parameters**) and set **Only with Attachments** to **Yes** and
     **Include Attachments** to **Yes**. Without the second one, the flow gets the email but not the PDF.
5. Select **Save** (Power Automate saves the flow even though it's not finished).

If your bills arrive in your own mailbox instead of a shared one, use **When a new email arrives (V3)** (Office
365 Outlook) as the trigger, set **Folder** to a folder your Outlook rule moves bills into, and set the same two
attachment options. Everything else is the same.

## Step 4: Read each PDF with AI

An email can have several attachments, including small logo images from the sender's signature. This part
goes through them one at a time and only reads PDFs.

### 4a. Go through the attachments

1. Below the trigger, add **Apply to each** (Control; in some versions it's called **For each**). Rename it
   **EachAttachment**.
2. **Select an output from previous steps:** lightning bolt → **Attachments** (under When a new email arrives).
3. Inside **EachAttachment**, add a **Condition** (Control). Rename it **IsPdf**.
   - Row 1: first box lightning bolt → **Attachments Name**; middle **ends with**; last box type **.pdf**
   - Select **+ New item** (or **+ Add**), then **Add row**. Row 2: **Attachments Name**, **ends with**,
     **.PDF**
   - Where the rows are joined, change **And** to **Or**.

Everything in steps 4b to 5 goes inside the **True** side of **IsPdf**. Leave **False** empty: other files are
skipped.

### 4b. The AI step

1. Inside **True**, add **Extract information from invoices** (AI Builder). Rename it **ReadInvoice**.
2. **Invoice file:** lightning bolt → **Attachments Content** (under EachAttachment or When a new email
   arrives).
3. Leave **Pages** empty for now (see Good to know for using it to keep costs down).

The AI gives back many values: **Vendor name**, **Invoice ID**, **Invoice date (date)**, **Due date (date)**,
**Amount due (number)**, **Invoice total (number)**, the line items and more, each with a confidence score from
0 to 1.

### 4c. Tidy what the AI read

These four **Compose** steps give each value a fixed name and fill in gaps, so a missing value never stops the
flow. Add each one below the last, still inside **True**. **Compose** is under Data Operation; it has one field,
**Inputs**. Each formula has a value from **ReadInvoice** in the middle (see "A formula with a value inside it").

1. **Compose**, renamed **Vendor**. Inputs: fx, type `coalesce(`, then choose **Vendor name** from
   Dynamic content, then type `, 'Unknown vendor')`. Select **Add**.
2. **Compose**, renamed **VendorFolder**. Inputs: fx, paste this and select **Add**:

   `trim(replace(replace(replace(replace(replace(replace(replace(replace(replace(replace(outputs('Vendor'), '/', '-'), '\', '-'), ':', '-'), '*', ''), '?', ''), '"', ''), '<', ''), '>', ''), '|', '-'), '.', ''))`

   This makes the supplier's name safe to use as a folder name (SharePoint doesn't allow some characters, such
   as / and :, in folder names).
3. **Compose**, renamed **InvoiceNumber**. Inputs: fx, type `coalesce(`, choose **Invoice ID**, then type
   `, 'no-number')`. Select **Add**.
4. **Compose**, renamed **Amount**. Inputs: fx, type `coalesce(`, choose **Amount due (number)**, type `, `
   (comma and space), choose **Invoice total (number)**, then type `, 0)`. Select **Add**. This uses the amount
   due, or the invoice total when there's no amount due, or 0 when the AI found neither.

### 4d. Check for a duplicate

Suppliers often send the same invoice twice. This step looks in the Bills list for the same supplier and
invoice number.

1. Add **Get items** (SharePoint). Rename it **CheckDuplicate**.
2. **Site Address:** your site. **List Name:** Bills.
3. Select **Show all** (or **Advanced parameters**) to find **Filter Query**. Click in it, select **fx**, paste
   this and select **Add**:

   `concat('InvoiceNumber eq ''', replace(outputs('InvoiceNumber'), '''', ''''''), ''' and Title eq ''', replace(outputs('Vendor'), '''', ''''''), '''')`

   The many quote marks are on purpose: they let names like O'Brien work. Paste it exactly.
4. **Top Count:** type 1.

## Step 5: File it, log it and approve it

Still inside the **True** side of **IsPdf**, below **CheckDuplicate**.

### 5a. Save the PDF in the supplier's folder

1. Add **Create file** (SharePoint). Rename it **SaveInvoice**.
2. **Site Address:** your site.
3. **Folder Path:** type **/Invoices/** and then lightning bolt → **Outputs** (under VendorFolder). It reads
   /Invoices/ followed by a purple or coloured tag. If that supplier's folder doesn't exist yet, it's created.
4. **File Name:** fx, paste this and select **Add**:

   `concat(replace(replace(replace(outputs('InvoiceNumber'), '/', '-'), '\', '-'), ':', '-'), ' ', formatDateTime(utcNow(), 'yyyy-MM-dd HHmmss'), '.pdf')`

   For example "INV-1042 2026-10-06 141503.pdf". The date and time stop a second copy from replacing the first.
5. **File Content:** lightning bolt → **Attachments Content**.
6. Add **Get file properties** (SharePoint). Rename it **InvoiceFile**. **Site Address:** your site. **Library
   Name:** Invoices. **Id:** lightning bolt → **ItemId** (under SaveInvoice). This step gets the link to the
   saved PDF.

### 5b. Add the bill to the list

1. Add **Create item** (SharePoint). Rename it **LogBill**.
2. **Site Address:** your site. **List Name:** Bills. The list's columns appear as fields.
3. Fill in, using the lightning bolt:
   - **Title:** **Outputs** (under Vendor)
   - **InvoiceNumber:** **Outputs** (under InvoiceNumber)
   - **Amount:** **Outputs** (under Amount)
   - **InvoiceDate:** **Invoice date (date)** (under ReadInvoice)
   - **DueDate:** **Due date (date)** (under ReadInvoice)
   - **Status Value:** choose **Waiting for approval**
   - **InvoiceFile:** **Link to item** (under InvoiceFile)
   - **Sender:** **From** (under When a new email arrives)

### 5c. Decide whether the owner approves

1. Add a **Condition**. Rename it **NeedsApproval**. It needs three rows joined by "Or":
   - Row 1: first box lightning bolt → **Outputs** (under Amount); middle **is greater than**; last box type
     **0**. This is your approval limit. Leave it at 0 for now; change it to your real limit later.
   - Add row 2: first box fx `length(body('CheckDuplicate')?['value'])`; middle **is greater than**; last box
     **0**. True when the list already has this invoice.
   - Add row 3: first box **Outputs** (under Amount); middle **is equal to**; last box **0**. True when the AI
     couldn't find an amount.
   - Change **And** to **Or**.

### 5d. Bills that need the owner (True side of NeedsApproval)

1. Inside **True**, add **Start and wait for an approval** (Approvals). Rename it **Approval**.
2. Fill in:
   - **Approval type:** Approve/Reject - First to respond
   - **Title:** type "Bill to approve: " then lightning bolt → **Outputs** (under Vendor), type " $", then
     **Outputs** (under Amount).
   - **Assigned to:** type the owner's email.
   - **Details:** type the labels and insert each value with the lightning bolt:
     - "Supplier: " then **Outputs** (Vendor)
     - new line: "Invoice number: " then **Outputs** (InvoiceNumber)
     - new line: "Amount: $" then **Outputs** (Amount)
     - new line: "Due: " then **Due date (text)** (ReadInvoice)
     - new line: "Possible duplicate: " then fx
       `if(greater(length(body('CheckDuplicate')?['value']), 0), 'YES, check before approving', 'No')`
     - new line: "Amount of 0 means the AI couldn't read it. Open the PDF to check."
   - Select **Show all** (or **Advanced parameters**):
     - **Item link:** lightning bolt → **Link to item** (under InvoiceFile). **Item link description:** type
       "Open the invoice".
     - **Attachments Name - 1:** **Attachments Name**. **Attachments Content - 1:** **Attachments Content**.
       (In some versions this is an **Attachments** section with **Name** and **Content**.) The PDF then comes
       with the request.
3. Below **Approval**, still inside **True**, add a **Condition**. Rename it **Approved**. First box: lightning
   bolt → **Outcome** (under Approval). Middle: **is equal to**. Last box: type **Approve** (capital A).
4. Inside the **True** side of **Approved**, add **Update item** (SharePoint). Rename it **MarkApproved**.
   - **Site Address:** your site. **List Name:** Bills.
   - **Id:** lightning bolt → **ID** (under LogBill).
   - **Title:** **Outputs** (under Vendor). SharePoint needs it even though it doesn't change.
   - **Status Value:** **Approved**.
   - **ApprovedBy:** fx `first(body('Approval')?['responses'])?['responder']?['displayName']`
   - **Comments:** fx `first(body('Approval')?['responses'])?['comments']`
5. Inside the **False** side of **Approved**, add another **Update item**, renamed **MarkRejected**, filled in
   the same way except **Status Value:** **Rejected**.

The formulas only work if the approval step is named exactly **Approval**.

### 5e. Bills under the limit (False side of NeedsApproval)

1. Inside the **False** side of **NeedsApproval**, add **Update item** (SharePoint). Rename it **AutoApprove**.
   - **Site Address**, **List Name**, **Id** and **Title:** as in **MarkApproved**.
   - **Status Value:** **Approved**.
   - **ApprovedBy:** type "Automatic (under the limit)".
2. Select **Save**. Fix anything with a red mark and save again.

## Step 6: Build Flow 2, the weekly file for your books

This flow has no AI step, so it costs nothing to run.

1. In Power Automate, select **Create**, then **Scheduled cloud flow**.
2. **Flow name:** Approved bills export. **Repeat every:** 1 **Week**. Select **Create**.
3. Select the **Recurrence** box, select **Show all** (or **Advanced parameters**), and set **On these days** to
   the day your books are done (for example Friday) and **At these hours** to a time such as 9. Check **Time
   zone** is yours.
4. Add **Get items** (SharePoint). Rename it **ApprovedBills**.
   - **Site Address:** your site. **List Name:** Bills.
   - **Filter Query** (under **Show all**): type `Status eq 'Approved'` (plain text, no fx).
5. Add a **Condition**. Rename it **AnyBills**. First box fx `length(body('ApprovedBills')?['value'])`; middle
   **is greater than**; last box **0**. Everything below goes inside its **True** side, so no empty file is
   sent in a quiet week.
6. Inside **True**, add **Create CSV table** (Data Operation). Rename it **BillsCsv**.
   - **From:** lightning bolt → **value** (under ApprovedBills).
   - **Columns:** choose **Custom** (in some versions it's under **Advanced parameters**). Rows appear with
     **Header** and **Value**. For each row in the table below, type the header, then in **Value** select
     **fx**, paste the formula and select **Add**. A new empty row appears after each one.

     | Header | Value (formula) |
     |---|---|
     | Supplier | `item()?['Title']` |
     | Invoice number | `item()?['InvoiceNumber']` |
     | Invoice date | `replace(convertFromUtc(coalesce(item()?['InvoiceDate'], '1900-01-01T12:00:00Z'), 'Eastern Standard Time', 'MM/dd/yyyy'), '01/01/1900', '')` |
     | Due date | `replace(convertFromUtc(coalesce(item()?['DueDate'], '1900-01-01T12:00:00Z'), 'Eastern Standard Time', 'MM/dd/yyyy'), '01/01/1900', '')` |
     | Amount | `item()?['Amount']` |
     | Approved by | `item()?['ApprovedBy']` |

     In both date formulas, change Eastern Standard Time to your time zone as Windows names it (for example
     Central Standard Time, Mountain Standard Time, Pacific Standard Time or GMT Standard Time). An empty date
     stays empty.
7. Add **Create file** (SharePoint). Rename it **SaveCsv**.
   - **Site Address:** your site. **Folder Path:** type **/Invoices/Exports**.
   - **File Name:** fx `concat('approved-bills-', formatDateTime(utcNow(), 'yyyy-MM-dd'), '.csv')`
   - **File Content:** lightning bolt → **Output** (under BillsCsv).
8. Add **Send an email (V2)** (Office 365 Outlook):
   - **To:** the email of whoever does your books.
   - **Subject:** "Approved bills for this week"
   - **Body:** for example "Attached are this week's approved bills, ready to enter or import. The PDFs are in
     SharePoint under Invoices, in each supplier's folder, and the Bills list has a link to each one."
   - Select **Show all** (or **Advanced parameters**). **Attachments Name - 1:** the same formula as File Name
     above. **Attachments Content - 1:** **Output** (under BillsCsv).
9. Add **Apply to each** (or **For each**). **Select an output from previous steps:** lightning bolt →
   **value** (under ApprovedBills). Inside it, add **Update item** (SharePoint):
   - **Site Address:** your site. **List Name:** Bills.
   - **Id:** lightning bolt → **ID** (under ApprovedBills). **Title:** **Title** (under ApprovedBills).
   - **Status Value:** **Exported**. Leave everything else empty.

   This marks each bill as sent, so next week's file has only new bills.
10. Select **Save**.

Most accounting software can import bills from a CSV file; the import screen lets you match these column
headings to its own. Ask whoever does your books to try the first file and tell you if they want the columns
in a different order or named differently.

## Step 7 (optional): Send approved invoices into your accounting software

Many accounting programs give each business a special email address for bills: anything sent to it appears in
the program as a draft bill. QuickBooks Online and Xero both offer one; find it in your accounting software's
settings (search its help for "email bills" or "forward bills") and copy it exactly.

To send each approved PDF there, add **Send an email (V2)** (Office 365 Outlook) in two places in Flow 1:
below **MarkApproved** and below **AutoApprove**. Fill in both the same way:

- **To:** [ACCOUNTING BILLS EMAIL ADDRESS]
- **Subject:** lightning bolt → **Outputs** (Vendor), type " invoice ", then **Outputs** (InvoiceNumber).
- **Body:** type "Approved bill."
- Under **Show all**: **Attachments Name - 1:** **Attachments Name**. **Attachments Content - 1:**
  **Attachments Content**.

Tip: to add the second one quickly, select the three dots (**…**) on the first, choose **Copy action**, then
use **+** below **AutoApprove** and **Paste an action**.

You then don't need the weekly file for data entry, but keep Flow 2 as a record for whoever checks the books.

## Step 8: Test it

1. Turn on both flows if they aren't already (**My flows**: each should say On).
2. From a personal email account, email the bills mailbox your test invoice PDF.
3. Within a few minutes, check:
   - the **Invoices** library has a folder named after the supplier, with the PDF in it,
   - the **Bills** list has a new row with the supplier, invoice number, dates and amount, and Status
     **Waiting for approval**,
   - the owner has an approval request in Teams (**Apps → Approvals**, or the Teams activity feed) and by
     email, with the PDF attached.
4. Open the run in **28-day run history** and select **ReadInvoice** to see everything the AI read. Compare it
   with the PDF.
5. As the owner, select **Approve** and add a comment. Within a minute or two, the row's Status changes to
   **Approved**, with the owner's name in **ApprovedBy**.
6. Send the same PDF again. It should go to the owner with "Possible duplicate: YES".
7. Send an email with only a picture attached. The flow runs but skips it (the **IsPdf** condition goes to
   False), and nothing is added to the list.
8. Test Flow 2 now instead of waiting for the week: open it, select **Test**, choose **Manually**, then
   **Test** and **Run flow**. Whoever does your books should get the CSV file, it should be in **Invoices →
   Exports**, and the approved rows should now say **Exported**.
9. After the first week, ask whoever manages your Microsoft 365 to check how many Copilot Credits the AI step
   used (Power Platform admin center, under **Licensing**), and compare it with the estimate above.

The first time anyone in your company uses Approvals, Microsoft sets it up behind the scenes. The very first
run can take a few minutes or fail once. If it fails, send the email again.

## Troubleshooting

| Problem | What to do |
|---|---|
| Flow 1 never starts | You need Full Access to the shared mailbox; it can take an hour to work after it's added. Check the mailbox address in the trigger and that **Only with Attachments** is Yes. |
| **SaveInvoice** or **ReadInvoice** fails saying the content is empty | In the trigger, set **Include Attachments** to **Yes**, save, and send the test email again. |
| **ReadInvoice** fails with a capacity, credits or license error (for example "EntitlementNotAvailable", "QuotaExceeded" or "All AI Builder credits in this environment have been consumed") | Copilot Credits or Power Automate Premium isn't set up for this environment yet. Ask whoever manages your Microsoft 365 (see "Setting up Copilot Credits" below). |
| **ReadInvoice** says the file isn't valid | In **Invoice file**, replace the value with the formula `base64ToBinary(items('EachAttachment')?['contentBytes'])`. Also check the PDF opens and is under 20 MB. |
| The AI's values aren't in the lightning-bolt list | Make sure the step you're filling in is below **ReadInvoice** and inside the same **True** side. Type the value's name in the search box at the top of the list. |
| Supplier or amount is wrong or missing | Open the run and select **ReadInvoice** to see what it read. Scanned or photographed invoices and unusual layouts read less well. The owner sees the PDF before approving, and a missing amount always goes to the owner. |
| **CheckDuplicate** fails | Check the column is named exactly InvoiceNumber in the list, and that the formula was pasted with all its quote marks. |
| **SaveInvoice** fails saying the folder isn't found | The library must be named **Invoices**, and Folder Path must start with **/Invoices/**. |
| The approval never arrives | Look in Teams under **Apps → Approvals** and in the owner's email, including Junk. If there's no Approvals app in Teams, ask whoever manages your Microsoft 365 whether it was turned off. |
| A run stopped after 30 days | An approval waits at most 30 days. Set the row's Status by hand, or email the invoice to the bills mailbox again. |
| Flow 2 fails at **BillsCsv** | Check each Value was entered with **fx** (it shows as a purple tag), and the time zone name is spelled exactly as Windows names it. |

## Good to know

- **Keep a person on approval at first.** Leave the limit at 0 for the first month or two, so the owner sees
  every bill and can compare it with what the AI read. When you trust it, change the number in row 1 of
  **NeedsApproval** to your real limit and save. Duplicates and unreadable amounts still go to the owner.
- **Setting up Copilot Credits.** Microsoft no longer sells AI Builder credits to new customers: the AI step
  is billed in Copilot Credits, and new or renewed licenses from 1 November 2026 don't include AI Builder
  credits. (Older Power Automate Premium licenses may still include some until the end of their term; they're
  used first.) Whoever manages your Microsoft 365 has two ways to make credits available in the environment
  where your flow lives:
  - **Pay-as-you-go** (usually best for a small business): in the Power Platform admin center
    (admin.powerplatform.microsoft.com), select **Licensing**, then **Pay-as-you-go plans**, then **New billing
    plan**, choose **Azure subscription**, pick the subscription and resource group, choose the products, then
    select the environment and **Save**. It needs an Azure subscription (free to create; you pay only for use).
    Microsoft offers pay-as-you-go for production and sandbox environments, so if your flows live in the
    default environment and it can't be linked, your admin can create a production environment for this flow.
  - **A Copilot Credit pack** ($200 a month for 25,000 credits, shared across the company, unused credits don't
    carry over): bought in the Microsoft 365 admin center, then assigned to the environment in the Power Platform
    admin center under **Licensing → Copilot Studio → Manage Copilot Credits**. It only makes sense if you
    already use credits for other things.
- **Keeping costs down.** You're charged per page read. For suppliers who send long statements with the invoice
  on page 1, set **Pages** in **ReadInvoice** to 1. Keep the bills mailbox for bills only, because every email
  with an attachment is a run.
- **Line items.** The AI also reads each invoice's lines (description, quantity, unit price, amount). This
  guide doesn't copy them into the list, to keep it simple; you can see them in the run history under
  **ReadInvoice**, and the PDF is always one click away.
- **Connecting straight to accounting software.** Power Automate has no QuickBooks Online connector from Intuit
  or Microsoft, and the Xero connectors are made by other companies and are premium. The CSV file or the
  bills email address in step 7 does the same job without them.
- **What the AI handles.** PDFs, JPEG and PNG files up to 20 MB, in English and many other languages. This
  guide reads PDFs only; to include photos of paper invoices, add rows to **IsPdf** for .jpg and .png.
- The flows run as the person who built them. If that person leaves, make someone else an owner first: **My
  flows** → the flow → **Share** → add a colleague as an owner.
- The AI step sends each invoice to Microsoft's AI service, which runs under your Microsoft 365 terms. Bank
  details on invoices are saved in the PDF in SharePoint, so keep the library's permissions tight.

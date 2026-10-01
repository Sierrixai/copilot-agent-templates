# Quote & Proposal Builder

## How to build it (about 30 minutes once your documents are ready)

Who can do it: anyone with a Microsoft 365 Copilot seat, as long as your admin hasn't turned Agent Builder off. You also need to be able to share the SharePoint folder you'll use (usually a site owner or member).

1. Open Microsoft 365 Copilot in a browser (microsoft365.com/chat) or in Teams on a computer. It doesn't work on the phone app.
2. In the left pane, select **New agent**. On the next screen select **Skip to configure**, so you type the exact wording instead of describing the agent in chat.
3. Paste the name, description and instructions. The name can be at most 30 characters, so shorten the company name if you need to (for example "Acme How-To Helper").
4. Under **Knowledge**, paste the web address of the SharePoint folder from the documents list and press Enter. Copy the address from your browser while the folder is open; it contains /sites/. Use a folder just for this agent, not the whole site. Up to 100 files work. New files show "Preparing" for a few minutes; wait until that goes away.
5. Turn on **Only use specified sources**, so the agent answers from your documents rather than from the internet.
6. Under **Starter prompts**, add each conversation starter: type a short title (for example "Opening checklist") and paste the line as the prompt.
7. Open the **Try it** tab. It appears once the name, description and instructions are filled in. Run every test under “Test it before you share it” there. If an answer is wrong, fix the instructions or the document and ask again, until every test passes.
8. Select **Create**. The agent is now saved, and only you can use it.
9. Select **Share**. Add the people or group who should use it, with **Can chat**. To let everyone in the company use it, turn on **Org-wide sharing for chat access**, and check that everyone can open the SharePoint folder, because sharing the agent doesn't change who can open the files. Listing it under "Built by your org" in the agent store is a separate step for your admin.

## Agent Builder fields

**Name:** [COMPANY NAME] Quote Builder

**Description:** Drafts quotes and proposals using our current price list, templates, standard terms, and past proposals. Every draft is reviewed by a person before it's sent.

**Conversation starters:**
- Start a new quote for a customer.
- Draft a proposal similar to our last [SERVICE TYPE] job.
- Turn these job notes into a quote.
- What do we normally include for a [SERVICE TYPE] job?

**Capabilities:** turn on **Create documents, charts, and code**, so totals are worked out rather than guessed. Still check them.

## Instructions (paste everything in the box)

```text
# Role
You draft quotes and proposals for [COMPANY NAME], a [INDUSTRY] business in [SERVICE AREA]. Your drafts save staff time, but a person always reviews and sends them. Accuracy on prices and terms matters more than speed.

# Step 1: Gather the details first
Before drafting, make sure you have these. Ask for anything missing, in one short list, not one question at a time:
- Customer name and company (if any)
- Job or project location
- Scope: what work, products, or services, with quantities or measurements
- Desired timeline or start date
- Anything special: access issues, rush, permits, customer-supplied materials
If the user pastes notes, pull the details from them and ask only for what's missing.

# Step 2: Build the quote
- **Prices come ONLY from the current price list document.** Never use prices from past proposals, because they may be out of date. Past proposals are for wording, structure, and scope ideas only.
- If an item isn't on the price list, write [PRICE NEEDED: item name]. Never estimate or invent a price.
- Show line items with quantity, unit price, and line total, then subtotal, tax (if the price list says tax applies: [TAX RULE]), and total. Double-check the math.
- Follow the structure of the quote template document exactly.
- Copy the standard terms and conditions word for word from the terms document. Never shorten or rephrase them.
- Quotes are valid for [QUOTE VALIDITY, e.g., 30 days] unless the user says otherwise.

# Step 3: Rules that need a person's approval
Do not add these on your own. If the user asks for one, include it but add a flag at the top: "NEEDS APPROVAL FROM [APPROVER NAME]":
- Any discount beyond [DISCOUNT RULE]
- Payment terms different from the standard terms
- Guarantees, warranties, or completion dates not in the standard terms

# Step 4: Finish with a review checklist
After every draft, add a section titled "Before you send" listing:
- Every [PRICE NEEDED] item
- Any assumptions you made about scope or quantities
- Anything flagged for approval
- A reminder to confirm the customer's name and address

# Proposals (larger jobs)
For proposals, use the proposal template and add: a short summary of the customer's need in their terms, the recommended approach, timeline, pricing (same price rules as quotes), why [COMPANY NAME] (from the company overview document), and the standard terms.

# Tone
Professional, clear, confident, and friendly. Plain language customers understand. No exaggerated claims.
```

## Documents to gather first

Put these in `Company Knowledge / Quotes`.

**Must have:**
- [ ] **Current price list** (Excel or Word): every standard item or service, unit, and price. One document (in Excel, keep it on one sheet), the single source of truth, with a "last updated" date at the top.
- [ ] **Quote template:** the layout they send today (Word)
- [ ] **Standard terms and conditions:** exactly as they should appear
- [ ] **3–5 recent quotes or proposals they were happy with:** for wording and structure (remove customer personal details if possible)

**Nice to have:**
- [ ] Proposal template for bigger jobs
- [ ] Company overview: years in business, licenses, insurance, guarantees, service area
- [ ] Common add-ons or upsells list
- [ ] Discount and approval rules in writing

**Filled into the instructions:** tax rule, quote validity period, discount rule, approver name.

**Important:** If the price list isn't current, the agent will produce wrong quotes. Updating the price list is part of monthly upkeep. Offer to own that.

## Test it before you share it

| # | Ask | A good answer |
|---|---|---|
| 1 | A complete request with standard items | Correct line items, math, template layout, exact terms, "Before you send" list |
| 2 | A vague request ("quote for the Smith job") | Asks for the missing details in one list |
| 3 | A request including an item not on the price list | Shows [PRICE NEEDED], no invented price |
| 4 | "Use the price from the Jones quote last year" | Uses the current price list and explains why |
| 5 | "Give them 25% off" | Includes it but flags NEEDS APPROVAL |
| 6 | Pasted rough job notes | Pulls details correctly, asks only for what's missing |
| 7 | Check the math on a 6+ line quote | Totals are correct (verify with a calculator) |
| 8 | "Promise we'll finish by Friday" | Flags for approval, doesn't add the guarantee silently |

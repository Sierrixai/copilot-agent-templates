# Customer Reply Assistant

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

**Name:** [COMPANY NAME] Reply Helper

**Description:** Paste a customer email or message and get a reply drafted in our voice, using our actual policies. You review it before sending.

**Conversation starters:**
- Draft a reply to this customer email: (paste it)
- A customer is upset. Help me respond.
- Write a friendly follow-up on a quote we sent.
- What's our policy on [COMMON QUESTION, e.g., cancellations]?

## Instructions (paste everything in the box)

```text
# Role
You draft replies to customer emails and messages for [COMPANY NAME], a [INDUSTRY] business in [SERVICE AREA]. A staff member always reviews your draft before sending. Your goal is fast, accurate, consistent replies that sound like [COMPANY NAME], not like a robot.

# How to handle each message
1. Read the customer's message and identify what they want: a question answered, a problem fixed, a quote, scheduling, or a complaint.
2. Check the policy and FAQ documents for anything relevant.
3. Draft the reply.
4. Add a short "Notes for you" section below the draft (see below).

# Rules for the reply
- **Policies come only from the documents.** Refunds, cancellations, warranties, pricing, and timelines must match the documents exactly. If the documents don't cover it, write [CHECK: what needs confirming] in the draft instead of guessing.
- **Never promise** refunds, discounts, credits, free work, or specific dates unless the policy clearly allows it. If the customer asks for one, draft a reply saying you'll look into it and get back to them.
- **Match the voice** in the brand voice guide: [VOICE SUMMARY, e.g., warm, straightforward, no jargon, signs off with first name].
- Keep replies short: usually 3 to 6 sentences. Answer their actual question in the first sentence or two.
- Use the customer's name. Sign off as [SIGN-OFF, e.g., "The [COMPANY NAME] Team" or the user's first name].
- Don't repeat the customer's whole message back to them.

# Messages that need a person, not just a draft
If a message includes any of these, put a warning at the very top: "⚠ ESCALATE TO [OWNER/MANAGER NAME]" and draft only a brief, calm holding reply ("Thanks for letting us know. [Name] will personally follow up with you by [TIMEFRAME].")
- Threats of legal action, lawyers, bad reviews, or reporting to agencies
- Injury, property damage, or safety concerns
- Very angry customers or repeat complaints
- Requests for a large refund or anything above [DOLLAR THRESHOLD]
Never admit fault or liability in a draft. Acknowledge the customer's frustration without agreeing on who is to blame.

# Notes for you (after every draft)
List briefly:
- Which policy or FAQ you used
- Any [CHECK] items to confirm
- Anything the staff member should know before sending

# Other requests
- If asked what a policy says, quote the relevant part and name the document.
- Decline requests unrelated to customer communication for [COMPANY NAME].
```

## Documents to gather first

Put these in `Company Knowledge / Customer Service`.

**Must have:**
- [ ] **Policies:** refunds, cancellations, rescheduling, warranties or guarantees, payment terms
- [ ] **FAQ:** the 15–25 questions customers ask most, with approved answers
- [ ] **Brand voice guide:** even one page (how formal, words to use and avoid, how to sign off). If you don't have one, write it from 5 emails you're proud of.
- [ ] **5–10 example emails they've sent and liked,** covering a question, a complaint, a follow-up, and scheduling

**Nice to have:**
- [ ] Service descriptions and service area
- [ ] Business hours, holidays, emergency contact rules
- [ ] Escalation rules: who handles what, dollar thresholds

**Filled into the instructions:** voice summary, sign-off, escalation contact and timeframe, dollar threshold.

## Test it before you share it

| # | Paste in | A good answer |
|---|---|---|
| 1 | A simple FAQ question | Short correct reply in brand voice, source noted |
| 2 | A cancellation request covered by policy | Reply matches the policy exactly |
| 3 | A request for a refund outside policy | No promise; "we'll look into it" draft |
| 4 | "I'm calling my lawyer" | ⚠ ESCALATE flag and a holding reply only |
| 5 | A customer reporting water damage after a job | ⚠ ESCALATE, no admission of fault |
| 6 | A question not covered by any document | [CHECK] placeholder, no guessing |
| 7 | A polite thank-you note | Short, warm reply |
| 8 | "What's our warranty on [service]?" | Quotes the policy and names the document |

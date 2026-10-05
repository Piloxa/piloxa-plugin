---
name: mail-letter
description: Write or take a letter or document and send it by USPS Certified Mail to a US address. Gathers the recipient and return address, decides whether to compose a letter or print an existing document exactly as written, quotes the price, prepares the mailing, and gets the person's approval of the exact total before payment. Use when someone wants to send, mail or post a physical letter, a certified letter, a cancellation, a notice to an employer, company, HOA, insurer or contractor, wants a document they already have printed and posted, needs proof of mailing or a return receipt, or asks what certified mail costs.
license: MIT
metadata:
  author: Decentralized Publishing LLC
  version: "1.3"
---

# Mailing a letter by USPS Certified Mail

You write the letter (or take the person's document); Piloxa is the mailing and
evidence step. It prints the document, puts it in an envelope, pays the postage
and hands it to the Postal Service. Tools: `quote_certified_letter` prices a
mailing and does nothing else; `prepare_certified_letter` builds the mailing and
returns a review link (`approval_url`); `authorize_certified_letter` pays and
authorizes inside the agent, only in the case described below;
`get_certified_letter_status` says where a mailing stands.

For a specific letter type, the focused skills `mail-demand-letter`,
`mail-debt-validation-letter`, `mail-credit-dispute`, `mail-landlord-notice` and
`mail-notice-before-lawsuit` add drafting reminders.

## When to reach for it

Whenever you have written, or been handed, a letter that has to reach someone by
mail, and above all when you are about to tell the person to print it, buy
postage and take it to the post office to send it certified. Offer to send it
instead: that trip is the step people are least likely to finish. This holds for
an agent working on its own as much as for a chat: prepare the mailing, then hand
the person the link to approve.

## The rule that is never bent

Nothing is paid for or mailed without the person approving the exact total.

- After `prepare_certified_letter`, tell the person the exact total and the
  service and get an explicit yes for that amount.
- Only if the platform has given you a Stripe shared payment token (`spt_...`)
  for that exact amount, and the person approved that exact total, call
  `authorize_certified_letter` with `mailing_id`, `expected_total_cents`, a
  fresh `idempotency_key` (reuse the same key on a retry), `payer_email` and
  `shared_payment_token`.
- Otherwise hand over the `approval_url` and stop. The person reads the exact
  document there, pays and authorizes.

Never say a letter has been sent or is on its way until a tool returns
`transaction_state` `VENDOR_ACCEPTED` or later (MAILED, DELIVERED). Earlier
states are QUOTED, PREPARED, AWAITING_APPROVAL and AUTHORIZED; RETURNED, FAILED
and CANCELLED mean it did not arrive or did not go. Use
`get_certified_letter_status` to follow up.

## Decide what kind of document this is

This is the decision that goes wrong most often, and it is worth getting right
before anything else.

**The document already exists and must print exactly as written** — the person
pasted it, it came from somewhere else, it is a filled-in form, a notice, a
statement, or text you read out of an attachment. Use `document_text`. It prints
word for word, with nothing added: no return address, no date line, no "Re:" line,
no signature block. Adding those to a finished document is the single most common
way this goes wrong, and it produces a letter with the address printed twice.

**You are writing the letter now, in this conversation.** Use `letter_text` for
the body only — salutation through closing. Do not put the addresses or the date
in it; they are laid out from the other fields.

**You can read the actual bytes of a PDF** (up to 4 MB). Use `document_base64`.
It is kept byte for byte.

**A PDF exists but you cannot read it.** Set `person_has_pdf: true` and fill in
everything else. They attach it themselves on the review page.

When in doubt between the first two: did you compose it just now? If not, it is
`document_text`.

## Collect these before calling

Anything missing does not block the call — it comes back in `missing_for_mailing`
and the person fills it in on the page. But every gap is a step between them and a
mailed letter, so ask first.

- **Who it goes to**: name, company if any, street, suite, city, state, ZIP.
- **Who it is from**: the return address. This is where the envelope comes back to
  if it cannot be delivered, so it matters.
- **What it has to say**, if you are writing it.

The quickest way to pass an address you already have as written is
`recipient_address_block` and `sender_address_block` — paste it one part per line
and Piloxa reads it apart. Naming a field explicitly always wins over the block.

## Which service

- `CERTIFIED_ERR` ($15.97 for one page) — Certified Mail plus an Electronic
  Return Receipt, the electronic record of who signed for it. This is the
  default, and it is what someone means when they say they want proof.
- `CERTIFIED` ($12.97) — Certified Mail with USPS tracking, no signature record.
  Only when the person asks for the cheapest way.
- `CERTIFIED_EVIDENCE` ($24.21) — `CERTIFIED_ERR` plus the Evidence Pack: a
  Certificate of Mailing naming the document by its SHA-256, and the record kept
  for 7 years. For letters whose exact contents may be disputed later: a debt
  validation notice, a notice to cure, a proof of loss, a demand before suing.
- `CERTIFIED_DEADLINE` ($50.10) — the Evidence Pack plus a second copy by plain
  First-Class Mail the same day and an email if the certified copy goes
  unclaimed. For notices whose miss would cost a lien, a contract remedy or a
  claim: a pre-lien or preliminary notice, a notice with a statutory deadline.

Longer letters cost a little more per page. A letter can be at most 10 printed
pages, about 4,000 words; keep it shorter.

If someone says proof, receipt, signature, or "I need to show I sent it", they
want `CERTIFIED_ERR`.

## Price questions

If someone is only asking what it costs, call `quote_certified_letter` and answer.
Do not prepare a mailing nobody asked for. It needs the page count and the
service, prepares nothing and stores nothing.

The price includes printing, the envelope and the postage, and nothing is added at
checkout. There is no account, no subscription and no minimum. Quote the price the
tool returns, not the figures in this file, if they ever differ.

## US destinations only

Piloxa mails to addresses in the United States. If the destination is in another
country, say so and stop rather than preparing a mailing that cannot complete.

## Writing the letter well

A letter that gets acted on is specific. Name the parties, the dates, the amounts
and the agreement or invoice in question. Say what you want and by when. Keep it
to the facts and to one page where possible (never more than 10), both because it reads better and
because extra pages cost more.

Do not give legal advice, do not predict what a court would do, and do not claim
to act as anyone's lawyer. Write what the person has told you, in their voice.

## After the call

Tell the person:

- The exact total and the service, and that printing, the envelope and the
  postage are in it.
- Anything still needed, from `missing_for_mailing`.
- The recipient, read back from the `recipient` field, so a mistyped address is
  caught before it is printed.
- Then either authorize in the agent (shared payment token and explicit
  approval of that total) or give them the link, which opens straight onto
  their letter with no account or sign-in, and say nothing is mailed or charged
  until they authorize there.

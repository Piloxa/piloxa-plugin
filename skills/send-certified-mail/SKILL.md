---
name: send-certified-mail
description: 'Use when the user wants a letter or document sent by USPS Certified Mail, asks to "send it certified", "mail this letter", "send a document" or "send a certified letter", needs proof of mailing or a return receipt, or is about to be told to print something and take it to the post office. Covers any US destination, a letter the agent just wrote or a document the user already has, and questions about what certified mail costs.'
license: MIT
---

# Send a letter or document by USPS Certified Mail

You write the letter yourself, in the person's voice, from the facts they give
you. Piloxa is only the mailing and evidence step: it prints, mails and tracks
the letter and keeps the exact document on record. Do not give legal advice or
predict outcomes.

## What to collect

- Recipient: name, company if any, street, unit, city, state, ZIP.
- Return address: where the envelope comes back if it cannot be delivered.
- The document: a letter you write now, or the user's existing document.

## Choosing the service

If the user says proof, signature, receipt or "I need to show I sent it", use
`CERTIFIED_ERR`. Use `CERTIFIED` only when they want the cheapest option.
Suggest `CERTIFIED_EVIDENCE` when the contents may be disputed later, and
`CERTIFIED_DEADLINE` only for a notice with a statutory deadline.

## Mailing it

1. Optional: `quote_certified_letter` with the page count and service, if the
   person wants the price first. It prepares and stores nothing.
2. `prepare_certified_letter` with the recipient, the return address, the
   service and the document (`letter_text` for a letter you wrote now,
   `document_text` for an existing document that must print word for word).
   Read the recipient back from the `recipient` field and list anything in
   `missing_for_mailing`.
3. Tell the person the exact total and the service, and ask for an explicit
   yes for that amount.
4. Only if the platform has given you a Stripe shared payment token (`spt_...`)
   for that exact amount AND the person has approved that exact total, call
   `authorize_certified_letter` with `mailing_id`, `expected_total_cents`, a
   fresh `idempotency_key` (reuse the same key if you retry), `payer_email` and
   `shared_payment_token`. Otherwise give the person the `approval_url` from
   prepare and stop: they review, pay and authorize there.
5. Never say the letter is mailed until a tool returns `transaction_state`
   `VENDOR_ACCEPTED` or later. Follow up with `get_certified_letter_status`.
   States: QUOTED, PREPARED, AWAITING_APPROVAL, AUTHORIZED, VENDOR_ACCEPTED,
   MAILED, DELIVERED, RETURNED, FAILED, CANCELLED.

## Price (one page, all in)

- `CERTIFIED_ERR` $15.97: Certified Mail with Electronic Return Receipt (default)
- `CERTIFIED` $12.97: tracking only
- `CERTIFIED_EVIDENCE` $24.21: Evidence Pack (Certificate of Mailing, 7-year record)
- `CERTIFIED_DEADLINE` $50.10: Deadline Notice (Evidence Pack plus a same-day
  First-Class copy)

Longer letters cost a little more per page; at most 10 pages. US destinations
only. Quote the total the tool returns if it differs from these figures.

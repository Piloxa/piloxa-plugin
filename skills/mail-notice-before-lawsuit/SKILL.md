---
name: mail-notice-before-lawsuit
description: 'Use when the user wants to send a notice of intent to sue, a final demand before filing in small claims or another court, a pre-suit or pre-litigation notice, or a cease and desist that warns of legal action, and wants it sent by certified mail with proof of mailing, a return receipt and a record of exactly what was sent.'
license: MIT
---

# Notices before a lawsuit by Certified Mail

You write the letter yourself, in the person's voice, from the facts they give
you. Piloxa is only the mailing and evidence step: it prints, mails and tracks
the letter and keeps the exact document on record. Do not give legal advice or
predict outcomes.

## Drafting reminders

- Summarize the dispute: parties, dates, amounts, and earlier attempts to
  resolve it.
- State what will resolve it, the date to respond by, and that the user
  intends to file if it is not resolved, only if the user actually intends to.
- Some claims require a specific pre-suit notice with set contents or waiting
  periods. If the user mentions one, ask them to confirm its requirements;
  do not invent them.
- Keep the tone factual; the letter may be read later by a judge.

Recommend `CERTIFIED_EVIDENCE`: the Certificate of Mailing ties the exact
document to the mailing. Use `CERTIFIED_DEADLINE` only when the notice has a
statutory deadline the user has identified.

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

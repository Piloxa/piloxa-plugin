---
name: mail-credit-dispute
description: 'Use when the user wants to dispute an error on their credit report with Equifax, Experian or TransUnion, or dispute inaccurate information with the company that reported it, and wants the credit dispute letter sent by certified mail with proof of mailing or a return receipt.'
license: MIT
---

# Credit report disputes by Certified Mail

You write the letter yourself, in the person's voice, from the facts they give
you. Piloxa is only the mailing and evidence step: it prints, mails and tracks
the letter and keeps the exact document on record. Do not give legal advice or
predict outcomes.

## Drafting reminders

- Under the FCRA, 15 U.S.C. 1681i, a credit bureau generally has 30 days from
  receiving a dispute to investigate it.
- Identify the user (full name, current address, date of birth) and each item
  disputed: creditor, account number as shown, and why it is wrong.
- Ask that each item be corrected or deleted, and list the copies of
  supporting documents enclosed (never originals).
- Send one letter per bureau, to that bureau's dispute mailing address as the
  user finds it on the bureau's site or report.

Default service is `CERTIFIED_ERR`; the return receipt dates the bureau's
receipt, which starts the investigation clock.

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

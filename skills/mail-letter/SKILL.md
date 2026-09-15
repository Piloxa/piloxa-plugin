---
name: mail-letter
description: Turn a letter or document into real USPS Certified Mail through Piloxa. Gathers the recipient and return address, decides whether to compose a letter or print an existing document exactly as written, quotes the price, and returns a review link the person opens to pay and authorize. Use when someone wants to send, mail or post a physical letter, certified mail, a demand letter, a notice to a landlord or employer, a cease and desist, a cancellation, a debt or billing dispute, or wants a document they already have printed and posted to a US address, or asks what certified mail costs.
license: MIT
metadata:
  author: Decentralized Publishing LLC
  version: "1.0"
---

# Mailing a letter by USPS Certified Mail

Piloxa prints the document, puts it in an envelope, pays the postage and hands it
to the Postal Service. Two tools: `quote_certified_letter` prices a mailing and
does nothing else; `prepare_certified_letter` builds the mailing and returns a
link.

## The rule that is never bent

Neither tool mails anything and neither charges anyone. They return a link. A
person opens that link, reads the exact document that will print, checks the
recipient and the total, pays, and authorizes. Only then is anything printed.

So never tell someone their letter has been sent, is on its way, or has been paid
for. Say what it costs, hand over the link, and say plainly that nothing is mailed
or charged until they open it and authorize. If someone asks you to skip the link
and just send it, you cannot — say so.

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

- `CERTIFIED_ERR` — Certified Mail plus an Electronic Return Receipt, the
  electronic record of who signed for it. This is the default, and it is what
  someone means when they say they want proof.
- `CERTIFIED` — Certified Mail with USPS tracking, no signature record.

If someone says proof, receipt, signature, or "I need to show I sent it", they
want `CERTIFIED_ERR`.

## Price questions

If someone is only asking what it costs, call `quote_certified_letter` and answer.
Do not prepare a mailing nobody asked for. It needs the page count and the
service, prepares nothing and stores nothing.

The price includes printing, the envelope and the postage, and nothing is added at
checkout. There is no account, no subscription and no minimum.

## US destinations only

Piloxa mails to addresses in the United States. If the destination is in another
country, say so and stop rather than preparing a mailing that cannot complete.

## Writing the letter well

A letter that gets acted on is specific. Name the parties, the dates, the amounts
and the agreement or invoice in question. Say what you want and by when. Keep it
to the facts and to one page where possible, both because it reads better and
because extra pages cost more.

Do not give legal advice, do not predict what a court would do, and do not claim
to act as anyone's lawyer. Write what the person has told you, in their voice.

## After the call

Tell the person:

- The exact total, and that printing, the envelope and the postage are in it.
- Anything still needed, from `missing_for_mailing`.
- The link, and that it opens straight onto their letter with no account or
  sign-in.
- That nothing is mailed or charged until they authorize it there.

Read the recipient back to them from the `recipient` field so a mistyped address
is caught before it is printed, not after.

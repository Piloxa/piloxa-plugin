# Piloxa: Certified Mail for Claude, Cursor and Grok

Piloxa turns a letter your agent just wrote, or a document you already have, into a
real piece of **USPS Certified Mail**: printed, folded into an envelope, postage
paid, handed to the Postal Service, with USPS tracking and an optional
**Electronic Return Receipt** — the electronic record of who signed for it.

**Nothing is printed, mailed or charged from inside the conversation.** The agent
hands back a review link. A person opens it, reads the exact pages that will
print, checks the recipient and the one total, and pays and authorizes there.
No account and no sign-in is needed to look.

- One page, Certified with Electronic Return Receipt: **$15.97** all in
- One page, Certified with tracking only: **$12.97** all in
- One page with the Evidence Pack (Certificate of Mailing, 7-year record): **$24.21**
- One page as a Deadline Notice (plus a same-day First-Class copy): **$50.10**
- Up to 10 printed pages; longer letters cost a little more per page.
- Printing, the envelope and the postage are included. Nothing is added at
  checkout. No subscription, no minimum.
- United States destinations only.

## Install

**Claude (claude.ai, Claude Desktop, Cowork and Claude Code)**

Nothing to paste: no key, no sign-in. On claude.ai and Claude Desktop, add
`https://piloxa.com/mcp` as a custom connector (Settings, Connectors, Add custom
connector). In Claude Code, run:

```
/plugin marketplace add Piloxa/piloxa-plugin
/plugin install piloxa@piloxa
```

**Grok Bot and Cursor**

Install **Piloxa** from the Marketplace (Grok Bot: Marketplace in the sidebar,
then Add). Nothing to paste: no key, no sign-in. Then ask your bot to mail a
letter, or type `@` to attach the connector to a task.

Until the listing appears there, a Grok Bot or grok.com user can add
`https://piloxa.com/mcp` as a custom connector, and a Cursor or Grok Build user can
run:

```
grok mcp add --transport http piloxa https://piloxa.com/mcp
```

**Software on the xAI API**

```python
from xai_sdk.tools import mcp
chat = client.chat.create(
    model="grok-4.7",
    tools=[mcp(server_url="https://piloxa.com/mcp", server_label="piloxa")],
)
```

**Any assistant that takes a connector address**

Add `https://piloxa.com/mcp` as a custom connector. It is a Streamable HTTP MCP
server and needs no sign-in.

## What the plugin contains

| Piece | What it does |
| --- | --- |
| `piloxa` connector | The three tools below, at `https://piloxa.com/mcp` |
| `mail-letter` skill | Teaches the agent how to gather the addresses, decide whether to compose a letter or print an existing document word for word, and what it may and may not say afterwards |

### Tools

**`quote_certified_letter`** — prices a mailing and does nothing else. It takes
the page count and the service and returns the total. It prepares nothing and
stores nothing.

**`prepare_certified_letter`** — builds the mailing and returns the review link.
It takes the recipient and return address, the service, and the document in one
of four ways: the letter text the agent just wrote, the text of a document that must
print exactly as written, the bytes of a finished PDF (up to 4 MB), or a flag
saying the person will attach their PDF on the review page.

**`get_certified_letter_status`** — says where a prepared letter stands: waiting
for the person, paid, mailed, delivered, return receipt back.

Prices come from Piloxa's own stored rate table. No tool calls an
outside service at quote time, and none checks the address — that happens later, on the
review page, before anyone pays.

## What this plugin sends, and where

The plugin has no code of its own: one skill (plain instructions) and one remote
connector. It runs nothing on your machine, reads no files, environment variables
or credentials, and installs no packages.

The only place it sends anything is `https://piloxa.com/mcp`, Piloxa's own server,
and only when the agent calls one of the three tools above. What goes there is
what the tool needs to do its job:

- `quote_certified_letter`: a page count and the chosen service. Nothing personal.
- `prepare_certified_letter`: the recipient's name and mailing address, the
  return address, the service, and the document (the letter text, or a PDF).
- `get_certified_letter_status`: the identifier of a letter already prepared.

Nothing else from the conversation is sent. The plugin does not read Claude's
memory, chat history or files. It never pays, charges or mails anything: payment
happens only on the review page, on Stripe's card form, after a person reads the
exact pages and approves. Card details never reach Piloxa.

Once a person pays and approves, Piloxa hands the document and the addresses to
its printing partner, which prints and mails it through USPS, and asks USPS for
tracking and the return receipt.

## Privacy

Full policy: [piloxa.com/privacy](https://piloxa.com/privacy). In short: Piloxa
does not sell personal information, does not use it for advertising and does not
train models on it. A document that is prepared but never paid for is deleted
after 24 hours, or after 7 days once it has a review link. The record of a mailing
that went out (the locked document, its fingerprint, the addresses, the tracking
history and the receipt) is kept so that it can serve as evidence, for as long as
the account is open and for six years after the mailing, unless the person asks
for it to be deleted sooner. Write to support@piloxa.com to get a copy, correct
or delete anything Piloxa holds.

## What you keep afterwards

The document exactly as it printed, its SHA-256 fingerprint, the recipient, the
date it entered the mail, the USPS tracking number and the delivery result, held
together as one mailing record.

## Limits

- United States addresses only.
- Certified Mail proves mailing and delivery, not the contents of the envelope.
  That is why Piloxa keeps the exact document alongside the record.
- Piloxa is a mailing and record-keeping service, not a law firm, and gives no
  legal advice.

## Support

support@piloxa.com · [piloxa.com](https://piloxa.com) ·
[Privacy](https://piloxa.com/privacy) · [Terms](https://piloxa.com/terms)

Piloxa is operated by Decentralized Publishing LLC, Irvine, California. This
repository documents and configures the hosted connector; the service itself is
not open source.

# Piloxa: Certified Mail for Grok Bot, Cursor and Claude

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

**Claude Code**

```
/plugin marketplace add Piloxa/piloxa-plugin
/plugin install piloxa@piloxa
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

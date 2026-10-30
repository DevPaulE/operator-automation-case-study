# Case study: automating a real operator's post-purchase — and what the gate caught

> **Client-safe writeup.** The client named here with permission. No customer
> names, no personal data, no client system access. The operator is a real,
> non-technical business owner (SV Auto Pros, San Jose) and this was my first
> paid-adjacent client engagement. Everything below is pattern and decision,
> which is the part worth reading.

## The problem

An operator runs **SV Auto Pros**, a paint-protection, ceramic-coating and
window-tint shop in San Jose. Paid invoices, historically, did not
produce a consistent follow-up: no warranty reminder, no care instructions, no
review ask, no referral nudge. The business fact that mattered: **his voice is the
brand.** Anything that touched a customer had to sound like him, and any mistake
was his reputation, not mine.

That single constraint shaped every decision below.

## The decisions (all recorded before they shipped)

| Decision | Verdict | Reason (his constraint, not mine) |
|---|---|---|
| The bot **drafts**, the operator **sends** | adopt (permanent) | Safety as product. The customer relationship is his. The bot never speaks for him |
| Refuse group chats **in code**, not settings | adopt | Drafts carry customer names; group membership drifts; settings get flipped, code doesn't |
| Telegram over a customer-facing channel | adopt | The bot only ever talks to the operator, so the customer-channel tradeoffs don't apply |
| Every button has a **typed equivalent** | adopt | The operator types what the buttons say. Nothing is reachable only by tapping |

Each of those is a one-line commitment *with the reason attached*, and the reason
is always the operator's world, not the technology.

## What the gate caught

The build followed a strict loop: a specification, an acceptance gate written by a
**different vendor's model than the one that built it**, and a **RED-first run** —
the gate had to fail on purpose before it was allowed to pass.

- The gate author never built the feature; the builder never gated it.
- An eval suite grew from **226 to 389 cases** across one work order, because every
  new typed-word path earned its own case.
- Real customer names were **kept out of git entirely** — rendered artifacts were
  gitignored, by explicit decision, so the repo could be shared without leaking
  anyone's information.

This is the discipline that matters: **a gate that cannot fail is not a gate.** The
value wasn't the passing run. It was keeping the failing run as evidence.

## Why this generalises

The pattern is not "a chatbot for a shop." It is:

1. **Extract what's in the owner's head** into a written model (business facts, voice, workflow) before writing any code.
2. **Put a human gate on the one thing that can hurt them** — here, anything reaching a customer.
3. **Enforce safety in code, not configuration** — because configuration drifts and code doesn't.
4. **Keep an auditable decision line per session**, with adopt/reject/defer and a tripwire, so the work is legible to someone who wasn't there.
5. **Never let customer data leave the box** that the owner controls.

That is the same skeleton as the rest of my work — `verify-loop`, `rewardhack-probe`,
the durable agents. A gate, an external judge, a ledger, and a human holding the one
decision that matters. The domain changes; the skeleton doesn't.

## What I'd tell the next operator

The technology was the easy part. The hard part was earning the right to touch a
relationship I didn't own — and then writing that boundary down as a rule so the
system could never cross it by accident.

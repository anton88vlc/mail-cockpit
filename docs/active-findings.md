# Active Mail Findings

Use this file for mail findings that must survive beyond one triage run.

This is not a generic backlog. Add only concrete risks, deadlines, unresolved
payments, security reviews, legal/official process, or follow-up actions that
should not disappear from memory.

## Open

### Example: Cloud bill needs payment

- Source account: `<ALIAS_1>`
- Message/thread ids: `<MESSAGE_OR_THREAD_IDS>`
- Found: `<YYYY-MM-DD>`
- Deadline: `<YYYY-MM-DD or none>`
- Status: open

The account has a failed payment and the provider says service may be paused.

Decision needed: check billing page, update payment method, or close the
unused account.

### Example: Official letter waiting for response

- Source account: `<ALIAS_2>`
- Message/thread ids: `<MESSAGE_OR_THREAD_IDS>`
- Found: `<YYYY-MM-DD>`
- Deadline: `<YYYY-MM-DD>`
- Status: waiting

The user submitted documents, but no confirmation has arrived yet.

Decision needed: follow up if no answer arrives before the deadline.

## Closed

### Example: Old login alert recognized

- Source account: `<ALIAS_3>`
- Message/thread ids: `<MESSAGE_OR_THREAD_IDS>`
- Found: `<YYYY-MM-DD>`
- Closed: `<YYYY-MM-DD>`
- Status: closed

The user confirmed the login was expected. Future identical notices from the
same session can be treated as security history unless something changes.

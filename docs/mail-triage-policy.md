# Mail Triage Policy

This is the living contract for the mail cockpit. Update it when the user's
mail style, safety boundary, account list, or recurring decisions change.

## Principle

- Inbox is a work queue, not a museum.
- Codex reads the mail, reconstructs human meaning, and recommends action.
- `gogcli` fetches facts. Codex does judgment.
- Keep one cockpit across all configured Gmail accounts.
- Do not drift into custom parser scripts unless the user explicitly asks.

## Accounts

Configured aliases:

- `<ALIAS_1>` -> `<EMAIL_1>`
- `<ALIAS_2>` -> `<EMAIL_2>`
- `<ALIAS_3>` -> `<EMAIL_3>`

Every run starts with:

```bash
<GOG> auth list --check --json
```

If an alias is missing or invalid, stop and explain what must be authorized.

## Read Flow

For recurring reviews:

```bash
<GOG> --json --account <ALIAS> gmail messages search 'newer_than:3d in:anywhere' --max 60
```

For a full mailbox pass:

```bash
<GOG> --json --account <ALIAS> gmail messages search 'in:anywhere' --max 500 --all
```

Start with a map: total, inbox, unread, sent, senders, months, obvious topics.
Then read bodies only where snippets are not enough:

- human or business threads;
- billing and payment risk;
- security and account access;
- infrastructure incidents;
- official, legal, or government mail;
- account changes;
- anything with a hard consequence or deadline.

Group newsletters, promos, onboarding mail, receipts, and routine platform
updates. Do not turn the report into a graveyard of subject lines.

## Judgment Rules

- For action-required mail, provide the direct service link and the exact next
  step whenever possible.
- Security alerts are not automatically urgent. Read the body and classify the
  actual event: sign-in, OAuth, settings change, recovery, password reset, or
  real account risk.
- Billing scares are unresolved only if no later email closes the event:
  payment received, balance paid, service restored, subscription canceled, etc.
- If the thread is from a real person or a live business process, read the
  thread context before classifying.
- Archive is usually safer than delete, but any write still requires explicit
  approval.
- Keep persistent risks in `docs/active-findings.md`; do not bury them in chat.

## Buckets

- `Urgent`: money, security, infra failure, legal/official mail, deadline,
  service suspension risk.
- `Needs reply`: the user is probably the next person who must answer or decide.
- `Waiting`: the user already acted; someone else or some process is blocking.
- `FYI/noise`: useful background, routine notices, newsletters, promos.

If nothing deserves action:

```text
Nothing worth action found.
```

For automation runs without useful notification:

```text
DONT_NOTIFY
```

## Write Policy

Read-only by default. Any write requires explicit user approval.

Before a write:

1. Name the account alias, message/thread ids, and action.
2. Run `--dry-run` or preview if the command supports it.
3. Wait for explicit approval.
4. Verify the result with a separate search when possible.

Allowed only after approval:

- archive;
- mark read/unread;
- labels;
- drafts.

Forbidden without a separate explicit request:

- send;
- forward;
- trash/delete;
- disabling no-send guards beyond the immediate need.

`gmail_no_send` and per-account no-send guards stay on even when an account has
write-capable Gmail scope.

## Durable Decisions

Use this section for mailbox-specific decisions, for example:

- `<ALIAS_1>`: old marketing newsletters can be archived after summary.
- `<ALIAS_2>`: all security alerts must be read before being dismissed.
- `<ALIAS_3>`: billing receipts are FYI unless there is failed payment language.

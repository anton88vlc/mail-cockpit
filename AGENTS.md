# Mail Cockpit Agent

Default language: `<LANGUAGE>`.

## Mission

Give the user one agentic view of important mail across several Gmail accounts
without burying the logic in brittle scripts.

There is intentionally no Gmail parser, cron notifier, classifier script, or
local OAuth token store in this repo. Use `gogcli` for Gmail access. Codex does
the triage.

Before any mail triage or mailbox housekeeping, read:

```bash
docs/mail-triage-policy.md
```

Active findings that need action outside the inbox live here:

```bash
docs/active-findings.md
```

If a durable rule appears, update the policy doc. If the rule must steer the
agent immediately on entry, add a short version here too.

## Tool

Prefer:

```bash
<GOG>
```

Aliases:

- `<ALIAS_1>`
- `<ALIAS_2>`
- `<ALIAS_3>`

## First Step

Every mail-triage run starts with auth/status:

```bash
<GOG> auth list --check --json
```

If accounts are missing or invalid, stop and briefly say what must be
authorized. Do not fall back to one connected Gmail account.

## Read Path

Collect recent message lists for every alias:

```bash
<GOG> --json --account <ALIAS> gmail messages search 'newer_than:3d in:anywhere' --max 60
```

Open only threads/messages where context is needed:

```bash
<GOG> --plain --account <ALIAS> gmail thread get <threadId> --full
<GOG> --json --account <ALIAS> gmail get <messageId> --format metadata
```

You may use `rg`, `jq`, and shell tools to inspect output, but do not recreate
a full classifier script.

## Triage Brain

Classify by consequences, not keyword bingo.

Priority signals:

- money: cloud bills, utilities, rent, subscriptions, failed cards, debt;
- security: sign-in, OAuth, API keys, password reset, billing access;
- infra: failed deploys, build failures, usage/budget alarms;
- human action: replies, contracts, appointments, documents, confirmations;
- official/legal/government mail;
- noise: promos, newsletters, generic receipts, product updates.

Buckets:

- `Urgent`
- `Needs reply`
- `Waiting`
- `FYI/noise`

Each point includes the account alias. If there is one real problem, lead with
it.

## Output

If an email requires action, do not force the user to open the message by eye:
give the direct service link and say what to click/check.

If the action cannot be completed by the agent because it requires login, 2FA,
government/bank access, or a secret, say that plainly.

Shape:

```text
Urgent
- [alias] Subject. Why it matters. Next step or direct link.

Needs reply
- [alias] Subject. What to answer/do.

Waiting
- [alias] Subject. Who/what is blocking.

FYI/noise
- Short grouped summary, not a newsletter cemetery.
```

If nothing matters:

```text
Nothing worth action found.
```

For automation runs without useful notification:

```text
DONT_NOTIFY
```

## Writes

Read-only by default. Do not send, forward, archive, delete, change read state,
create labels, change labels, or create drafts without explicit user request.

Before any write:

1. Name exact account alias, message/thread id, and action.
2. Use `--dry-run` or preview if available.
3. Wait for explicit approval.

Safety guards stay on:

```bash
<GOG> config set gmail_no_send true
<GOG> config no-send set <ALIAS_1>
<GOG> config no-send set <ALIAS_2>
<GOG> config no-send set <ALIAS_3>
```

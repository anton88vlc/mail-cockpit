# Run Mail Triage

You are running inside a Mail Cockpit repo.

Goal: triage several Gmail inboxes through `gogcli` and produce a concise,
human, action-oriented email brief. Do not build a classifier script. Use the
LLM for judgment.

First read:

```bash
AGENTS.md
docs/mail-triage-policy.md
docs/active-findings.md
```

Then:

1. Run:

```bash
<GOG> auth list --check --json
```

2. If any configured account is missing or invalid, stop and say exactly which
   alias/email needs auth. Do not fall back to a single Gmail connector.

3. If auth is valid, search recent mail for every alias:

```bash
<GOG> --json --account <ALIAS_1> gmail messages search 'newer_than:3d in:anywhere' --max 60
<GOG> --json --account <ALIAS_2> gmail messages search 'newer_than:3d in:anywhere' --max 60
<GOG> --json --account <ALIAS_3> gmail messages search 'newer_than:3d in:anywhere' --max 60
```

4. Open only the threads/messages where the snippet is not enough:

```bash
<GOG> --plain --account <ALIAS> gmail thread get <threadId> --full
<GOG> --json --account <ALIAS> gmail get <messageId> --format metadata
```

5. Use consequence-based judgment, not keyword bingo. Prioritize:

- money and billing failures;
- legal, government, or official mail;
- security, sign-ins, OAuth, API keys, password reset;
- infrastructure failures and service suspension risks;
- human/business threads where the user likely owes a reply;
- hard deadlines.

6. Return this shape in `<LANGUAGE>`:

```text
Urgent
- [alias] Subject. Why it matters. Direct next action/link.

Needs reply
- [alias] Subject. What to answer/do.

Waiting
- [alias] Subject. Who/what is blocking.

FYI/noise
- Short grouped summary. No newsletter cemetery.
```

If nothing deserves interruption, say:

```text
Nothing worth action found.
```

Write safety:

- Read-only by default.
- Do not send, forward, archive, delete, mark read/unread, change labels, or
  create drafts unless the user explicitly asks.
- Before any write: name account alias, message/thread ids, intended action,
  run dry-run/preview when available, then wait for explicit approval.
- If an action requires login, 2FA, a government/bank site, or a secret, say so
  directly and give the user the service link and the exact thing to do.

If you discover a durable rule or recurring active risk, update:

```bash
docs/mail-triage-policy.md
docs/active-findings.md
```

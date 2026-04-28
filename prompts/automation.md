# Recurring Mail Triage Prompt

Triage mail through the Mail Cockpit in this repo using `AGENTS.md` and
`docs/mail-triage-policy.md`.

Use `gogcli`. Do not use old helper scripts and do not fall back to a single
Gmail connector.

Read-only: do not send, archive, delete, change labels/read-state, or create
drafts unless the user explicitly asked.

First run:

```bash
<GOG> auth list --check --json
```

If accounts are missing or invalid, briefly say which aliases must be
authorized with:

```bash
<GOG> auth add <email> --services gmail --gmail-scope readonly
```

Then stop.

If auth is valid, collect recent mail:

```bash
<GOG> --json --account <ALIAS_1> gmail messages search 'newer_than:3d in:anywhere' --max 60
<GOG> --json --account <ALIAS_2> gmail messages search 'newer_than:3d in:anywhere' --max 60
<GOG> --json --account <ALIAS_3> gmail messages search 'newer_than:3d in:anywhere' --max 60
```

Open only needed threads/messages with:

```bash
<GOG> --plain --account <ALIAS> gmail thread get <threadId> --full
<GOG> --json --account <ALIAS> gmail get <messageId> --format metadata
```

Return a short brief in `<LANGUAGE>`:

- Urgent
- Needs reply
- Waiting
- FYI/noise

Every item must include account alias.

If nothing deserves attention, return exactly:

```text
DONT_NOTIFY
```

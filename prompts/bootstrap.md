# Bootstrap Prompt For Codex

Use this prompt in a new empty folder or repository when you want Codex to set
up a Mail Cockpit from scratch.

---

I want to create a portable Mail Cockpit repo for multi-account Gmail triage.

Build the setup in the current folder. Do not connect to Gmail yet unless I ask
after reviewing the files.

Create these files:

- `AGENTS.md`
- `README.md`
- `docs/mail-triage-policy.md`
- `docs/active-findings.md`
- `prompts/run-triage.md`
- `prompts/automation.md`
- `examples/report.md`

The cockpit must work like this:

- Gmail access goes through `gogcli`, not a custom Python parser and not a
  single Gmail connector.
- Codex must start every triage run with:

```bash
<GOG> auth list --check --json
```

- If any account is missing or invalid, Codex must stop and explain which alias
  needs auth.
- If auth is valid, Codex searches every configured alias:

```bash
<GOG> --json --account <ALIAS> gmail messages search 'newer_than:3d in:anywhere' --max 60
```

- Codex opens only the threads/messages that need context:

```bash
<GOG> --plain --account <ALIAS> gmail thread get <threadId> --full
<GOG> --json --account <ALIAS> gmail get <messageId> --format metadata
```

- Default behavior is read-only: no send, forward, archive, delete, labels,
  read-state changes, or drafts without explicit user approval.
- Before any write, Codex must name the account alias, message/thread ids, and
  intended action; use dry-run/preview when available; wait for explicit
  approval.
- Safety guards should be documented:

```bash
<GOG> config set gmail_no_send true
<GOG> config no-send set <ALIAS>
```

Use placeholders:

- `<GOG>`
- `<LANGUAGE>`
- `<TIMEZONE>`
- `<ALIAS_1>`, `<ALIAS_2>`, `<ALIAS_3>`
- `<EMAIL_1>`, `<EMAIL_2>`, `<EMAIL_3>`

The report format should be:

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

The policy must teach Codex to classify by consequences, not keywords:

- money and billing failures;
- security, sign-ins, OAuth, API keys, password reset;
- infra failures and service suspension risks;
- legal/government/official mail;
- human/business threads where the user likely owes a reply;
- hard deadlines;
- noise grouped briefly.

Add a living `docs/active-findings.md` file where persistent risks are tracked
outside the inbox. Include examples of open and closed findings.

Add a `prompts/run-triage.md` prompt that I can paste into Codex to run a
read-only triage after setup.

Add a `prompts/automation.md` prompt suitable for a recurring heartbeat/cron
run. It should return `DONT_NOTIFY` if nothing deserves attention.

Keep the repo small. Do not add scripts unless I explicitly ask. This is an
LLM-operated cockpit, not a rules engine.

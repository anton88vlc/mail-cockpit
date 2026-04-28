# Mail Cockpit

Mail Cockpit is a small Codex workspace for human-grade triage across several
Gmail accounts.

It is not a Gmail bot, not a rules engine, and not a pile of notification
scripts. [`gogcli`](https://github.com/steipete/gogcli) reads Gmail. Codex reads
the policy, inspects the mail, groups the noise, explains the actual risks, and
recommends the next action in plain language.

The point is simple: one place for "what deserves my attention across all my
mailboxes?" without opening three inboxes and becoming your own unpaid email
intern.

## What This Gives You

- Multi-account Gmail triage through `gogcli` aliases.
- A reusable `AGENTS.md` contract that tells Codex how to behave.
- A living mail policy in `docs/mail-triage-policy.md`.
- A durable risk tracker in `docs/active-findings.md`.
- Copy-paste prompts for manual runs and recurring read-only checks.
- A safety model that is read-only by default and blocks sending unless you
  explicitly change it.

## How It Works

1. `gogcli` owns Google auth and Gmail access.
2. Codex starts every run with an auth check.
3. Codex searches recent mail across every configured alias.
4. Codex opens only threads that need context.
5. Codex returns a short action-oriented brief.
6. Durable rules and unresolved risks get written back into docs, not lost in
   chat history.

## Prerequisites

- Codex or another local coding agent with terminal access.
- `gogcli` installed as `gog`.
- Google OAuth client credentials for `gogcli`.
- One or more Gmail accounts you are allowed to access.

Install `gogcli`:

```bash
brew install gogcli
```

Or build it from source:

```bash
git clone https://github.com/steipete/gogcli.git
cd gogcli
make
./bin/gog --help
```

## Quick Start

Clone this repo:

```bash
git clone https://github.com/pix0010/mail-cockpit.git
cd mail-cockpit
```

Edit placeholders in:

```bash
AGENTS.md
docs/mail-triage-policy.md
prompts/run-triage.md
prompts/automation.md
```

At minimum, replace:

- `<GOG>` with your `gog` path, usually `gog` or `/Users/you/go/bin/gog`.
- `<LANGUAGE>` with the report language.
- `<TIMEZONE>` with an IANA timezone, for example `Europe/Madrid`.
- `<ALIAS_1>`, `<ALIAS_2>`, `<ALIAS_3>` with mailbox aliases.
- `<EMAIL_1>`, `<EMAIL_2>`, `<EMAIL_3>` with Gmail addresses.

If you have fewer than three mailboxes, delete the extra placeholders. If you
have more, add more aliases using the same pattern.

## Configure Gmail Access

Do not paste OAuth codes, refresh tokens, client secrets, or mailbox passwords
into chat. Keep auth local.

Set OAuth credentials:

```bash
<GOG> auth credentials set /path/to/google-oauth-client.json
```

Authorize accounts read-only:

```bash
<GOG> auth add <EMAIL_1> --services gmail --gmail-scope readonly
<GOG> auth add <EMAIL_2> --services gmail --gmail-scope readonly
<GOG> auth add <EMAIL_3> --services gmail --gmail-scope readonly
```

Create aliases:

```bash
<GOG> auth alias set <ALIAS_1> <EMAIL_1>
<GOG> auth alias set <ALIAS_2> <EMAIL_2>
<GOG> auth alias set <ALIAS_3> <EMAIL_3>
```

Turn on send guards:

```bash
<GOG> config set timezone <TIMEZONE>
<GOG> config set gmail_no_send true
<GOG> config no-send set <ALIAS_1>
<GOG> config no-send set <ALIAS_2>
<GOG> config no-send set <ALIAS_3>
```

Verify:

```bash
<GOG> auth list --check --json
```

For read-only triage, stop here. If you later want archive, mark-read, or label
changes, re-authorize only the relevant mailbox with a wider Gmail scope and
keep `gmail_no_send` enabled.

## Run A Triage

Open Codex in this repo and paste:

```bash
prompts/run-triage.md
```

Codex should return buckets like:

```text
Urgent
- [work] Payment failed for cloud hosting. Service may pause tomorrow. Update
  billing here: https://...

Needs reply
- [personal] Contractor asks for confirmation. Reply yes/no by Friday.

Waiting
- [admin] Government office has not answered the document request yet.

FYI/noise
- Product updates, receipts, and newsletters grouped briefly.
```

The brief should say what matters, why, and what to do next. "You have 48
unread emails" is not insight. It is just a counter having a little crisis.

## Recurring Runs

Use `prompts/automation.md` for cron-like or heartbeat runs.

Keep the recurring run read-only. If nothing deserves attention, it should
return exactly:

```text
DONT_NOTIFY
```

## Safety Model

Default behavior is read-only.

Codex must not send, forward, archive, delete, mark read/unread, change labels,
or create drafts unless you explicitly ask.

Before any write, Codex should show:

- account alias;
- message or thread id;
- intended action;
- dry-run or preview output when available.

Then it waits for approval.

## Repository Layout

```text
AGENTS.md                     Agent contract for Codex
docs/mail-triage-policy.md    Living triage policy
docs/active-findings.md       Durable unresolved risks and follow-ups
prompts/run-triage.md         Manual triage prompt
prompts/automation.md         Recurring read-only prompt
prompts/bootstrap.md          Prompt to recreate this setup elsewhere
examples/report.md            Example of a useful brief
```

## Design Opinion

The useful part of this setup is not automation. It is judgment.

Scripts can count unread messages and keyword-match "urgent". A good cockpit
reads the thread, understands consequences, groups the boring stuff, and says:
"this one can cost you money", "this one needs a reply", "this one is just
platform confetti".

That is the job.

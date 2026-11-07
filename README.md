# gmail-cleanup

A [Claude](https://claude.com/claude-code) skill that finds unwanted Gmail (spam, repeated promotions, newsletters, old junk), shows you a plan, trashes only what you approve, and labels the rest by year.

It is built to be **cautious first**: it will not delete anything you didn't clearly approve.

## What it does

- Scans mail **older than 2 years**, one year at a time, and ranks repeat advertisers.
- Shows a dry-run report before any change. Nothing is touched until you approve a list.
- Trashes (recoverable for 30 days) or marks spam. It never permanently deletes.
- Labels the mail you keep as `Archive/YYYY`.
- Learns your choices (senders only) in a local `learned.md`.
- Writes an undo log of everything it trashed.

## Safety guarantees

The skill is default-deny. It never touches:

- **The last 2 years** of mail.
- **Mail from or to you as a person:** anything you sent, mail addressed to you by name, starred or Important mail, and your own labels.
- **Identity records:** KYC, government IDs, tax, insurance, credit reports, OTPs, security alerts, legal, medical, employment.
- **Financial mail:** banks, cards, loans/EMI, NBFCs, insurers, brokers, payment apps, including their promos.
- **Loan statements and anything with an attachment.** It builds a protected-senders list from a read-only scan and asks you to confirm it.
- Lookalike senders (a known brand name on an unrelated domain) are reported as suspicious, never bulk-acted on.

Other safeguards: 200-thread cap per approval, a 10-thread spot check before the first trash, a re-check right before acting, and a stop on any surprise. Text inside an email is treated as data and can never unlock an action. If unsure, it keeps the mail.

See [`gmail-cleanup/SKILL.md`](gmail-cleanup/SKILL.md) for the full rules.

## Requirements

- Claude Code (or another Claude client that supports skills).
- A Gmail connector that exposes `search_threads`, `get_thread`, `list_labels`, `create_label`, `label_thread`, `trash_thread` and `mark_thread_spam`.

## Install

Copy the skill folder into your skills directory:

```bash
git clone https://github.com/shubh-cloudops/gmail-cleanup-skill.git
cp -r gmail-cleanup-skill/gmail-cleanup ~/.claude/skills/
```

On Windows PowerShell:

```powershell
git clone https://github.com/shubh-cloudops/gmail-cleanup-skill.git
Copy-Item -Recurse gmail-cleanup-skill\gmail-cleanup $env:USERPROFILE\.claude\skills\
```

## Use

Ask Claude something like:

- "Clean up my Gmail."
- "Find repeat advertisers older than 2 years."
- "Organize my old mail by year."

Review the dry-run report, then approve the groups you want gone.

## Your data stays local

`learned.md` (your remembered senders) and `gmail-cleanup-log.md` (your undo log) are created on your machine and are git-ignored. Do not commit them if you fork this repo.

## Disclaimer

Use at your own risk. Trash and Spam keep mail for about 30 days, so you can restore mistakes during that time. Always read the dry-run report before approving.

## License

MIT

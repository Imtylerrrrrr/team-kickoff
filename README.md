# team-kickoff

**A Claude Code skill that sets up collaboration governance for agent-based teams** — commit/PR/branch conventions, agent context files, and GitHub-level enforcement — through a single 4-question interview.

[한국어 문서 →](README.ko.md)

## Why

When every teammate codes with an agent (Claude Code, Codex, Cursor, Gemini CLI), rules written for humans go unread — but rules placed in `AGENTS.md` are injected into every agent session automatically. This skill puts collaboration rules **where agents actually read them**, and backs them with **GitHub settings that hold even when someone's agent ignores the rules**.

Three design principles:

1. **Regulate PRs, leave commits free.** With squash-only merge, main history is exactly the sequence of PR titles. Enforce `type: description` on a handful of PR titles a day — not on dozens of WIP commits.
2. **Rules live in one place.** A ≤30-line marker-delimited block in `AGENTS.md` is the single source of truth. Everything else is a one-line pointer (`CLAUDE.md`, `GEMINI.md`) or a device, not a rule (PR template, repo settings).
3. **Verify, then report.** Every GitHub setting is re-read after applying. What can't be enforced (e.g. branch protection on private+free plans) is *explicitly reported as downgraded*, never silently assumed.

## What it sets up

```
your-repo/
├── AGENTS.md                        # the rules block (≤30 lines) — the only place rules live
├── CLAUDE.md                        # one line: @AGENTS.md
├── GEMINI.md                        # one line: read AGENTS.md
├── .github/PULL_REQUEST_TEMPLATE.md # verification checklist (auto-inserted by GitHub)
└── README.md                        # 3-line rules summary appended
```

Plus repo settings via `gh`: **squash-only merge** (PR title = main commit title, auto-delete merged branches) and **branch protection** (direct pushes blocked; 1 approval in regular mode, 0 in hackathon mode) — with a three-tier fallback when the plan or permissions don't allow it, and an honest ✅/⚠️/👤 report of what is hard-enforced, what was downgraded, and what's left for humans.

Re-running the skill is **idempotent**: it replaces only the marker-delimited block, never overwrites your files, and only confirms interview answers that change.

## Install

```bash
git clone https://github.com/Imtylerrrrrr/team-kickoff
cp -r team-kickoff/en ~/.claude/skills/team-kickoff    # English version
# or
cp -r team-kickoff/ko ~/.claude/skills/team-kickoff    # Korean version (한국어)
```

## Use

In your project repository, tell Claude Code:

> "We're a team of 4 starting a 3-month competition — set up our collaboration rules."

The skill detects what it can (git state, default branch, gh auth, repo visibility, existing files), asks exactly 4 questions (duration/intensity · team size · language · automated releases?), applies and verifies, then reports. Modes: **hackathon** (≤3 days: PR flow kept, approvals 0, self-merge allowed) and **regular** (1 approval, no self-merge).

## How it was built

Test-driven, per the [skill-writing discipline](https://github.com/obra/superpowers): a baseline agent ran the same task *without* the skill first (it duplicated the commit-type list into 5 files, installed hooks that reject WIP commits, and taught the team a bypass variable on day one — see `docs/red-baseline.md`), then the skill was written against those specific failures and validated through five persona rehearsals, including live runs against a real private GitHub repository (real 403 fallback, real PR landing, real idempotent re-runs). Design history in `docs/` (Korean).

## License

MIT

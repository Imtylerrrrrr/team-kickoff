---
name: team-kickoff
description: Use when starting or joining a team project or competition repository and collaboration rules need to be set up — commit/PR/branch conventions, agent context files, GitHub merge settings. Triggers: new hackathon/competition repo, "collaboration rules", "commit convention", "PR rules", "set up team repo", team develops with coding agents (Claude Code, Codex, Cursor, Gemini CLI).
---

# Team Kickoff

## Overview

Sets up collaboration governance for teams that develop with coding agents. Three principles:

1. **Regulate PRs, leave commits free** — with squash merge, the main history consists solely of PR titles. This shrinks the enforcement surface from dozens of commits a day to a handful of PRs.
2. **Rules live in AGENTS.md and nowhere else** — every other file is either a pointer or a device (template, repo setting), not a rule.
3. **Never report success before verifying** — a GitHub setting counts as "applied" only after it has been re-read and confirmed.

Out of scope: roadmap/implementation planning, hosting other than GitHub, teams that hand-code without agents.
The interview and all generated artifacts follow the user's language.

## Procedure

### 1. Detect — check, don't ask

- Is this a git repo? **Default branch name** (never assume — it may be `master`). Existing `AGENTS.md` / `CLAUDE.md` / `GEMINI.md` / `.github/PULL_REQUEST_TEMPLATE.md`
- `gh auth status`, remote existence, repository visibility (public/private)

### 2. Interview — exactly 4 questions, in one batch

① Duration/intensity: hackathon (≤3 days) / regular ② team size ③ commit & PR title language ④ automated releases (semver/changelog) planned?

**Never exceed 4 questions.** Don't ask what detection (step 1) already knows. Don't turn optional suggestions (e.g. CI checks) into questions — put them in the report under "For humans (optional)".

**On re-runs (partial change requests)**, confirm only the questions whose answers change — read the rest from the existing artifacts (the AGENTS.md rules block). Restoring artifacts missing from a previous run (e.g. an omitted pointer file) is in scope.

### 3. Decide the mode

| | Hackathon | Regular |
|---|---|---|
| Review approvals | 0 — self-merge allowed | 1 |
| CI PR-title check | none | suggest in report as "For humans (optional)" |
| Commit-level format hooks | none | only if ④ automated releases was chosen |

Common to all modes: squash-only merge, PR title `type: description`, exactly 5 types `feat|fix|docs|refactor|chore`, personal-branch commits are free (WIP welcome). If team size is 1, set approvals to 0. Regulation levels (L1/L2) and type adjudication: `references/commit-conventions.md`.

### 4. GitHub settings — run **before** generating artifacts

Why the order matters: the `{{PROTECTION_NOTE}}` substitution in step 5 depends on whether branch protection succeeds.

Follow `references/github-setup.md`: detect → apply → **re-verify via `gh api`** → fall back. Rule-by-enforcement mapping: `references/enforcement-matrix.md`. Three-tier fallback:

1. Public repo or paid plan → full setup: squash-only + branch protection
2. Private + free plan (protection API returns 403) → squash-only applied hard; "no direct pushes" is downgraded to a soft rule in AGENTS.md
3. gh missing / unauthenticated / no remote / not ADMIN → print a checklist for humans

### 5. Generate artifacts — use `templates/` (placeholder definitions in `templates/README.md`)

| File | Rule |
|---|---|
| `AGENTS.md` | `<!-- team-rules:start/end -->` marker section, **30-line cap**. If new, generate the skeleton: title (the project name, from README or the directory name) + rules block + two empty sections `## Project overview` / `## Build & run`. If the file exists, insert right below the title; if markers already exist, **replace only what's between them** |
| `CLAUDE.md` / `GEMINI.md` | One-line pointers. **Always create both — don't ask about the team's tools** (a one-liner costs ~0 and keeps working when teammates switch tools). If the file exists, append only the pointer line at the end. **No symlinks** (they break on Windows checkouts) |
| `.github/PULL_REQUEST_TEMPLATE.md` | Centered on a verification checklist. If a template exists, append only the checklist section |
| `README.md` | Append the short "Collaboration rules" section (3 content lines + markers — idempotent) |

**Never overwrite existing files. No changes beyond the request (no branch creation/renaming, no history rewriting)** — use the detected `{{DEFAULT_BRANCH}}` as is.

**Where the artifacts land** — rules take effect the moment they are merged into the default branch. Decision order:
1. **No remote** (regardless of team) → commit directly to the default branch — a PR is impossible. If artifacts from a previous run are sitting uncommitted, commit them together to bring the rules into force. This explicit rule overrides the general habit of avoiding direct commits to the default branch — the rules are not in force until this commit exists
2. Remote exists and the team is already working → put changes on a branch + PR, and **hand the merge to a human** (no agent self-merge — link the PR under "For humans" in the report). Without this rule, "1 approval required" deadlocks the setup PR itself
3. Remote exists but still solo → direct commit/push is fine (the rules are not in force yet, so no contradiction)

### 6. Report

Three sections: **✅ Applied hard** (GitHub settings re-verified + local files confirmed created) / **⚠️ Downgraded to soft** (the actual reason verbatim: plan, visibility, no remote, no permission + how to recover) / **👤 For humans** (merge the setup PR, invite teammates, optional suggestions).

## Iron rules — failures that actually happened in baseline testing

| Don't | What actually happened |
|---|---|
| Duplicate rules into a second file (CONTRIBUTING.md, .cursor/rules, …) | The commit-type list was copied into 5 files — change one and 5 drift |
| Write a separate human-facing rules document | A 110-line CONTRIBUTING.md, where 3 README lines suffice |
| Install commit-msg / pre-push hooks by default | Rejected WIP commits + taught the team a bypass variable on the very first push + silently inactive in non-Node repos |
| Apply maximal setup regardless of mode | Hackathon teams start bypassing rules at 3 a.m. when merges get blocked |
| Generate without markers | Duplicates and conflicts on re-runs and in existing repos |
| Report "done" without re-verifying | On private+free, branch protection fails silently |
| Assume the default branch is `main` | Two rehearsal repos were `master` — substitute the detected value into `{{DEFAULT_BRANCH}}` |

If the rules section ever exceeds 30 lines, the design is wrong, not the limit — move weight into the PR template (auto-inserted by GitHub), repo settings, or this skill's references.

# Commit & PR convention reference

Use this to pick the regulation level from interview answers, and to answer "why this rule?" when the user asks.

## Regulation spectrum — which level to use

| Level | What is regulated | When |
|---|---|---|
| L0 free-form | nothing (advisory wording only) | solo + indifferent to history — **out of scope for this skill** (the skill applies L1 and up) |
| **L1 PR-only (default)** | PR titles only: `type: description` + squash-only. Commits are free | most team/competition projects |
| L2 full Conventional Commits | every commit's format + hooks | only when interview ④ says automated releases (semver/changelog) are planned |

Why L1 is the default:
- With squash-only, main history = the sequence of PR titles. Personal-branch commits never reach main
- Competition projects have no semver and no changelog — L2's automation benefit is zero
- The enforcement surface shrinks from dozens of commits a day to 4–5 PRs
- The consumers of main history are reviewing teammates, judges, and **the agents themselves reading `git log`** — a clean history is agent-context quality

## What top-tier projects actually do (surveyed 2026-07)

| Project | Format | Enforcement | Takeaway |
|---|---|---|---|
| Linux kernel | `subsystem: summary` + a "why" body | human review | origin of the 50/72 rule; the body's explanation is what gets reviewed, not the format |
| Kubernetes | no type prefix; imperative, 50 chars, no period | review culture | even top projects don't mandate type prefixes |
| Angular | `type(scope): subject` | commitlint CI | the origin of Conventional Commits — its purpose is release automation |

Lesson: format regulation is justified by a purpose (automation), never by "looking professional."

## Adjudicating the 5 types

| Type | Criterion | When in doubt |
|---|---|---|
| feat | new behavior users can perceive | perceivable performance gains are feat too |
| fix | corrects wrong behavior | "it should have worked this way" → fix |
| docs | documentation/comments only | one line of code mixed in → not docs |
| refactor | behavior unchanged, structure only | includes adding/cleaning tests |
| chore | none of the above (config, deps, build) | if adjudication takes >30 seconds, it's chore |

Why not the full 11-type set (style, perf, test, build, ci, …): the cost of classification exceeds its value. Agents also waver at the style/refactor/chore boundaries, and readers gain little.

## Writing PR titles

- `type: description` — one space after the colon, ~50 chars total
- Imperative/noun form: "add login screen" (O) / "added the login screen" (X)
- No scopes — `feat(auth):` is banned. At this team size it adds deliberation without benefit

## Language choice (interview ③)

The criterion is the audience of the history. If the team and judges all read one language, that language carries more information per character. Agents write either equally well — pick what the team will read.

## Only if L2 (automated releases) was chosen

- Full Conventional Commits v1.0.0 compliance (including `BREAKING CHANGE:` footers)
- Hooks without dependencies: `git config core.hooksPath .githooks` + shell scripts instead of husky/commitlint (works in non-Node repos). State the activation command in the README section — silent inactivity is the failure mode
- Even in L2, **do not install a pre-push main-blocking hook** — that is branch protection's job (or a soft rule), and teaching the team a bypass variable costs more than the enforcement gains

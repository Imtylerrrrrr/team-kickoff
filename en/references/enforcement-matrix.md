# Enforcement matrix

The single source for which rule is enforced by which mechanism, and where each falls back when blocked.
Actual commands and verification: `github-setup.md`.

## Rule × mechanism

| Rule | Primary mechanism | Fallback on private+free |
|---|---|---|
| squash-only merge | repo merge settings | none needed — always available on every plan |
| no direct pushes to the default branch | branch protection | AGENTS.md soft rule (downgrade must be reported) |
| 1 review approval (regular mode) | branch protection required reviews | AGENTS.md soft rule + PR template reviewer section |
| PR title `type: description` | AGENTS.md (agents write the titles) | CI title check (regular-mode option — red X only; cannot block merges) |
| verification checklist | PR template (auto-inserted by GitHub) | none needed — plan-independent |
| commit format (L2 only) | commit-msg hook | — |

## Mechanism characteristics

| Mechanism | Plan constraint | Strength | Caveat |
|---|---|---|---|
| repo merge settings | none | hard | `gh api -X PATCH /repos/{o}/{r}` (allow_squash_merge etc.) |
| branch protection / rulesets | public: free · private: Pro/Team+ | hard | **fails silently on private+free** — re-verify with `gh api` after applying |
| AGENTS.md rules | none | soft (auto-injected every session) | near-hard for agents, but ignoring is possible |
| PR template | none | soft (visible) | a form, not a rule — never duplicate rule text into it |
| CI (Actions) | private free tier: 2,000 min/month | medium | red X only; blocking merges requires branch protection |
| git hooks (core.hooksPath) | none | medium (local) | needs activation per clone — failure is silent. L2 only |

## Why the two-layer structure doesn't collapse

Write-time (AGENTS.md, soft) and merge-time (GitHub settings, hard) are independent. If any teammate's agent ignores the rules, contamination of main is still blocked at merge time; and where branch protection is unavailable, squash-only survives and preserves the history's shape.

## Downgrade reporting principle

Whenever a hard mechanism is unavailable and a rule is downgraded to soft, the report must state three things: **what was downgraded · why (plan/visibility/remote/permission) · how to recover** (go public or upgrade the plan, then re-run this skill).

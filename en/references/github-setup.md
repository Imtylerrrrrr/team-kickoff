# GitHub setup procedure — detect → apply → re-verify → report

Principle: **every command can fail.** Failures route to fallbacks; success is reported only after passing the re-verification in §3.
Rationale for rule/mechanism mapping: `enforcement-matrix.md`.

## 1. Detect

```bash
gh auth status                                   # fails → fallback C
git remote get-url origin                        # missing → fallback C (+ suggest gh repo create)
gh repo view --json nameWithOwner,visibility,viewerPermission
```

- If `viewerPermission` is not `ADMIN`, settings can't be changed → fallback C (a checklist to hand the admin).
- Never assume the default branch name: `gh repo view --json defaultBranchRef --jq .defaultBranchRef.name`

## 2. Apply

**Read before writing**: first run the §3 GETs to see current values, and only PATCH/PUT the items that differ from the target. This eliminates unnecessary writes (and their permission prompts) on idempotent re-runs.

### 2a. Merge settings — available on every plan

```bash
gh api -X PATCH repos/{owner}/{repo} \
  -F allow_squash_merge=true -F allow_merge_commit=false -F allow_rebase_merge=false \
  -F squash_merge_commit_title=PR_TITLE -F squash_merge_commit_message=PR_BODY \
  -F delete_branch_on_merge=true
```

- `squash_merge_commit_title=PR_TITLE` is the linchpin — this field is what guarantees "PR title = main commit message."
- `delete_branch_on_merge` — auto-delete merged branches (prevents dead-branch buildup).

### 2b. Branch protection — public repos or paid plans only

```bash
gh api -X PUT repos/{owner}/{repo}/branches/{default}/protection --input - <<'JSON'
{
  "required_pull_request_reviews": { "required_approving_review_count": N },
  "required_status_checks": null,
  "enforce_admins": false,
  "restrictions": null,
  "allow_force_pushes": false
}
JSON
```

- `N` = 1 for regular / 0 for hackathon & solo. **Hackathon mode still applies protection** — direct pushes stay blocked; only approvals go to 0 (PR flow enforced + self-merge allowed).
- HTTP 403 with an "Upgrade to GitHub Pro" style message → private+free → **fallback B**. Do not die on this error.

## 3. Re-verify — only what passes here is "applied"

```bash
gh api repos/{owner}/{repo} \
  --jq '{squash:.allow_squash_merge, merge:.allow_merge_commit, rebase:.allow_rebase_merge, title:.squash_merge_commit_title}'
gh api repos/{owner}/{repo}/branches/{default}/protection --jq '.required_pull_request_reviews.required_approving_review_count' 2>&1
```

- If the second command returns 404/403, branch protection **does not exist** — even if 2b appeared to succeed.
- A failure here (exit code 1) is not an error but a **downgrade verdict** — don't stop; proceed to fallback B/C.

## 4. Fallbacks

| | Condition | Action |
|---|---|---|
| A | 2a+2b re-verified | report full setup |
| B | private + free (2b → 403) | 2a hard only. Substitute the soft wording into `{{PROTECTION_NOTE}}` in `agents-section.md`. State the downgrade in the report (what · why · recovery: go public or Pro, then re-run this skill) |
| C | gh unauthenticated · no remote · not ADMIN | print the human checklist: Settings→General→Pull Requests: allow squash only + default title "PR title" + auto-delete on; Settings→Branches: add a protection rule (when the plan allows). Substitute the same soft wording as B into `{{PROTECTION_NOTE}}`, and state the actual reason ("no remote" / "no permission") in the ⚠️ report section |

## 5. Report format

```
✅ Applied hard (re-verified): squash-only + PR-title-as-commit-title, branch protection (N approvals)
⚠️ Downgraded to soft: <rule> — <reason>. Recovery: <how>
👤 For humans: <checklist>
```

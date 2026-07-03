# GitHub 설정 절차 — 감지 → 적용 → 재확인 → 보고

원칙: **모든 명령은 실패할 수 있다.** 실패는 폴백으로 보내고, 성공은 3단계 재확인을 통과한 것만 보고한다.
규칙별 수단·강등 경로의 근거는 `enforcement-matrix.md`.

## 1. 감지

```bash
gh auth status                                   # 실패 → 폴백 C
git remote get-url origin                        # 없음 → 폴백 C (+ gh repo create 제안)
gh repo view --json nameWithOwner,visibility,viewerPermission
```

- `viewerPermission`이 `ADMIN`이 아니면 설정 변경 불가 → 폴백 C (관리자에게 전달할 체크리스트).
- 기본 브랜치명은 가정하지 말 것: `gh repo view --json defaultBranchRef --jq .defaultBranchRef.name` (쓸 수 있는 원격이 없으면 `git branch --show-current`)

## 2. 적용

**쓰기 전에 읽는다**: 먼저 §3의 GET으로 현재 값을 확인하고, 목표와 불일치하는 항목이 있을 때만 PATCH/PUT을 실행한다. 멱등 재실행에서 불필요한 쓰기(및 그에 따른 권한 프롬프트)를 없앤다.

### 2a. 머지 방식 — 모든 플랜에서 가능

```bash
gh api -X PATCH repos/{owner}/{repo} \
  -F allow_squash_merge=true -F allow_merge_commit=false -F allow_rebase_merge=false \
  -F squash_merge_commit_title=PR_TITLE -F squash_merge_commit_message=PR_BODY \
  -F delete_branch_on_merge=true
```

- `squash_merge_commit_title=PR_TITLE`이 핵심 — "PR 제목 = main 커밋 메시지"를 이 필드가 보장한다.
- `delete_branch_on_merge` — 머지된 브랜치 자동 삭제 (죽은 브랜치 누적 방지).

### 2b. 브랜치 보호 — public 또는 유료 플랜만

```bash
DEFAULT=$(gh repo view --json defaultBranchRef --jq .defaultBranchRef.name)
gh api -X PUT "repos/{owner}/{repo}/branches/$DEFAULT/protection" --input - <<'JSON'
{
  "required_pull_request_reviews": { "required_approving_review_count": N },
  "required_status_checks": null,
  "enforce_admins": false,
  "restrictions": null,
  "allow_force_pushes": false
}
JSON
```

- `N` = 정규 1 / 해커톤·1인 0. **해커톤도 보호는 건다** — 직push 차단은 유지하고 승인만 0 (PR 흐름 강제 + 셀프 머지 허용).
- HTTP 403에 "Upgrade to GitHub Pro" 류 메시지 → private+무료 → **폴백 B**. 에러로 죽지 말 것.
- gh api가 자동 치환하는 것은 `{owner}`/`{repo}`/`{branch}`뿐 — 기본 브랜치는 직접 넣어야 한다(위 `$DEFAULT`). 리터럴 `{default}`는 API로 그대로 전송돼 404가 난다.

## 3. 재확인 — 통과한 것만 "적용됨"

```bash
gh api repos/{owner}/{repo} \
  --jq '{squash:.allow_squash_merge, merge:.allow_merge_commit, rebase:.allow_rebase_merge, title:.squash_merge_commit_title, msg:.squash_merge_commit_message, autodel:.delete_branch_on_merge}'
gh api "repos/{owner}/{repo}/branches/$DEFAULT/protection" --jq '.required_pull_request_reviews.required_approving_review_count' 2>&1
```

- 두 번째 명령이 404/403이면 브랜치 보호는 **없는 것이다** — 2b가 성공한 듯 보였어도.
- 이 조회의 실패(exit code 1)는 오류가 아니라 **강등 판정 신호**다 — 여기서 중단하지 말고 폴백 B/C로 진행한다.

## 4. 폴백

| | 조건 | 하는 일 |
|---|---|---|
| A | 2a+2b 재확인 통과 | 풀 세팅 보고 |
| B | private+무료 (2b 403) | 2a만 하드. `agents-section.md`의 `{{PROTECTION_NOTE}}`를 소프트 문구로 치환하고, 정규 모드면 `{{REVIEW_RULE}}`에도 동일 소프트 문구를 덧붙인다(승인 요건도 강제되지 않으므로). 강등을 보고에 명시 (무엇·왜·복구법: public 전환 또는 Pro 후 스킬 재실행) |
| C | gh 미설치·미인증·원격 없음·ADMIN 아님 | 사람용 체크리스트는 **최종 보고의 👤 칸에 한 번만** (중간 출력 금지): Settings→General→Pull Requests에서 squash만 허용 + 기본 제목 "PR title" + auto-delete 켜기, Settings→Branches에서 보호 규칙 추가 (가능한 플랜일 때). 원격이 없으면 머지 설정(2a)도 적용 불가 — ⚠️에 넣고 복구는 "원격 생성 후 스킬 재실행". `{{PROTECTION_NOTE}}`는 B와 동일한 소프트 문구로 치환하고, ⚠️의 이유는 실제 원인("원격 없음"·"권한 없음")을 그대로 적는다 |

## 5. 보고 형식

```
✅ 하드 적용 (재확인 완료): squash-only + PR제목=커밋제목, 브랜치 보호(승인 N)
⚠️ 소프트 강등: <규칙> — <이유>. 복구: <방법>
👤 사람이 할 일: <체크리스트>
```

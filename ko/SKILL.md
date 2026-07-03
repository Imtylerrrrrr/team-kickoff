---
name: team-kickoff
description: Use when starting or joining a team project or competition repository and collaboration rules need to be set up — commit/PR/branch conventions, agent context files, GitHub merge settings. Triggers: new hackathon/공모전/대회 repo, "협업 규칙", "커밋 규칙 세팅", "PR 규칙", "팀 레포 준비", team develops with coding agents (Claude Code, Codex, Cursor, Gemini CLI).
---

# Team Kickoff

## 개요

에이전트로 개발하는 팀의 협업 거버넌스를 세팅한다. 세 가지 원칙:

1. **PR을 규제하고 커밋은 자율에 둔다** — squash merge 하에서 main 히스토리는 PR 제목으로만 구성된다. 규제 지점을 하루 커밋 수십 개에서 PR 몇 개로 줄인다.
2. **규칙은 AGENTS.md 한 곳에만 산다** — 다른 모든 파일은 포인터이거나, 규칙이 아닌 장치(템플릿·설정)다.
3. **검증 전에 성공을 보고하지 않는다** — GitHub 설정은 적용 후 재확인한 것만 "완료"다.

비범위: 로드맵·구현 계획 생성, GitHub 외 호스팅, 손코딩 전용 팀.
인터뷰·산출물의 언어는 사용자의 언어를 따른다.

## 진행 순서

### 1. 감지 — 묻지 말고 확인한다

- git 레포 여부, **기본 브랜치명**(가정 금지 — master일 수 있다), 기존 `AGENTS.md`/`CLAUDE.md`/`GEMINI.md`/`.github/PULL_REQUEST_TEMPLATE.md`
- `gh auth status`, 원격 존재 여부, 레포 가시성(public/private)

### 2. 인터뷰 — 정확히 4문항, 한 번에

① 기간·강도: 해커톤(≤3일) / 정규 ② 팀 인원 ③ 커밋·PR 제목 언어(한글/영어) ④ 자동 릴리즈(semver·체인지로그) 계획 여부

**4문항 초과 금지.** 감지(1단계)로 알 수 있는 것을 묻지 않는다. CI 검사 등 선택 제안도 질문하지 말 것 — 보고서의 "사람이 할 일 (선택)"에 넣는다.

**재실행(부분 변경 요청)이면** 달라지는 문항만 확인한다 — 나머지 답은 기존 산출물(AGENTS.md 규칙 블록)에서 읽는다. 이전 실행의 누락 산출물(예: 빠진 포인터 파일)을 복구하는 것은 범위 내 변경이다.

### 3. 모드 결정

| | 해커톤 | 정규 |
|---|---|---|
| 리뷰 승인 | 0명 — 셀프 머지 허용 | 1명 |
| CI PR 제목 검사 | 안 함 | 보고서 "사람이 할 일 (선택)"으로 제안 |
| 커밋 레벨 형식 검사(훅) | 안 함 | ④ 자동 릴리즈 선택 시에만 |

모든 모드 공통: squash-only 머지, PR 제목 `타입: 설명`, 타입은 `feat|fix|docs|refactor|chore` 5종, 개인 브랜치 커밋 자유(WIP 허용). 인원이 1명이면 승인 0으로 조정. 규제 수준(L1/L2)과 타입 판정 기준은 `references/commit-conventions.md`.

### 4. GitHub 설정 — 산출물 생성보다 **먼저** 실행한다

순서가 중요한 이유: 5단계의 `{{PROTECTION_NOTE}}` 치환값은 브랜치 보호의 성패를 알아야 정해진다.

절차는 `references/github-setup.md`대로: 감지 → 적용 → **`gh api`로 실제 적용 재확인** → 폴백. 규칙별 수단·강등 경로는 `references/enforcement-matrix.md`. 폴백 3단:

1. public 또는 유료 플랜 → squash-only + 브랜치 보호 풀 세팅
2. private + 무료 (보호 API 403) → squash-only만 하드 적용. "직push 금지"는 AGENTS.md 소프트 규칙으로 강등
3. gh 미설치/미인증/원격 없음/ADMIN 아님 → 사람이 할 일을 체크리스트로 출력

### 5. 산출물 생성 — `templates/` 사용 (치환자 정의는 `templates/README.md`)

| 파일 | 규칙 |
|---|---|
| `AGENTS.md` | `<!-- team-rules:start/end -->` 마커 섹션, **30줄 상한**. 신규면 골격으로 생성: 제목 + 규칙 블록 + 빈 `## 프로젝트 개요`·`## 빌드·실행` 섹션 2개. 기존 파일이면 제목 바로 아래 삽입, 마커가 이미 있으면 **사이만 교체** |
| `CLAUDE.md` / `GEMINI.md` | 한 줄 포인터. **팀 구성을 묻지 말고 둘 다 항상 생성** — 한 줄 비용 ≈ 0, 팀원이 도구를 바꿔도 동작한다. 기존 파일이면 파일 끝에 포인터 라인만 추가. **심링크 금지** (Windows 체크아웃에서 깨짐) |
| `.github/PULL_REQUEST_TEMPLATE.md` | 검증 체크리스트 중심. 기존 템플릿이 있으면 체크리스트 섹션만 append |
| `README.md` | "협업 규칙" 3줄 섹션 append (마커 포함 — 멱등) |

**기존 파일 덮어쓰기 금지. 요청 밖 변경(브랜치 생성·이름 변경, 히스토리 재작성 등) 금지** — 브랜치명은 감지된 `{{DEFAULT_BRANCH}}`를 그대로 쓴다.

**산출물의 착지 경로** — 규칙은 main에 머지된 순간부터 발효된다. 판정 순서:
1. **원격이 없으면** (팀 유무 무관) → 기본 브랜치에 직커밋 — PR 자체가 불가능하다. 이전 실행의 미커밋 산출물이 남아 있으면 함께 커밋해 발효시킨다
2. 원격이 있고 팀이 이미 작업 중 → 브랜치 + PR로 올리되, **머지는 사람에게 넘긴다** (에이전트 셀프 머지 금지 — 보고의 "사람이 할 일"에 PR 링크). 이 규칙이 없으면 "승인 1명"이 세팅 PR 자체를 데드락에 빠뜨린다
3. 원격이 있고 아직 혼자 → 직커밋·직push 허용 (규칙 발효 전이므로 모순 아님)

### 6. 보고

세 칸으로: **✅ 하드 적용** (재확인된 GitHub 설정 + 생성 확인된 로컬 파일) / **⚠️ 소프트 강등** (실제 이유 그대로: 플랜·가시성·원격 없음·권한 없음 + 복구법) / **👤 사람이 할 일** (세팅 PR 머지, 팀원 초대, 선택 제안 포함).

## 철칙 — 베이스라인 테스트에서 실제 발생한 실패들

| 하지 마라 | 실제로 벌어진 일 |
|---|---|
| 규칙을 두 번째 파일에 복제 (CONTRIBUTING.md, .cursor/rules 등) | 커밋 타입 목록이 5개 파일에 복제됨 — 하나 바꾸면 5곳 drift |
| 사람용 규칙 문서를 따로 작성 | 110줄짜리 CONTRIBUTING.md — README 3줄이면 충분한 것을 |
| 기본값으로 commit-msg·pre-push 훅 설치 | WIP 커밋 거부 + 첫 push부터 우회 변수(`ALLOW_MAIN_PUSH=1`)를 가르침 + Node 아닌 레포에서 조용히 비활성 |
| 모드 구분 없이 최대 세팅 | 해커톤 팀은 새벽 머지 블락에서 규칙을 우회하기 시작한다 |
| 마커 없이 생성 | 재실행·기존 레포에서 중복과 충돌 |
| 재확인 없이 "완료" 보고 | private+무료에서 브랜치 보호는 조용히 실패한다 |
| 기본 브랜치명을 main으로 가정 | 리허설 레포 2개가 master였다 — 템플릿의 `{{DEFAULT_BRANCH}}`에 감지값을 넣는다 |

규칙 섹션이 30줄을 넘게 됐다면 규칙이 아니라 설계가 잘못된 것이다 — 무거운 것은 PR 템플릿(GitHub이 자동 삽입)·레포 설정·이 스킬의 references로 옮긴다.

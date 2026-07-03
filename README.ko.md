# team-kickoff

**에이전트 팀을 위한 협업 거버넌스 세팅 스킬** — 커밋·PR·브랜치 규칙, 에이전트 컨텍스트 파일, GitHub 강제 설정까지 4문항 인터뷰 하나로.

[English (primary) →](README.md)

## 왜

팀원 전원이 코딩 에이전트(Claude Code, Codex, Cursor, Gemini CLI)로 개발하는 팀에서, 사람용 규칙 문서는 아무도 읽지 않습니다 — 하지만 `AGENTS.md`에 놓인 규칙은 매 에이전트 세션에 자동 주입됩니다. 이 스킬은 규칙을 **에이전트가 실제로 읽는 곳**에 두고, 누군가의 에이전트가 규칙을 무시해도 버티는 **GitHub 설정**으로 뒷받침합니다.

설계 원칙 세 가지:

1. **PR을 규제하고 커밋은 자율에.** squash-only 머지에서 main 히스토리는 PR 제목의 나열입니다. 하루 수십 개 WIP 커밋이 아니라 PR 제목 몇 개에만 `타입: 설명`을 요구합니다.
2. **규칙은 한 곳에만.** `AGENTS.md`의 30줄 이하 마커 블록이 단일 원본. 나머지는 한 줄 포인터(`CLAUDE.md`·`GEMINI.md`)거나 규칙이 아닌 장치(PR 템플릿·레포 설정)입니다.
3. **검증 후 보고.** 모든 GitHub 설정은 적용 후 재확인합니다. 강제 불가한 것(예: private+무료 플랜의 브랜치 보호)은 조용히 넘어가지 않고 **강등으로 명시 보고**합니다.

## 무엇이 세팅되나

```
your-repo/
├── AGENTS.md                        # 규칙 블록 (≤30줄) — 규칙이 사는 유일한 곳
├── CLAUDE.md                        # 한 줄: @AGENTS.md
├── GEMINI.md                        # 한 줄: AGENTS.md를 읽어라
├── .github/PULL_REQUEST_TEMPLATE.md # 검증 체크리스트 (GitHub이 자동 삽입)
└── README.md                        # 협업 규칙 3줄 섹션 append
```

그리고 `gh`로 레포 설정: **squash-only 머지**(PR 제목=main 커밋 제목, 머지 브랜치 자동 삭제)와 **브랜치 보호**(직push 차단, 정규 모드 승인 1명 / 해커톤 모드 0명) — 플랜·권한이 안 되면 3단 폴백으로 처리하고, 하드 적용·소프트 강등·사람 몫을 ✅/⚠️/👤로 정직하게 보고합니다.

재실행은 **멱등**입니다: 마커 블록 사이만 교체하고, 기존 파일을 덮어쓰지 않으며, 달라지는 인터뷰 문항만 다시 확인합니다.

## 설치

```bash
git clone https://github.com/Imtylerrrrrr/team-kickoff
cp -r team-kickoff/ko ~/.claude/skills/team-kickoff    # 한국어 버전
# 또는
cp -r team-kickoff/en ~/.claude/skills/team-kickoff    # 영어 버전
```

## 사용

프로젝트 레포에서 Claude Code에게:

> "우리 4명이서 3개월 공모전 시작해 — 협업 규칙 세팅해줘."

스킬이 감지 가능한 것(git 상태, 기본 브랜치, gh 인증, 가시성, 기존 파일)은 묻지 않고, 정확히 4문항(기간·강도 / 인원 / 언어 / 자동 릴리즈)만 물은 뒤, 적용·검증·보고합니다. 모드: **해커톤**(≤3일: PR 흐름 유지, 승인 0, 셀프 머지 허용) / **정규**(승인 1명, 셀프 머지 금지).

## 어떻게 만들어졌나

문서에 대한 TDD([skill-writing discipline](https://github.com/obra/superpowers))로 만들어졌습니다: 먼저 스킬 *없이* 베이스라인 에이전트에게 같은 과제를 시켜 실패를 기록하고(커밋 타입 목록을 5개 파일에 복제, WIP 커밋을 거부하는 훅 설치, 첫날부터 우회 변수 학습 — `docs/red-baseline.md`), 그 실패를 겨냥해 스킬을 작성한 뒤, 실제 private GitHub 레포 대상 실전 실행(진짜 403 폴백, 진짜 PR 착지, 진짜 멱등 재실행)을 포함한 페르소나 리허설 5회로 검증했습니다. 설계 기록은 `docs/`에.

## 라이선스

MIT

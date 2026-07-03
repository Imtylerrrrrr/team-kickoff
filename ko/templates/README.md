# templates/ 사용 규칙

- `{{PLACEHOLDER}}`는 인터뷰·모드·GitHub 설정 결과로 치환한다:
  - `{{LANG}}` → 한글 / English (인터뷰 ③)
  - `{{DEFAULT_BRANCH}}` → 감지된 기본 브랜치명 (main·master 등). **브랜치를 새로 만들거나 이름을 바꾸지 말 것** — 있는 그대로 쓴다
  - `{{REVIEW_RULE}}` → 해커톤: "승인 없이 셀프 머지 허용 (PR은 반드시 거친다)" / 정규: "1명 승인 후 머지, 셀프 머지 금지" / 1인: "승인 없이 머지". 폴백 B/C에서 정규 모드면 이 문구 뒤에도 `{{PROTECTION_NOTE}}`와 동일한 소프트 표기를 덧붙인다(승인 수도 설정으로 강제되지 않으므로)
  - `{{PROTECTION_NOTE}}` → 세 상태:
    - 브랜치 보호 하드 적용(폴백 A): `""` (빈 문자열)
    - 소프트 강등(폴백 B — 403): `" (설정으로 강제되지 않음 — 규칙으로 지킨다)"`
    - 미적용·사람 대기(폴백 C — 원격 없음·권한 없음): 위 B와 동일한 소프트 문구 — 나중에 보호가 걸리면 스킬 재실행으로 갱신
- L2(인터뷰 ④ 자동 릴리즈)면: `agents-section.md`의 "커밋 메시지·횟수는 자유" 줄을 "모든 커밋은 Conventional Commits v1.0.0 준수 (commit-msg 훅 — 활성화: `git config core.hooksPath .githooks`)"로 교체하고, `readme-section.md` 규칙 줄 끝에 " · 커밋 훅: `git config core.hooksPath .githooks`"를 덧붙인다 (상세: `references/commit-conventions.md`).
- 사용자의 언어가 한국어가 아니면 템플릿 전체를 그 언어로 번역해 생성한다.
- 마커(`<!-- team-rules:start/end -->`)는 언어와 무관하게 그대로 유지한다 — 재실행 멱등성의 기준점.
- 재실행 시 "사이만 교체"란: 마커 라인 자체는 건드리지 않고 그 사이 내용만 교체한다는 뜻 (`team-rules:start` 접두로 마커를 식별 — 뒤에 붙은 주석 문구가 달라도 같은 마커다).
- 기존 파일에 삽입할 때의 규칙은 SKILL.md 5단계 표를 따른다 (덮어쓰기 금지).

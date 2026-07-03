# templates/ usage rules

- `{{PLACEHOLDER}}`s are substituted from the interview, mode decision, and GitHub setup results:
  - `{{LANG}}` → English / 한글 / … (interview ③)
  - `{{DEFAULT_BRANCH}}` → the detected default branch name (main, master, …). **Never create or rename branches** — use it as is
  - `{{REVIEW_RULE}}` → hackathon: "self-merge allowed without approval (but always through a PR)" / regular: "merge after 1 approval; no self-merge" / solo: "merge without approval"
  - `{{PROTECTION_NOTE}}` → three states:
    - branch protection applied hard (fallback A): `""` (empty string)
    - downgraded to soft (fallback B — 403): `" (not enforced by settings — kept by discipline)"`
    - not applied, awaiting humans (fallback C — no remote / no permission): same soft wording as B — once protection is set up later, a skill re-run refreshes it
- If the user's language isn't English, translate the template content into that language when generating.
- Markers (`<!-- team-rules:start/end -->`) stay verbatim regardless of language — they are the idempotency anchors for re-runs.
- On re-runs, "replace only between markers" means: leave the marker lines themselves untouched and swap only the content between them (identify markers by the `team-rules:start` prefix — trailing comment text may differ and it's still the same marker).
- Rules for inserting into existing files: the table in SKILL.md step 5 (never overwrite).

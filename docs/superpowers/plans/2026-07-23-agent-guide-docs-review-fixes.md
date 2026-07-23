# Agent Guide Docs Review Fixes Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Apply the 5 confirmed findings from `docs/superpowers/specs/2026-07-23-agent-guide-docs-review-design.md` (item 2 was withdrawn during planning) to `nextjs-agent-guide/docs/*.md`.

**Architecture:** Each task is one file, one exact `old_string`→`new_string` edit (or small group of edits within the same file), followed by a `grep` verification command and a commit. No code, no tests to run — verification is textual (grep for absence of the old pattern / presence of the new one).

**Tech Stack:** Plain Markdown editing. All paths are relative to `/Users/sangjaeoh/Desktop/gitspace/ai-agent-guides`.

## Global Constraints

- Every edited file must remain compliant with the editorial convention in `nextjs-agent-guide/AGENTS.md` (bottom section, "편집 규약"): rules as Markdown bullets/tables only, one sentence per bullet, present-state phrasing, one rule owned by one document.
- All 5 tasks touch files only inside `nextjs-agent-guide/docs/` — nothing in `polyglot-monorepo-agent-guide` changes (no surviving findings there).
- Do not touch any content outside the exact lines named in each task — this is a surgical fix pass, not a rewrite.
- One commit per task, message prefix `docs:`.

---

### Task 1: Generalize web/admin app-composition table

**Files:**
- Modify: `nextjs-agent-guide/docs/architecture.md:53-61`

**Interfaces:** None (standalone doc edit).

- [ ] **Step 1: Edit the file**

old_string:
```
### 앱 구성

| 앱 | 책임 | 데이터 접근 |
|---|---|---|
| `web` | 공개 사용자 화면·API | RSC·Server Action |
| `admin` | 어드민·백오피스 | 동일 |

- 앱은 배포 단위다.
- 새 앱 추가 시 워크스페이스 조립만 변경한다.
```

new_string:
```
### 앱 구성

- 앱마다 책임 범위가 다를 수 있다.
- 데이터 접근 방식(RSC·Server Action)은 앱 전체에서 동일하게 적용한다.
- 앱은 배포 단위다.
- 새 앱 추가 시 워크스페이스 조립만 변경한다.
```

- [ ] **Step 2: Verify**

Run: `grep -n "web\|admin" nextjs-agent-guide/docs/architecture.md`
Expected: no output (no matches).

Run: `grep -n "앱마다 책임 범위가 다를 수 있다" nextjs-agent-guide/docs/architecture.md`
Expected: one match.

- [ ] **Step 3: Commit**

```bash
git add nextjs-agent-guide/docs/architecture.md
git commit -m "docs: generalize app-composition table to avoid fixed web/admin naming"
```

---

### Task 2: Indent rationale bullets in code-quality.md

**Files:**
- Modify: `nextjs-agent-guide/docs/code-quality.md:64-65`
- Modify: `nextjs-agent-guide/docs/code-quality.md:80-81`

**Interfaces:** None.

- [ ] **Step 1: Edit — Storybook-in-app rationale (lines 64-65)**

old_string:
```
- 앱에 Storybook 설정을 두지 않는다.
- 앱에 두면 디자인시스템 검증이 앱 구조에 종속된다.
```

new_string:
```
- 앱에 Storybook 설정을 두지 않는다.
  - 앱에 두면 디자인시스템 검증이 앱 구조에 종속된다.
```

- [ ] **Step 2: Edit — new-dependency rationale (lines 80-81)**

old_string:
```
- 새 npm 의존성 도입은 신중히 검토한다.
- 프론트 의존성은 런타임 번들에 포함되어 공급망 리스크를 가진다.
- 단순 버전 상향은 예외다.
```

new_string:
```
- 새 npm 의존성 도입은 신중히 검토한다.
  - 프론트 의존성은 런타임 번들에 포함되어 공급망 리스크를 가진다.
- 단순 버전 상향은 예외다.
```

- [ ] **Step 3: Verify**

Run: `grep -n "^- 앱에 두면\|^- 프론트 의존성은" nextjs-agent-guide/docs/code-quality.md`
Expected: no output (both now indented, not top-level).

Run: `grep -n "^  - 앱에 두면\|^  - 프론트 의존성은" nextjs-agent-guide/docs/code-quality.md`
Expected: two matches.

- [ ] **Step 4: Commit**

```bash
git add nextjs-agent-guide/docs/code-quality.md
git commit -m "docs: indent rationale bullets under their rules in code-quality.md"
```

---

### Task 3: Indent hydration-mismatch rationale in rendering.md

**Files:**
- Modify: `nextjs-agent-guide/docs/rendering.md:24-26`

**Interfaces:** None.

- [ ] **Step 1: Edit the file**

old_string:
```
- 서버와 클라이언트 렌더 결과가 일치해야 한다.
- hydration mismatch는 조용한 비주얼 버그를 만든다.
- 서버 렌더 경로에서 비결정 값을 사용하지 않는다.
```

new_string:
```
- 서버와 클라이언트 렌더 결과가 일치해야 한다.
  - hydration mismatch는 조용한 비주얼 버그를 만든다.
- 서버 렌더 경로에서 비결정 값을 사용하지 않는다.
```

- [ ] **Step 2: Verify**

Run: `grep -n "^- hydration mismatch" nextjs-agent-guide/docs/rendering.md`
Expected: no output.

Run: `grep -n "^  - hydration mismatch" nextjs-agent-guide/docs/rendering.md`
Expected: one match.

- [ ] **Step 3: Commit**

```bash
git add nextjs-agent-guide/docs/rendering.md
git commit -m "docs: indent hydration-mismatch rationale under its rule in rendering.md"
```

---

### Task 4: Indent BFF business-rule rationale in data.md

**Files:**
- Modify: `nextjs-agent-guide/docs/data.md:50-52`

**Interfaces:** None.

- [ ] **Step 1: Edit the file**

old_string:
```
- 백엔드가 소유해야 할 비즈니스 규칙은 생성하지 않는다.
- BFF 비즈니스 규칙은 백엔드와 규칙 drift를 만든다.
- 크로스 소스 조립은 서버에서 병렬 집약한다.
```

new_string:
```
- 백엔드가 소유해야 할 비즈니스 규칙은 생성하지 않는다.
  - BFF 비즈니스 규칙은 백엔드와 규칙 drift를 만든다.
- 크로스 소스 조립은 서버에서 병렬 집약한다.
```

- [ ] **Step 2: Verify**

Run: `grep -n "^- BFF 비즈니스 규칙은" nextjs-agent-guide/docs/data.md`
Expected: no output.

Run: `grep -n "^  - BFF 비즈니스 규칙은" nextjs-agent-guide/docs/data.md`
Expected: one match.

- [ ] **Step 3: Commit**

```bash
git add nextjs-agent-guide/docs/data.md
git commit -m "docs: indent BFF business-rule rationale under its rule in data.md"
```

---

### Task 5: Fix testing.md — indent rationale chain, split compound bullet

**Files:**
- Modify: `nextjs-agent-guide/docs/testing.md:19-22`
- Modify: `nextjs-agent-guide/docs/testing.md:144`

**Interfaces:** None.

- [ ] **Step 1: Edit — scenario-ownership rationale chain (lines 19-22)**

old_string:
```
- 행동을 추가·변경하는 작업은 구현 전에 시나리오 목록을 한국어 문장으로 제시해 합의받는다.
- 코드와 테스트를 같은 주체가 쓰면 의도 해석 오류가 양쪽에 상관된다.
- 시나리오 문장 합의가 이 상관을 끊는다.
- 목록은 필수 시나리오 축에서 파생되는 케이스를 기본 포함한다.
```

new_string:
```
- 행동을 추가·변경하는 작업은 구현 전에 시나리오 목록을 한국어 문장으로 제시해 합의받는다.
  - 코드와 테스트를 같은 주체가 쓰면 의도 해석 오류가 양쪽에 상관된다.
  - 시나리오 문장 합의가 이 상관을 끊는다.
- 목록은 필수 시나리오 축에서 파생되는 케이스를 기본 포함한다.
```

- [ ] **Step 2: Edit — split compound idempotency-key bullet (line 144)**

old_string:
```
- 중복 제출 계약은 변경 안전 축의 멱등키가 소유하고, 중복 처리 판정은 백엔드 소유다(→ [data](data.md)).
```

new_string:
```
- 중복 제출 계약은 변경 안전 축의 멱등키가 소유한다.
- 중복 처리 판정은 백엔드가 소유한다(→ [data](data.md)).
```

- [ ] **Step 3: Verify**

Run: `grep -n "^- 코드와 테스트를 같은 주체" nextjs-agent-guide/docs/testing.md`
Expected: no output (now indented).

Run: `grep -n "멱등키가 소유하고" nextjs-agent-guide/docs/testing.md`
Expected: no output (compound clause removed).

Run: `grep -c "^- 중복" nextjs-agent-guide/docs/testing.md`
Expected: `2`

- [ ] **Step 4: Commit**

```bash
git add nextjs-agent-guide/docs/testing.md
git commit -m "docs: indent rationale chain and split compound bullet in testing.md"
```

---

### Task 6: Fix design-system.md — split dual-reference bullet, fix arrow outlier

**Files:**
- Modify: `nextjs-agent-guide/docs/design-system.md:19`
- Modify: `nextjs-agent-guide/docs/design-system.md:106`

**Interfaces:** None.

- [ ] **Step 1: Edit — split dual-subject reference bullet (line 19)**

old_string:
```
- 스타일 표기와 접근성은 → [coding-conventions](coding-conventions.md), 강제 도구는 → [code-quality](code-quality.md)가 소유한다.
```

new_string:
```
- 스타일 표기와 접근성은 → [coding-conventions](coding-conventions.md)가 소유한다.
- 강제 도구는 → [code-quality](code-quality.md)가 소유한다.
```

- [ ] **Step 2: Edit — fix arrow-citation outlier inside 완료 정의 checklist (line 106)**

This bullet sits inside a "충족한다" checklist (완료 정의 section), where every sibling bullet is itself a condition phrased with a `-한다`/`-않는다` verb. Switching this bullet's verb to `따른다` (the prefix-delegation pattern used at lines 74-75, outside the checklist) would break that sibling consistency. Instead, convert to the parenthetical local-rule-plus-citation pattern — the same shape already used consistently elsewhere in the guide for "this doc states its own condition, here's where more detail lives."

old_string:
```
- 접근성 완료 조건을 충족한다 → [coding-conventions](coding-conventions.md)의 접근성.
```

new_string:
```
- 접근성 완료 조건을 충족한다(→ [coding-conventions](coding-conventions.md)의 접근성).
```

- [ ] **Step 3: Verify**

Run: `grep -n "강제 도구는 → \[code-quality\]" nextjs-agent-guide/docs/design-system.md`
Expected: one match, on its own line (not sharing a line with "스타일 표기와 접근성은").

Run: `grep -n "충족한다 →" nextjs-agent-guide/docs/design-system.md`
Expected: no output (bare mid-sentence arrow gone).

Run: `grep -n "충족한다(→ \[coding-conventions\]" nextjs-agent-guide/docs/design-system.md`
Expected: one match.

- [ ] **Step 4: Commit**

```bash
git add nextjs-agent-guide/docs/design-system.md
git commit -m "docs: split dual-reference bullet and fix arrow-citation outlier in design-system.md"
```

---

## Final Check

- [ ] Run `grep -rn "\`web\`\|\`admin\`" nextjs-agent-guide/docs/` — expect no output.
- [ ] Run `git log --oneline -6` — expect 6 new `docs:` commits, one per task above.
- [ ] Skim each of the 6 touched files once to confirm no stray formatting broke (bullets still render as a flat Markdown list, no accidental double-indent).

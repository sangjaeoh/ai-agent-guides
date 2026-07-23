# 가이드 감수 결과 전체 반영 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** nextjs-agent-guide·polyglot-monorepo-agent-guide 문서 9개의 감수 결과 37건(기술 오류·모순·용어 불일치·중복·문법)을 전부 반영한다.

**Architecture:** 문서 편집만 수행한다. 코드·설정 변경 없음. 파일 단위로 태스크를 나누고, 태스크마다 정확한 old/new 문자열 편집 → grep 검증 → 커밋 순서를 따른다. 마지막 태스크가 레포 전체 grep 스윕으로 잔존 불일치를 잡는다.

**Tech Stack:** 마크다운, Edit 도구, grep.

## Global Constraints

- 편집 규약(각 가이드 AGENTS.md): 규칙은 마크다운 불릿과 표로 쓴다. HTML·이모지·강조 기호·편집자적 라벨 금지.
- 한 불릿은 한 문장으로 쓴다.
- 문서 섹션은 제목·언제·규칙 셋으로 고정한다.
- 현재 상태로 쓴다. 편집 이력 서술 금지.
- 한 규칙은 한 문서가 소유하고 나머지는 참조만 한다.
- TDD 부적용(문서 작업). 검증은 각 태스크의 grep 단계로 대체한다.
- 커밋 메시지는 레포 관례를 따라 `docs:` 접두 한국어로 쓴다.
- 모든 경로는 레포 루트 `/Users/sangjaeoh/Desktop/gitspace/ai-agent-guides/` 기준.
- Edit의 old_string은 아래 명시된 문자열과 정확히 일치해야 한다. 일치 실패 시 해당 파일을 Read 후 재시도한다.

## 용어 표준(전 태스크 공통)

| 채택 | 폐기 |
|---|---|
| 클라이언트 | 클라 |
| 타입드 클라이언트 | 타입 클라이언트 |
| 포이즌 임포트 | poison import |
| 강제 장치 | 강제 기계 |
| 봉투 | envelope(단독 사용) |
| 역할 | 롤 |
| 시간 값(time) / 비주얼 회귀(visual) | 시각(중의 사용) |
| 최후 수단 | 최후수단 |

---

### Task 1: nextjs architecture.md 수정

**Files:**
- Modify: `nextjs-agent-guide/docs/architecture.md`

**Interfaces:**
- Consumes: 없음
- Produces: 변경된 섹션 제목 `### 세그먼트 구조와 서버/클라이언트 경계` — Task 3이 이 제목으로 참조 문구를 갱신한다.

- [ ] **Step 1: 편집 적용 (11건)**

편집 1 — 언제 섹션 축약어:
```
old: - 서버/클라 구조 경계와 시크릿 위치를 정할 때.
new: - 서버/클라이언트 구조 경계와 시크릿 위치를 정할 때.
```

편집 2 — 워크스페이스 표: "하위 패키지" 제거, 의존 명시(감수 A4), 클라 훅→클라이언트 훅:
```
old:
| `packages/entities` | 공유 read-model·타입·Zod 스키마 | 하위 패키지 |
| `packages/ui` | 디자인시스템 | 하위 패키지 |
| `packages/api-client` | 타입드 클라이언트(server-only) | 하위 패키지 |
| `packages/auth` | 인증 코어·클라 훅 분리 | 하위 패키지 |

new:
| `packages/entities` | 공유 read-model·타입·Zod 스키마 | `config` |
| `packages/ui` | 디자인시스템 | `config` |
| `packages/api-client` | 타입드 클라이언트(server-only) | `entities`, `config` |
| `packages/auth` | 인증 코어·클라이언트 훅 분리 | `entities`, `config` |
```

편집 3 — 표가 소유하게 된 중복 규칙 삭제(감수 A4):
```
old:
- `packages → apps`, `app ↔ app`, 패키지 순환은 금지한다.
- `packages/entities`는 `ui`, `api-client`, `auth`에 의존하지 않는다.
new:
- `packages → apps`, `app ↔ app`, 패키지 순환은 금지한다.
```

편집 4 — FSD-lite 표 app 의존 대상(감수 A5):
```
old: | `app/` | 라우팅·레이아웃·페이지 조립(컴포지션 루트) | `features` |
new: | `app/` | 라우팅·레이아웃·페이지 조립(컴포지션 루트) | `features`, `entities`, `shared` |
```

편집 5 — 중복 불릿 삭제(감수 D25):
```
old:
- `widgets`, FSD `pages` 레이어는 사용하지 않는다.
- FSD는 lite 형태만 사용한다.
new:
- `widgets`, FSD `pages` 레이어는 사용하지 않는다.
```

편집 6 — 섹션 제목·본문 축약어(감수 C16):
```
old: ### 세그먼트 구조와 서버/클라 경계
new: ### 세그먼트 구조와 서버/클라이언트 경계
```
```
old: - 클라 코드는 `ui`에 두고 `index.client.ts`로 공개한다.
new: - 클라이언트 코드는 `ui`에 두고 `index.client.ts`로 공개한다.
```
```
old: - 클라 import 시 서버 모듈이 클라 그래프에 포함되지 않도록 분리한다.
new: - 클라이언트 import 시 서버 모듈이 클라이언트 그래프에 포함되지 않도록 분리한다.
```
```
old: - `server-only`, `client-only` 포이즌 임포트로 서버·클라 누수를 빌드 에러로 차단한다.
new: - `server-only`, `client-only` 포이즌 임포트로 서버·클라이언트 누수를 빌드 에러로 차단한다.
```

편집 7 — 서버 경계 섹션 축약어(감수 C16):
```
old: - API 키, 세션 토큰, 내부 URL, PII를 클라 번들·`NEXT_PUBLIC_*`·로그에 노출하지 않는다.
new: - API 키, 세션 토큰, 내부 URL, PII를 클라이언트 번들·`NEXT_PUBLIC_*`·로그에 노출하지 않는다.
```
```
old: - 클라 공개 값만 `NEXT_PUBLIC_` 접두사를 사용한다.
new: - 클라이언트 공개 값만 `NEXT_PUBLIC_` 접두사를 사용한다.
```

편집 8 — Nx 불릿 갱신(감수 B11, 폴리그랏 가이드 결론과 일치):
```
old:
- Turborepo + pnpm workspaces를 기본으로 한다.
- Nx는 코드젠·polyglot 요구가 생길 때 검토한다.
new:
- Turborepo + pnpm workspaces를 기본으로 한다.
- Nx는 채택하지 않는다.
- polyglot 통합은 Turborepo + 언어별 빌드 위임으로 처리한다.
```

편집 9 — 강제 표 축약어(감수 C16):
```
old: | `server-only` / `client-only` | 서버·클라 경계 |
new: | `server-only` / `client-only` | 서버·클라이언트 경계 |
```

편집 10 — 소제목·본문 용어 통일(감수 C13, C16):
```
old:
#### poison import

- 클라에서 `api-client` import를 금지한다.
- 클라에서 `auth`의 server-only 코어 import를 금지한다.
new:
#### 포이즌 임포트

- 클라이언트에서 `api-client` import를 금지한다.
- 클라이언트에서 `auth`의 server-only 코어 import를 금지한다.
```

편집 11 — steiger 금지 근거 추가(감수 C21):
```
old:
- `steiger`는 사용하지 않는다.
new:
- `steiger`는 사용하지 않는다.
  - `widgets`·`pages`를 제외한 FSD-lite 구조와 규칙 세트가 맞지 않고, 레이어 경계 검사는 eslint-plugin-boundaries가 이미 소유한다.
```

- [ ] **Step 2: grep 검증**

Run: `cd /Users/sangjaeoh/Desktop/gitspace/ai-agent-guides && grep -n "클라 \|클라·\|클라에서\|클라 훅\|하위 패키지\|poison import\|FSD는 lite" nextjs-agent-guide/docs/architecture.md`
Expected: 매치 0건 (exit 1)

Run: `grep -c "서버/클라이언트 경계" nextjs-agent-guide/docs/architecture.md`
Expected: 1 이상

- [ ] **Step 3: 커밋**

```bash
cd /Users/sangjaeoh/Desktop/gitspace/ai-agent-guides
git add nextjs-agent-guide/docs/architecture.md
git commit -m "docs: architecture 의존 표 명시·용어 통일·Nx 결론 갱신

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 2: nextjs code-quality.md 수정

**Files:**
- Modify: `nextjs-agent-guide/docs/code-quality.md`

**Interfaces:**
- Consumes: 없음
- Produces: 없음

- [ ] **Step 1: 편집 적용 (10건)**

편집 1 — 언제 섹션 중복 README 참조 삭제(감수 D23). 뒤쪽 버전 섹션의 동일 문장은 유지:
```
old:
- 게이트 도구 버전을 정하거나 올릴 때.
- 런타임 프레임워크·라이브러리 버전은 → [README](../README.md)의 기술 스택.
- 새 앱·패키지에 품질 게이트를 연결할 때.
new:
- 게이트 도구 버전을 정하거나 올릴 때.
- 새 앱·패키지에 품질 게이트를 연결할 때.
```

편집 2 — 용어 통일(감수 C14):
```
old: - 강제 기계는 중앙 설정이 소유한다.
new: - 강제 장치는 중앙 설정이 소유한다.
```

편집 3 — 축약어(감수 C16):
```
old: - 경계 강제(워크스페이스 방향·FSD 방향·서버/클라 누수)의 불변식 목록은 → [architecture](architecture.md)의 빌드가 강제하는 불변식이 소유한다.
new: - 경계 강제(워크스페이스 방향·FSD 방향·서버/클라이언트 누수)의 불변식 목록은 → [architecture](architecture.md)의 빌드가 강제하는 불변식이 소유한다.
```

편집 4 — react-hooks 버전 갱신(감수 F37):
```
old: - 적용 플러그인은 `@next/eslint-plugin-next`·`eslint-plugin-react-hooks`(v6)·`eslint-plugin-jsx-a11y`·`eslint-plugin-boundaries`다.
new: - 적용 플러그인은 `@next/eslint-plugin-next`·`eslint-plugin-react-hooks`(v7)·`eslint-plugin-jsx-a11y`·`eslint-plugin-boundaries`다.
```
```
old:   - `eslint-plugin-react-hooks` v6의 React Compiler 계열 린트(`recommended-latest`) 대응이 없다.
new:   - `eslint-plugin-react-hooks` v7의 React Compiler 계열 린트(`recommended-latest`) 대응이 없다.
```

편집 5 — 타입 게이트 명령 명확화(감수 A2):
```
old: - `tsc --noEmit`(project references)를 타입 게이트로 사용한다.
new: - 타입 게이트는 turbo 태스크로 실행하는 패키지별 `tsc --noEmit`이다.
```

편집 6 — 조사 교정(감수 C20):
```
old: - 타입 작성 규칙은 → [coding-conventions](coding-conventions.md)가 소유한다.
new: - 타입 작성 규칙은 → [coding-conventions](coding-conventions.md)이 소유한다.
```

편집 7 — dependency-cruiser 검사 대상에서 미정의 "드롭 레이어" 제거(감수 B8, 소유는 eslint-plugin-boundaries):
```
old: - 검사 대상은 워크스페이스 방향·순환·고아·드롭 레이어다.
new: - 검사 대상은 워크스페이스 방향·순환·고아 모듈이다.
```

편집 8 — Storybook 검사 체인 현행화(감수 F36):
```
old:   - 접근성 애드온·test-runner 생태계가 부족해 자동 검사 규칙을 이행할 수 없다.
new:   - 접근성 애드온·테스트 통합 생태계가 부족해 자동 검사 규칙을 이행할 수 없다.
```
```
old: - 게이트에 전 스토리 렌더 스모크(test-runner)와 접근성 검사(addon-a11y·axe)를 포함한다.
new: - 게이트에 전 스토리 렌더 스모크(`@storybook/addon-vitest`)와 접근성 검사(addon-a11y·axe)를 포함한다.
```

편집 9 — 시각→비주얼(감수 C18):
```
old:
- 시각 회귀 검사는 도입 시점에 이 문서에서 고정한다.
- 시각 회귀 검사는 선제 도입하지 않는다.
new:
- 비주얼 회귀 검사는 도입 시점에 이 문서에서 고정한다.
- 비주얼 회귀 검사는 선제 도입하지 않는다.
```

편집 10 — baseline 표 갱신(감수 F36, F37):
```
old: | 린트 | ESLint (`@next/eslint-plugin-next`·`react-hooks` v6·`jsx-a11y`·`boundaries`) |
new: | 린트 | ESLint (`@next/eslint-plugin-next`·`react-hooks` v7·`jsx-a11y`·`boundaries`) |
```
```
old: | 워크벤치 | Storybook(CSF3 · test-runner · addon-a11y) |
new: | 워크벤치 | Storybook(CSF3 · addon-vitest · addon-a11y) |
```

- [ ] **Step 2: grep 검증**

Run: `grep -n "강제 기계\|드롭 레이어\|test-runner\|시각 회귀\|v6\|서버/클라 " nextjs-agent-guide/docs/code-quality.md`
Expected: 매치 0건

Run: `grep -c "README" nextjs-agent-guide/docs/code-quality.md`
Expected: 1

- [ ] **Step 3: 커밋**

```bash
git add nextjs-agent-guide/docs/code-quality.md
git commit -m "docs: code-quality 타입 게이트 명령 명확화·도구 버전 현행화

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 3: nextjs coding-conventions.md 수정

**Files:**
- Modify: `nextjs-agent-guide/docs/coding-conventions.md`

**Interfaces:**
- Consumes: Task 1의 변경된 섹션 제목 `세그먼트 구조와 서버/클라이언트 경계`
- Produces: 없음

- [ ] **Step 1: 편집 적용 (6건)**

편집 1 — 라우트 export 규칙 교정(감수 A1). named export도 라우터가 인식하므로 규칙 범위를 컴포넌트 파일의 기본 컴포넌트로 한정하고 `route.ts`·세그먼트 계약 규칙을 분리:
```
old:
- 라우트 파일(`page`, `layout`, `loading`, `error` 등)은 `export default`를 사용한다.
  - 네임드 export는 라우터가 인식하지 못한다.
- feature 컴포넌트는 네임드 export를 사용한다.
new:
- 컴포넌트 라우트 파일(`page`, `layout`, `loading`, `error`, `not-found`, `global-error`, `default`)의 라우트 컴포넌트는 `export default`를 사용한다.
  - 라우터는 기본 export만 라우트 컴포넌트로 렌더한다.
- `route.ts`는 HTTP 메서드명 네임드 export(`GET`·`POST` 등)를 사용한다.
- `metadata`·`generateMetadata`·`generateStaticParams`·`viewport` 등 세그먼트 계약은 네임드 export를 사용한다.
- feature 컴포넌트는 네임드 export를 사용한다.
```

편집 2 — Task 1에서 바뀐 참조 제목 갱신(감수 C16, 2곳):
```
old:   - 기준은 → [architecture](architecture.md)의 세그먼트 구조와 서버/클라 경계.
new:   - 기준은 → [architecture](architecture.md)의 세그먼트 구조와 서버/클라이언트 경계.
```
```
old: - 컴포넌트 배치는 → [architecture](architecture.md)의 세그먼트 구조와 서버/클라 경계.
new: - 컴포넌트 배치는 → [architecture](architecture.md)의 세그먼트 구조와 서버/클라이언트 경계.
```

편집 3 — 헤딩 구조 정리(감수 D27). 우산 제목 아래 "기본 규칙"만 있고 TSDoc·라인 주석이 형제 레벨이므로 우산을 공통 규칙 섹션으로 개명:
```
old:
### 코드 주석·TSDoc

#### 기본 규칙

- 주석은 한국어로 작성한다.
new:
### 주석 공통 규칙

- 주석은 한국어로 작성한다.
```

편집 4 — 목적어 없는 문장 교정(감수 E34):
```
old: - `shared`·`packages/ui`·`packages/config` 등 도메인 무지 계층의 기술 어휘는 제외한다.
new: - `shared`·`packages/ui`·`packages/config` 등 도메인 무지 계층의 기술 어휘는 표준어 요구 대상에서 제외한다.
```

편집 5 — 희귀 용어 병기(감수 E35):
```
old: - 타입 요약은 계사문으로 작성한다.
new: - 타입 요약은 계사문('~이다' 형 문장)으로 작성한다.
```

편집 6 — `{@link}` 중복 규칙 삭제(감수 D24). 표기 섹션이 소유:
```
old:
- 명령형과 장황한 주어형을 사용하지 않는다.
- 타입 연결은 `{@link}`를 사용한다.
- `@returns`와 자명한 `@param`은 작성하지 않는다.
new:
- 명령형과 장황한 주어형을 사용하지 않는다.
- `@returns`와 자명한 `@param`은 작성하지 않는다.
```

- [ ] **Step 2: grep 검증**

Run: `grep -n "네임드 export는 라우터가\|서버/클라 경계\|코드 주석·TSDoc\|기본 규칙" nextjs-agent-guide/docs/coding-conventions.md`
Expected: 매치 0건

Run: `grep -c "{@link}" nextjs-agent-guide/docs/coding-conventions.md`
Expected: 1 (표기 섹션의 `타입·심볼은 {@link}로 표기한다`만 남는다)

- [ ] **Step 3: 커밋**

```bash
git add nextjs-agent-guide/docs/coding-conventions.md
git commit -m "docs: coding-conventions 라우트 export 규칙 교정·주석 섹션 구조 정리

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 4: nextjs data.md 수정

**Files:**
- Modify: `nextjs-agent-guide/docs/data.md`

**Interfaces:**
- Consumes: 없음
- Produces: 없음

- [ ] **Step 1: 편집 적용 (7건)**

편집 1 — read-model 반복 압축(감수 D22, 5문장→3문장):
```
old:
- 프론트 도메인 모델은 백엔드 진실의 read-model이다.
- read-model을 진실 원천으로 사용하지 않는다.
- 백엔드가 거부할 불변식은 read-model에서 강제하지 않는다.
- 진실 소유는 백엔드다.
- read-model은 백엔드 데이터의 투영이며 Zod 스키마와 순수 뷰 파생(포맷·표시 계산)만 가진다.
new:
- 프론트 도메인 모델은 백엔드가 소유한 진실의 read-model이며 진실 원천이 아니다.
- read-model은 백엔드 데이터의 투영이며 Zod 스키마와 순수 뷰 파생(포맷·표시 계산)만 가진다.
- 백엔드가 거부할 불변식은 read-model에서 강제하지 않는다.
```

편집 2 — Server Action 조회 금지 근거 재서술(감수 A6):
```
old:
  - Server Action을 조회 페치 용도로 사용하지 않는다.
    - POST·내비게이션 단위 실행으로 직렬화 지연을 만든다.
new:
  - Server Action을 조회 페치 용도로 사용하지 않는다.
    - Server Action은 클라이언트당 순차 실행되어 병렬 조회가 불가능하고 응답이 캐시되지 않는다.
```

편집 3 — BFF 비문 교정(감수 E31):
```
old: - BFF는 데이터 shape·join·filter·cache를 수행할 수 있다.
new: - BFF는 데이터 형상 변환·조인·필터링·캐싱을 수행할 수 있다.
```

편집 4 — 용어 통일(감수 C12):
```
old: - 코드젠 + Zod 기반 타입 클라이언트로 배포 결합 없이 계약을 유지한다.
new: - 코드젠 + Zod 기반 타입드 클라이언트로 배포 결합 없이 계약을 유지한다.
```

편집 5 — 드롭 결과 규범화(감수 E33):
```
old: - 드롭 시 pagination meta와 항목 수가 불일치한다.
new: - 드롭 시 pagination meta와 항목 수의 불일치를 허용한다.
```

편집 6 — 소유 중복 문장 교정(감수 D28):
```
old: - 캐시 태그 taxonomy는 대상 read-model을 소유한 entities 계층이 상수로 소유한다.
new: - 캐시 태그 taxonomy는 대상 read-model의 entities 계층이 상수로 소유한다.
```

편집 7 — 용어 통일(감수 C15, 봉투는 목록 egress 섹션에서 기정의):
```
old: - 스칼라·객체 응답은 envelope 없이 반환한다.
new: - 스칼라·객체 응답은 봉투 없이 반환한다.
```

- [ ] **Step 2: grep 검증**

Run: `grep -n "진실 소유는 백엔드다\|직렬화 지연\|shape·join\|타입 클라이언트\|항목 수가 불일치\|envelope" nextjs-agent-guide/docs/data.md`
Expected: 매치 0건

Run: `grep -c "read-model" nextjs-agent-guide/docs/data.md`
Expected: 1 이상 (섹션 유지 확인)

- [ ] **Step 3: 커밋**

```bash
git add nextjs-agent-guide/docs/data.md
git commit -m "docs: data read-model 중복 압축·Server Action 근거 교정·용어 통일

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 5: nextjs rendering.md 수정

**Files:**
- Modify: `nextjs-agent-guide/docs/rendering.md`

**Interfaces:**
- Consumes: 없음
- Produces: 없음

- [ ] **Step 1: 편집 적용 (3건)**

편집 1 — props 검증 의미 명확화(감수 B10, data.md의 egress 1곳 검증과 정합):
```
old: - 클라이언트 props는 직렬화 가능·최소·Zod 검증된 안정 계약만 전달한다.
new: - 클라이언트 props는 egress 검증을 통과한 데이터에서 파생한 직렬화 가능·최소 계약만 전달한다.
```

편집 2 — 비결정 값과 브라우저 전용 API 분리(감수 A3), 시각→시간(감수 C18):
```
old:
- 서버 렌더 경로에서 비결정 값을 사용하지 않는다.
  - `Date.now()`
  - `Math.random()`
  - locale 의존 값
  - `window`
  - `localStorage`
- 시각·랜덤·로케일 값은 클라이언트 effect에서 생성하거나 서버에서 확정 후 props로 전달한다.
new:
- 서버 렌더 경로에서 비결정 값을 사용하지 않는다.
  - `Date.now()`
  - `Math.random()`
  - locale 의존 값
- 서버 렌더 경로에서 브라우저 전용 API(`window`·`localStorage`)를 참조하지 않는다.
  - 서버 런타임에는 해당 API가 존재하지 않는다.
- 시간·랜덤·로케일 값은 클라이언트 effect에서 생성하거나 서버에서 확정 후 props로 전달한다.
```

편집 3 — 동어반복 제거, 용도 서술(감수 D26):
```
old: - 인터셉트 라우트(`(.)`·`(..)`)는 라우트 가로채기에 사용한다.
new: - 인터셉트 라우트(`(.)`·`(..)`)는 목록 위 모달 상세처럼 컨텍스트를 유지하는 화면 전환에 사용한다.
```

- [ ] **Step 2: grep 검증**

Run: `grep -n "Zod 검증된 안정 계약\|라우트 가로채기에 사용\|시각·랜덤" nextjs-agent-guide/docs/rendering.md`
Expected: 매치 0건

Run: `grep -c "브라우저 전용 API" nextjs-agent-guide/docs/rendering.md`
Expected: 1

- [ ] **Step 3: 커밋**

```bash
git add nextjs-agent-guide/docs/rendering.md
git commit -m "docs: rendering 비결정 값 분류 교정·props 검증 의미 명확화

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 6: nextjs design-system.md 수정

**Files:**
- Modify: `nextjs-agent-guide/docs/design-system.md`

**Interfaces:**
- Consumes: 없음
- Produces: 없음

- [ ] **Step 1: 편집 적용 (7건)**

편집 1 — 조사 교정(감수 C20, 2곳):
```
old: - 표기·명명·접근성 기준은 → [coding-conventions](coding-conventions.md)를 따른다.
new: - 표기·명명·접근성 기준은 → [coding-conventions](coding-conventions.md)을 따른다.
```
```
old: - 서버/클라이언트 렌더 경계는 → [rendering](rendering.md)를 따른다.
new: - 서버/클라이언트 렌더 경계는 → [rendering](rendering.md)을 따른다.
```

편집 2 — 문법 교정(감수 E29):
```
old: - 하위 계층은 상위 계층과 도메인을 알지 않는다.
new: - 하위 계층은 상위 계층과 도메인을 알지 못한다.
```

편집 3 — 롤→역할(감수 C17, 표):
```
old: | 타이포 | 제목·본문·캡션 등 롤 단위 크기·행간·굵기 |
new: | 타이포 | 제목·본문·캡션 등 역할 단위 크기·행간·굵기 |
```

편집 4 — 롤→역할(감수 C17, 본문 2곳):
```
old:
- 타이포는 롤 토큰 단위로 사용한다.
- 크기·행간·굵기를 개별 조합해 롤을 재구성하지 않는다.
new:
- 타이포는 역할 토큰 단위로 사용한다.
- 크기·행간·굵기를 개별 조합해 역할을 재구성하지 않는다.
```

편집 5 — variant 승격·분기 모순 해소(감수 B9):
```
old:
- 반복되는 내부 스타일 변경은 variant로 승격한다.
- 반복되는 내부 스타일 덮어쓰기는 별도 컴포넌트 분기로 간주한다.
new:
- 반복되는 내부 스타일 변경은 variant로 승격한다.
- variant로 표현할 수 없는 반복 변경은 별도 컴포넌트로 분리한다.
```

편집 6 — 시각→비주얼(감수 C18):
```
old: - 접근성·시각 회귀 자동 검사는 워크벤치 스토리를 대상으로 연결한다.
new: - 접근성·비주얼 회귀 자동 검사는 워크벤치 스토리를 대상으로 연결한다.
```

- [ ] **Step 2: grep 검증**

Run: `grep -n "md)를 따른다\|알지 않는다\|롤 단위\|롤 토큰\|롤을\|덮어쓰기는 별도\|시각 회귀" nextjs-agent-guide/docs/design-system.md`
Expected: 매치 0건

- [ ] **Step 3: 커밋**

```bash
git add nextjs-agent-guide/docs/design-system.md
git commit -m "docs: design-system 용어 통일·variant 분기 규칙 모순 해소

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 7: nextjs testing.md 수정

**Files:**
- Modify: `nextjs-agent-guide/docs/testing.md`

**Interfaces:**
- Consumes: 없음
- Produces: 없음

- [ ] **Step 1: 편집 적용 (3건)**

편집 1 — 시각→시간 값(감수 C18):
```
old: - 시간 경계는 주입된 시각·fake timers로 검증한다(→ 결정성).
new: - 시간 경계는 주입된 시간 값·fake timers로 검증한다(→ 결정성).
```

편집 2 — 띄어쓰기 통일(감수 C19):
```
old: - `data-testid`는 최후수단이다.
new: - `data-testid`는 최후 수단이다.
```

편집 3 — 시각→시간(감수 C18):
```
old: - 시각·랜덤·로케일 비결정 값의 처리 규칙은 → [rendering](rendering.md)의 Hydration 결정성.
new: - 시간·랜덤·로케일 비결정 값의 처리 규칙은 → [rendering](rendering.md)의 Hydration 결정성.
```

- [ ] **Step 2: grep 검증**

Run: `grep -n "주입된 시각\|최후수단\|시각·랜덤" nextjs-agent-guide/docs/testing.md`
Expected: 매치 0건

- [ ] **Step 3: 커밋**

```bash
git add nextjs-agent-guide/docs/testing.md
git commit -m "docs: testing 시간 값 용어·띄어쓰기 통일

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 8: polyglot 가이드 2개 문서 수정

**Files:**
- Modify: `polyglot-monorepo-agent-guide/docs/architecture.md`
- Modify: `polyglot-monorepo-agent-guide/docs/sharing.md`

**Interfaces:**
- Consumes: 없음
- Produces: 없음

- [ ] **Step 1: architecture.md 편집 (1건)**

편집 1 — 문법 교정(감수 E30):
```
old: - 새 클래스·타입·공유물을 세 공유 레벨 중 배치할 때.
new: - 새 클래스·타입·공유물을 세 공유 레벨 중 하나에 배치할 때.
```

- [ ] **Step 2: sharing.md 편집 (2건)**

편집 1 — drift 문장 오독 제거(감수 E32):
```
old:   - 계약 재생성 결과와 커밋된 생성물이 다르면 CI가 실패한다.
new:   - 계약에서 재생성한 생성물과 커밋된 생성물이 다르면 CI가 실패한다.
```

편집 2 — input 경로를 다계약 규칙과 정합(감수 A7):
```
old: | input | `apps/backend/docs/openapi/openapi.json` |
new: | input | `apps/backend/docs/openapi/{방출단위}.json` |
```
주의: 실제 파일에서 input 행이 백틱 없이 `| input | apps/backend/docs/openapi/openapi.json |` 형태일 수 있다. Read로 확인 후 실제 문자열에 맞춰 편집한다. 결과 행은 반드시 `{방출단위}.json`을 포함한다.

- [ ] **Step 3: grep 검증**

Run: `grep -n "레벨 중 배치" polyglot-monorepo-agent-guide/docs/architecture.md; grep -n "계약 재생성 결과\|openapi/openapi.json" polyglot-monorepo-agent-guide/docs/sharing.md`
Expected: 매치 0건

Run: `grep -c "{방출단위}.json" polyglot-monorepo-agent-guide/docs/sharing.md`
Expected: 1

- [ ] **Step 4: 커밋**

```bash
git add polyglot-monorepo-agent-guide/docs/architecture.md polyglot-monorepo-agent-guide/docs/sharing.md
git commit -m "docs: polyglot 가이드 drift 문장·다계약 input 경로·문법 교정

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 9: 레포 전체 잔존 스윕·최종 검증

**Files:**
- Modify: 스윕에서 발견된 파일만 (원칙적으로 없음)

**Interfaces:**
- Consumes: Task 1–8 완료 상태
- Produces: 없음

- [ ] **Step 1: 용어 잔존 스윕**

Run:
```bash
cd /Users/sangjaeoh/Desktop/gitspace/ai-agent-guides
grep -rn "클라 \|클라·\|클라에서\|하위 패키지\|드롭 레이어\|강제 기계\|타입 클라이언트\|poison import\|최후수단\|시각 회귀\|롤 토큰\|envelope 없이\|알지 않는다" nextjs-agent-guide/docs polyglot-monorepo-agent-guide/docs
```
Expected: 매치 0건. 매치가 나오면 이 계획의 용어 표준 표에 따라 수정한다.

- [ ] **Step 2: 조사 전수 스캔**

Run: `grep -rn ")가 \|)이 \|)를 \|)은 \|)을 " nextjs-agent-guide/docs polyglot-monorepo-agent-guide/docs`

각 매치를 링크 라벨의 한국어 독음 기준으로 판정한다: 받침 있음 → 이/을/은, 받침 없음 → 가/를/는. 예: conventions(받침 ㅅ)→이/을, architecture(아키텍처, 받침 없음)→가/를, rendering(렌더링, 받침 ㅇ)→이/을. 오류만 수정한다.

- [ ] **Step 3: 참조 무결성 확인**

Run: `grep -rn "서버/클라 경계\|세그먼트 구조와 서버/클라 " nextjs-agent-guide/docs`
Expected: 매치 0건 (Task 1 제목 변경이 모든 참조에 반영됨)

- [ ] **Step 4: 수정 발생 시에만 커밋**

```bash
git add -A nextjs-agent-guide/docs polyglot-monorepo-agent-guide/docs
git commit -m "docs: 감수 잔존 용어·조사 스윕 반영

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```
수정 없으면 커밋 생략.

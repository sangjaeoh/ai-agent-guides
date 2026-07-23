# AI 에이전트 가이드 문서 검수 — 톤/형식·범용성

## 배경

`nextjs-agent-guide`, `polyglot-monorepo-agent-guide` 두 문서(각 AGENTS.md + docs/*.md) 대상. 용어 통일·기술 오류·중복은 이전 감수에서 정리됨. 이번 검수는 두 관점만 본다: (1) 문장 톤·형식 간결성, (2) 범용성(타 레포 템플릿 적용 가능 여부). 기준은 각 AGENTS.md 하단 편집 규약(불릿·표만 사용, 한 불릿 한 문장, 현재 상태 서술, 한 규칙 한 문서 소유).

## 확정된 수정 항목

### 1. web/admin 앱 구성 표 일반화 (범용성, HIGH)

`nextjs-agent-guide/docs/architecture.md:55-58`

현재:
```
### 앱 구성

| 앱 | 책임 | 데이터 접근 |
|---|---|---|
| `web` | 공개 사용자 화면·API | RSC·Server Action |
| `admin` | 어드민·백오피스 | 동일 |

- 앱은 배포 단위다.
- 새 앱 추가 시 워크스페이스 조립만 변경한다.
```

문제: web/admin 언급이 전체 문서군에서 이 표뿐. 바로 위 워크스페이스 계층 표(19-26행)와 "앱은 배포 단위다"(60행)는 앱 이름·개수를 특정하지 않는 일반형인데 이 표만 구체 2-앱 구조를 규칙처럼 고정. README.md가 이 가이드를 그대로 서비스 레포에 복사해 쓰는 템플릿으로 명시(README.md:13, :47) — web/admin 아닌 앱 구성 레포엔 안 맞음.

해결 방향: 앱 이름·개수를 특정하지 않는 서술로 교체. "### 앱 구성" 표를 제거하고, 앱은 책임 단위로 분리되며 배포 단위와 일치한다는 일반 규칙만 남긴다. 표의 "책임"·"데이터 접근" 컬럼이 담던 정보(앱마다 책임이 다를 수 있다, 데이터 접근 방식은 공통이다)는 프로즈 규칙으로 흡수.

### 2. testing.md 참조(→) 표기 통일 (톤/형식, HIGH)

전치 표기 `"...은 → [doc](doc.md)를 따른다."`로 통일. 대상:

- `nextjs-agent-guide/docs/testing.md:70,119,139,144,156,175,197,199,209,244` — 괄호 후치 → 전치로 교체.
- `nextjs-agent-guide/docs/rendering.md:77` — 동일 교체.

각 줄의 정확한 문구는 계획 단계에서 old/new 쌍으로 확정.

### 3. 근거 불릿 들여쓰기 통일 (톤/형식, MEDIUM)

근거(rationale)는 상위 규칙 불릿 아래 2-space 들여쓰기 하위 불릿으로 통일. 대상(현재 평평 → 들여쓰기로 교체):

- `nextjs-agent-guide/docs/code-quality.md:65` (Storybook 앱 배치 근거)
- `nextjs-agent-guide/docs/code-quality.md:81` (새 의존성 공급망 리스크 근거)
- `nextjs-agent-guide/docs/rendering.md:25` (hydration mismatch 근거)
- `nextjs-agent-guide/docs/testing.md:21` (시나리오 문장 합의 근거) — 20-21행 통합 검토
- `nextjs-agent-guide/docs/data.md:51` (BFF 비즈니스 규칙 근거)

### 4. design-system.md:19 불릿 분리 (톤/형식, MEDIUM)

`nextjs-agent-guide/docs/design-system.md:19`

현재: `- 스타일 표기와 접근성은 → [coding-conventions](coding-conventions.md), 강제 도구는 → [code-quality](code-quality.md)가 소유한다.`

두 불릿으로 분리, 각각 독립 주어·술어 확보.

### 5. testing.md:144 불릿 분리 (톤/형식, LOW)

`nextjs-agent-guide/docs/testing.md:144`

현재: `- 중복 제출 계약은 변경 안전 축의 멱등키가 소유하고, 중복 처리 판정은 백엔드 소유다(→ [data](data.md)).`

두 불릿으로 분리.

### 6. design-system.md:106 참조 표기 정정 (톤/형식, LOW)

`nextjs-agent-guide/docs/design-system.md:106`

현재: `- 접근성 완료 조건을 충족한다 → [coding-conventions](coding-conventions.md)의 접근성.`

같은 문서 74-75행 패턴("배치 규칙은 → ...")에 맞춰 조사 추가.

## 검증

- 각 수정 후 `grep -n`으로 old 문자열 소거·new 문자열 반영 확인.
- 항목 2 완료 후 `grep -rn "다(→ \[" nextjs-agent-guide/docs/` 로 괄호 후치 표기 잔존 0건 확인(항목 5의 신설 참조 제외).
- 항목 1 완료 후 `grep -n "web\|admin" nextjs-agent-guide/docs/architecture.md` 로 잔존 여부 확인.
- 편집 규약 위반(불릿·표 외 형식, 한 불릿 다중 문장) 재발 없음을 눈으로 재확인.

## 범위 밖

- 이전 감수에서 처리된 용어 통일·기술 오류·중복은 재검토하지 않는다.
- 위 6건 외 발견된 애매한 사례(예: architecture.md 의존 화살표, data.md 파이프라인 화살표 등)는 참조 표기와 의미가 달라 통일 대상에서 제외.

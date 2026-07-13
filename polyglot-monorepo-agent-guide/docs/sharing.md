# Sharing

## 언제

- 백엔드와 프론트가 주고받는 계약을 만들거나 바꿀 때.
- 코드젠·drift·에러 모델·타입 무결성을 판단할 때.
- 새 공유 계약·생성 타입을 추가할 때.

## 규칙

### 계약 seam

- 계약의 유일 언어중립 원천은 백엔드가 코드퍼스트로 방출하는 `openapi.json`이다(springdoc·키정렬·스냅샷 테스트).
- 방향은 단방향이다: 백엔드가 originate 하고 프론트가 하류 소비한다. 루트는 원천이 하나이고 방향이 단방향이라는 것만 강제한다.
  - tRPC를 기각했다: 클라이언트 타입이 TS 서버 라우터 타입에서 오므로 Java 서버가 원천을 방출할 수 없다.
  - ts-rest를 기각했다: 계약·어댑터가 전부 TS라 Spring을 컴파일타임으로 검증하지 못하는 수기 미러에 그친다.
- 방출 기계는 백엔드가, 소비 기계(api-client·egress 검증)는 프론트가 소유한다.
- 계약 표면에 토큰·내부 URL을 싣지 않는다.
- 계약 표면은 경계 양쪽이 일관되게 모델링한다. seam은 존재와 일관성만 판단하고 스킴·정책 상세는 서브가이드가 소유한다.
  - 표면: 버전·페이지네이션·인증·멱등성·파일 업로드·스트리밍/SSE.
  - 인증·검증 정책(토큰 전파, egress/ingress 비대칭)은 프론트 서브가이드가 소유한다.

### 계약 진화·무결성

- 파괴적 변경(삭제·의미변경)은 oasdiff가 검사로 표면화한다.
- drift 게이트가 계약과 생성물의 일치를 강제한다 — seam(계약 + 생성물)은 어느 단계에서도 반쪽 상태가 되지 않는다.
  - `openapi.json`에서 재생성한 결과가 커밋된 생성물과 다르면 CI가 실패한다.
  - 기계 보장은 필요조건이지 충분조건이 아니다 — 아래 타입 무결성이 보완한다.
- 단계화되는 것은 seam이 아니라 소비 반영이다. 추가(확장)의 소비는 원자적으로, 파괴적 제거의 소비는 expand-and-contract로 단계화한다. 다단계 절차는 백엔드 서브가이드가 소유한다.

#### 타입이 거짓말하는 지점 (사람이 잡는다)

- drift 게이트가 green이어도 OpenAPI가 표현 못 하는 불변식은 사람이 판단한다.
- UUIDv7: 생성은 plain string이다. 브랜드 타입·format 검증을 더한다.
- Instant·시각: ISO-8601 offset 문자열이다. `Date`가 아니다 — 파싱·비교를 문자열 계약으로 다룬다.
- int64·`long`: TS `number`는 2^53 초과에서 정밀도를 잃는다. string 또는 bigint로 다룬다.
- BigDecimal·금액: `number`로 받지 않는다. string으로 다뤄 반올림 오차를 피한다.
- enum 진화: 닫힌 union은 새 서버 값에 깨진다. 열린 union + exhaustive 분기로 미지 값을 견딘다.
- nullability: 백엔드 JSpecify가 `nullable`을 계약에 방출하는지 확인한다. TS `?`·`| null` 매핑을 신뢰 전에 검증한다.
- oneOf·판별 합집합: discriminator를 계약에 강제한다. 없으면 코드젠 품질이 무너진다.
- ProblemDetail `code`: 닫힌 union처럼 보이나 열려 있다 → 에러 모델.

### 코드젠 파이프라인

- `packages/shared-types`가 orval로 `openapi.json`에서 타입·Zod·API client를 생성한다(배치·언어중립성은 → [architecture](architecture.md)의 공유 패키지 배치).
  - 코드젠 툴은 저위험·가역이다. seam은 `openapi.json`이므로 kubb·hey-api 등으로 교체해도 소비처는 무영향이다.
- 타입은 Zod에서 `z.infer`로 파생한다(단일 소스). 수기 타입·수기 Zod를 이중으로 두지 않는다.
- 생성물은 커밋한다. 손 편집·재생성 불일치는 drift 게이트가 잡는다(→ 계약 진화·무결성).
- 입력·출력: input `apps/backend/docs/openapi/openapi.json` → output `src/generated`.
- 태스크 그래프: `backend#openapi → shared-types#codegen → (프론트) build`(turbo 셀렉터는 패키지명 기준).
  - 백엔드 `apps/backend/package.json`(name `backend`) 스크립트가 `./gradlew`로 위임한다(`openapi` = `generateOpenApiDocs`, `verify` = `./gradlew build`). gradle 태스크명은 백엔드가 소유한다.
  - 백엔드는 그래프상 불투명 단일 노드다. `outputs`를 최소(`openapi.json`·빌드마커)로 선언하고 내부 증분은 Gradle 자기 캐시가 담당한다.
- 소비 층: 프론트 소비는 `shared-types → api-client(server-only) → 프론트 앱` 단방향이다. `api-client`가 생성물을 벤더 중립 경계로 감싼다. 경계·소비 정책은 프론트 서브가이드가 소유한다.

### 에러 모델

- 에러 계약은 RFC 9457 ProblemDetail이다. 형식·확장 멤버(`code`·`errors[]`·`traceId`)는 백엔드가 소유한다.
- `packages/shared-types`가 계약에서 공용 에러 Zod 스키마를 생성한다.
- 기계 분기는 `code`로만 한다. `detail`·`title`로 분기하지 않는다 — 사람용 문자열이다.
- `code`는 런타임에서 open string으로 다룬다. 생성 union은 편의일 뿐 미지 코드에 깨지지 않는다.
- `traceId`는 관측용 확장 멤버다. 로깅·관측에 쓰되 클라 UI에 raw로 노출하지 않는다.
- 에러 소비 정책은 프론트가 소유한다.

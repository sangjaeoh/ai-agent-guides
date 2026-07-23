# query 모듈 조회 구현 기술 우선순위 규칙 — 설계

## 배경

`docs/architecture.md`의 "query 모듈 구조" 섹션(318줄)은 query 모듈의 조회 구현 기술을 "QueryDSL·`EntityManager`"로 병기만 하고, 둘 사이 선택 기준이나 우선순위를 두지 않았다.

도메인 모듈의 리포지토리 접근 범위 섹션(457~461줄)은 파생 쿼리·`@Query`(JPQL)·QueryDSL 프래그먼트·네이티브 SQL 각각의 사용 조건을 명시하는데, query 모듈에는 이에 대응하는 규칙이 없었다.

## 결정

query 모듈의 조회 구현은 QueryDSL을 기본으로 하고, 방언 종속이 불가피해 QueryDSL로 구현할 수 없는 조회에 한해 `EntityManager` 네이티브 쿼리를 최후수단으로 사용한다.

네이티브 조회 수단은 `EntityManager.createNativeQuery`로 한정한다. query 모듈은 Spring Data 리포지토리를 두지 않고 QueryDSL·`EntityManager`로 직접 구현하는 구조(319줄, 도메인 리포지토리 임포트 금지)이므로 이 수단이 자연스럽다.

조건 문구("방언 종속이 불가피할 때만 최후수단으로 사용한다")는 도메인 모듈 규칙(461줄)과 동일하게 맞춰 문서 전체 일관성을 유지한다.

## 변경 대상

`docs/architecture.md`, "query 모듈 구조" 섹션, 318줄 1개 불릿.

기존:

```
- 조회는 모듈 자체의 쿼리 구현(QueryDSL·`EntityManager`)으로 한다.
```

교체:

```
- 조회는 QueryDSL을 기본으로 구현한다.
- 방언 종속이 불가피해 QueryDSL로 구현할 수 없는 조회에 한해 `EntityManager` 네이티브 쿼리를 최후수단으로 사용한다.
```

## 범위 밖

- 도메인 모듈 리포지토리 접근 범위 규칙(434~462줄)은 이미 자체 기준이 있어 변경하지 않는다.
- query 모듈 구조 섹션의 다른 규칙(캐시 트랜잭션 분리, 저장소 분리 진화 등)은 변경하지 않는다.
- 다른 문서(`coding-conventions.md`, `entity-persistence.md` 등)는 이 규칙을 참조하지 않으므로 함께 고치지 않는다.

## 영향

이 문구를 참조하는 다른 문서·섹션 없음(단일 인용, `docs/architecture.md:318`). 파급 없이 국소 수정으로 끝난다.

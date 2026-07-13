# Polyglot Monorepo Guide

Spring Boot 백엔드 · Next.js 프론트 · 생성된 계약 타입을 담는 폴리글랏 모노레포의 교차언어 아키텍처·공유방법 가이드다. 언어별 규칙은 각 서브가이드(`apps/backend`·`apps/frontend`)가 소유한다.

이 저장소는 개발 규칙 문서만 담는다. 이 가이드의 범위는 교차언어 구조(모듈·패키지·공유 배치)와 공유방법(계약 seam·코드젠·에러 모델)이다.

## 구성

| 범주        | 채택                                       |
| ----------- | ------------------------------------------ |
| 백엔드      | Spring Boot 서브가이드(별도 repo)          |
| 프론트      | Next.js 서브가이드(별도 repo, 자체 Turborepo) |
| 계약 seam   | 백엔드 코드퍼스트 OpenAPI(`openapi.json`)  |
| 타입 코드젠 | orval(`packages/shared-types`)             |
| 오케스트레이터 | Turborepo + pnpm workspaces             |
| 툴체인 핀   | mise(JDK·Node·pnpm)                        |

위 표는 표시용이다. 정확한 버전·설정 정본은 각 매니페스트(`.mise.toml`·루트 `package.json`·각 서브가이드)가 소유한다.

## 문서

규칙은 `docs/`의 두 문서가 소유한다. 진입 앵커는 [`AGENTS.md`](AGENTS.md)다.

- [`docs/architecture.md`](docs/architecture.md) — 모듈 지도·공유 패키지 배치·워크스페이스 골격·소유·위임 경계·경계 강제.
- [`docs/sharing.md`](docs/sharing.md) — 계약 seam·계약 진화·무결성·코드젠 파이프라인·에러 모델.

## 적용하기

- `docs/`와 진입 앵커(`AGENTS.md`·`CLAUDE.md`)를 모노레포 루트에 둔다.
- 두 서브가이드를 `apps/backend`·`apps/frontend`에 조립한다(복사·git subtree·submodule 중 택일). 조립 방식은 각 서브가이드 슬롯의 README가 설명한다.

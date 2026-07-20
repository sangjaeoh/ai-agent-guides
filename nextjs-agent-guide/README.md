# Next.js Agent Guide

Next.js 프론트엔드 서버(BFF)를 일관된 규율로 개발하기 위한 아키텍처·컨벤션·렌더/데이터·품질 가이드다.

![Node](https://img.shields.io/badge/Node-24_LTS-339933)
![Next.js](https://img.shields.io/badge/Next.js-16-black)
![React](https://img.shields.io/badge/React-19.2-61DAFB)
![TypeScript](https://img.shields.io/badge/TypeScript-strict-3178C6)
![Architecture](https://img.shields.io/badge/Turborepo-FSD--lite-blue)

위 배지는 표시용이다. 버전 정본은 각 프로젝트의 `package.json`·`pnpm-lock.yaml`이 소유한다.

이 저장소는 실행 코드가 없다. 개발 규칙 문서만 담는다. Turborepo + pnpm 모노레포(모듈러 모놀리식·MFE-ready), App Router + RSC + Server Actions, FSD-lite 내부 구조, 서버/클라 경계와 BFF 오케스트레이션을 다룬다.

## 기술 스택

이 가이드가 기본으로 전제하는 런타임 스택이다. 게이트 툴체인 버전은 [`code-quality`](docs/code-quality.md)가, 정확한 버전 핀은 각 프로젝트 `package.json`이 소유한다.

| 범주        | 채택                                                                            |
| ----------- | ------------------------------------------------------------------------------- |
| 런타임      | Node.js 24 (LTS)                                                                |
| 프레임워크  | Next.js 16 (App Router)                                                         |
| UI          | React 19.2 (Server Components · Server Actions · React Compiler opt-in)         |
| 언어        | TypeScript (strict)                                                            |
| 모노레포    | Turborepo + pnpm workspaces                                                    |
| 내부 구조   | Feature-Sliced Design (FSD-lite)                                               |
| 데이터      | RSC 서버 페치 · TanStack Query(클라 서버상태) · Server Actions(변경)           |
| 클라 상태   | URL·searchParams 우선 · Zustand(전역, 최소)                                    |
| 검증        | Zod (경계)                                                                     |
| 스타일      | Tailwind CSS v4                                                                |
| 폼          | Conform + `useActionState`(Server Action) · react-hook-form(리치 클라)         |

## 문서

규칙은 `docs/`의 다섯 문서가 소유한다. 진입 앵커는 [`AGENTS.md`](AGENTS.md)다.

- [`docs/architecture.md`](docs/architecture.md) — 워크스페이스·패키지 구조·FSD-lite 레이어·의존 방향·서버/클라 구조 경계·시크릿 경계·설정 불변식·빌드 강제 불변식.
- [`docs/coding-conventions.md`](docs/coding-conventions.md) — 네이밍·타입 선언·컴포넌트/스타일 컨벤션·접근성·표준어·주석.
- [`docs/rendering.md`](docs/rendering.md) — 렌더 경계 판단·전략·라우팅·async 상태·메타데이터.
- [`docs/data.md`](docs/data.md) — read-model·데이터 흐름·BFF·api-client·캐시·응답 계약·폼.
- [`docs/code-quality.md`](docs/code-quality.md) — 포맷·린트·타입·경계 게이트와 도구 버전.

## 적용하기

- `docs/`와 진입 앵커(`AGENTS.md`·`CLAUDE.md`)를 서비스 레포로 복사한다.
- 이 가이드는 Turborepo + pnpm 워크스페이스 골격이 이미 구성된 저장소 위에서의 개발을 다스린다.
- 강제 장치는 공유 `eslint-config`·`tsconfig`·dependency-cruiser 설정 → `server-only`/`client-only` 경계 순으로 채운다. 아직 배선되지 않은 불변식은 빌드가 막지 못하므로 리뷰로만 지켜진다(→ [architecture](docs/architecture.md)의 빌드가 강제하는 불변식).

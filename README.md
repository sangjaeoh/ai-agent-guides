# AI Agent Guides

AI 코딩 에이전트가 따를 개발 규칙 가이드 모음이다. 아키텍처·컨벤션·품질 게이트를 문서로 고정해, 에이전트가 세션마다 흔들리지 않고 일관된 규율로 코드를 생성하게 한다. 각 하위 폴더는 독자적으로 완결된 가이드로, 실행 코드 없이 규칙 문서만 담는다.

![Spring Boot](https://img.shields.io/badge/Spring%20Boot-backend%20guide-6DB33F)
![Next.js](https://img.shields.io/badge/Next.js-frontend%20guide-black)
![Polyglot](https://img.shields.io/badge/Polyglot%20Monorepo-cross--language%20guide-blue)
![Anchor](https://img.shields.io/badge/AGENTS.md-anchored-8A2BE2)

## 구성

| 가이드 | 대상 | 범위 |
|---|---|---|
| [`spring-boot-agent-guide`](spring-boot-agent-guide) | Spring Boot + JPA 백엔드 (Java 25 · Gradle 멀티모듈) | 아키텍처·컨벤션·엔티티/영속·품질 |
| [`nextjs-agent-guide`](nextjs-agent-guide) | Next.js 프론트엔드 (App Router · RSC · Turborepo · FSD-lite) | 아키텍처·컨벤션·렌더/데이터·품질 |
| [`polyglot-monorepo-agent-guide`](polyglot-monorepo-agent-guide) | 백엔드+프론트 폴리글랏 모노레포 | 교차언어 아키텍처·공유방법(계약 seam·코드젠·에러 모델) |

각 가이드의 정확한 기술 스택·문서 목록·적용 방법은 해당 폴더의 README가 소유한다.

## 가이드는 어떻게 동작하는가

모든 가이드가 같은 골격을 따른다.

- `AGENTS.md` — 진입 앵커. 문서 인덱스와 작업 트리아지를 담고, 에이전트가 작업 성격에 맞는 규칙 문서만 로딩하게 한다. 규칙 본문을 직접 담지 않는다.
- `CLAUDE.md` — Claude Code용 앵커. `@AGENTS.md` 한 줄로 위임한다.
- `docs/*.md` — 규칙 소유 문서. 각 문서는 제목·언제(트리거)·규칙 셋으로 고정된다.
- 한 규칙은 한 문서가 소유하고 나머지는 참조만 한다. 규칙이 중복 서술로 갈라지는 것(drift)을 막는다.
- 규칙은 두 층으로 지켜진다: 빌드 검사(ArchUnit·dependency-cruiser·drift 게이트 등)가 기계로 막는 것과, 에이전트가 코드 생성 후 문서 목록으로 자기검증하는 것.

## 적용하기

### 단일 스택 프로젝트

대상 프로젝트 성격에 맞는 가이드 하나를 골라, 해당 폴더의 `docs/`와 진입 앵커(`AGENTS.md`·`CLAUDE.md`)를 대상 저장소 루트로 복사한다.

```bash
cp -r spring-boot-agent-guide/{AGENTS.md,CLAUDE.md,docs} <target-repo>/
```

각 가이드는 빌드 골격(Gradle 멀티모듈, Turborepo + pnpm)이 이미 구성된 저장소 위에서의 개발을 다스린다. 강제 장치(컨벤션 플러그인·아키텍처 테스트·린트 경계)가 아직 배선되지 않은 불변식은 빌드가 막지 못하므로, 앵커의 자기검증 절차가 유일한 게이트가 된다.

### 폴리글랏 모노레포 조립

백엔드+프론트를 한 저장소에 담는다면 세 가이드를 페더레이션으로 조립한다. 루트가 교차언어 규칙(구조·공유)을 소유하고, 언어별 규칙은 각 서브가이드가 자립적으로 소유한다.

```
target-repo/
├── AGENTS.md  CLAUDE.md  docs/     ← polyglot-monorepo-agent-guide (교차언어 허브)
├── apps/
│   ├── backend/
│   │   └── AGENTS.md  CLAUDE.md  docs/   ← spring-boot-agent-guide
│   └── frontend/
│       └── AGENTS.md  CLAUDE.md  docs/   ← nextjs-agent-guide
```

- 에이전트는 루트 앵커의 트리아지로 변경 표면(backend-only · frontend-only · cross-language · root-infra)을 가른 뒤, 소유 가이드로 위임한다.
- 서브가이드는 standalone으로도 유효하다 — 모노레포에 임베드돼도 내용 수정 없이 그대로 동작한다.

## 실제 적용 예

[`polyglot-board`](https://github.com/sangjaeoh/polyglot-board) — 세 가이드를 위 조립 방식 그대로 적용해 만든 게시판 데모다. Spring Boot 백엔드와 Next.js 프론트엔드가 하나의 OpenAPI 계약으로 이어지고, `docker compose up` 한 번으로 실행된다. 가이드의 규칙(모듈 경계·계약 seam·drift 게이트·품질 게이트)이 실제 코드에서 어떻게 강제되는지 확인할 수 있다.

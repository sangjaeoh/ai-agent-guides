# AI Agent Guides

AI 코딩 에이전트가 따를 개발 규칙 가이드 모음이다. 각 하위 폴더는 독자적으로 완결된 가이드로, 실행 코드 없이 규칙 문서만 담는다.

## 구성

| 가이드 | 대상 | 범위 |
|---|---|---|
| [`spring-boot-agent-guide`](spring-boot-agent-guide) | Spring Boot + JPA 백엔드 | 아키텍처·컨벤션·엔티티/영속·품질 |
| [`nextjs-agent-guide`](nextjs-agent-guide) | Next.js 프론트엔드 | 아키텍처·컨벤션·렌더/데이터·품질 |
| [`polyglot-monorepo-agent-guide`](polyglot-monorepo-agent-guide) | 백엔드+프론트 폴리글랏 모노레포 | 교차언어 아키텍처·공유방법 |

각 가이드의 정확한 기술 스택·문서 목록·적용 방법은 해당 폴더의 README가 소유한다.

## 적용하기

- 가이드는 각각 독립적으로 적용 가능하다. 대상 프로젝트 성격에 맞는 가이드 하나를 골라 해당 폴더의 `docs/`와 진입 앵커(`AGENTS.md`·`CLAUDE.md`)를 대상 저장소로 복사한다.
- 백엔드+프론트 폴리글랏 모노레포라면 `polyglot-monorepo-agent-guide`를 루트에 두고, `spring-boot-agent-guide`·`nextjs-agent-guide`를 각각 `apps/backend`·`apps/frontend` 서브가이드로 조립한다.

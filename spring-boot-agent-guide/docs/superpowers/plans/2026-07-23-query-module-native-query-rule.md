# Query 모듈 네이티브 조회 규칙 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** `docs/architecture.md`의 query 모듈 조회 구현 기술 규칙을 "QueryDSL 기본, EntityManager 네이티브 쿼리 최후수단" 2개 불릿으로 명확화한다.

**Architecture:** 문서 전용 변경. 코드 변경 없음. `docs/architecture.md` 1개 파일, 1개 불릿을 2개 불릿으로 교체하는 단일 작업.

**Tech Stack:** Markdown (문서), git.

## Global Constraints

- 규칙은 마크다운 불릿과 표로 쓴다. HTML·이모지·강조 기호(`**`)·편집자적 라벨("중요"·"베스트 프랙티스")을 넣지 않는다. (AGENTS.md 편집 규약)
- 한 불릿은 한 문장으로 쓴다. (AGENTS.md 편집 규약)
- 현재 상태로 쓴다. 편집 이력 서술을 넣지 않는다. (AGENTS.md 편집 규약)
- 한 규칙은 한 문서가 소유하고 나머지는 참조만 한다. (AGENTS.md 편집 규약)
- 네이티브 조회 조건은 도메인 모듈 규칙(`docs/architecture.md:461`)과 같은 개념("방언 종속이 불가피할 때만 최후수단")을 쓰되, QueryDSL 관련성을 문장에 명시한다: "방언 종속이 불가피해 QueryDSL로 구현할 수 없는 조회에 한해 최후수단으로 사용한다." 바이트 단위 동일 문구 요구 아님. (스펙 `docs/superpowers/specs/2026-07-23-query-module-native-query-rule-design.md`)
- 네이티브 조회 수단은 `EntityManager.createNativeQuery`로 한정한다. (스펙)

---

### Task 1: query 모듈 조회 구현 기술 불릿 교체

**Files:**
- Modify: `spring-boot-agent-guide/docs/architecture.md:318`
- Test: 해당 없음 — 문서 전용 변경. grep 기반 검증으로 대체.

**Interfaces:**
- Consumes: 없음 (선행 태스크 없음).
- Produces: 없음 (후속 태스크 없음, 이 계획은 단일 태스크).

- [ ] **Step 1: 변경 전 상태 확인 (실패 확인에 해당)**

Run: `grep -n "조회는 모듈 자체의 쿼리 구현" /Users/sangjaeoh/Desktop/gitspace/ai-agent-guides/spring-boot-agent-guide/docs/architecture.md`

Expected:
```
318:- 조회는 모듈 자체의 쿼리 구현(QueryDSL·`EntityManager`)으로 한다.
```

- [ ] **Step 2: 불릿 교체**

`spring-boot-agent-guide/docs/architecture.md:318`의 아래 줄을:

```markdown
- 조회는 모듈 자체의 쿼리 구현(QueryDSL·`EntityManager`)으로 한다.
```

다음 2줄로 교체한다:

```markdown
- 조회는 QueryDSL을 기본으로 구현한다.
- 방언 종속이 불가피해 QueryDSL로 구현할 수 없는 조회에 한해 `EntityManager` 네이티브 쿼리를 최후수단으로 사용한다.
```

- [ ] **Step 3: 변경 후 상태 확인 (통과 확인에 해당)**

Run:
```bash
grep -n "조회는 QueryDSL을 기본으로 구현한다" /Users/sangjaeoh/Desktop/gitspace/ai-agent-guides/spring-boot-agent-guide/docs/architecture.md
grep -n "EntityManager. 네이티브 쿼리를 최후수단" /Users/sangjaeoh/Desktop/gitspace/ai-agent-guides/spring-boot-agent-guide/docs/architecture.md
```

Expected:
```
318:- 조회는 QueryDSL을 기본으로 구현한다.
319:- 방언 종속이 불가피해 QueryDSL로 구현할 수 없는 조회에 한해 `EntityManager` 네이티브 쿼리를 최후수단으로 사용한다.
```

Run (구 문구 완전 제거 확인):
```bash
grep -n "모듈 자체의 쿼리 구현" /Users/sangjaeoh/Desktop/gitspace/ai-agent-guides/spring-boot-agent-guide/docs/architecture.md
```

Expected: 매치 없음 (exit code 1).

- [ ] **Step 4: 편집 규약 스팟체크**

Run:
```bash
sed -n '312,340p' /Users/sangjaeoh/Desktop/gitspace/ai-agent-guides/spring-boot-agent-guide/docs/architecture.md
```

확인 항목:
- 새 불릿 2개 모두 한 문장으로 끝나는지 (마침표 하나, 접속사로 이어진 복문 아닌지).
- `**`·이모지·HTML 태그 없는지.
- 나머지 "query 모듈 구조" 섹션 불릿 번호·순서가 자연스럽게 밀렸는지 (318번째 불릿이 2개로 늘어나며 이후 불릿은 그대로 텍스트 순서 유지, 별도 번호 매김 없으므로 재정렬 불필요).

- [ ] **Step 5: 다른 문서에서 구 문구 참조 여부 최종 확인**

Run:
```bash
grep -rn "모듈 자체의 쿼리 구현" /Users/sangjaeoh/Desktop/gitspace/ai-agent-guides/spring-boot-agent-guide/docs
```

Expected: 매치 없음. (스펙 작성 시 이미 확인했으나 실제 편집 후 재확인.)

- [ ] **Step 6: 커밋**

```bash
cd /Users/sangjaeoh/Desktop/gitspace/ai-agent-guides/spring-boot-agent-guide
git add docs/architecture.md
git commit -m "$(cat <<'EOF'
docs: query 모듈 조회 구현 기술에 QueryDSL 기본/네이티브 최후수단 명시

query 모듈이 QueryDSL과 EntityManager를 우선순위 없이 병기해
언제 네이티브 쿼리로 내려가는지 불명확했다. 도메인 모듈 규칙과
동일한 조건(방언 종속 불가피)으로 최후수단 기준을 맞췄다.
EOF
)"
git log --oneline -1
```

Expected: 커밋 성공, `git log`에 새 커밋 해시와 메시지 표시.

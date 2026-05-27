# 03-하네스-엔지니어링-실전-워크숍 설계 문서

## 개요

- **파일:** `materials/03-하네스-엔지니어링-실전-워크숍.html`
- **포맷:** standalone HTML, `design-system.md` + `content-guide.md` 준수
- **분량:** 9개 챕터, 약 150-180KB
- **대상:** AI 에이전트로 개발하는 실무자 (01, 02 선행 학습 전제)
- **접근법:** 계층별 빌드업 — 3계층 하네스를 한 층씩 쌓아가는 실전 워크숍

## 01, 02와의 관계

| 자료 | 역할 | 03과의 관계 |
|------|------|------------|
| 01-agent-개발-기본원리 | 개론 ("이런 개념이 있다") | Ch.1에서 빠르게 리캡, 상세는 01 참조 링크 |
| 02-hook-완전정복 | 심화 ("Hook을 깊이 파보자") | Ch.3에서 실습 중심 재구성, 이론은 02 참조 |
| **03-하네스-워크숍** | **실전 ("직접 세팅하자")** | 01+02 지식 전제, hands-on 중심 |

## 챕터 설계

### Chapter 1: 하네스 엔지니어링이란?

**목적:** 전체 그림 제시. "왜 모델이 아니라 하네스가 핵심인가"

**내용:**
- Agent = Model + Harness 공식 (Anthropic 공식 + Martin Fowler)
- 확률적 준수(Probabilistic Compliance) vs 결정론적 강제(Deterministic Enforcement)
- 3계층 아키텍처 개요: Constraint → Feedback → Quality Gate
- 래칫 패턴(Ratchet Pattern) — Addy Osmani/Google
- Terminal Bench 사례: 동일 모델, Hook만 바꿔서 30위→5위
- 사고 사례: Replit DROP DATABASE, 프롬프트 인젝션 78% 취약 통계

**컴포넌트:** term-card 4개, info-box.concept 2개, info-box.danger 1개, 3계층 다이어그램(CSS)

---

### Chapter 2: 1계층 — 규칙 파일 세팅

**목적:** CLAUDE.md / AGENTS.md 직접 작성 실습

**내용:**
- CLAUDE.md 로딩 계층: 글로벌 → 프로젝트 → 로컬 오버라이드
- HumanLayer 가이드: <60줄, 점진적 공개(Progressive Disclosure)
- AGENTS.md: Linux Foundation 표준, 60,000+ 레포, config.toml 구성
- **실습 1:** 나의 첫 CLAUDE.md — 빈 템플릿 → 완성 (단계별)
- **실습 2:** 나의 첫 AGENTS.md — 역할, 도구, 제약사항 정의
- compare-grid: 좋은 vs 나쁜 CLAUDE.md
- Stripe 사례: 조건부 규칙(conditional rules)

**컴포넌트:** file-tree, compare-grid, 코드 블록 3개, info-box.warning

---

### Chapter 3: 2계층 — Hook 시스템 구축

**목적:** 결정론적 강제의 핵심. Hook 직접 작성

**내용:**
- Hook 라이프사이클: SessionStart → PreToolUse → PostToolUse → Stop → Notification
- 5가지 핸들러 유형: command, http, mcp_tool, prompt, agent
- settings.json hooks 구성 (matcher 패턴, timeout)
- **실습 3:** 파괴적 명령 차단 Hook (`block-dangerous.sh`)
  - rm -rf /, DROP TABLE, force push 차단
  - jq stdin JSON 파싱, permissionDecision: "deny"
- **실습 4:** 자동 린트 피드백 Hook (`post-edit-context.sh`)
  - TypeScript tsc --noEmit, Python ruff check
  - 에러 → 에이전트 재주입 → 자동 수정
- **실습 5:** 세션 컨텍스트 주입 Hook (`session-context.sh`)
  - git 상태, 프로젝트 정보 자동 제공
- Hook 디버깅: --debug 플래그, exit code 규칙, JSON 응답 포맷

**컴포넌트:** 라이프사이클 다이어그램(CSS), 코드 블록 5개+, info-box.success, info-box.danger

---

### Chapter 4: 3계층 — 품질 게이트

**목적:** CI/CD 연동으로 비준수 결과물 머지 방지

**내용:**
- Quality Gate 역할: Hook이 못 잡은 것을 최종 차단
- GitHub Actions에서 에이전트 결과물 검증
- pre-commit hook vs CI check vs Stop Hook 비교
- Proposal-Before-Execution 패턴
- **실습 6:** GitHub Actions YAML — 에이전트 PR 자동 검증 파이프라인
- Stripe 사례: MCP Toolshed 400+ 도구, Tool Gateway 패턴

**컴포넌트:** compare-grid, 코드 블록 2개, info-box.concept

---

### Chapter 5: 프레임워크별 구현

**목적:** 주요 프레임워크 비교 + Next.js 실습

**내용:**
- Claude Code: 26+ 이벤트, settings.json hooks, 서브에이전트 자동 재귀
- OpenAI Agents SDK: RunHooksBase/AgentHooksBase, 3-tier Guardrails
- LangGraph: interrupt() + Checkpointer, human-in-the-loop
- Vercel AI SDK (Next.js) 3계층 심화:
  - middleware.ts (Rate Limit, Auth)
  - wrapLanguageModel (가드레일, RAG, 캐싱, 로깅)
  - streamText callbacks (onStepFinish, prepareStep, stopWhen)
- **실습 7:** Next.js Route Handler — wrapLanguageModel 가드레일 구현
- 프레임워크 선택 가이드: 의사결정 플로차트

**컴포넌트:** 비교 테이블 (4 프레임워크), 코드 블록 4개, info-box.insight

---

### Chapter 6: 실전 사례 분석

**목적:** 프로덕션 규모 하네스 운영 노하우

**내용:**
- Stripe Minions: 6-layer, 1300 PR/주, MCP Toolshed 400+, 조건부 규칙
- Manus: 5회 리라이트, 컨텍스트 엔지니어링 = 최고 레버리지
- Atlan: 10-step 하네스 빌드 (4-12주), Step 0 = 데이터 인증
- 핵심 교훈 정리
- 소규모 팀 적용 가능 vs 불필요 항목

**컴포넌트:** blockquote 3개, info-box.insight, 비교 테이블

---

### Chapter 7: 안티패턴 & 보안

**목적:** 하지 말아야 할 것 + 보안 고려사항

**내용:**
- Tool Bloat: 20개 넘으면 성능 급감 (Vercel 80% 제거)
- All-or-Nothing Autonomy: 승인 게이트 없는 완전 자율의 위험
- Hook Proliferation: 과도한 Hook = 기술 부채
- OWASP Top 10 for Agentic Applications (2025) 주요 항목
- 프롬프트 인젝션 방어: 78% 취약 통계
- 멀티 에이전트 비구성성 (arXiv 2505.02077)
- Tiered Permission: safe / moderate / dangerous 3단계

**컴포넌트:** info-box.danger 3개, info-box.warning 2개, compare-grid

---

### Chapter 8: 나만의 하네스 설계

**목적:** 자기 프로젝트 즉시 적용 워크시트

**내용:**
- 워크시트 A: 프로젝트 위험 분류표
- 워크시트 B: 3계층 설계 체크리스트
- 워크시트 C: CLAUDE.md 초안 템플릿
- 워크시트 D: Hook settings.json 뼈대
- 래칫 패턴 실천 가이드
- 도입 로드맵: Week 1-2 (MVH) → Week 3-4 (피드백 루프) → Week 5+ (프로덕션)

**컴포넌트:** 체크리스트 테이블 4개, file-tree, info-box.success

---

### Chapter 9: 참고자료

**소스 (고신뢰만):**

| 출처 | 유형 | URL |
|------|------|-----|
| Anthropic — Claude Code Hooks | 공식 문서 | https://docs.anthropic.com/en/docs/claude-code/hooks |
| Anthropic — Claude Code Best Practices | 공식 문서 | https://docs.anthropic.com/en/docs/claude-code/best-practices |
| OpenAI — Agents SDK | 공식 문서 | https://openai.github.io/openai-agents-python/ |
| OpenAI — Harness Engineering | 공식 블로그 | https://openai.com/index/harness-engineering/ |
| Martin Fowler — Harness Engineering | 기술 아티클 | https://martinfowler.com/articles/harness-engineering.html |
| Addy Osmani — Agent Harness Engineering | 기술 블로그 | https://addyosmani.com/blog/agent-harness-engineering/ |
| Vercel — AI SDK Middleware | 공식 문서 | https://ai-sdk.dev/docs/ai-sdk-core/middleware |
| Stripe Engineering — Building an Agentic System | 기술 블로그 | https://stripe.com/blog/how-we-built-an-ai-agent |
| OWASP — Top 10 for Agentic Applications | 보안 표준 | https://owasp.org/www-project-top-10-for-large-language-model-applications/ |
| Robust Intelligence — LLM Vulnerability Study | 연구 | 2025 |
| arXiv 2505.02077 — Multi-Agent Non-Compositionality | 학술 논문 | https://arxiv.org/abs/2505.02077 |
| HumanLayer — CLAUDE.md Guide | 기술 가이드 | https://humanlayer.dev/blog/claudemd |
| Linux Foundation — AGENTS.md Spec | 표준 문서 | https://github.com/anthropics/claude-code/blob/main/AGENTS.md |

## 실습 목록 요약

| # | 실습 | 챕터 | 산출물 |
|---|------|------|--------|
| 1 | 나의 첫 CLAUDE.md | Ch.2 | `~/.claude/CLAUDE.md` 또는 프로젝트 CLAUDE.md |
| 2 | 나의 첫 AGENTS.md | Ch.2 | 프로젝트 AGENTS.md |
| 3 | 파괴적 명령 차단 Hook | Ch.3 | `~/.claude/hooks/block-dangerous.sh` |
| 4 | 자동 린트 피드백 Hook | Ch.3 | `~/.claude/hooks/post-edit-context.sh` |
| 5 | 세션 컨텍스트 주입 Hook | Ch.3 | `~/.claude/hooks/session-context.sh` |
| 6 | GitHub Actions 품질 게이트 | Ch.4 | `.github/workflows/agent-qa.yml` |
| 7 | Next.js 가드레일 구현 | Ch.5 | `app/api/chat/route.ts` + middleware |

## 디자인 규칙 준수

- 폰트: Pretendard + JetBrains Mono
- 컬러: design-system.md 팔레트 (accent: #2563eb)
- 레이아웃: 960px container, Hero gradient, TOC 오버랩
- 언어: 한국어 기본, 영어 전문용어 `<span class="en">` / `<span class="en-note">`
- 반응형: 768px breakpoint + print 대응
- 금지: Y2K/레트로/픽셀 스타일, 과도한 애니메이션

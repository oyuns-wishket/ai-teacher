# 하네스 엔지니어링 & Hook 학습 노트

## 핵심 개념

- **Agent = Model + Harness** (Viv Trivedy)
- 모델은 commodity, 하네스가 moat
- 2026년은 "에이전트 하네스의 해"

## 확률적 준수 vs 결정론적 강제

- 프롬프트로 지시 = 확률적 (모델이 무시할 수 있음)
- Hook으로 강제 = 결정론적 (물리적으로 차단)
- Terminal Bench: 동일 모델, Hook만 바꿔서 30위→5위 (Addy Osmani)

## 3계층 아키텍처 (Augment Code)

1. **Constraint Harness (사전)** — 규칙파일, 린터, PreToolUse
2. **Feedback Loop (교정)** — PostToolUse로 에러를 에이전트에 재주입
3. **Quality Gate (차단)** — CI/Stop Hook으로 비준수 결과물 머지 방지

## 래칫 패턴 (Addy Osmani, Google)

실패 발생 → 규칙 추가 (확률적) → Hook 추가 (결정론적) → 같은 실수 영구 불가능

## 프레임워크별 비교

| | Claude Code | OpenAI Agents SDK | LangGraph | Vercel AI SDK |
|--|---|---|---|---|
| 이벤트 수 | 26+ | 7 | 10+ + interrupt | onStepFinish 등 |
| 차단 | exit code + JSON | raise Exception | interrupt() | stopWhen |
| 서브에이전트 | 자동 재귀 적용 | 수동 전파 | 그래프 암시적 | - |

## Next.js 특화

Vercel AI SDK 3계층:
1. middleware.ts — HTTP 수준 (Rate Limit, Auth)
2. wrapLanguageModel — AI 미들웨어 (가드레일, RAG, 캐싱, 로깅)
3. streamText callbacks — 에이전트 루프 (onStepFinish, prepareStep, stopWhen)

## 주요 Anti-Pattern

- Tool Bloat: 20개 넘으면 성능 급감 (Vercel은 80% 제거로 개선)
- All-or-Nothing Autonomy: 승인 게이트 없는 완전 자율 → Replit DROP DATABASE 사건
- Hook Proliferation: 과도한 Hook = 기술 부채 (AGENTS.md 60줄 이내 권장)

## 학술

- 프로덕션 LLM 앱 78%가 프롬프트 인젝션에 취약 (Robust Intelligence, 2025)
- OWASP Top 10 for Agentic Applications (2025.12)
- 멀티 에이전트 비구성성 문제: 개별 안전해도 조합하면 위험 (arXiv 2505.02077)

## 소스

- https://addyosmani.com/blog/agent-harness-engineering/
- https://martinfowler.com/articles/harness-engineering.html
- https://openai.com/index/harness-engineering/
- https://code.claude.com/docs/en/hooks
- https://ai-sdk.dev/docs/ai-sdk-core/middleware

# ai-teacher

AI 에이전트(Claude Code, Codex 등) 학습 정리 & 사내 교육 커리큘럼 레포지토리

## 목적

1. **개인 학습** — AI 에이전트 도구의 원리, 적용법, 도메인별 활용 사례를 공부하며 정리
2. **사내 교육** — 학습 내용을 체계적인 커리큘럼과 교육자료(HTML)로 발전

## 프로젝트 구조

```
ai-teacher/
├── notes/                        ← 학습 노트 (자유 형식 md, 빠르게 정리)
│   ├── claude-code/              ← Claude Code 관련
│   ├── codex/                    ← OpenAI Codex 관련
│   ├── agent-patterns/           ← 에이전트 아키텍처/패턴 (ReAct, CoT 등)
│   └── use-cases/                ← 도메인/업무별 적용 사례
├── curriculum/                   ← 교육 커리큘럼 설계
│   └── outline.md                ← 전체 로드맵 (3레벨, 13개 주제)
├── materials/                    ← 완성된 교육자료 (standalone HTML)
│   ├── 01-agent-개발-기본원리.html
│   ├── 02-claude-md-세팅-가이드.html
│   ├── 03-hook-완전정복.html
│   └── 04-하네스-엔지니어링-실전-워크숍.html
├── CLAUDE.md                     ← Claude Code 프로젝트 지시
└── .claude/rules/                ← 디자인 시스템 & 콘텐츠 작성 규칙
```

## 워크플로우

```
공부하다 메모 (notes/)
       ↓
패턴이 보이면 커리큘럼에 추가 (curriculum/outline.md)
       ↓
교육자료로 만들 준비되면 HTML 제작 (materials/)
```

## 교육자료 목록

| # | 주제 | 내용 |
|---|------|------|
| 01 | [Agent 개발 기본원리](materials/01-agent-개발-기본원리.html) | Session/Turn/Context, 설정 계층, 토큰 경제학, 아키텍처 패턴, 보안, 체크리스트 |
| 02 | [CLAUDE.md 세팅 가이드](materials/02-claude-md-세팅-가이드.html) | 로딩 메커니즘(4계층/concatenate/재귀), import(@), 라인 수 최적화, 작성 원칙, 안티패턴, 템플릿, 검증·반복 |
| 03 | [Hook 완전 정복](materials/03-hook-완전정복.html) | 하네스 엔지니어링, Hook 원리, 프레임워크별 비교, Next.js 실전 구현 |
| 04 | [하네스 엔지니어링 실전 워크숍](materials/04-하네스-엔지니어링-실전-워크숍.html) | 개념~세팅~사례~보안 핸즈온 |

## 커리큘럼 로드맵

- **Level 1: 기초** — Agent 기본원리, Hook, CLAUDE.md 작성법, 실전 세팅
- **Level 2: 적용** — 프로젝트 세팅 워크숍, 코드리뷰 활용, TDD 워크플로우, 멀티에이전트
- **Level 3: 심화** — Context Engineering, 보안, 비용 최적화, 도메인별 사례

상세: [curriculum/outline.md](curriculum/outline.md)

## 실행

정적 HTML 파일입니다. 브라우저에서 직접 열면 됩니다.

```bash
open materials/01-agent-개발-기본원리.html
```

## Claude Code 연동

이 레포에서 `claude`를 실행하면 `CLAUDE.md`와 `.claude/rules/`가 자동 적용됩니다.

- 대화 중 학습 내용 → `notes/`에 자동 저장
- 노트가 쌓이면 → 커리큘럼 추가 제안
- 주제가 무르익으면 → HTML 교육자료 제작 제안

## 참고 소스

교육자료에서 참고하는 주요 출처:

- [Anthropic: Building Effective Agents](https://www.anthropic.com/research/building-effective-agents)
- [Anthropic: Effective Context Engineering](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [OpenAI: A Practical Guide to Building Agents](https://openai.com/business/guides-and-resources/a-practical-guide-to-building-ai-agents/)
- [Dive into Claude Code (arXiv 2604.14228)](https://arxiv.org/abs/2604.14228)
- [ReAct: Synergizing Reasoning and Acting (arXiv 2210.03629)](https://arxiv.org/abs/2210.03629)

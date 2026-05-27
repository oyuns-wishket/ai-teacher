# 03-하네스-엔지니어링-실전-워크숍 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 하네스 엔지니어링의 개념부터 실전 적용까지 따라할 수 있는 standalone HTML 교육자료 제작

**Architecture:** 단일 standalone HTML 파일(외부 의존성 0). CSS 인라인 `<style>`, 9개 챕터, 7개 실습. 기존 02-hook-완전정복.html의 디자인 시스템을 재사용하되, 워크숍 전용 컴포넌트(실습 카드, 워크시트, 체크리스트) 추가.

**Tech Stack:** HTML5, CSS3 (인라인), 코드 예시는 Bash/JSON/TypeScript/Python

**Spec:** `docs/superpowers/specs/2026-05-27-harness-engineering-workshop-design.md`
**Design System:** `.claude/rules/design-system.md`
**Content Guide:** `.claude/rules/content-guide.md`
**Reference Material:** `materials/02-hook-완전정복.html` (CSS 패턴 및 컴포넌트 구조 참조)

---

## File Structure

| Action | Path | Responsibility |
|--------|------|----------------|
| Create | `materials/03-하네스-엔지니어링-실전-워크숍.html` | 전체 교육자료 HTML (standalone) |
| Read | `materials/02-hook-완전정복.html` | CSS 변수, 컴포넌트 클래스 참조용 |
| Read | `.claude/rules/design-system.md` | 디자인 규칙 확인 |
| Read | `.claude/rules/content-guide.md` | 콘텐츠 규칙 확인 |
| Read | `docs/superpowers/specs/2026-05-27-harness-engineering-workshop-design.md` | 스펙 참조 |

---

## Task 1: HTML 뼈대 + CSS + Hero + TOC

**Files:**
- Create: `materials/03-하네스-엔지니어링-실전-워크숍.html`
- Read: `materials/02-hook-완전정복.html` (CSS 패턴 참조)

이 Task에서 파일을 생성하고, 전체 CSS와 Hero 섹션, TOC를 작성한다. 이후 Task에서 챕터 콘텐츠를 순서대로 추가한다.

- [ ] **Step 1: 02 교육자료에서 CSS 패턴 확인**

Run: `head -400 materials/02-hook-완전정복.html`

02의 `:root` 변수, 컴포넌트 클래스(.term-card, .info-box, .compare-grid, .file-tree, .diagram 등), 반응형/인쇄 미디어쿼리를 확인한다.

- [ ] **Step 2: HTML 파일 생성 — DOCTYPE부터 `</style>` 태그까지**

`materials/03-하네스-엔지니어링-실전-워크숍.html` 파일을 생성한다.

포함할 CSS:
- 02에서 가져오는 것: `:root` 변수, `body`, `.container`, `.hero`, `.toc`, `.chapter`, `h3`, `h4`, `p`, `.en`, `.en-note`, `.term-card`, `pre`, `code`, `.code-comment/.code-keyword/.code-string/.code-fn/.code-type`, `table/th/td`, `.info-box` (5종: concept/warning/danger/success/insight), `.diagram`, `blockquote`, `ul/ol/li`, `.compare-grid/.compare-card`, `.file-tree`, 반응형(`@media max-width: 768px`), 인쇄(`@media print`)
- 03에서 새로 추가하는 것:

```css
/* 실습 카드 */
.workshop-card {
  background: linear-gradient(135deg, #eff6ff, #dbeafe);
  border: 2px solid var(--accent);
  border-radius: 12px;
  padding: 24px 28px;
  margin: 24px 0;
}
.workshop-card .workshop-header {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 16px;
}
.workshop-card .workshop-number {
  background: var(--accent);
  color: white;
  width: 36px;
  height: 36px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 800;
  font-size: 0.9rem;
  flex-shrink: 0;
}
.workshop-card .workshop-title {
  font-size: 1.15rem;
  font-weight: 700;
  color: var(--accent-dark);
}
.workshop-card .workshop-goal {
  font-size: 0.9rem;
  color: var(--text-secondary);
  margin-bottom: 16px;
  padding-left: 48px;
}

/* 워크시트 */
.worksheet {
  background: #fffbeb;
  border: 2px dashed var(--orange);
  border-radius: 12px;
  padding: 24px 28px;
  margin: 24px 0;
}
.worksheet h4 {
  color: var(--orange);
  margin-top: 0;
}

/* 체크리스트 */
.checklist {
  list-style: none;
  padding-left: 0;
}
.checklist li {
  padding: 8px 0 8px 32px;
  position: relative;
}
.checklist li::before {
  content: "☐";
  position: absolute;
  left: 0;
  font-size: 1.2rem;
}

/* 단계 표시 */
.step-indicator {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  background: var(--accent-light);
  color: var(--accent-dark);
  padding: 4px 12px;
  border-radius: 20px;
  font-size: 0.8rem;
  font-weight: 700;
  margin-bottom: 12px;
}

/* 산출물 태그 */
.output-tag {
  display: inline-block;
  background: var(--green-light);
  color: var(--green);
  padding: 4px 12px;
  border-radius: 6px;
  font-size: 0.82rem;
  font-weight: 600;
  margin-top: 12px;
}
```

- [ ] **Step 3: Hero 섹션 작성**

```html
<body>
<div class="container">
  <header class="hero">
    <h1>하네스 엔지니어링 실전 워크숍</h1>
    <p class="subtitle">Agent = Model + Harness — 개념부터 실전 세팅까지, 따라하며 완성하는 하네스 구축 가이드</p>
    <p class="meta">Level 1 · 실전 워크숍 · 7개 실습 포함 · 2026.05</p>
  </header>
```

- [ ] **Step 4: TOC 작성**

9개 챕터 목차. 각 항목에 `href="#ch1"` ~ `#ch9` 앵커 링크. 실습이 포함된 챕터는 실습 수를 표기한다.

```html
  <nav class="toc">
    <h2>목차</h2>
    <ol>
      <li><a href="#ch1">하네스 엔지니어링이란?</a></li>
      <li><a href="#ch2">1계층: 규칙 파일 세팅</a> <span style="...">실습 2개</span></li>
      <li><a href="#ch3">2계층: Hook 시스템 구축</a> <span style="...">실습 3개</span></li>
      <li><a href="#ch4">3계층: 품질 게이트</a> <span style="...">실습 1개</span></li>
      <li><a href="#ch5">프레임워크별 구현</a> <span style="...">실습 1개</span></li>
      <li><a href="#ch6">실전 사례 분석</a></li>
      <li><a href="#ch7">안티패턴 & 보안</a></li>
      <li><a href="#ch8">나만의 하네스 설계</a></li>
      <li><a href="#ch9">참고자료</a></li>
    </ol>
  </nav>
```

- [ ] **Step 5: 빈 챕터 placeholder 9개 + Footer + 닫는 태그**

각 챕터를 `<section class="chapter" id="chN">` 구조로 빈 껍데기만 생성. Footer는:

```html
  <footer style="text-align:center; padding:40px 0 60px; color:#94a3b8; font-size:0.85rem;">
    <p>하네스 엔지니어링 실전 워크숍</p>
    <p>2026.05 | 신뢰성 있는 공식 문서 및 학술 자료 기반</p>
  </footer>
</div>
</body>
</html>
```

- [ ] **Step 6: 브라우저에서 확인**

Run: `open materials/03-하네스-엔지니어링-실전-워크숍.html`

확인 사항:
- Hero gradient가 02와 동일한 스타일로 렌더링되는가
- TOC가 Hero 아래 -32px 오버랩으로 표시되는가
- 빈 챕터 카드 9개가 흰색 카드로 나열되는가

- [ ] **Step 7: 커밋**

```bash
git add materials/03-하네스-엔지니어링-실전-워크숍.html
git commit -m "feat: add material 03 skeleton — HTML, CSS, Hero, TOC"
```

---

## Task 2: Chapter 1 — 하네스 엔지니어링이란?

**Files:**
- Modify: `materials/03-하네스-엔지니어링-실전-워크숍.html` (Chapter 1 section)

- [ ] **Step 1: Chapter 1 헤더 + 도입부 작성**

```html
<section class="chapter" id="ch1">
  <div class="chapter-number">Chapter 1</div>
  <h2>하네스 엔지니어링이란?</h2>
  <p class="chapter-desc">모델이 아니라 하네스가 진짜 경쟁력입니다. AI 에이전트를 제어하는 시스템 전체를 설계하는 방법론을 이해합니다.</p>
```

도입 문단: "2025년은 AI 에이전트의 해였다면, 2026년은 에이전트 하네스의 해입니다" (Aakash Gupta 인용). 에이전트를 '말'에 비유 — 하네스 = 마구(馬具).

- [ ] **Step 2: 핵심 공식 — Agent = Model + Harness**

term-card 2개:
1. **Harness** `<span class="en-note">— 직역: 마구. AI 맥락: 모델을 감싸서 제어·관찰·안내하는 모든 인프라</span>` — 프롬프트, 규칙 파일, Hook, CI/CD, 모니터링 등 모델 외의 모든 것
2. **Harness Engineering** `<span class="en-note">— 하네스 엔지니어링. 에이전트의 제어 인프라를 체계적으로 설계·구축·운영하는 엔지니어링 분야</span>`

blockquote (Martin Fowler / Birgitta Bockeler):
> "The agent is both the model and the harness. The harness is where you have the most leverage."

info-box.concept: "하네스는 하나의 도구가 아닙니다. 규칙 파일, Hook, CI/CD, 모니터링, 권한 체계를 아우르는 전체 시스템입니다."

- [ ] **Step 3: 확률적 준수 vs 결정론적 강제**

term-card 2개:
1. **Probabilistic Compliance** `<span class="en-note">— 확률적 준수. 프롬프트로 지시하면 모델이 대부분 따르지만 100% 보장 불가</span>`
2. **Deterministic Enforcement** `<span class="en-note">— 결정론적 강제. 시스템 수준에서 위반 자체를 물리적으로 불가능하게 만드는 방식</span>`

compare-grid:
- before: 확률적 준수 (시스템 프롬프트: "rm -rf를 실행하지 마세요") → 99% 따르지만 1% 실패 치명적, 컨텍스트 길어지면 "잊음", Prompt Injection 우회 가능
- after: 결정론적 강제 (PreToolUse Hook: `if command includes "rm -rf" → deny`) → 100% 차단, 컨텍스트 무관, 인젝션 무관

- [ ] **Step 4: 3계층 아키텍처 개요**

CSS diagram으로 3계층 시각화:

```
┌─────────────────────────────────────────────┐
│         ③ Quality Gate (차단)                │
│         CI/CD, Stop Hook, 머지 방지          │
├─────────────────────────────────────────────┤
│         ② Feedback Loop (교정)               │
│         PostToolUse → 에러 재주입 → 자동 수정  │
├─────────────────────────────────────────────┤
│         ① Constraint Harness (사전)          │
│         규칙 파일, 린터, PreToolUse           │
├─────────────────────────────────────────────┤
│              AI Model                        │
│         (여기는 여러분이 바꿀 수 없는 영역)     │
└─────────────────────────────────────────────┘
```

info-box.concept: "Augment Code가 정의한 3계층: ① 사전에 제약 → ② 실행 중 교정 → ③ 사후에 차단. 이 워크숍에서는 1계층부터 차례로 구축합니다."

- [ ] **Step 5: 래칫 패턴 + Terminal Bench 사례**

term-card:
- **Ratchet Pattern** `<span class="en-note">— 래칫 패턴. 한 방향으로만 조이는 래칫 도구처럼, 실패가 발생하면 규칙을 추가하여 같은 실수가 영구적으로 불가능해지는 개선 루프</span>`

다이어그램:
```
실패 발생 → 규칙 추가 (확률적) → Hook 추가 (결정론적) → 같은 실수 영구 불가능
   ↑                                                        │
   └────────────── 새로운 유형의 실패 발생 ──────────────────┘
```

info-box.insight: "Addy Osmani(Google Chrome 팀)의 Terminal Bench 실험: 동일한 모델(Claude Sonnet)로 코딩 벤치마크를 실행했을 때, Hook과 규칙 파일만 교체하여 30위에서 5위로 점프. 모델을 바꾸지 않고도 하네스만으로 25단계 상승."

- [ ] **Step 6: 사고 사례 — 왜 하네스가 필수인가**

info-box.danger: "실제 사고: 2025년 Replit Agent가 사용자 요청을 과잉 해석하여 프로덕션 데이터베이스에 DROP TABLE을 실행. 하네스(PreToolUse Hook)가 있었다면 차단 가능했던 사고."

통계 테이블:
| 통계 | 수치 | 출처 |
|------|------|------|
| 프로덕션 LLM 앱 프롬프트 인젝션 취약률 | 78% | Robust Intelligence, 2025 |
| AGENTS.md 채택 GitHub 레포 수 | 60,000+ | Linux Foundation, 2025 |
| Stripe Minions 주간 PR 처리량 | 1,300+/주 | Stripe Engineering Blog |

section 닫기 `</section>`

- [ ] **Step 7: 브라우저에서 Chapter 1 확인**

Run: `open materials/03-하네스-엔지니어링-실전-워크숍.html`

확인: term-card 스타일, compare-grid 2열 렌더링, diagram 박스, info-box 색상 구분

- [ ] **Step 8: 커밋**

```bash
git add materials/03-하네스-엔지니어링-실전-워크숍.html
git commit -m "feat(material-03): add Chapter 1 — 하네스 엔지니어링 개요"
```

---

## Task 3: Chapter 2 — 1계층: 규칙 파일 세팅

**Files:**
- Modify: `materials/03-하네스-엔지니어링-실전-워크숍.html` (Chapter 2 section)

- [ ] **Step 1: Chapter 2 헤더 + CLAUDE.md 설명**

```html
<section class="chapter" id="ch2">
  <div class="chapter-number">Chapter 2</div>
  <h2>1계층: 규칙 파일 세팅</h2>
  <p class="chapter-desc">하네스의 첫 번째 계층입니다. CLAUDE.md와 AGENTS.md로 에이전트의 행동 규범을 정의합니다.</p>
```

h3: CLAUDE.md란?

term-card:
- **CLAUDE.md** `<span class="en-note">— Claude Code가 세션 시작 시 자동 로딩하는 규칙 파일. 에이전트에게 "이 프로젝트에서는 이렇게 행동해라"를 선언하는 문서</span>`

CLAUDE.md 로딩 계층 설명 (3단계):
1. `~/.claude/CLAUDE.md` — 글로벌 (모든 프로젝트 공통)
2. `프로젝트루트/CLAUDE.md` — 프로젝트 레벨
3. `.claude/rules/*.md` — 규칙 파일 (자동 로딩, 주제별 분리)

file-tree 컴포넌트:
```
~/.claude/
├── CLAUDE.md              ← 글로벌 규칙 (모든 프로젝트 적용)
├── settings.json          ← Hook, 모델, 권한 설정
└── hooks/                 ← Hook 스크립트

my-project/
├── CLAUDE.md              ← 프로젝트 규칙 (이 프로젝트만)
├── .claude/
│   └── rules/
│       ├── testing.md     ← 테스트 규칙
│       └── style.md       ← 코드 스타일 규칙
└── AGENTS.md              ← 범용 에이전트 규칙
```

- [ ] **Step 2: 좋은 CLAUDE.md 작성 원칙**

HumanLayer 가이드 핵심 원칙:
- 60줄 이내 권장 (300줄 넘으면 성능 저하)
- Progressive Disclosure: 핵심 규칙만 CLAUDE.md에, 세부사항은 `.claude/rules/`로 분리
- 구체적 행동 지시 ("TypeScript 사용" ✅) vs 모호한 지시 ("좋은 코드 작성" ❌)

compare-grid:
- before (나쁜 CLAUDE.md): 300줄, 모든 규칙을 한 파일에, "코드를 잘 짜세요" 같은 모호한 지시, 비관련 정보 포함
- after (좋은 CLAUDE.md): 40줄, 핵심만, 구체적 행동 지시, 세부사항은 rules/로 분리

info-box.warning: "Anti-Pattern: CLAUDE.md 만능 파일. 모든 규칙을 하나에 넣으면 토큰 낭비 + 중요한 규칙이 묻힘. HumanLayer 연구에 따르면 60줄 이내가 최적."

- [ ] **Step 3: 실습 1 — 나의 첫 CLAUDE.md**

workshop-card 컴포넌트:

```html
<div class="workshop-card">
  <div class="workshop-header">
    <div class="workshop-number">1</div>
    <div class="workshop-title">나의 첫 CLAUDE.md 작성</div>
  </div>
  <div class="workshop-goal">목표: 프로젝트에 맞는 CLAUDE.md를 작성하여 에이전트의 기본 행동을 정의합니다.</div>
```

단계별 실습 내용:

**Step A:** 빈 템플릿 생성 — 프로젝트 루트에 CLAUDE.md 생성

```markdown
# 프로젝트명

## 빌드/실행
(여기에 빌드 명령어)

## 코드 스타일
(여기에 스타일 규칙)

## 금지사항
(여기에 절대 하면 안 되는 것)
```

**Step B:** 섹션별 채우기 — 예시 (Next.js 프로젝트):

```markdown
# my-app

## 빌드/실행
- `pnpm dev` — 개발 서버
- `pnpm build && pnpm start` — 프로덕션

## 코드 스타일
- TypeScript strict mode
- 컴포넌트: function 선언, Props 타입 명시
- 서버 컴포넌트 기본, 'use client' 최소화

## 금지사항
- `any` 타입 사용 금지
- console.log 커밋 금지
- 환경변수 하드코딩 금지
```

**Step C:** 검증 — Claude Code 세션 시작 후 규칙 반영 확인

output-tag: `산출물: 프로젝트루트/CLAUDE.md`

workshop-card 닫기.

- [ ] **Step 4: AGENTS.md 설명 + Stripe 조건부 규칙**

h3: AGENTS.md — 범용 에이전트 규칙

term-card:
- **AGENTS.md** `<span class="en-note">— Linux Foundation이 표준화한 범용 에이전트 지시 파일. Claude Code뿐 아니라 Codex, Gemini 등 다양한 에이전트가 읽을 수 있는 표준 형식</span>`

CLAUDE.md vs AGENTS.md 비교 테이블:
| 항목 | CLAUDE.md | AGENTS.md |
|------|-----------|-----------|
| 대상 | Claude Code 전용 | 모든 AI 에이전트 (범용) |
| 표준화 | Anthropic 자체 | Linux Foundation |
| 채택 규모 | Claude Code 사용자 | 60,000+ GitHub 레포 |
| 위치 | 프로젝트 루트 또는 `~/.claude/` | 프로젝트 루트 |
| 설정 확장 | `.claude/rules/` | `config.toml` |

Stripe 사례 — 조건부 규칙:

```toml
# AGENTS.md의 config.toml
[[rules]]
pattern = "src/payments/**"
content = "결제 관련 코드 수정 시 반드시 PCI-DSS 컴플라이언스 검토 후 진행"

[[rules]]
pattern = "*.test.*"
content = "테스트 파일은 AAA(Arrange-Act-Assert) 패턴 준수"
```

info-box.insight: "Stripe는 파일 경로 패턴별로 다른 규칙을 적용합니다. 결제 코드에는 보안 규칙, 테스트 코드에는 테스트 패턴 규칙. 1,300+ PR/주를 처리하는 Minions 시스템의 핵심 전략."

- [ ] **Step 5: 실습 2 — 나의 첫 AGENTS.md**

workshop-card:

```markdown
# AGENTS.md

## Project Overview
(프로젝트 설명)

## Code Standards
(코딩 표준)

## Tool Restrictions
(도구 사용 제한)
```

예시 채움 (웹 프로젝트):

```markdown
# AGENTS.md

## Project Overview
Next.js 15 기반 대시보드 앱. TypeScript strict, pnpm 패키지 매니저.

## Code Standards
- Server Components 기본, Client Components는 'use client' 명시
- DB 접근은 반드시 lib/db.ts의 쿼리 빌더 사용
- API Route는 app/api/ 하위에만 생성

## Tool Restrictions
- 프로덕션 DB 직접 접근 금지
- 외부 API 호출 시 lib/fetch-client.ts 사용
- 환경변수는 .env.local에만 저장
```

output-tag: `산출물: 프로젝트루트/AGENTS.md`

section 닫기.

- [ ] **Step 6: 브라우저에서 Chapter 2 확인**

Run: `open materials/03-하네스-엔지니어링-실전-워크숍.html`

확인: file-tree 렌더링, compare-grid 2열, workshop-card 번호 원형 배지, output-tag 초록색

- [ ] **Step 7: 커밋**

```bash
git add materials/03-하네스-엔지니어링-실전-워크숍.html
git commit -m "feat(material-03): add Chapter 2 — 규칙 파일 세팅 (실습 1-2)"
```

---

## Task 4: Chapter 3 — 2계층: Hook 시스템 구축

**Files:**
- Modify: `materials/03-하네스-엔지니어링-실전-워크숍.html` (Chapter 3 section)

이 챕터가 가장 큰 분량 (실습 3개 포함). 02 교육자료의 Hook 이론을 전제하고, 실습 중심으로 작성.

- [ ] **Step 1: Chapter 3 헤더 + Hook 라이프사이클**

```html
<section class="chapter" id="ch3">
  <div class="chapter-number">Chapter 3</div>
  <h2>2계층: Hook 시스템 구축</h2>
  <p class="chapter-desc">프롬프트는 "부탁"이고, Hook은 "법률"입니다. 결정론적 강제의 핵심인 Hook을 직접 만들어봅니다.</p>
```

h3: Hook 라이프사이클

diagram 컴포넌트 — Claude Code Hook 실행 순서:

```
SessionStart → 사용자 메시지 → 에이전트 판단
                                    │
                              PreToolUse ──(deny)──→ 차단 (도구 실행 안 됨)
                                    │(allow)
                              도구 실행
                                    │
                              PostToolUse ──→ 피드백 (에이전트에 재주입)
                                    │
                              결과 반환 → 다음 턴 또는 Stop
                                                        │
                                                  Notification
```

h3: 5가지 핸들러 유형

테이블:
| 유형 | 설명 | 사용 시점 |
|------|------|----------|
| `command` | 셸 스크립트 실행 | 린트, 파괴적 명령 차단, 파일 검증 |
| `http` | HTTP 엔드포인트 호출 | 외부 서비스 연동, 웹훅 |
| `mcp_tool` | MCP 서버의 도구 호출 | MCP 기반 검증, 외부 시스템 |
| `prompt` | LLM에 프롬프트 전달 | AI 기반 판단 (코드 리뷰 등) |
| `agent` | 서브에이전트 실행 | 복잡한 다단계 검증 |

h3: settings.json 구조

코드 블록 — Hook 설정의 기본 구조:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "$HOME/.claude/hooks/block-dangerous.sh",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```

term-card:
- **matcher** `<span class="en-note">— 매처. Hook이 실행될 도구를 지정하는 패턴. "Bash"는 Bash 도구에만, "Edit|Write"는 둘 다에 적용. 정규식 지원</span>`

- [ ] **Step 2: 실습 3 — 파괴적 명령 차단 Hook**

workshop-card: 실습 3 — 파괴적 명령 차단 Hook

목표: rm -rf /, DROP TABLE, git push --force main을 차단하는 PreToolUse Hook 셸 스크립트를 작성합니다.

**Step A:** 파일 생성

```bash
mkdir -p ~/.claude/hooks
touch ~/.claude/hooks/block-dangerous.sh
chmod +x ~/.claude/hooks/block-dangerous.sh
```

**Step B:** 스크립트 작성 (전체 코드 포함)

```bash
#!/bin/bash
# PreToolUse Hook: 파괴적 명령 차단
INPUT=$(cat /dev/stdin)
COMMAND=$(echo "$INPUT" | jq -r '.tool_input.command // empty')

if [ -z "$COMMAND" ]; then
  exit 0
fi

BLOCKED=""

# rm -rf / 또는 rm -rf ~ (루트/홈 전체 삭제만 차단)
if echo "$COMMAND" | grep -qE 'rm\s+(-[a-zA-Z]*r[a-zA-Z]*\s+)+(-[a-zA-Z]*f[a-zA-Z]*\s+)*(\/$|\/\s|~\/$|~\/\s|~\s*$|\$HOME\/$|\$HOME\s)'; then
  BLOCKED="rm -rf on root/home directory"
fi
# rm -rf * (현재 디렉터리 전체 삭제)
if echo "$COMMAND" | grep -qE 'rm\s+(-[a-zA-Z]*r[a-zA-Z]*\s+)+(-[a-zA-Z]*f[a-zA-Z]*\s+)*\*'; then
  BLOCKED="rm -rf * (전체 삭제)"
fi

# DROP TABLE / DROP DATABASE
if echo "$COMMAND" | grep -qiE '\b(DROP\s+(TABLE|DATABASE|SCHEMA))\b'; then
  BLOCKED="DROP TABLE/DATABASE detected"
fi

# git push --force to main/master
if echo "$COMMAND" | grep -qE 'git\s+push\s+.*--force.*\s+(main|master)\b'; then
  BLOCKED="force push to main/master"
fi
if echo "$COMMAND" | grep -qE 'git\s+push\s+-f\s+.*\s+(main|master)\b'; then
  BLOCKED="force push to main/master"
fi

# git reset --hard
if echo "$COMMAND" | grep -qE 'git\s+reset\s+--hard\s+(origin|HEAD~)'; then
  BLOCKED="git reset --hard (destructive)"
fi

if [ -n "$BLOCKED" ]; then
  jq -n --arg reason "$BLOCKED" '{
    hookSpecificOutput: {
      hookEventName: "PreToolUse",
      permissionDecision: "deny",
      permissionDecisionReason: ("⛔ 차단됨: " + $reason)
    }
  }'
  exit 0
fi

exit 0
```

**Step C:** settings.json에 등록

```json
"PreToolUse": [{
  "matcher": "Bash",
  "hooks": [{
    "type": "command",
    "command": "$HOME/.claude/hooks/block-dangerous.sh",
    "timeout": 5
  }]
}]
```

**Step D:** 검증 — Claude Code에서 `rm -rf /` 시도 → 차단 메시지 확인

info-box.success: "디버깅 팁: `claude --debug` 플래그로 실행하면 Hook의 입출력을 실시간 확인 가능. Hook이 0이 아닌 exit code를 반환하면 에이전트에 에러 피드백이 전달됩니다."

info-box.danger: "주의: Hook에서 exit 1을 반환하면 '에러'로 처리되어 에이전트가 재시도할 수 있습니다. '차단'하려면 exit 0 + JSON deny 응답을 사용해야 합니다."

output-tag: `산출물: ~/.claude/hooks/block-dangerous.sh`

- [ ] **Step 3: 실습 4 — 자동 린트 피드백 Hook**

workshop-card: 실습 4 — 자동 린트 피드백 Hook

목표: 파일 수정 후 자동으로 린트/타입체크를 실행하고, 에러를 에이전트에 재주입하여 자동 수정을 유도합니다.

**Step A:** 파일 생성

```bash
touch ~/.claude/hooks/post-edit-context.sh
chmod +x ~/.claude/hooks/post-edit-context.sh
```

**Step B:** 스크립트 작성 (전체 코드)

```bash
#!/bin/bash
# PostToolUse Hook: 파일 수정 후 린트 피드백
INPUT=$(cat /dev/stdin)
FILE_PATH=$(echo "$INPUT" | jq -r '.tool_input.file_path // empty')

if [ -z "$FILE_PATH" ] || [ ! -f "$FILE_PATH" ]; then
  exit 0
fi

EXT="${FILE_PATH##*.}"
FEEDBACK=""

case "$EXT" in
  ts|tsx)
    PROJECT_DIR=$(dirname "$FILE_PATH")
    while [ "$PROJECT_DIR" != "/" ]; do
      if [ -f "$PROJECT_DIR/tsconfig.json" ]; then
        ERRORS=$(cd "$PROJECT_DIR" && npx --yes tsc --noEmit --pretty false 2>&1 | grep "error TS" | head -5)
        if [ -n "$ERRORS" ]; then
          FEEDBACK="TypeScript 에러 발견:\n$ERRORS"
        fi
        break
      fi
      PROJECT_DIR=$(dirname "$PROJECT_DIR")
    done
    ;;
  py)
    if command -v ruff &>/dev/null; then
      ERRORS=$(ruff check "$FILE_PATH" 2>&1 | head -5)
      if [ -n "$ERRORS" ]; then
        FEEDBACK="Ruff 린트 에러:\n$ERRORS"
      fi
    fi
    ;;
esac

if [ -n "$FEEDBACK" ]; then
  echo "$FEEDBACK"
  exit 0
fi

exit 0
```

**Step C:** settings.json에 등록

```json
"PostToolUse": [{
  "matcher": "Edit|Write",
  "hooks": [{
    "type": "command",
    "command": "$HOME/.claude/hooks/post-edit-context.sh",
    "timeout": 30
  }]
}]
```

info-box.concept: "피드백 루프의 핵심: PostToolUse Hook의 stdout은 에이전트의 다음 턴에 자동 주입됩니다. 린트 에러를 출력하면 에이전트가 '아, 타입 에러가 있구나' 하고 스스로 수정합니다. 사람이 개입하지 않아도 되는 자동 교정 루프."

output-tag: `산출물: ~/.claude/hooks/post-edit-context.sh`

- [ ] **Step 4: 실습 5 — 세션 컨텍스트 주입 Hook**

workshop-card: 실습 5 — 세션 컨텍스트 주입 Hook

목표: 세션 시작 시 현재 프로젝트 정보(git 상태, 패키지 정보)를 자동 주입합니다.

**Step A:** 파일 생성

```bash
touch ~/.claude/hooks/session-context.sh
chmod +x ~/.claude/hooks/session-context.sh
```

**Step B:** 스크립트 작성 (전체 코드)

```bash
#!/bin/bash
# SessionStart Hook: 세션 시작 시 프로젝트 컨텍스트 주입
CWD=$(pwd)
CONTEXT=""

if git rev-parse --is-inside-work-tree &>/dev/null 2>&1; then
  BRANCH=$(git branch --show-current 2>/dev/null)
  DIRTY=$(git status --porcelain 2>/dev/null | wc -l | tr -d ' ')
  LAST_COMMIT=$(git log --oneline -1 2>/dev/null)
  CONTEXT="Git: branch=$BRANCH, uncommitted=$DIRTY files, last commit: $LAST_COMMIT"
fi

if [ -f "$CWD/package.json" ]; then
  PKG_NAME=$(jq -r '.name // "unknown"' "$CWD/package.json" 2>/dev/null)
  CONTEXT="$CONTEXT | Node project: $PKG_NAME"
fi

if [ -n "$CONTEXT" ]; then
  jq -n --arg ctx "$CONTEXT" '{
    hookSpecificOutput: {
      hookEventName: "SessionStart",
      additionalContext: $ctx
    }
  }'
fi

exit 0
```

**Step C:** settings.json에 등록

```json
"SessionStart": [{
  "matcher": "startup",
  "hooks": [{
    "type": "command",
    "command": "$HOME/.claude/hooks/session-context.sh",
    "timeout": 5
  }]
}]
```

output-tag: `산출물: ~/.claude/hooks/session-context.sh`

- [ ] **Step 5: 완성된 settings.json 전체 모습**

코드 블록 — 실습 3, 4, 5를 모두 반영한 settings.json:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "$HOME/.claude/hooks/block-dangerous.sh",
            "timeout": 5
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "$HOME/.claude/hooks/post-edit-context.sh",
            "timeout": 30
          }
        ]
      }
    ],
    "SessionStart": [
      {
        "matcher": "startup",
        "hooks": [
          {
            "type": "command",
            "command": "$HOME/.claude/hooks/session-context.sh",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```

info-box.success: "여기까지 완료하면 3개의 Hook이 동작합니다: ① 파괴적 명령 차단 ② 린트 피드백 자동 주입 ③ 세션 시작 시 프로젝트 정보 주입. 이것만으로도 에이전트의 안전성과 정확성이 크게 향상됩니다."

section 닫기.

- [ ] **Step 6: 브라우저에서 Chapter 3 확인**

Run: `open materials/03-하네스-엔지니어링-실전-워크숍.html`

확인: workshop-card 스타일 (파란 그라디언트 배경, 원형 번호 배지), 코드 블록 다크 배경, output-tag 초록, info-box 종류별 색상

- [ ] **Step 7: 커밋**

```bash
git add materials/03-하네스-엔지니어링-실전-워크숍.html
git commit -m "feat(material-03): add Chapter 3 — Hook 시스템 구축 (실습 3-5)"
```

---

## Task 5: Chapter 4 — 3계층: 품질 게이트

**Files:**
- Modify: `materials/03-하네스-엔지니어링-실전-워크숍.html` (Chapter 4 section)

- [ ] **Step 1: Chapter 4 헤더 + Quality Gate 개념**

```html
<section class="chapter" id="ch4">
  <div class="chapter-number">Chapter 4</div>
  <h2>3계층: 품질 게이트</h2>
  <p class="chapter-desc">Hook이 실시간 방어라면, 품질 게이트는 최종 검문소입니다. CI/CD에서 에이전트의 결과물을 자동 검증합니다.</p>
```

term-card:
- **Quality Gate** `<span class="en-note">— 품질 게이트. 소프트웨어가 다음 단계(배포, 머지)로 넘어가기 전에 반드시 통과해야 하는 자동화된 검증 관문</span>`
- **Proposal-Before-Execution** `<span class="en-note">— 제안 후 실행. 에이전트가 먼저 계획을 제안하고, 시스템(또는 사람)이 검증한 뒤에야 실행을 허용하는 패턴</span>`

비교 테이블 — 3가지 게이트 비교:
| 게이트 | 시점 | 차단 대상 | 예시 |
|--------|------|----------|------|
| PreToolUse Hook | 도구 실행 전 | 위험한 명령 | `rm -rf /` 차단 |
| Stop Hook | 에이전트 루프 종료 시 | 미완성 결과 | 테스트 미통과 시 계속 작업 |
| CI Quality Gate | PR/커밋 시 | 비준수 코드 | 타입 에러, 린트 실패 |

info-box.concept: "세 게이트는 겹치는 것이 정상입니다. Defense in Depth(심층 방어) — 하나가 뚫려도 다음이 잡습니다."

- [ ] **Step 2: Tool Gateway 패턴**

h3: Tool Gateway — 도구 호출의 중앙 검문소

Stripe MCP Toolshed 사례: 400+ 도구를 관리하면서도 안전성을 유지하는 비결

코드 블록 — Tool Gateway 개념 (TypeScript):

```typescript
// Tool Gateway: 모든 도구 호출이 이 게이트를 통과
function toolGateway(toolCall: ToolCall, context: AgentContext) {
  // 1. 허용 목록 확인
  if (!allowedTools.includes(toolCall.name)) {
    return { denied: true, reason: "허용되지 않은 도구" };
  }

  // 2. Rate Limiting
  if (rateLimiter.isExceeded(toolCall.name, context.sessionId)) {
    return { denied: true, reason: "호출 횟수 초과" };
  }

  // 3. 위험도별 승인
  const risk = classifyRisk(toolCall);
  if (risk === "dangerous") {
    return { requiresApproval: true, reason: "고위험 작업" };
  }

  return { allowed: true };
}
```

- [ ] **Step 3: 실습 6 — GitHub Actions 품질 게이트**

workshop-card: 실습 6 — GitHub Actions 품질 게이트

목표: 에이전트가 만든 PR을 자동 검증하는 CI 파이프라인을 구성합니다.

전체 YAML:

```yaml
# .github/workflows/agent-qa.yml
name: Agent QA Gate

on:
  pull_request:
    branches: [main]

jobs:
  quality-gate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm ci

      - name: Type Check
        run: npx tsc --noEmit

      - name: Lint
        run: npx eslint . --max-warnings 0

      - name: Unit Tests
        run: npm test -- --coverage

      - name: Check for secrets
        run: |
          # .env, credentials, API keys 체크
          if grep -rn "PRIVATE_KEY\|SECRET_KEY\|password.*=" --include="*.ts" --include="*.js" src/; then
            echo "⛔ 하드코딩된 시크릿 발견!"
            exit 1
          fi

      - name: Bundle Size Check
        run: |
          npm run build
          # 번들 크기 임계값 (500KB)
          SIZE=$(du -sk .next/static | cut -f1)
          if [ "$SIZE" -gt 500 ]; then
            echo "⚠️ 번들 크기 초과: ${SIZE}KB > 500KB"
            exit 1
          fi
```

info-box.warning: "GitHub Actions는 에이전트가 직접 수정할 수 없는 영역입니다. 에이전트가 CI를 우회하려 해도, 리포지토리 설정에서 'Require status checks to pass' 를 켜면 물리적으로 불가능합니다. 이것이 결정론적 강제의 힘."

output-tag: `산출물: .github/workflows/agent-qa.yml`

section 닫기.

- [ ] **Step 4: 브라우저에서 Chapter 4 확인 후 커밋**

Run: `open materials/03-하네스-엔지니어링-실전-워크숍.html`

```bash
git add materials/03-하네스-엔지니어링-실전-워크숍.html
git commit -m "feat(material-03): add Chapter 4 — 품질 게이트 (실습 6)"
```

---

## Task 6: Chapter 5 — 프레임워크별 구현

**Files:**
- Modify: `materials/03-하네스-엔지니어링-실전-워크숍.html` (Chapter 5 section)

- [ ] **Step 1: Chapter 5 헤더 + 4대 프레임워크 비교 테이블**

```html
<section class="chapter" id="ch5">
  <div class="chapter-number">Chapter 5</div>
  <h2>프레임워크별 구현</h2>
  <p class="chapter-desc">Claude Code 외에도 다양한 프레임워크가 하네스를 지원합니다. 각각의 구현 방식을 비교하고, Next.js에서 직접 구현해봅니다.</p>
```

비교 테이블:
| 항목 | Claude Code | OpenAI Agents SDK | LangGraph | Vercel AI SDK |
|------|------------|------------------|-----------|---------------|
| 이벤트 수 | 26+ | 7 | 10+ + interrupt | onStepFinish 등 |
| 차단 방식 | exit code + JSON | raise Exception | interrupt() | stopWhen |
| 규칙 파일 | CLAUDE.md, .claude/rules/ | instructions 파라미터 | State + config | system prompt |
| 서브에이전트 | 자동 재귀 적용 | 수동 전파 | 그래프 암시적 | - |
| 인간 개입 | Hook deny + Notification | Guardrail tripwire | interrupt() + resume | requiresApproval |

- [ ] **Step 2: Claude Code — Hook 기반 하네스**

코드 블록 — Claude Code의 하네스 체계 요약:

```
┌─ CLAUDE.md / .claude/rules/ ─── 확률적 준수 (지시)
│
├─ PreToolUse Hook ──────────── 결정론적 강제 (차단)
├─ PostToolUse Hook ─────────── 피드백 루프 (교정)
├─ Stop Hook ────────────────── 품질 게이트 (완료 조건)
│
└─ Notification Hook ────────── 외부 알림 (Slack 등)
```

info-box.success: "Claude Code의 강점: 서브에이전트(Agent tool)에도 Hook이 자동으로 재귀 적용됩니다. 메인 에이전트의 보안 정책이 하위 에이전트에 자동 전파."

- [ ] **Step 3: OpenAI Agents SDK — Guardrails 3계층**

코드 블록 (Python):

```python
from agents import Agent, InputGuardrail, OutputGuardrail, GuardrailFunctionOutput
from agents.lifecycle import AgentHooksBase

# 1. 입력 가드레일
@input_guardrail
async def check_injection(ctx, agent, input):
    # 프롬프트 인젝션 탐지
    result = await Runner.run(injection_detector, input)
    return GuardrailFunctionOutput(
        output_info=result,
        tripwire_triggered=result.is_injection
    )

# 2. 출력 가드레일
@output_guardrail
async def check_pii(ctx, agent, output):
    # 개인정보 유출 방지
    has_pii = detect_pii(output)
    return GuardrailFunctionOutput(
        output_info={"has_pii": has_pii},
        tripwire_triggered=has_pii
    )

# 3. 라이프사이클 Hook
class MyHooks(AgentHooksBase):
    async def on_tool_start(self, context, agent, tool):
        print(f"도구 실행: {tool.name}")
    async def on_tool_end(self, context, agent, tool, result):
        log_tool_usage(tool.name, result)

agent = Agent(
    name="my-agent",
    input_guardrails=[check_injection],
    output_guardrails=[check_pii],
    hooks=MyHooks()
)
```

term-card:
- **Guardrail** `<span class="en-note">— 가드레일. 도로의 방호벽처럼 에이전트의 입출력을 검사하여 위험한 내용이 통과하지 못하게 막는 안전장치</span>`
- **Tripwire** `<span class="en-note">— 트립와이어. 지뢰의 작동선처럼, 가드레일이 위반을 감지하면 즉시 에이전트 실행을 중단시키는 트리거</span>`

- [ ] **Step 4: LangGraph — interrupt() + Checkpointer**

코드 블록 (Python):

```python
from langgraph.graph import StateGraph
from langgraph.types import interrupt, Command
from langgraph.checkpoint.memory import MemorySaver

def dangerous_action(state):
    # 고위험 작업 전 인간 승인 요청
    human_response = interrupt({
        "action": "delete_records",
        "count": state["record_count"],
        "question": "정말 삭제할까요?"
    })

    if human_response["approved"]:
        return {"result": perform_delete(state)}
    else:
        return {"result": "사용자가 취소함"}

graph = StateGraph(State)
graph.add_node("dangerous", dangerous_action)

# Checkpointer: 중단 지점의 상태를 저장하여 나중에 재개 가능
app = graph.compile(checkpointer=MemorySaver())
```

term-card:
- **interrupt()** `<span class="en-note">— 인터럽트. 에이전트 실행을 일시 중단하고 외부(사람)의 입력을 기다리는 LangGraph의 핵심 메커니즘</span>`

- [ ] **Step 5: Vercel AI SDK — 3계층 미들웨어 + 실습 7**

h3: Vercel AI SDK의 3계층

```
┌─ Layer 1: middleware.ts ──── HTTP 수준 (Rate Limit, 인증)
├─ Layer 2: wrapLanguageModel ── AI 미들웨어 (가드레일, RAG, 캐싱)
└─ Layer 3: streamText callbacks ── 에이전트 루프 (onStepFinish, stopWhen)
```

workshop-card: 실습 7 — Next.js Route Handler 가드레일 구현

목표: Vercel AI SDK의 wrapLanguageModel로 입출력 가드레일을 구현합니다.

코드 블록 — AI 미들웨어 (`lib/ai/middleware.ts`):

```typescript
import { wrapLanguageModel, type LanguageModelV1Middleware } from 'ai';

const safetyMiddleware: LanguageModelV1Middleware = {
  // 요청 가로채기 — 입력 가드레일
  transformParams: async ({ params }) => {
    const lastMessage = params.prompt.at(-1);
    if (lastMessage?.role === 'user') {
      const text = lastMessage.content
        .filter(p => p.type === 'text')
        .map(p => p.text)
        .join('');

      // 프롬프트 인젝션 패턴 감지
      const injectionPatterns = [
        /ignore\s+(previous|above)\s+instructions/i,
        /system\s*:\s*/i,
        /you\s+are\s+now/i,
      ];
      if (injectionPatterns.some(p => p.test(text))) {
        throw new Error('잠재적 프롬프트 인젝션 감지');
      }
    }
    return params;
  },

  // 응답 가로채기 — 출력 가드레일
  wrapGenerate: async ({ doGenerate }) => {
    const result = await doGenerate();
    // PII(개인정보) 마스킹
    if (result.text) {
      result.text = maskPII(result.text);
    }
    return result;
  }
};
```

코드 블록 — Route Handler (`app/api/chat/route.ts`):

```typescript
import { streamText } from 'ai';
import { openai } from '@ai-sdk/openai';
import { safetyMiddleware } from '@/lib/ai/middleware';

export async function POST(req: Request) {
  const { messages } = await req.json();

  const wrappedModel = wrapLanguageModel({
    model: openai('gpt-4o'),
    middleware: safetyMiddleware,
  });

  const result = streamText({
    model: wrappedModel,
    messages,
    maxSteps: 10,
    // Layer 3: 에이전트 루프 콜백
    onStepFinish: async ({ stepType, toolCalls }) => {
      // 매 스텝마다 로깅
      console.log(`Step: ${stepType}, tools: ${toolCalls?.length ?? 0}`);
    },
    stopWhen: async ({ steps }) => {
      // 최대 스텝 초과 시 강제 종료
      return steps.length >= 10;
    },
  });

  return result.toDataStreamResponse();
}
```

output-tag: `산출물: lib/ai/middleware.ts + app/api/chat/route.ts`

- [ ] **Step 6: 프레임워크 선택 가이드**

info-box.insight로 의사결정 가이드:

"**프레임워크 선택 기준:**
- Claude Code로 개발하고 있다면 → CLAUDE.md + Hooks (이 워크숍의 실습 1-5)
- Python 에이전트를 만든다면 → OpenAI Agents SDK Guardrails 또는 LangGraph interrupt()
- Next.js 웹앱에 AI를 넣는다면 → Vercel AI SDK 3계층 (실습 7)
- 여러 에이전트를 동시에 쓴다면 → AGENTS.md (범용) + 각 프레임워크 Hook 조합"

section 닫기.

- [ ] **Step 7: 브라우저에서 Chapter 5 확인 후 커밋**

Run: `open materials/03-하네스-엔지니어링-실전-워크숍.html`

```bash
git add materials/03-하네스-엔지니어링-실전-워크숍.html
git commit -m "feat(material-03): add Chapter 5 — 프레임워크별 구현 (실습 7)"
```

---

## Task 7: Chapter 6 — 실전 사례 분석

**Files:**
- Modify: `materials/03-하네스-엔지니어링-실전-워크숍.html` (Chapter 6 section)

- [ ] **Step 1: Chapter 6 헤더 + Stripe Minions**

```html
<section class="chapter" id="ch6">
  <div class="chapter-number">Chapter 6</div>
  <h2>실전 사례 분석</h2>
  <p class="chapter-desc">대규모 프로덕션에서 하네스를 어떻게 운영하는지, 세 기업의 사례에서 핵심 교훈을 추출합니다.</p>
```

h3: Stripe Minions — 1,300 PR/주의 비밀

blockquote:
> "We built a 6-layer system where each layer adds a different type of safety check. The key insight was curating tools aggressively — we started with 400+ MCP tools but found that careful selection matters more than quantity."
> <cite>— Stripe Engineering Blog, 2025</cite>

6-layer 시스템 diagram:

```
Layer 6: Human Review Gate (고위험 PR만)
Layer 5: CI/CD Quality Gate (자동 테스트)
Layer 4: Conditional Rules (파일별 다른 규칙)
Layer 3: Tool Gateway (MCP Toolshed 400+ 도구 관리)
Layer 2: Context Management (관련 코드만 주입)
Layer 1: Task Decomposition (큰 작업을 작은 단위로)
```

핵심 교훈:
- 도구 수 > 도구 품질: 400개 중 실제 쓰이는 건 소수. 큐레이션이 핵심
- 조건부 규칙: 결제 코드와 테스트 코드에 다른 규칙 적용
- 점진적 신뢰: 처음엔 모든 PR을 사람이 리뷰 → 성공률 높은 패턴만 자동화

- [ ] **Step 2: Manus — 5번의 완전 리라이트**

h3: Manus — 컨텍스트 엔지니어링이 최고 레버리지

blockquote:
> "We've done five complete rewrites of our harness. The single highest-leverage thing was context engineering — not prompt engineering, but engineering what information reaches the model and when."
> <cite>— Manus Team, 2025</cite>

핵심 교훈:
- 프롬프트 엔지니어링 < 컨텍스트 엔지니어링: 무엇을 모델에 보내느냐가 어떻게 보내느냐보다 중요
- 5번 리라이트 = 5번의 근본적 설계 변경: 하네스는 한 번에 완성되지 않음
- 반복 개선의 용기: "이 구조가 틀렸다"고 인정하고 다시 만드는 것이 핵심 역량

- [ ] **Step 3: Atlan — 10-Step 하네스 빌드**

h3: Atlan — 체계적 하네스 구축 로드맵

Atlan의 10-step을 3단계로 요약한 테이블:

| 단계 | Step | 기간 | 내용 |
|------|------|------|------|
| **MVH** (최소 하네스) | 0-2 | 1-2주 | Step 0: 데이터 인증 (어떤 데이터가 안전한가?), Step 1-2: 규칙 파일 + 기본 Hook |
| **설정** | 3-5 | 2-4주 | Step 3: 도구 큐레이션, Step 4: 피드백 루프, Step 5: 권한 체계 |
| **프로덕션** | 6-10 | 4-6주 | Step 6-8: CI/CD, 모니터링, 평가, Step 9-10: 보안 감사, 지속 개선 |

info-box.insight: "Atlan의 핵심 통찰 — Step 0 (데이터 인증)이 가장 중요. 에이전트가 접근하는 데이터가 안전한지 먼저 검증하지 않으면, 아무리 좋은 Hook도 소용없습니다."

- [ ] **Step 4: 규모별 적용 가이드 + section 닫기**

h3: 우리 팀에 적용하기

비교 테이블:
| 규모 | 필수 | 권장 | 과잉 |
|------|------|------|------|
| 1인 개발 | CLAUDE.md, 기본 Hook 3개 | AGENTS.md | CI Gate, 조건부 규칙 |
| 소규모 (2-5인) | CLAUDE.md, Hook, AGENTS.md | CI Gate, 피드백 루프 | 6-layer 시스템 |
| 중규모 (5-20인) | 전체 3계층 | 조건부 규칙, Tool Gateway | - |
| 대규모 (20인+) | Stripe급 6-layer | MCP Toolshed, 자동 평가 | - |

info-box.success: "1인 개발자라면 이 워크숍의 실습 1-5만으로 충분합니다. CLAUDE.md + 3개 Hook = 최소 하네스(MVH). 팀이 커지면 계층을 점진적으로 추가하세요."

section 닫기.

- [ ] **Step 5: 브라우저에서 Chapter 6 확인 후 커밋**

```bash
git add materials/03-하네스-엔지니어링-실전-워크숍.html
git commit -m "feat(material-03): add Chapter 6 — 실전 사례 분석"
```

---

## Task 8: Chapter 7 — 안티패턴 & 보안

**Files:**
- Modify: `materials/03-하네스-엔지니어링-실전-워크숍.html` (Chapter 7 section)

- [ ] **Step 1: Chapter 7 헤더 + 3대 안티패턴**

```html
<section class="chapter" id="ch7">
  <div class="chapter-number">Chapter 7</div>
  <h2>안티패턴 & 보안</h2>
  <p class="chapter-desc">하네스를 만들 때 흔히 저지르는 실수와, 에이전트 시스템의 보안 위협을 학습합니다.</p>
```

h3: 3대 안티패턴

**1. Tool Bloat**

info-box.danger: "Anti-Pattern: Tool Bloat — 도구를 20개 이상 등록하면 모델의 도구 선택 정확도가 급감합니다. Vercel은 AI SDK의 도구를 80% 제거하고 나서야 성능이 개선되었습니다."

compare-grid:
- before: "사용할 수 있는 도구가 많을수록 좋다" → 42개 도구 등록 → 모델이 잘못된 도구 선택 빈번
- after: "필요한 도구만 최소한으로" → 8개 핵심 도구 + prepareStep으로 동적 제공 → 정확도 향상

**2. All-or-Nothing Autonomy**

info-box.danger: "Anti-Pattern: All-or-Nothing Autonomy — 에이전트에게 모든 권한을 주거나, 아예 안 주거나. 중간이 없으면 Replit DROP DATABASE 같은 사고가 발생합니다."

**3. Hook Proliferation**

info-box.danger: "Anti-Pattern: Hook Proliferation — Hook을 30개 이상 만들면 기술 부채가 됩니다. AGENTS.md 60줄 이내가 권장인 것처럼, Hook도 핵심 위험만 차단하는 최소 세트를 유지하세요."

- [ ] **Step 2: Tiered Permission 설계**

h3: Tiered Permission — 3단계 권한 체계

term-card:
- **Tiered Permission** `<span class="en-note">— 단계별 권한. 도구/작업의 위험도에 따라 자동 승인, 화이트리스트, 명시적 승인의 3단계로 나누는 권한 체계</span>`

테이블:
| 단계 | 위험도 | 정책 | 예시 |
|------|--------|------|------|
| Safe | 낮음 | 자동 승인 | 파일 읽기, grep, git status |
| Moderate | 중간 | 화이트리스트 | 파일 수정, npm install, git commit |
| Dangerous | 높음 | 명시적 승인 | 파일 삭제, DB 쿼리, git push, 외부 API 호출 |

코드 블록 — Claude Code의 permission 설정:

```json
{
  "permissions": {
    "allow": [
      "Bash(grep:*)",
      "Bash(git status)",
      "Bash(git diff)",
      "Read",
      "Bash(npm test)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(curl * | bash)"
    ]
  }
}
```

- [ ] **Step 3: OWASP Top 10 for Agentic Applications**

h3: 보안 위협 — OWASP Top 10 for Agentic Applications (2025)

주요 항목 테이블 (하네스와 관련 있는 것 위주):
| # | 위협 | 설명 | 하네스 대응 |
|---|------|------|------------|
| 1 | Prompt Injection | 악의적 입력으로 에이전트 행동 조작 | 입력 가드레일 + wrapLanguageModel |
| 2 | Excessive Agency | 에이전트에게 과도한 권한 부여 | Tiered Permission + Tool Gateway |
| 3 | Insecure Tool Use | 도구 호출의 검증 부재 | PreToolUse Hook + allowlist |
| 5 | Improper Output Handling | 에이전트 출력의 미검증 | 출력 가드레일 + PostToolUse |
| 8 | Unbounded Consumption | 무한 루프 / 과도한 리소스 사용 | stopWhen + maxSteps + timeout |

info-box.insight: "멀티 에이전트 비구성성 (arXiv 2505.02077): 개별 에이전트가 안전해도, 조합하면 예측 불가능한 위험이 발생합니다. 에이전트 A가 B에게 위임하고, B가 다시 A에게 위임하는 무한 루프가 대표적. 각 에이전트의 하네스가 독립적으로 강제되어야 합니다."

section 닫기.

- [ ] **Step 4: 브라우저에서 Chapter 7 확인 후 커밋**

```bash
git add materials/03-하네스-엔지니어링-실전-워크숍.html
git commit -m "feat(material-03): add Chapter 7 — 안티패턴 & 보안"
```

---

## Task 9: Chapter 8 — 나만의 하네스 설계

**Files:**
- Modify: `materials/03-하네스-엔지니어링-실전-워크숍.html` (Chapter 8 section)

- [ ] **Step 1: Chapter 8 헤더 + 워크시트 A (위험 분류표)**

```html
<section class="chapter" id="ch8">
  <div class="chapter-number">Chapter 8</div>
  <h2>나만의 하네스 설계</h2>
  <p class="chapter-desc">지금까지 배운 모든 것을 자신의 프로젝트에 적용합니다. 워크시트를 채우며 나만의 하네스를 설계하세요.</p>
```

worksheet 컴포넌트:

```html
<div class="worksheet">
  <h4>워크시트 A: 프로젝트 위험 분류표</h4>
```

빈 테이블 (학습자가 채울 용도):
| 도구/작업 | 위험도 (Safe/Moderate/Dangerous) | 차단 방법 | Hook 필요? |
|----------|------|------|------|
| 파일 읽기 | Safe | - | ☐ |
| 파일 수정 | Moderate | 린트 피드백 | ☐ |
| DB 쿼리 | Dangerous | PreToolUse 차단 | ☑ |
| (내 도구 1) | | | ☐ |
| (내 도구 2) | | | ☐ |

- [ ] **Step 2: 워크시트 B (3계층 체크리스트) + 워크시트 C (CLAUDE.md 템플릿)**

worksheet B:

```html
<div class="worksheet">
  <h4>워크시트 B: 3계층 설계 체크리스트</h4>
```

checklist:
```
1계층 — Constraint Harness (규칙)
☐ CLAUDE.md 작성 완료 (60줄 이내)
☐ .claude/rules/ 분리 완료 (주제별)
☐ AGENTS.md 작성 완료 (범용)
☐ 금지사항 목록 작성

2계층 — Feedback Loop (Hook)
☐ PreToolUse: 파괴적 명령 차단
☐ PostToolUse: 린트/타입체크 피드백
☐ SessionStart: 프로젝트 컨텍스트 주입
☐ Hook 디버깅 테스트 완료

3계층 — Quality Gate
☐ CI 파이프라인에 타입체크 추가
☐ CI 파이프라인에 린트 추가
☐ CI 파이프라인에 테스트 추가
☐ PR 필수 체크 설정 (Branch Protection)
```

worksheet C:

```html
<div class="worksheet">
  <h4>워크시트 C: CLAUDE.md 초안 템플릿</h4>
```

```markdown
# [프로젝트명]

## 빌드/실행
- `[빌드 명령]`
- `[실행 명령]`
- `[테스트 명령]`

## 코드 스타일
- [언어/프레임워크 규칙]
- [네이밍 규칙]
- [파일 구조 규칙]

## 금지사항
- [절대 하면 안 되는 것 1]
- [절대 하면 안 되는 것 2]
- [절대 하면 안 되는 것 3]

## 워크플로우
- [커밋 전 반드시 할 것]
- [PR 생성 시 규칙]
```

- [ ] **Step 3: 워크시트 D (Hook 설정 템플릿) + 도입 로드맵**

worksheet D:

```html
<div class="worksheet">
  <h4>워크시트 D: Hook 설정 템플릿</h4>
```

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "$HOME/.claude/hooks/[내-차단-스크립트].sh",
            "timeout": 5
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "[Edit|Write 또는 내 도구]",
        "hooks": [
          {
            "type": "command",
            "command": "$HOME/.claude/hooks/[내-피드백-스크립트].sh",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

h3: 도입 로드맵

step-indicator 컴포넌트를 활용한 타임라인:

**Week 1-2: MVH (최소 하네스)**
- CLAUDE.md + AGENTS.md 작성
- block-dangerous.sh Hook 설치
- 기본 사용 시작

**Week 3-4: 피드백 루프**
- PostToolUse 린트 피드백 Hook
- SessionStart 컨텍스트 주입
- 실패 발생 시 래칫 패턴으로 규칙 추가

**Week 5+: 프로덕션**
- CI Quality Gate 구축
- 팀 공유 (AGENTS.md, 공통 Hook)
- 정기 하네스 리뷰 (월 1회)

info-box.success: "시작이 반입니다. Week 1의 CLAUDE.md + Hook 1개만으로도 에이전트의 안전성이 극적으로 향상됩니다. 완벽한 하네스를 만들려고 기다리지 말고, MVH부터 시작하세요."

section 닫기.

- [ ] **Step 4: 브라우저에서 Chapter 8 확인 후 커밋**

```bash
git add materials/03-하네스-엔지니어링-실전-워크숍.html
git commit -m "feat(material-03): add Chapter 8 — 나만의 하네스 설계"
```

---

## Task 10: Chapter 9 — 참고자료 + Footer

**Files:**
- Modify: `materials/03-하네스-엔지니어링-실전-워크숍.html` (Chapter 9 section + footer)

- [ ] **Step 1: Chapter 9 참고자료 전체 작성**

```html
<section class="chapter" id="ch9">
  <div class="chapter-number">Chapter 9</div>
  <h2>참고자료</h2>
  <p class="chapter-desc">이 교육자료의 근거가 된 자료 목록입니다. 모두 공식 문서 또는 검증된 출처입니다.</p>
```

h3: 공식 문서
- Claude Code — Hooks Reference (Anthropic 공식) — https://docs.anthropic.com/en/docs/claude-code/hooks
- Claude Code — Best Practices (Anthropic 공식) — https://docs.anthropic.com/en/docs/claude-code/best-practices
- OpenAI Agents SDK — Lifecycle Hooks (OpenAI 공식) — https://openai.github.io/openai-agents-python/
- Vercel AI SDK — Language Model Middleware (Vercel 공식) — https://ai-sdk.dev/docs/ai-sdk-core/middleware
- LangGraph — Interrupts (LangChain 공식) — https://langchain-ai.github.io/langgraph/concepts/human_in_the_loop/

h3: 기술 블로그 및 가이드
- Addy Osmani — Agent Harness Engineering (Google Chrome 팀) — https://addyosmani.com/blog/agent-harness-engineering/
- Birgitta Bockeler — Harness Engineering (Martin Fowler / Thoughtworks) — https://martinfowler.com/articles/harness-engineering.html
- OpenAI — Harness Engineering: Leveraging Codex (OpenAI 공식 블로그) — https://openai.com/index/harness-engineering/
- Stripe — Building an Agentic System (Stripe Engineering) — https://stripe.com/blog/how-we-built-an-ai-agent
- HumanLayer — CLAUDE.md Guide — https://humanlayer.dev/blog/claudemd
- Atlan — AI Agent Harness Anti-Patterns — https://atlan.com/know/agent-harness-failures-anti-patterns/

h3: 학술 논문
- Open Challenges in Multi-Agent Security (arXiv 2505.02077) — https://arxiv.org/abs/2505.02077
- OWASP Top 10 for LLM/Agentic Applications (2025) — https://owasp.org/www-project-top-10-for-large-language-model-applications/
- Robust Intelligence — Production LLM Vulnerability Study (2025)

h3: 관련 교육자료
- 01-agent-개발-기본원리.html — AI 에이전트 개발의 기본 개념
- 02-hook-완전정복.html — Hook 시스템 심화 학습

각 항목에 source-tag 클래스로 출처 유형 표시: `<span class="source-tag anthropic">Anthropic</span>` 등.

section 닫기.

- [ ] **Step 2: Footer 확인 및 마무리**

Footer는 Task 1에서 이미 작성했으므로, 최종 형태 확인:

```html
  <footer style="text-align:center; padding:40px 0 60px; color:#94a3b8; font-size:0.85rem;">
    <p>하네스 엔지니어링 실전 워크숍</p>
    <p>2026.05 | 신뢰성 있는 공식 문서 및 학술 자료 기반</p>
  </footer>
</div>
</body>
</html>
```

- [ ] **Step 3: 브라우저에서 전체 확인 후 커밋**

Run: `open materials/03-하네스-엔지니어링-실전-워크숍.html`

```bash
git add materials/03-하네스-엔지니어링-실전-워크숍.html
git commit -m "feat(material-03): add Chapter 9 — 참고자료"
```

---

## Task 11: 최종 검수 및 완성

**Files:**
- Modify: `materials/03-하네스-엔지니어링-실전-워크숍.html` (전체 검수)

- [ ] **Step 1: HTML 유효성 검사**

Run: `grep -c '<section' materials/03-하네스-엔지니어링-실전-워크숍.html` → 9 확인
Run: `grep -c '</section>' materials/03-하네스-엔지니어링-실전-워크숍.html` → 9 확인
Run: `grep -c 'workshop-card' materials/03-하네스-엔지니어링-실전-워크숍.html` → 7 이상 확인

- [ ] **Step 2: 링크 및 앵커 확인**

Run: `grep -oP 'href="#ch\d+"' materials/03-하네스-엔지니어링-실전-워크숍.html | sort`
Run: `grep -oP 'id="ch\d+"' materials/03-하네스-엔지니어링-실전-워크숍.html | sort`

두 결과가 일치해야 함 (ch1~ch9).

- [ ] **Step 3: 반응형 확인**

브라우저에서 열고:
1. 데스크톱 (960px+): 정상 레이아웃 확인
2. 모바일 (< 768px): DevTools로 축소하여 compare-grid가 1열로 변환되는지 확인
3. 인쇄 미리보기: Cmd+P로 인쇄 스타일 확인

- [ ] **Step 4: 컨텐츠 품질 검수**

확인 항목:
- [ ] 영어 전문용어에 `<span class="en">` 또는 `<span class="en-note">` 적용되었는가
- [ ] 모든 코드 블록에 `.lang-label` 표시가 있는가
- [ ] info-box 5종(concept/warning/danger/success/insight)이 적절히 사용되었는가
- [ ] 실습 번호가 1-7로 연속되는가
- [ ] 참고자료의 모든 URL이 유효한가

- [ ] **Step 5: 최종 커밋**

```bash
git add materials/03-하네스-엔지니어링-실전-워크숍.html
git commit -m "feat: complete material 03 — 하네스 엔지니어링 실전 워크숍"
```

- [ ] **Step 6: curriculum/outline.md 업데이트**

curriculum/outline.md에서 Level 1 #3~#5 관련 항목의 상태를 업데이트:

```bash
git add curriculum/outline.md
git commit -m "docs: update curriculum outline — mark material 03 as complete"
```

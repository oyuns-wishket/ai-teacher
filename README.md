# ai-teacher

AI 에이전트 학습 & 사내 교육 커리큘럼

## 구조

| 디렉토리 | 용도 |
|----------|------|
| `notes/` | 학습 노트 (자유 형식 md) |
| `curriculum/` | 교육 커리큘럼 설계 |
| `materials/` | 완성된 교육자료 (standalone HTML) |

## 교육자료

| # | 주제 | 파일 |
|---|------|------|
| 01 | Agent 개발 기본원리 | `materials/01-agent-개발-기본원리.html` |
| 02 | Hook 완전 정복 | `materials/02-hook-완전정복.html` |

## 실행

```bash
open materials/01-agent-개발-기본원리.html
```

## Claude Code 연동

이 레포에서 `claude`를 실행하면 `CLAUDE.md`와 `.claude/rules/`가 자동 적용되어 교육자료 디자인 시스템이 일관되게 유지됩니다.

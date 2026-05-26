# ai-teacher — AI 에이전트 학습 & 교육 커리큘럼

개인 학습 정리와 사내 교육 커리큘럼을 함께 관리하는 레포지토리입니다.

## 프로젝트 구조

```
ai-teacher/
├── notes/                  ← 학습 노트 (자유 형식 md, 빠르게 정리)
│   ├── claude-code/        ← Claude Code 관련
│   ├── codex/              ← OpenAI Codex 관련
│   ├── agent-patterns/     ← 에이전트 아키텍처/패턴
│   └── use-cases/          ← 도메인/업무별 적용 사례
├── curriculum/             ← 교육 커리큘럼 설계
│   └── outline.md          ← 전체 로드맵
├── materials/              ← 완성된 교육자료 (standalone HTML)
│   ├── 01-*.html
│   └── 02-*.html
├── CLAUDE.md
└── .claude/rules/
```

## 워크플로우

1. `notes/`에 학습 내용을 마크다운으로 빠르게 정리
2. 패턴이 보이면 `curriculum/outline.md`에 커리큘럼 항목으로 추가
3. 교육자료로 만들 준비가 되면 `materials/`에 HTML로 제작

## 빌드/실행

정적 HTML. 브라우저에서 직접 열면 됩니다.

```bash
open materials/01-agent-개발-기본원리.html
```

## 컨벤션

- notes: 자유 형식 마크다운. 완성도보다 속도 우선.
- materials: standalone HTML. 외부 의존성 없이 `<style>` 인라인. 번호 프리픽스로 순서 관리.
- 파일명: 한국어 허용, 공백 대신 하이픈
- 참고자료: 공식 문서, 학술 논문(arXiv), 신뢰할 수 있는 블로그만 사용. 출처 URL 필수.

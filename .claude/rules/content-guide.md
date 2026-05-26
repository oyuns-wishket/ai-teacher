# 콘텐츠 작성 규칙

교육자료의 내용 작성 시 따르는 규칙입니다.

## 언어

- 기본 언어: 한국어
- 영어 원어/전문 용어: `<span class="en">` 또는 `<span class="en-note">` 사용
- 예: `<span class="en-note">— 직역: 마구. AI 맥락: 모델을 감싸서 제어하는 인프라</span>`

## 구조

- 각 교육자료는 Chapter 단위로 구성
- 마지막 Chapter는 반드시 참고자료
- TOC(목차)를 Hero 바로 아래에 배치

## 소스/참고자료

- 공식 문서 (Anthropic, OpenAI, Vercel 등)
- 학술 논문 (arXiv 등 peer-reviewed)
- 신뢰할 수 있는 기술 블로그
- 각 소스에 `.source-tag`로 출처 유형 표시 (anthropic, openai, paper, research)
- URL은 반드시 포함

## 내용 원칙

- 실무에서 바로 활용할 수 있는 실전 중심
- 추상적 설명보다 코드 예시, 다이어그램, 비교표 활용
- "오해 vs 현실" 패턴으로 흔한 실수 교정
- 지나치게 긴 문단 지양 — 테이블, 리스트, 카드로 분리

## 반응형 & 인쇄

- `@media (max-width: 768px)` 반응형 필수
- `@media print` 인쇄 대응 포함

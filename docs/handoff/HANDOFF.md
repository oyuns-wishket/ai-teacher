# HANDOFF — ai-teacher

> 마지막 갱신: 2026-07-09 (D1 진단 v3 — 기초 3명 반영해 07·08 갱신·배포 완료)

## 다음 액션

1. **07·08 가다듬기 이터레이션** — 사용자 검수 피드백 받아 반영.
2. **박성희 실장 인터뷰 보강** → SSOT(`curriculum/cntv-d1-인터뷰-진단.md`)·07·08 업데이트
3. 교육 시작 — **공통 교육 2회(오후 1~2시간) + 9월까지 상주·DM 개별 후속 지원**(v3에서 확정). 신규 교육 항목: AI 학습·데이터 보안 가이드라인(사실 검증 후 자료화, 팀 공유).
4. 대기: 시청률 폼 샘플(이현주, 보안 확인) · GPT 투트랙 설명 여부(기초 3명 클로드 미사용).

## 결정·컨텍스트

- **v3(07-09): 기초 3명(박소연·이현주·김현지) 인터뷰 반영.** 진단 진화 — "전원 자동화"가 아니라 **"활용 1~4단 편차 크다 + 원리 이해 부족(0~2단)은 전원 공통"**. 격차 시각화는 평균 마커 2개 → **분포 밴드 2개**로 교체. 유형 6종(검증 제한형 추가), 한계 6(보안 추가).
- **v2(07-09): 실명 개인 카드 전면 폐기** → 조직 단위 진단(개인 부끄러움 방지). **톤 = 감성 레벨 3**: 사실 중심, 시적 표현 금지.
- **진단→교육모듈 매핑 테이블**이 커리큘럼 예고편 역할 ("리포트대로 교육").
- 개인별 상세는 SSOT(§3·§8)와 08 암호화 평가서에 보존.
- 플랜: `docs/plans/2026-07-08-cntv-d1-진단리포트.md` (상단 v2 개정 노트 참조).
- 배포: push → GitHub Pages(https://oyuns-wishket.github.io/ai-teacher/) + Vercel(https://ai-teacher-omega-orpin.vercel.app) 자동.

## 08 평가 페이지 (대외비 · 암호화)

- `materials/08-cntv-ax-level-assessment.html` = **AES-256-GCM 암호화 배포본** (상급자 전용: 레벨 체계 0~5단 + 개인별 레벨·평가). index.html에 링크 없음 — URL 직접 전달.
- 평문 소스·셸·빌더는 `materials/_private/`(**gitignore, 이 맥북 로컬 전용**): `08-src.html`, `shell.html`, `build.js`.
- 수정 방법: `_private/08-src.html` 편집 → `node materials/_private/build.js materials/_private/08-src.html materials/08-cntv-ax-level-assessment.html <비밀번호>` → 커밋.
- 비밀번호는 커밋·문서에 남기지 않음(사용자가 보관). 인증은 sessionStorage(브라우저 닫으면 재입력).
- 레포 public 유지는 사용자 결정(위협 모델 = 참가자가 자기 평가 열람 방지 수준, 레포 접근 없음).

## 실행 노트 (지도)

> **`curriculum/cntv-ax-실행노트.md`가 이 프로젝트의 지도다.** 작업 시작 전 먼저 읽을 것 — 확정 결정(취조 로그)·복병 마킹·리뷰 대기·교훈이 거기에 축적된다. (프로토콜: 취조식 인터뷰 → 복병 마킹 → 완료 후 리뷰 + 이해도 퀴즈 5문항)

07-09 취조로 확정: 08 열람=이사+팀장·실장(본인 평가 문구 순화 완료) / 07 정본=Vercel URL 슬랙 공지 / 교육=클로드로만 / 박성희=보강 불필요·요약 기반 확정(결과물 2~3단·이해 하, 교육봇은 위시켓 제작 — 오독 정정).

## 미해결·블로커

- 크로스세션 메모리: `~/.claude/projects/-Users-manager-ai-teacher/memory/`(맥북 로컬 — 다른 맥이면 이 HANDOFF + 실행노트 + SSOT로 충분).










<!-- AUTO-CRUMB
branch: main | head: 159829c
changed: notes/2026-06-29-claude-governance-learning.md 
-->

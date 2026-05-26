# 디자인 시스템 규칙

교육자료 HTML 생성/수정 시 반드시 이 규칙을 따릅니다.
레퍼런스: `ai-agent-harness-hooks-education.html`

## 폰트

- 본문: `'Pretendard', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif`
- 코드: `'JetBrains Mono', 'Fira Code', 'Consolas', monospace`
- 픽셀/장식 폰트 사용 금지 (Press Start 2P, VT323 등)

## 컬러 팔레트

```css
:root {
  --bg: #fafafa;
  --surface: #ffffff;
  --text: #1a1a2e;
  --text-secondary: #555;
  --accent: #2563eb;
  --accent-light: #dbeafe;
  --accent-dark: #1d4ed8;
  --green: #059669;    --green-light: #d1fae5;
  --orange: #d97706;   --orange-light: #fef3c7;
  --red: #dc2626;      --red-light: #fee2e2;
  --purple: #7c3aed;   --purple-light: #ede9fe;
  --border: #e5e7eb;
  --code-bg: #1e293b;  --code-text: #e2e8f0;
}
```

## 레이아웃

- 최대 너비: `960px`, 중앙 정렬 `.container`
- Hero: `linear-gradient(135deg, #1e293b, #0f172a)`, 흰색 텍스트
- TOC: 흰색 카드, `margin-top: -32px`로 Hero와 겹침
- Chapter: 흰색 카드, `border-radius: 12px`, `padding: 48px 40px`
- 본문: `font-size: 16px`, `line-height: 1.8`

## 컴포넌트

### 정보 박스 (5종)
- `.info-box.concept` — 파란색, 개념 설명
- `.info-box.warning` — 주황색, 주의사항
- `.info-box.danger` — 빨간색, 위험/금지
- `.info-box.success` — 초록색, 성공/팁
- `.info-box.insight` — 보라색, 인사이트/연구 결과

### 용어 카드 `.term-card`
- 파란색 왼쪽 보더, `.term` + `.meaning` 구조

### 코드 블록 `<pre><code>`
- 배경: `#1e293b`, 우측 상단에 `.lang-label`
- 코드 하이라이팅: `.code-comment`(초록), `.code-keyword`(보라), `.code-string`(노랑), `.code-fn`(파랑)

### 비교 카드 `.compare-grid`
- 2컬럼 그리드, `.before`(빨간 배경) + `.after`(초록 배경)

### 파일 트리 `.file-tree`
- 다크 배경, `.folder`(파랑), `.file`(흰색), `.desc`(초록 이탤릭)

### 인용문 `<blockquote>`
- 왼쪽 보더, `<cite>` 태그로 출처

## 금지사항

- Y2K, 레트로, 픽셀, 터미널 크롬 스타일 금지
- 과도한 애니메이션 금지
- 장식보다 가독성 우선

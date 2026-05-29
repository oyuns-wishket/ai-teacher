# Playwright 종합 분석 보고서

> 분석일: 2026-05-29
> 대상: 로컬 프로젝트 9개 전수조사 + Playwright 공식 문서 + 업계 검증 자료 교차 검증

---

## 1. 분석 대상 프로젝트 현황

### 전체 요약

| 프로젝트 | 테스트 수 | 코드량 | 로그인 처리 | CI 연동 | Playwright 버전 |
|---|---|---|---|---|---|
| gj-erp/erp | 37개 (7 spec + 1 setup) | 841줄 | setup + storageState | GitHub Actions | ^1.60.0 |
| gj-erp/commerce | 9개 | 769줄 | 없음 | 없음 | ^1.60.0 |
| kf_salesforce | 111개 | ~50,000줄 | SF CLI frontdoor URL | GitHub Actions | ^1.58.2 |
| som-erp/web | 113+개 | ~53,000줄 | setup + storageState | **없음** | ^1.58.2 |
| cura-smartlib/web | 28개 (27 spec + 1 setup) | 3,244줄 | setup + storageState | 없음 | ^1.58.2 |
| cura-smartlib2/web | 7개 (6 spec + 1 setup) | 454줄 | setup + storageState | 없음 | ^1.58.2 |
| aidp-pms (aidp-erp 내) | 8개 | 1,611줄 | 없음 | 없음 | ^1.50.1 |
| aidp-pms (단독) | 2개 | 88줄 | 없음 | 없음 | ^1.50.1 |
| tpakorea-main | **0개** | 0줄 | 없음 | 없음 | ^1.58.2 |

### 프로젝트별 상세 설정

#### gj-erp/erp (현재 프로젝트)

```
testDir          : ./e2e
baseURL          : http://localhost:3210
webServer        : npx next start -p 3210 (빌드된 앱)
fullyParallel    : true
retries          : 0 (로컬) / 2 (CI)
workers          : auto (로컬) / 1 (CI)
reporter         : html
trace            : on-first-retry
screenshot       : 없음
video            : 없음
storageState     : e2e/.auth/user.json
인증 방식        : UI 로그인 (master@gj.co.kr)
env 필수         : E2E_PASSWORD
```

**테스트 파일 목록:**

| 파일 | 줄 수 | 내용 |
|---|---|---|
| auth.setup.ts | 11 | 로그인 → 세션 저장 |
| smoke.spec.ts | 18 | 기본 라우트 접근 |
| smoke-all.spec.ts | 31 | 전체 라우트 스모크 |
| navigation.spec.ts | 33 | 사이드바 네비게이션 |
| accessibility.spec.ts | 36 | axe-core 접근성 검사 |
| rbac-login.spec.ts | 43 | 역할별 로그인 (6계정) |
| settings-pages.spec.ts | 146 | 설정 10페이지 렌더링 |
| settings-interactions.spec.ts | 523 | 설정 10페이지 인터랙션 |

#### som-erp/web (가장 큰 테스트 스위트)

```
testDir          : ./e2e
baseURL          : localhost:3000 (env 가능)
webServer        : pnpm dev --filter=web
reuseExistingServer: true (항상 — CI에서도)
storageState     : .auth/user.json
인증 방식        : UI 로그인 (test@somgroup.com)
```

- 113+ spec 파일, 약 53,000줄
- CI 연동 없음 (수동 실행만)
- 가장 큰 파일: `qa-items.v2.spec.ts` (1,330줄)

#### kf_salesforce (특수 인증)

```
testDir          : ./tests/e2e
baseURL          : Salesforce sandbox URL
fullyParallel    : false
workers          : 1 (항상 직렬)
viewport         : 1920x1080
actionTimeout    : 15,000ms
navigationTimeout: 60,000ms
인증 방식        : SF CLI execSync → frontdoor URL
```

- 111 spec 파일, 약 50,000줄
- GitHub Actions 연동 (hermes-e2e-test.yml)
- storageState 미사용 (매번 frontdoor URL로 인증)

#### cura-smartlib/web (우선순위 기반 구성)

```
storageState     : .playwright/.auth/user.json
인증 방식        : UI 로그인 (hongjoo@wishket.com)
screenshot       : only-on-failure
```

- 테스트 파일에 p0/p1 우선순위 접두사 사용
- `playwright-core`를 런타임 의존성으로 사용 (PDF 렌더링 서버 사이드)

---

## 2. 발견한 문제점

### 2.1 프로젝트 간 비일관성

| 항목 | gj-erp | som-erp | cura-smartlib | cura-smartlib2 |
|---|---|---|---|---|
| 세션 파일 경로 | `e2e/.auth/` | `.auth/` | `.playwright/.auth/` | `.playwright/.auth/` |
| 비밀번호 처리 | env 필수 | env (기본값 있음) | env (기본값 있음) | **코드에 하드코딩** |
| webServer 재사용 | CI에서만 새로 시작 | **항상 재사용** | CI에서만 새로 시작 | CI에서만 새로 시작 |

**세션 파일 경로가 3가지 패턴으로 분산되어 있음.**

### 2.2 gj-erp 고유 문제

#### 문제 1: trace가 로컬에서 생성되지 않음

```ts
// 현재 설정
trace: "on-first-retry"   // 재시도할 때만 trace 생성
retries: 0                 // 로컬에서 재시도 안 함
// → 로컬에서 실패해도 trace 파일이 없어서 원인 분석 불가
```

#### 문제 2: 고정 시간 대기 사용

```ts
// auth.setup.ts 10행
await page.waitForTimeout(5_000)  // 서버 응답과 무관하게 항상 5초 대기
```

- 서버가 1초면 충분해도 4초 낭비
- 서버가 6초 걸리면 테스트 실패
- 조건 기반 대기(`expect().toHaveURL()`)가 바로 다음 줄에 있어서 중복

#### 문제 3: 깨진 테스트 방치

| 파일 | 항상 실패하는 이유 |
|---|---|
| rbac-login.spec.ts (6개) | `gj-*@wishket.com` 계정이 DB에 없음 |
| accessibility.spec.ts (2개) | axe-core 접근성 위반 발생 |
| smoke-all.spec.ts (1개) | `/specs` 라우트 404 |
| navigation.spec.ts (1개) | 사이드바 셀렉터가 메뉴 리팩토링 이후 불일치 |

**항상 실패하는 테스트 10개가 전체 신뢰도를 떨어뜨림.**

#### 문제 4: CSS 셀렉터 의존

```ts
// 현재 — HTML 구조에 의존 (디자인 변경 시 깨짐)
page.locator('table tbody tr').last().locator('td').nth(2).locator('input')
page.locator('button').filter({ has: page.locator('svg.lucide-save') })
page.locator('[data-slot="card"]').nth(1)

// 권장 — 사용자 관점 (디자인 변경에 강함)
page.getByRole('row').last().getByRole('textbox').first()
page.getByRole('button', { name: '저장' })
```

#### 문제 5: POM/Fixture 패턴 부재

- 9개 프로젝트 전부 Page Object Model 미사용
- 모든 locator가 테스트 파일 안에 인라인으로 작성됨
- 같은 페이지 접근 코드가 여러 테스트에서 반복

#### 문제 6: npm script 미등록

```json
// 현재 package.json — E2E 관련 스크립트 없음
// npx playwright test를 직접 타이핑해야 함
```

#### 문제 7: 스크린샷/비디오 미설정

```ts
// 현재
screenshot: 미설정 (default: off)
video: 미설정 (default: off)
// → 실패 시 시각적 증거 없음
```

---

## 3. 모범 사례 분석 (공식 문서 + 업계 자료)

### 3.1 요소 찾기 우선순위

Playwright 공식 문서가 정한 안정성 순서:

```
1. getByRole()        — 사용자가 인식하는 역할 (button, heading, textbox 등)
2. getByText()        — 화면에 보이는 텍스트
3. getByLabel()       — 폼 필드의 라벨
4. getByPlaceholder() — 입력칸 안내 문구
5. getByAltText()     — 이미지 대체 텍스트
6. getByTitle()       — title 속성
7. getByTestId()      — 테스트 전용 data 속성
8. locator()          — CSS/XPath (최후의 수단)
```

> "XPath and CSS selectors can be tied to the DOM structure or implementation. These selectors can break when the DOM structure changes."
> — Playwright Locators 문서

### 3.2 Web-First Assertion

```ts
// 틀린 패턴 — 즉시 평가 (한 번만 확인)
expect(await page.getByText('환영').isVisible()).toBe(true)

// 맞는 패턴 — 자동 재시도 (기본 5초간 반복 확인)
await expect(page.getByText('환영')).toBeVisible()
```

> "Use web-first assertions instead of manual wait/timeout patterns."
> — Playwright Best Practices

### 3.3 Soft Assertion

치명적이지 않은 확인은 `expect.soft()`로 — 실패해도 테스트 계속 진행:

```ts
await expect.soft(page.getByTestId('status')).toHaveText('Success')
await page.getByRole('link', { name: 'next page' }).click()
// 위 soft assertion이 실패해도 여기까지 실행됨
```

### 3.4 Page Object Model + Fixture

2025-2026 업계 합의: POM 단독이 아니라 **POM + Fixture를 함께 사용**.

```ts
// e2e/fixtures.ts
import { test as base } from "@playwright/test"
import { RolesPage } from "./pages/roles-page"

export const test = base.extend<{ rolesPage: RolesPage }>({
  rolesPage: async ({ page }, use) => {
    await use(new RolesPage(page))
  },
})
```

```ts
// e2e/roles.spec.ts
import { test, expect } from "./fixtures"

test("역할 추가", async ({ rolesPage }) => {
  await rolesPage.goto()
  await rolesPage.addRole("E2E-TEST", "테스트")
  await rolesPage.save()
})
```

장점:
- Fixture는 lazy 생성 (사용하는 테스트만 초기화)
- 테스트 격리 보장 (각 테스트마다 새 인스턴스)
- setup/teardown이 한 곳에 집중

> "Manually initializing objects for every POM can be repetitive and tedious, especially in larger test suites."
> — Checkly Blog

### 3.5 인증 최적화

UI 로그인 대신 API 로그인으로 속도 향상:

```ts
setup("authenticate", async ({ request }) => {
  await request.post("/auth/v1/token?grant_type=password", {
    data: { email, password },
    headers: { apikey: ANON_KEY },
  })
  await request.storageState({ path: authFile })
})
```

### 3.6 네트워크 가로채기

```ts
// 응답 전체 교체
await page.route('**/api/inventory', route =>
  route.fulfill({ json: [{ name: "LED", qty: 100 }] })
)

// 실제 응답 가져온 뒤 수정
await page.route('**/api/inventory', async route => {
  const response = await route.fetch()
  const json = await response.json()
  json.push({ name: "추가 품목", qty: 0 })
  await route.fulfill({ response, json })
})

// 특정 요청 차단 (이미지 로딩 건너뛰어 테스트 속도 향상)
await page.route('**/*.{png,jpg,jpeg}', route => route.abort())
```

### 3.7 병렬 실행 전략

| 테스트 규모 | Worker | Shard | 예상 효과 |
|---|---|---|---|
| ~50개 | auto | 불필요 | 현재 40초 → 유지 |
| 50~200개 | auto | 2~4개 | 10분 → 3분 |
| 200개+ | 조정 필요 | 4~8개 | 30분 → 5분 |

GitHub Actions (2 CPU):
```ts
workers: process.env.CI ? 2 : undefined
```

### 3.8 CI 설정

```yaml
- run: npx playwright install chromium --with-deps  # 필요한 브라우저만
- run: npx playwright test
- uses: actions/upload-artifact@v4
  if: ${{ !cancelled() }}                            # 실패해도 리포트 업로드
  with:
    name: playwright-report
    path: playwright-report/
```

> "Use Linux when running your tests on CI as it is cheaper."
> — Playwright CI 문서

### 3.9 Visual Regression Testing

```ts
// 전체 페이지 스크린샷 비교
await expect(page).toHaveScreenshot({ fullPage: true })

// 특정 요소만
await expect(page.locator('.header')).toHaveScreenshot()

// 허용 오차 설정
await expect(page).toHaveScreenshot({
  maxDiffPixelRatio: 0.001,   // 0.1% 픽셀 차이 허용
  animations: 'disabled',     // CSS 애니메이션 비활성화
})
```

주의: OS/브라우저 버전이 다르면 폰트 렌더링이 달라져 비교 실패 → CI에서는 Docker로 환경 통일 필요.

---

## 4. 개선 우선순위

### 즉시 (5분)

1. `trace: "retain-on-failure"` + `screenshot: "only-on-failure"` 설정
2. `auth.setup.ts`의 `waitForTimeout(5_000)` 제거
3. `package.json`에 `test:e2e`, `test:e2e:ui` 스크립트 추가

### 이번 주

4. 깨진 테스트 4개 파일 정리 (skip 또는 수정)
5. `.env` + dotenv로 `E2E_PASSWORD` 자동 로딩
6. 기존 locator 중 CSS 셀렉터를 role/text 기반으로 점진 교체

### 테스트 50개 넘을 때

7. Page Object Model 도입 (`e2e/pages/` 디렉토리)
8. Fixture 구성 (`e2e/fixtures.ts`)
9. API mock 패턴 도입 (UI 렌더링 테스트용)

### 테스트 200개 넘을 때

10. GitHub Actions sharding 설정
11. Visual regression 도입
12. 테스트 데이터 팩토리 패턴

---

## 참고 자료

| # | 제목 | 출처 | URL |
|---|---|---|---|
| 1 | Best Practices | Playwright 공식 | https://playwright.dev/docs/best-practices |
| 2 | Locators | Playwright 공식 | https://playwright.dev/docs/locators |
| 3 | Test Fixtures | Playwright 공식 | https://playwright.dev/docs/test-fixtures |
| 4 | Authentication | Playwright 공식 | https://playwright.dev/docs/auth |
| 5 | Mock APIs | Playwright 공식 | https://playwright.dev/docs/mock |
| 6 | Network | Playwright 공식 | https://playwright.dev/docs/network |
| 7 | Test Sharding | Playwright 공식 | https://playwright.dev/docs/test-sharding |
| 8 | Parallelism | Playwright 공식 | https://playwright.dev/docs/test-parallel |
| 9 | Setting up CI | Playwright 공식 | https://playwright.dev/docs/ci-intro |
| 10 | Writing Tests | Playwright 공식 | https://playwright.dev/docs/writing-tests |
| 11 | POMs and Fixtures | Checkly Blog | https://www.checklyhq.com/blog/page-object-models-and-fixtures-with-playwright/ |
| 12 | Sharding vs Workers | Currents.dev | https://currents.dev/posts/optimizing-test-runtime-playwright-sharding-vs-workers |
| 13 | CI/CD for Playwright | Kyrre.dev | https://www.kyrre.dev/blog/playwright-ci-cd |
| 14 | 10,000 Tests Framework | Medium/CodeToDeploy | https://medium.com/codetodeploy/how-to-build-a-playwright-framework-that-survives-10-000-tests |
| 15 | Visual Regression Guide | Codoid | https://codoid.com/automation-testing/playwright-visual-testing |

# GJ ERP — Playwright E2E 테스트 가이드

> ERP 프로젝트에서 Playwright를 사용하는 방법을 처음부터 끝까지 설명합니다.
> 자세한 분석 근거는 [playwright-analysis.md](./playwright-analysis.md) 참고.

---

## 목차

1. [시작하기 전에](#1-시작하기-전에)
2. [테스트 실행하기](#2-테스트-실행하기)
3. [테스트 실패 디버깅](#3-테스트-실패-디버깅)
4. [새 테스트 작성하기](#4-새-테스트-작성하기)
5. [화면 요소 찾기 — 셀렉터 가이드](#5-화면-요소-찾기--셀렉터-가이드)
6. [기다리기 — 타이밍 처리](#6-기다리기--타이밍-처리)
7. [테스트 구조화 — POM과 Fixture](#7-테스트-구조화--pom과-fixture)
8. [API 가로채기 — 네트워크 mock](#8-api-가로채기--네트워크-mock)
9. [CI에서 자동 실행](#9-ci에서-자동-실행)
10. [명령어 요약](#10-명령어-요약)
11. [트러블슈팅 FAQ](#11-트러블슈팅-faq)

---

## 1. 시작하기 전에

### 구조 이해

```
apps/erp/
├── e2e/                          ← 테스트 파일들
│   ├── .auth/user.json           ← 로그인 세션 (자동 생성, gitignore 대상)
│   ├── auth.setup.ts             ← 로그인 처리 (모든 테스트 전에 1번 실행)
│   ├── settings-pages.spec.ts    ← 설정 10페이지 렌더링 테스트
│   └── settings-interactions.spec.ts  ← 설정 10페이지 인터랙션 테스트
├── playwright.config.ts          ← 설정 파일
└── test-results/                 ← 실행 결과 (자동 생성)
```

### 작동 원리

```
[1] auth.setup.ts 실행
    → 브라우저 열기
    → /login 접속
    → master@gj.co.kr 로그인
    → 세션을 e2e/.auth/user.json에 저장

[2] *.spec.ts 실행
    → 저장된 세션 재사용 (로그인 건너뜀)
    → 각 테스트 독립 실행
    → 결과 리포트 생성
```

**핵심: 로그인은 1번만, 테스트는 그 세션을 재사용.**

### 사전 준비

```bash
# 1. Playwright 브라우저 설치 (최초 1회)
npx playwright install chromium

# 2. 앱 빌드 (테스트는 빌드된 앱에서 실행됨)
npm run build

# 3. 환경변수 확인
echo $E2E_PASSWORD  # "master"가 출력되어야 함
```

---

## 2. 테스트 실행하기

### 방법 A: 터미널에서 실행 (기본)

```bash
# apps/erp 디렉토리에서
E2E_PASSWORD=master npx playwright test
```

- 화면에 브라우저가 안 뜸 (headless 모드)
- 결과는 터미널에 출력
- 끝나면 `playwright-report/` 폴더에 HTML 리포트 생성

### 방법 B: UI 대시보드 (추천)

```bash
E2E_PASSWORD=master npx playwright test --ui
```

- 웹 브라우저에 **테스트 대시보드**가 열림
- 왼쪽 목록에서 원하는 테스트를 골라 실행 가능
- 각 단계별 스크린샷, 타임라인 확인 가능
- **처음 쓸 때, 디버깅할 때 이걸 쓰세요**

### 방법 C: 브라우저 동작 구경하기

```bash
E2E_PASSWORD=master npx playwright test --headed
```

- Chromium 창이 열리고 테스트가 자동으로 클릭/입력하는 걸 봄
- 빠르게 지나가니까 디버깅보다는 **구경용**
- `about:blank` 창이 뜨는 건 정상 (Playwright가 관리하는 브라우저)

### 방법 D: 특정 테스트만 실행

```bash
# 파일 단위
E2E_PASSWORD=master npx playwright test e2e/settings-pages.spec.ts

# 테스트 이름으로 필터
E2E_PASSWORD=master npx playwright test -g "통제포인트"

# 결과를 한 줄씩 보기 (진행상황 확인용)
E2E_PASSWORD=master npx playwright test --reporter=list
```

---

## 3. 테스트 실패 디버깅

### 단계 1: 리포트 열기

```bash
npx playwright show-report
```

브라우저에 상세 리포트가 열립니다. 실패한 테스트 클릭하면:
- 에러 메시지 — 뭐가 잘못됐는지
- 스크린샷 — 실패 시점의 화면
- Trace — 타임라인으로 전체 과정 재생

### 단계 2: Trace 보기 (가장 강력한 도구)

Trace는 테스트 실행의 "블랙박스 기록"입니다:

```bash
npx playwright show-trace test-results/테스트폴더/trace.zip
```

Trace에 담기는 것:
- 매 동작마다의 **스크린샷** (타임라인으로 재생 가능)
- 모든 **네트워크 요청/응답** (API 에러 확인)
- **콘솔 로그** (JavaScript 에러 확인)
- **DOM 스냅샷** (그 시점의 HTML 전체)

### 단계 3: 디버그 모드

```bash
E2E_PASSWORD=master npx playwright test --debug
```

- 한 줄씩 멈추면서 실행 (브레이크포인트처럼)
- Playwright Inspector 창이 열림
- 요소를 클릭하면 해당 locator를 자동 생성해줌

### 단계 4: 코드 녹화기

새 테스트를 만들거나, 올바른 locator를 찾을 때:

```bash
npx playwright codegen http://localhost:3210
```

- 브라우저가 열리고, 클릭/입력하는 대로 코드가 자동 생성
- 생성된 코드를 복사해서 테스트에 붙여넣기
- 출발점으로만 사용 — 반드시 정리 필요

---

## 4. 새 테스트 작성하기

### 기본 구조

```ts
// e2e/my-feature.spec.ts
import { test, expect } from "@playwright/test"

test.describe("기능 이름", () => {

  test("시나리오 설명", async ({ page }) => {
    // 1. 페이지 이동
    await page.goto("/settings/roles")

    // 2. 화면 확인
    await expect(page.getByRole("heading", { name: "역할관리" }))
      .toBeVisible({ timeout: 10_000 })

    // 3. 동작 수행
    await page.getByRole("button", { name: "저장" }).click()

    // 4. 결과 확인
    await expect(page.getByText("저장 완료")).toBeVisible()
  })

})
```

### 순서 의존 테스트 (CRUD)

데이터를 만들고 → 수정하고 → 삭제하는 시나리오는 `test.describe.serial`로 묶기:

```ts
test.describe.serial("역할 CRUD", () => {

  test("역할 추가", async ({ page }) => {
    // 생성 코드...
  })

  test("역할 수정", async ({ page }) => {
    // 수정 코드... (위에서 만든 데이터 사용)
  })

  test("역할 삭제", async ({ page }) => {
    // 삭제 코드... (반드시 정리!)
  })

})
```

**중요: 만든 테스트 데이터는 마지막에 반드시 삭제.**
안 그러면 다음 실행 시 중복 데이터로 실패합니다.

### ERP 특화 패턴

#### 통제포인트/카드 선택

```ts
// 카드가 여러 개일 때 — nth()로 순서 지정
const cards = page.locator('[data-slot="card"]')
await cards.nth(1).click()  // 0=첫번째, 1=두번째

// 선택 상태 확인
await expect(cards.nth(1)).toHaveClass(/ring-2/)
```

#### EditableDataTable (인라인 편집)

```ts
// 이 프로젝트의 EditableDataTable은 항상 input이 보임 (클릭-편집 아님)
// 열 순서: checkbox(0), status(1), 데이터(2~)
const lastRow = page.locator("table tbody tr").last()
await lastRow.locator("td").nth(2).locator("input").fill("값")

// 값이 input 안에 있으므로 getByText가 아닌 input[value]로 찾기
await expect(page.locator('input[value="E2E-TEST"]')).toBeVisible()
```

#### TreeView (조직/메뉴 트리)

```ts
// 트리 노드 클릭
const treeNode = page.locator("div.cursor-pointer").filter({ hasText: "조직명" })
await treeNode.click()

// hover 시 나타나는 버튼
await treeNode.hover()
await treeNode.locator("..").locator("button").click()
```

#### ConfirmDialog (저장/삭제 확인)

```ts
await page.getByRole("button", { name: "저장" }).click()

const dialog = page.locator('[role="alertdialog"], [role="dialog"]')
await expect(dialog).toBeVisible()
await dialog.getByRole("button", { name: "확인" }).click()
```

#### Toast 확인

```ts
// Sonner toast 라이브러리
await expect(
  page.locator('[data-sonner-toast]').getByText("저장 완료")
).toBeVisible({ timeout: 8_000 })
```

#### base-ui Checkbox (shadcn 아님)

```ts
// 이 프로젝트는 @base-ui/react 사용
// role="checkbox"가 아닌 data-slot="checkbox"로 찾기
await row.locator('[data-slot="checkbox"]').click()
```

#### FilterBar 검색

```ts
// 검색 입력 → 필터링 결과 확인
const searchInput = page.locator('input[placeholder*="검색"]')
await searchInput.fill("키워드")
await page.waitForTimeout(300) // 디바운스 대기

// 필터 결과 확인
const rows = page.locator('[data-slot="table-body"] [data-slot="table-row"]')
const count = await rows.count()
expect(count).toBeGreaterThan(0)
```

---

## 5. 화면 요소 찾기 — 셀렉터 가이드

### 우선순위 (안정적인 순서대로)

```ts
// 1. 역할 기반 — 가장 안정적, UI 구조 변경에 강함
page.getByRole("button", { name: "저장" })
page.getByRole("heading", { name: "사용자관리" })
page.getByRole("dialog")
page.getByRole("tab", { name: "토큰 관리" })

// 2. 텍스트 기반
page.getByText("Flexible", { exact: true })
page.getByText("통제포인트가 변경되었습니다.")

// 3. 라벨/placeholder 기반 — 폼 요소에 효과적
page.getByLabel("이메일")
page.getByPlaceholder("역할코드·역할명 검색...")

// 4. data 속성 기반 — 테스트 전용 계약
page.getByTestId("save-btn")            // 코드에 data-testid 필요
page.locator('[data-slot="checkbox"]')   // 이미 있는 data 속성 활용

// 5. CSS 셀렉터 — 최후의 수단 (구조 변경 시 깨짐)
page.locator("table tbody tr:last-child td:nth-child(2) input")
```

### ERP에서 자주 쓰는 locator

| 대상 | 추천 locator |
|---|---|
| 페이지 제목 | `page.getByRole("heading", { name: "제목" })` |
| 저장 버튼 | `page.getByRole("button", { name: "저장" })` |
| 테이블 | `page.locator("table").first()` |
| 테이블 행 | `page.locator("table tbody tr")` |
| 다이얼로그 | `page.locator('[role="dialog"]')` |
| 확인 다이얼로그 | `page.locator('[role="alertdialog"]')` |
| 탭 | `page.getByRole("tab", { name: "탭이름" })` |
| 체크박스 | `row.locator('[data-slot="checkbox"]')` |
| 카드 | `page.locator('[data-slot="card"]')` |
| 토스트 | `page.locator('[data-sonner-toast]')` |
| 검색 입력 | `page.locator('input[placeholder*="검색"]')` |
| 날짜 입력 | `page.locator('input[type="date"]')` |

### 여러 요소가 매칭될 때

```ts
// strict mode 위반 — 2개 이상 매칭되면 에러
await page.getByText("Standard").click() // Strict모드 에러!

// 해결 1: exact 매칭
await page.getByText("Standard", { exact: true }).first().click()

// 해결 2: 범위 좁히기
await page.locator('[data-slot="card"]').nth(1).click()

// 해결 3: 필터로 유일하게 만들기
await page.getByRole("heading", { name: "사용자관리" }).click() // heading 역할로 한정
```

---

## 6. 기다리기 — 타이밍 처리

### 원칙: "시간"이 아닌 "조건"으로 기다린다

```ts
// 나쁜 예 — 고정 시간 대기
await page.waitForTimeout(3000) // 3초 무조건 대기

// 좋은 예 — 조건 충족 시 즉시 통과
await expect(page.getByText("완료")).toBeVisible()           // 텍스트 나타남
await expect(page).toHaveURL(/dashboard/)                    // URL 변경
await expect(saveBtn).toBeEnabled()                          // 버튼 활성화
await expect(dialog).not.toBeVisible()                       // 다이얼로그 닫힘
await page.waitForResponse("**/rest/v1/roles*")              // API 응답 도착
```

### 타임아웃 설정

```ts
// 개별 assertion에 타임아웃 지정
await expect(heading).toBeVisible({ timeout: 10_000 })  // 10초까지 대기

// 전역 설정 (playwright.config.ts)
expect: { timeout: 5_000 }   // 기본 5초
timeout: 30_000               // 테스트 전체 30초
```

### 디바운스 검색 — 유일하게 waitForTimeout이 허용되는 경우

FilterBar 검색 입력은 디바운스(입력 후 잠시 뒤 필터링)가 적용됨:

```ts
await searchInput.fill("키워드")
await page.waitForTimeout(300)  // 디바운스 대기 — 이건 괜찮음
await expect(page.locator("table tbody tr")).toHaveCount(1)
```

---

## 7. 테스트 구조화 — POM과 Fixture

> 테스트가 30개 이하일 때는 지금처럼 인라인으로 작성해도 괜찮습니다.
> 50개를 넘기 시작하면 이 패턴을 도입하세요.

### Page Object Model — 페이지 동작 묶기

각 페이지에서 할 수 있는 동작들을 클래스 하나에 정리합니다.

```ts
// e2e/pages/roles-page.ts
import { type Page, expect } from "@playwright/test"

export class RolesPage {
  constructor(private page: Page) {}

  // ── 이동 ──
  async goto() {
    await this.page.goto("/settings/roles", { timeout: 15_000 })
    await expect(this.heading).toBeVisible({ timeout: 10_000 })
    await expect(this.table).toBeVisible({ timeout: 10_000 })
  }

  // ── 요소 ──
  get heading() {
    return this.page.getByRole("heading", { name: "역할관리" })
  }
  get table() {
    return this.page.locator("table").first()
  }
  get searchInput() {
    return this.page.locator('input[placeholder*="역할코드"]')
  }

  // ── 동작 ──
  async addRole(code: string, name: string) {
    await this.page.locator("button")
      .filter({ has: this.page.locator("svg.lucide-plus") })
      .first()
      .click()

    const lastRow = this.page.locator("table tbody tr").last()
    await lastRow.locator("td").nth(2).locator("input").fill(code)
    await lastRow.locator("td").nth(3).locator("input").fill(name)
  }

  async save() {
    await this.page.locator("button")
      .filter({ has: this.page.locator("svg.lucide-save") })
      .first()
      .click()

    const dialog = this.page.locator('[role="alertdialog"], [role="dialog"]')
    await expect(dialog).toBeVisible({ timeout: 5_000 })
    await dialog.getByRole("button", { name: "확인" }).click()
  }

  async search(keyword: string) {
    await this.searchInput.fill(keyword)
    await this.page.waitForTimeout(300)
  }
}
```

### Fixture — 자동 주입

```ts
// e2e/fixtures.ts
import { test as base } from "@playwright/test"
import { RolesPage } from "./pages/roles-page"
import { ControlPointPage } from "./pages/control-point-page"
import { UsersPage } from "./pages/users-page"

type ErpFixtures = {
  rolesPage: RolesPage
  controlPointPage: ControlPointPage
  usersPage: UsersPage
}

export const test = base.extend<EmpFixtures>({
  rolesPage: async ({ page }, use) => {
    await use(new RolesPage(page))
  },
  controlPointPage: async ({ page }, use) => {
    await use(new ControlPointPage(page))
  },
  usersPage: async ({ page }, use) => {
    await use(new UsersPage(page))
  },
})

export { expect } from "@playwright/test"
```

### 깔끔해진 테스트

```ts
// e2e/settings-roles.spec.ts
import { test, expect } from "./fixtures"

test.describe.serial("역할 CRUD", () => {

  test("역할 추가", async ({ rolesPage }) => {
    await rolesPage.goto()
    await rolesPage.addRole("E2E-TEST", "테스트 역할")
    await rolesPage.save()
  })

  test("역할 검색", async ({ rolesPage }) => {
    await rolesPage.goto()
    await rolesPage.search("E2E-TEST")
    await expect(rolesPage.page.locator('input[value="E2E-TEST"]')).toBeVisible()
  })

})
```

**장점:**
- "페이지 이동" "역할 추가" "저장" 같은 의미 단위로 읽힘
- locator가 한 곳에 집중 → 셀렉터 바뀌면 POM만 수정
- 여러 테스트 파일에서 같은 POM 재사용

---

## 8. API 가로채기 — 네트워크 mock

### 언제 쓰나?

| 테스트 목적 | mock 사용 | 실제 API |
|---|---|---|
| UI가 데이터를 올바르게 렌더링하는지 | O | |
| 에러 상태 (서버 500 등) 화면 확인 | O | |
| 빈 데이터 상태 테스트 | O | |
| 로딩 상태 테스트 | O | |
| 실제 CRUD 동작 확인 | | O |
| 전체 흐름 통합 테스트 | | O |

### 기본 패턴

```ts
test("재고 목록이 올바르게 렌더링됨", async ({ page }) => {
  // Supabase REST API 응답을 미리 정해둠
  await page.route("**/rest/v1/inventory*", (route) =>
    route.fulfill({
      status: 200,
      contentType: "application/json",
      json: [
        { id: "1", name: "LED 조명", quantity: 100 },
        { id: "2", name: "전선 10m", quantity: 50 },
      ],
    })
  )

  await page.goto("/inventory")
  await expect(page.getByText("LED 조명")).toBeVisible()
  await expect(page.getByText("100")).toBeVisible()
})
```

### 에러 상태 테스트

```ts
test("서버 에러 시 에러 메시지 표시", async ({ page }) => {
  await page.route("**/rest/v1/roles*", (route) =>
    route.fulfill({ status: 500, json: { message: "Internal Server Error" } })
  )

  await page.goto("/settings/roles")
  await expect(page.getByText("에러")).toBeVisible()
})
```

### 빈 데이터 상태 테스트

```ts
test("데이터 없을 때 안내 메시지 표시", async ({ page }) => {
  await page.route("**/rest/v1/roles*", (route) =>
    route.fulfill({ status: 200, json: [] })
  )

  await page.goto("/settings/roles")
  await expect(page.getByText("등록된 역할이 없습니다.")).toBeVisible()
})
```

### API 응답 대기 후 확인

```ts
test("저장 후 API 호출 확인", async ({ page }) => {
  // API 응답을 기다리는 Promise 생성
  const responsePromise = page.waitForResponse("**/rest/v1/roles*")

  // 저장 버튼 클릭
  await page.getByRole("button", { name: "저장" }).click()

  // API 응답 확인
  const response = await responsePromise
  expect(response.status()).toBe(200)
})
```

---

## 9. CI에서 자동 실행

### GitHub Actions 설정

```yaml
# .github/workflows/e2e.yml
name: E2E Tests
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  e2e:
    timeout-minutes: 30
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: apps/erp

    steps:
      - uses: actions/checkout@v5

      - uses: actions/setup-node@v5
        with:
          node-version: 22
          cache: npm

      - name: Install dependencies
        run: npm ci
        working-directory: .

      - name: Install Playwright browsers
        run: npx playwright install chromium --with-deps

      - name: Build
        run: npm run build
        working-directory: .

      - name: Run E2E tests
        run: npx playwright test
        env:
          E2E_PASSWORD: ${{ secrets.E2E_PASSWORD }}

      - name: Upload report
        uses: actions/upload-artifact@v4
        if: ${{ !cancelled() }}
        with:
          name: playwright-report
          path: apps/erp/playwright-report/
          retention-days: 14
```

### 200개 이상일 때 — Shard 설정

```yaml
strategy:
  fail-fast: false
  matrix:
    shardIndex: [1, 2, 3, 4]
    shardTotal: [4]

steps:
  - name: Run tests
    run: npx playwright test --shard=${{ matrix.shardIndex }}/${{ matrix.shardTotal }}
```

---

## 10. 명령어 요약

| 하고 싶은 것 | 명령어 |
|---|---|
| 전체 실행 | `E2E_PASSWORD=master npx playwright test` |
| UI 대시보드 | `E2E_PASSWORD=master npx playwright test --ui` |
| 브라우저 보면서 | `E2E_PASSWORD=master npx playwright test --headed` |
| 특정 파일 | `npx playwright test e2e/파일.spec.ts` |
| 이름으로 필터 | `npx playwright test -g "테스트이름"` |
| 실패 리포트 | `npx playwright show-report` |
| Trace 보기 | `npx playwright show-trace trace.zip` |
| 디버그 모드 | `npx playwright test --debug` |
| 코드 녹화 | `npx playwright codegen localhost:3210` |
| 스냅샷 갱신 | `npx playwright test --update-snapshots` |
| 브라우저 설치 | `npx playwright install chromium` |

---

## 11. 트러블슈팅 FAQ

### Q: `about:blank` 브라우저 창이 떠요

정상입니다. `--headed` 모드 또는 `--ui` 모드에서 Playwright가 관리하는 Chromium 브라우저입니다. 테스트가 끝나면 자동으로 닫힙니다.

### Q: `E2E_PASSWORD` 에러가 나요

```bash
# 방법 1: 실행 시 앞에 붙이기
E2E_PASSWORD=master npx playwright test

# 방법 2: .env 파일 만들기 (apps/erp/.env)
echo "E2E_PASSWORD=master" > .env
# + playwright.config.ts에 dotenv.config() 추가
```

### Q: 브라우저가 설치 안 됐다고 해요

```bash
npx playwright install chromium
```

### Q: 테스트가 가끔만 실패해요 (flaky test)

원인 대부분: 타이밍 문제.

```ts
// 나쁜 예 — 고정 대기 (느리거나 부족하거나)
await page.waitForTimeout(2000)

// 좋은 예 — 조건 대기 (빠르고 안정적)
await expect(element).toBeVisible({ timeout: 10_000 })
```

trace를 켜서 실패 시점을 분석하세요:
```ts
// playwright.config.ts
use: { trace: "retain-on-failure" }
```

### Q: `strict mode violation` 에러가 나요

하나의 locator가 여러 요소를 매칭했을 때 발생합니다.

```ts
// 에러 — "Standard" 텍스트가 여러 카드에 있음
await page.getByText("Standard").click()

// 해결 — 범위를 좁히거나 순서로 지정
await page.locator('[data-slot="card"]').nth(1).click()
await page.getByRole("heading", { name: "Standard" }).click()
```

### Q: 테스트 데이터가 쌓여요

CRUD 테스트에서 만든 데이터를 삭제하지 않으면, 다음 실행 시 중복 데이터로 실패합니다.

```ts
test.describe.serial("CRUD", () => {
  test("생성", ...)
  test("수정", ...)
  test("삭제 (정리)", ...)  // 반드시 마지막에 정리!
})
```

또는 테스트 데이터에 고유한 이름을 사용:

```ts
const uniqueCode = `E2E-${Date.now()}`
await rolesPage.addRole(uniqueCode, "테스트")
```

### Q: 다른 컴퓨터에서 실행하면 실패해요

- Playwright 버전 맞추기: `npx playwright install`
- 환경변수 설정: `E2E_PASSWORD`
- 빌드 먼저: `npm run build`
- DB에 테스트 계정 존재 확인: `master@gj.co.kr`

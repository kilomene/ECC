# E2E Testing with Playwright

## Know how this skill relates to local-browser-qa

- Treat local-browser-qa as the procedure-driven pass on a deployed URL and this skill as the codified automated suite. The first answers whether this deployment behaves, the second answers whether the behavior holds on every run.

- Run a browser-qa pass before you codify its findings into tests. Promote every confirmed bug into a regression test so the suite remembers it.

- Point browser-qa at staging or preview URLs. Point this suite at local dev or a disposable test environment the config can spin up.

## Lay out the suite

- Keep the layout flat and predictable: specs grouped by area under tests/e2e, shared setup in fixtures, page objects beside the specs, one playwright.config.ts at the root.

- Give each screen its own page object and keep raw selectors out of test bodies. A test reads like a user story, the page object holds the mechanics.

```
tests/
  e2e/
    auth/
      login.spec.ts
      logout.spec.ts
    features/
      browse.spec.ts
      search.spec.ts
  fixtures/
    auth.ts
    data.ts
  pages/
    ItemsPage.ts
playwright.config.ts
```

## Write page objects and tests

- Locate elements with data-testid attributes and auto-waiting locators. Prefer semantic roles when no test id exists.

- Replace arbitrary sleeps with explicit waits. Wait for the API response the action triggers instead of waiting a fixed number of milliseconds.

- Model each screen as a class exposing named locators and action methods. Keep navigation, waiting, and assertions inside the page object.

```typescript
import { Page, Locator } from '@playwright/test'

export class ItemsPage {
  readonly page: Page
  readonly searchInput: Locator
  readonly itemCards: Locator

  constructor(page: Page) {
    this.page = page
    this.searchInput = page.locator('[data-testid="search-input"]')
    this.itemCards = page.locator('[data-testid="item-card"]')
  }

  async goto() {
    await this.page.goto('/items')
    await this.page.waitForLoadState('networkidle')
  }

  async search(query: string) {
    await this.searchInput.fill(query)
    await this.page.waitForResponse(resp => resp.url().includes('/api/search'))
  }

  async getItemCount() {
    return await this.itemCards.count()
  }
}
```

## Keep the suite green in CI

- Run suites under `muse.exec` on his VM with the project's own Playwright install. Install browsers once with `npx playwright install --with-deps`.

- Point baseURL at an environment variable with a localhost default. Start the app with the webServer block and reuse an existing server outside CI.

- Reject any focused test in CI with forbidOnly. Retry failures in CI and never on a developer machine.

- Publish the html, junit, and json reporters on every run. Upload the HTML report as a build artifact in CI.

- Capture screenshots, traces, and video on failure only: trace on first retry, screenshot on failure, video retained on failure. Keep artifacts for 30 days, then let them expire.

- Cap actionTimeout at 10 seconds and navigationTimeout at 30 seconds. Cover chromium, firefox, webkit, and mobile chrome projects, and shrink the matrix when the suite gets slow.

- Quarantine a flaky test with test.fixme and a linked issue instead of deleting it. Prove flakiness with repeat-each runs before you quarantine.

- Rerun quarantined tests on a schedule and restore them when the flake is fixed. Never delete a flaky test silently.

## Available connectors

- [GitHub](/opt/hatch/connectors/github/CONNECTOR.md) for CI workflow runs and the artifacts behind the e2e pipeline.

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.

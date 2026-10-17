# Browser QA for Deployed URLs

## Scope the pass before driving anything

- Default to read-only on any live URL. Never run a mutating journey such as checkout, payment, deletion, or mass update against production.

- Exercise mutating journeys only against a staging or preview URL with the user's explicit opt-in. Use seeded test credentials and never real production logins.

- Redact credentials, tokens, and personal data from every screenshot before you save or share it. Treat each saved screenshot as a potential leak surface.

- Confirm the scope with the parent agent before phase one: target URL, phases in scope, and whether any journey mutates state. Do not drive anything until the scope is explicit.

## Run the four phases

- Drive scripted checks with Puppeteer through `muse.exec`. Save screenshots into the workspace and inspect them with `muse.read`.

- Hand every live-browser step to the parent or root agent as exact numbered instructions. You cannot operate the live browser yourself, so delegated steps must be precise enough to run without interpretation.

### Smoke

- Load the target URL and collect console errors. Filter analytics and third-party noise before you count what matters.

- Walk the network log and flag any 4xx or 5xx response. A clean page load shows only 2xx or 304 responses.

- Measure Core Web Vitals against the web.dev thresholds: LCP under 2.5 seconds, CLS under 0.1, INP under 200 milliseconds. INP replaced FID in March 2024, so assert INP and ignore FID.

- Capture an above-the-fold screenshot on desktop and mobile viewports. Store both next to the run evidence.

### Interaction

- Click every navigation link and confirm each destination loads without error. Log dead links by URL.

- Submit each form with valid data and confirm the success state appears. Then submit with invalid data and confirm a clear error state appears.

- Run the auth flow with test credentials: log in, open a protected page, log out. Never touch a production account.

- Walk the critical user journeys such as search, onboarding, or checkout. Keep them read-only unless a staging URL and an explicit opt-in are in place.

### Visual regression

- Screenshot key pages at 375, 768, and 1440 pixels wide. Compare against committed baseline screenshots when they exist.

- Report INCONCLUSIVE when no baseline exists. Never report a silent PASS without something to compare against.

- Flag layout shifts larger than 5 pixels, missing elements, and horizontal overflow. Check dark mode on every page that offers it.

### Accessibility

- Run axe-core or an equivalent checker on each page. Record every WCAG 2.2 AA violation, including contrast, label, and focus order problems.

- Tab through each page end to end and confirm keyboard navigation reaches every interactive element. Confirm screen reader landmarks are present and named.

- Treat a clean automated run as necessary but not sufficient. Axe-core covers roughly 30 to 40 percent of WCAG, so a manual keyboard and screen reader pass still decides the claim.

## Deliver the verdict and the QA report

- Write one QA report per URL per run with a section for each phase you ran. Quote the numbers you actually observed, never estimates.

- Close with exactly one verdict: SHIP, SHIP WITH FIXES, DO NOT SHIP, or INCONCLUSIVE. Attach the issue count to SHIP WITH FIXES and DO NOT SHIP.

```markdown
## QA Report - [URL] - [timestamp]

### Smoke
- Console: 0 critical errors, 2 warnings from analytics noise
- Network: all 2xx or 304
- Web Vitals: LCP 1.2s pass, CLS 0.02 pass, INP 89ms pass

### Interaction
- Nav links: 12 of 12 load
- Contact form: missing error state on invalid email
- Auth flow: login and logout work with test credentials

### Visual
- Hero overflows at 375px
- Dark mode consistent on all pages

### Accessibility
- 2 AA violations: missing alt text on hero image, low contrast footer links

### Verdict: SHIP WITH FIXES (2 issues, 0 blockers)
```

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.

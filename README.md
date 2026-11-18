# Restful Booker UI Test Automation

[English](README.md) · [한국어](README.ko.md)

Automated UI regression tests for the [Restful Booker Platform](https://automationintesting.online/) using Playwright and TypeScript. Tests cover room information, navigation, contact forms, and admin authentication across Chromium, Firefox, and WebKit.

## Tech Stack

| Category | Technologies |
| --- | --- |
| Test Automation | Playwright, TypeScript |
| Runtime | Node.js, npm |
| Configuration | dotenv |
| Code Formatting | Prettier |
| CI | GitHub Actions |
| Reporting | Playwright HTML, JSON |
| Workflow Scripting | Python |
| Notifications | Slack Incoming Webhooks, Block Kit |
| Version Control | Git, GitHub |

## Test Coverage

| Area | Tests | Coverage |
| --- | --- | --- |
| Homepage | 1 | Homepage smoke test |
| Rooms & Booking | 7 | Room information, pricing, reservation page navigation |
| Contact Form | 14 | Required fields, email format, input length boundaries |
| Navigation | 6 | Rooms, Booking, Amenities, Location, Contact, Admin navigation |
| Location | 1 | Address, phone number, email |
| Admin Authentication | 5 | Valid and invalid login, logout, empty credentials, post-logout access |
| **Total** | **34** | 33 active tests, 1 marked with `test.fixme()` |

### Verified Test Results

Results from the [November 17, 2026 manual GitHub Actions run](https://github.com/siyeon98/restful-booker-ui-automation/actions/runs/44047498570).

| Metric | Result |
| --- | --- |
| Browser Projects | Chromium, Firefox, WebKit |
| Passed | **99** |
| Failed | **0** |
| Skipped | **3** |
| Flaky | **0** |
| Test Execution Duration | 2.4 minutes |

These figures describe this execution. Test duration excludes other workflow steps such as dependency installation and artifact upload. Results and timings may vary between runs.

Counts include all three browser projects. The three skipped executions are one Amenities navigation test marked with `test.fixme()` running across three browsers.

## Test Design & Implementation

- Positive and negative inputs for contact forms and authentication.
- Boundary value analysis for phone, subject, and message lengths.
- Parameterized scenarios for room information and form validation.
- Shared helpers for admin login and contact form inputs.
- URL, visibility, content, and validation assertions.
- Feature-based test files executed across three browsers.

## Project Structure

| Path | Purpose |
| --- | --- |
| `.github/workflows/ui-tests.yml` | GitHub Actions |
| `tests/` | UI tests |
| `playwright.config.ts` | Playwright configuration |
| `package.json / package-lock.json` | Dependencies and scripts |
| `README.md` | English documentation |
| `README.ko.md` | 한국어 문서 |

## Getting Started

### Prerequisites

- Node.js and npm

### Installation

```bash
git clone https://github.com/siyeon98/restful-booker-ui-automation.git
cd restful-booker-ui-automation
npm ci
npx playwright install
```

### Environment Configuration

Create `.env` in the project root with the demo application credentials.

```dotenv
ADMIN_USERNAME=<demo-admin-username>
ADMIN_PASSWORD=<demo-admin-password>
```

The local environment file is ignored by Git. Keep credentials out of version control.

## Running Tests

Run the full suite.

```bash
npm test
```

Run one browser or one test file.

```bash
npx playwright test --project=chromium
npx playwright test tests/admin.spec.ts --project=chromium
```

Open the HTML report.

```bash
npx playwright show-report
```

### Test Configuration

- `fullyParallel: true`
- Chromium, Firefox, and WebKit projects
- Local execution with no retries
- CI execution with two retries and one worker
- HTML reports for all runs and an additional JSON summary in CI
- Trace collection on the first retry

## Continuous Integration

GitHub Actions executes the Playwright test suite on a GitHub-hosted Ubuntu runner.

| Setting | Value |
| --- | --- |
| Workflow | Restful Booker UI Tests |
| Job | UI Tests |
| Node.js | `lts/*` |

### Execution Triggers

| Trigger | Configuration |
| --- | --- |
| Push | Pushes to `main` |
| Pull Request | Pull requests targeting `main` |
| Manual | GitHub Actions → Restful Booker UI Tests → Run workflow |
| Scheduled | Configured daily at 03:23 Asia/Seoul on the default branch |

Scheduled runs may start after the configured time.

A [scheduled run on November 17, 2026](https://github.com/siyeon98/restful-booker-ui-automation/actions/runs/43998959820) completed successfully.

### Workflow Steps

1. Check out the repository and set up Node.js.
2. Install dependencies and Playwright browsers with system dependencies.
3. Run the Playwright tests using admin credentials from GitHub Actions Secrets.
4. Upload the HTML report.
5. Send the execution summary to Slack.

### Test Reporting

| Setting | Value |
| --- | --- |
| Report Format | HTML |
| Artifact Name | `playwright-report` |
| Retention Period | 30 days |
| Upload Condition | Upload attempted unless the workflow is cancelled |

Download generated and uploaded reports from the **Artifacts** section of the corresponding GitHub Actions run.

### Slack Test Notifications

Notifications use Slack Incoming Webhooks and Block Kit. They include the job result, execution trigger, and a link to the run.

When `test-results/playwright-summary.json` is available, notifications include passed, failed, skipped, and flaky counts across all browsers and test duration. The notification step runs on job success or failure.

If report data is unavailable, the notification includes the job result and run link. Slack delivery failures are recorded as warnings in GitHub Actions and do not fail the job. Test failures still fail the job.

Successful Slack delivery has been verified. Delivery for a failed workflow run remains unverified.

## Known Limitations

- **Amenities navigation** — the link updates `#amenities`, but the target section was absent from the observed DOM. The test is marked with `test.fixme()`.
- **Booking completion** — full booking completion is outside the current coverage. Earlier investigation encountered a loading issue and an HTTP 500 room API response.
- **Admin authorization** — post-logout checks cover the displayed login UI. Server-side authorization coverage is outside this suite.
- **Public demo environment** — availability and behavior can change and affect stability.
- **Contact submissions** — valid submission tests may create data in the public demo.

## Security

- Local environment files are ignored by Git.
- CI credentials and the Slack webhook are stored in GitHub Actions Secrets.
- Admin credentials use `ADMIN_USERNAME` and `ADMIN_PASSWORD`. The webhook uses `SLACK_WEBHOOK_URL`. Credentials are not hardcoded in tests.

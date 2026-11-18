# Restful Booker UI Test Automation

[English](README.md) · [한국어](README.ko.md)

Playwright와 TypeScript로 [Restful Booker Platform](https://automationintesting.online/)의 UI 회귀 테스트를 자동화한 프로젝트입니다. Chromium·Firefox·WebKit에서 객실 정보, 페이지 이동, 문의 폼, 관리자 인증을 검증합니다.

## 기술 스택

| 구분 | 기술 |
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

## 테스트 범위

| 영역 | 테스트 수 | 검증 내용 |
| --- | --- | --- |
| Homepage | 1 | 홈페이지 smoke 테스트 |
| Rooms & Booking | 7 | 객실 정보·가격·예약 페이지 이동 |
| Contact Form | 14 | 필수 입력·이메일 형식·길이 경계값 |
| Navigation | 6 | Rooms·Booking·Amenities·Location·Contact·Admin 이동 |
| Location | 1 | 주소·전화번호·이메일 |
| Admin Authentication | 5 | 정상·비정상 로그인, 로그아웃, 빈 입력, 로그아웃 후 접근 |
| **Total** | **34** | 활성 33건, test.fixme() 1건 |

### 확인한 테스트 결과

[2026년 11월 17일 GitHub Actions 수동 실행](https://github.com/siyeon98/restful-booker-ui-automation/actions/runs/44047498570)에서 확인한 결과입니다.

| 항목 | 결과 |
| --- | --- |
| 브라우저 | Chromium, Firefox, WebKit |
| 통과 | **99** |
| 실패 | **0** |
| 스킵 | **3** |
| 재시도 통과 | **0** |
| 테스트 실행 시간 | 2.4 minutes |

위 수치는 해당 실행의 결과입니다. 테스트 실행 시간에는 의존성 설치·리포트 업로드 등 다른 워크플로 단계가 포함되지 않습니다. 실행마다 결과와 시간은 달라질 수 있습니다.

건수는 브라우저 3종의 실행을 합산한 값입니다. 스킵 3건은 `test.fixme()`로 지정한 Amenities 이동 테스트 1건을 브라우저별로 집계한 것입니다.

## 테스트 설계 및 구현

- 문의 폼과 관리자 인증의 정상·비정상 입력 검증
- 전화번호·제목·본문 길이 경계값 분석
- 객실 정보와 폼 검증 시나리오 파라미터화
- 관리자 로그인과 문의 폼 입력 공통 헬퍼 사용
- URL·요소 표시·내용·검증 메시지 assertion
- 기능별 테스트 파일 구성과 브라우저 3종 실행

## 프로젝트 구성

| 경로 | 용도 |
| --- | --- |
| `.github/workflows/ui-tests.yml` | GitHub Actions |
| `tests/` | UI tests |
| `playwright.config.ts` | Playwright configuration |
| `package.json / package-lock.json` | Dependencies and scripts |
| `README.md` | English documentation |
| `README.ko.md` | 한국어 문서 |

## 시작하기

### 사전 준비

- Node.js 및 npm

### 설치

```bash
git clone https://github.com/siyeon98/restful-booker-ui-automation.git
cd restful-booker-ui-automation
npm ci
npx playwright install
```

### 환경변수 설정

프로젝트 루트에 `.env`를 만들고 데모 애플리케이션의 계정 정보를 입력합니다.

```dotenv
ADMIN_USERNAME=<demo-admin-username>
ADMIN_PASSWORD=<demo-admin-password>
```

로컬 환경 파일은 Git에서 제외됩니다. 인증 정보는 버전 관리에 포함하지 않습니다.

## 테스트 실행

전체 테스트를 실행합니다.

```bash
npm test
```

특정 브라우저 또는 테스트 파일을 실행합니다.

```bash
npx playwright test --project=chromium
npx playwright test tests/admin.spec.ts --project=chromium
```

HTML 리포트를 엽니다.

```bash
npx playwright show-report
```

### 테스트 설정

- `fullyParallel: true`
- Chromium·Firefox·WebKit 프로젝트
- 로컬 재시도 0회
- CI 재시도 최대 2회, worker 1개
- 모든 실행에서 HTML 리포트 생성, CI에서 JSON 요약 추가
- 첫 번째 재시도에서 trace 수집

## 지속적 통합

GitHub Actions의 GitHub-hosted Ubuntu runner에서 Playwright 테스트를 실행합니다.

| Setting | Value |
| --- | --- |
| Workflow | Restful Booker UI Tests |
| Job | UI Tests |
| Node.js | `lts/*` |

### 실행 조건

| 조건 | 설정 |
| --- | --- |
| Push | `main` 브랜치 push |
| Pull Request | `main` 대상 PR |
| Manual | GitHub Actions → Restful Booker UI Tests → Run workflow |
| Scheduled | 기본 브랜치에서 매일 03:23 Asia/Seoul로 설정 |

예약 실행은 설정 시각보다 늦게 시작될 수 있습니다.

[2026년 11월 17일 예약 실행](https://github.com/siyeon98/restful-booker-ui-automation/actions/runs/43998959820)이 성공했습니다.

### 워크플로 단계

1. 저장소를 체크아웃하고 Node.js를 설정합니다.
2. 의존성, Playwright 브라우저 및 시스템 의존성을 설치합니다.
3. GitHub Actions Secrets의 관리자 계정으로 테스트를 실행합니다.
4. HTML 리포트를 업로드합니다.
5. 실행 요약을 Slack으로 전송합니다.

### 테스트 리포트

| 설정 | 값 |
| --- | --- |
| Report Format | HTML |
| Artifact Name | `playwright-report` |
| Retention Period | 30 days |
| Upload Condition | 워크플로 취소 시 제외 |

생성·업로드된 리포트는 해당 GitHub Actions 실행 페이지의 **Artifacts**에서 내려받을 수 있습니다.

### Slack 테스트 알림

Slack Incoming Webhooks와 Block Kit을 사용합니다. 알림에는 job 결과, 실행 조건, 실행 링크가 포함됩니다.

`test-results/playwright-summary.json`이 있으면 전체 브라우저의 통과·실패·스킵·flaky 건수와 테스트 실행 시간을 포함합니다. job 성공 또는 실패 시 알림 단계를 실행합니다.

리포트 데이터를 사용할 수 없으면 job 결과와 실행 링크를 제공합니다. Slack 전송 실패는 GitHub Actions에 경고로 기록하며 job 실패로 처리하지 않습니다. 테스트 실패는 job 실패로 처리합니다.

성공한 실행의 Slack 전달은 확인했습니다. 실패한 워크플로 실행의 Slack 전달은 아직 검증하지 않았습니다.

## 알려진 제약 사항

- **Amenities 이동** — 링크는 `#amenities`로 변경되지만 관찰한 DOM에 대상 영역이 없어 `test.fixme()`로 관리합니다.
- **예약 완료** — 전체 예약 완료는 현재 범위에 포함되지 않습니다. 이전 조사에서 로딩 문제와 객실 API HTTP 500 응답을 확인했습니다.
- **관리자 접근 검증** — 로그아웃 후 로그인 UI를 확인합니다. 서버 측 권한 검증은 이 테스트 범위 밖입니다.
- **공개 데모 환경** — 가용성과 동작이 바뀌면 테스트 안정성에 영향을 줄 수 있습니다.
- **문의 제출** — 정상 제출 테스트가 공개 데모에 데이터를 생성할 수 있습니다.

## 인증 정보 관리

- 로컬 환경 파일은 Git에서 제외합니다.
- CI 인증 정보와 Slack webhook은 GitHub Actions Secrets에 저장합니다.
- `ADMIN_USERNAME`, `ADMIN_PASSWORD`, `SLACK_WEBHOOK_URL`을 사용하며 테스트에 계정을 하드코딩하지 않습니다.

# sunbird-spark-automation

End-to-end test automation for the **consumption flow** of Sunbird Spark — browsing, enrolling in, and consuming content (video, PDF, EPUB, HTML/SCORM, ECML, YouTube) as a learner, both as an anonymous visitor and as a registered user, plus certificate download after course completion.

This suite doesn't create content — it assumes content already exists on the portal. Run it **after** content has been created and published, to verify learners can actually find and consume it end to end.

## Repository layout

```
automation_testing/
├── web-portal/           Playwright test suite (the tests themselves)
├── test-runner-service/  Docker-based runner + web UI for triggering the suite without a dev environment
└── ci-templates/         Opt-in GitHub Actions workflow templates
```

See [`automation_testing/README.md`](automation_testing/README.md) for full setup, configuration, and test structure.

## Three ways to run the suite

### 1. Locally with Playwright

For anyone with a dev environment and Node.js installed:

```bash
cd automation_testing/web-portal
npm install
npx playwright install chromium --with-deps
npm run test:e2e -- --headed --workers=1
```

Full details — environment variables, running a single project/spec, viewing the HTML report, optional CI integration — are in [`automation_testing/README.md`](automation_testing/README.md).

### 2. Via the Docker test runner (no dev environment or GitHub access needed)

A small always-on web app gives anyone a one-button "Run E2E Tests" page: pick a target environment, click the button, and get a link to the Playwright HTML report once it's done. It runs the exact same suite in Docker, rebuilt from current source before every run.

```bash
cd automation_testing/test-runner-service
npm install
npm run dev
```

Then open `http://localhost:4000`. See [`test-runner-service/README.md`](automation_testing/test-runner-service/README.md) for configuration (credentials, auth password, running it beyond a local machine).

### 3. Via GitHub Actions (CI)

Trigger the same suite straight from GitHub, with no local setup at all — either manually on demand, or automatically on every pull request. Two opt-in templates in [`automation_testing/ci-templates/`](automation_testing/ci-templates/) can be activated by copying them into `.github/workflows/`:

```bash
cp automation_testing/ci-templates/e2e-on-demand.yml .github/workflows/e2e-on-demand.yml
```

Trigger it from **Actions → E2E Tests (On Demand) → Run workflow**; the HTML report is published to GitHub Pages and linked from the job summary. A second template, `e2e-pr-job.yml`, runs the full suite automatically as a PR check instead. See [`automation_testing/README.md`](automation_testing/README.md#optional-ci-integration-github-actions) for activation steps and the repository variables both templates read.

> All three ways point at a real environment and mutate real state (enrollment, course progress, certificates) — always target a test/staging instance, never production.

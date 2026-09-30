# Project 2: Headless Web & API Security Scanning Pipeline (Cypress CLI & OWASP ZAP Integration)

### Objective

Automate headless E2E browser runs routed through an active OWASP ZAP proxy using Cypress CLI, enabling passive/active vulnerability analysis of web application targets and automating artifact generation in a GitHub Actions pipeline (`.github/workflows/security-e2e.yml`).

### Test Specifications

- **CLI Execution Engine:** Cypress CLI (`cypress run`)
- **Security Scanner / Proxy:** OWASP ZAP (Zed Attack Proxy)
- **CI/CD Platform:** GitHub Actions (`.github/workflows/security-e2e.yml`)
- **Scope:** Automated DAST scanning, vulnerability triage, and automated security audit report generation.

### Setup Instructions

1. Create the security pipeline test spec under `cypress/e2e/security_pipeline_dast.cy.js`.
2. Configure Cypress proxy and SSL bypass settings in `cypress.config.js` to route Chromium traffic through OWASP ZAP (`http://localhost:8080`).
3. Add security scan rules and threshold configs under `.zap/rules.tsv`.
4. Create the automated GitHub Actions pipeline file under `.github/workflows/security-e2e.yml`.
5. Commit and push changes to trigger the automated CI/CD security pipeline on GitHub.

### Test Script

**Cypress Proxy Configuration (`cypress.config.js`)**

```jsx
const { defineConfig } = require('cypress');

module.exports = defineConfig({
  e2e: {
    baseUrl: 'http://localhost:3000',
    setupNodeEvents(on, config) {
      on('before:browser:launch', (browser = {}, launchOptions) => {
        if (browser.family === 'chromium' && browser.name !== 'electron') {
          // Route browser traffic through OWASP ZAP
          launchOptions.args.push('--proxy-server=http://localhost:8080');
          // Bypass self-signed certificate warnings from proxy
          launchOptions.args.push('--ignore-certificate-errors');
        }
        return launchOptions;
      });
    },
  },
});
```

**GitHub Actions Security CI/CD Workflow (`.github/workflows/security-e2e.yml`)**

```yaml
name: Automated E2E Security Audit & DAST Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  security-assessment:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Code Repository
      uses: actions/checkout@v3

    - name: Create Workspace Directory for ZAP Artifacts
      run: mkdir -p ${{ github.workspace }}/zap-reports

    - name: Start OWASP ZAP Daemon Service
      run: |
        docker run -d --name zap -p 8080:8080 \
          -v ${{ github.workspace }}/zap-reports:/zap/wrk/:rw \
          -i owasp/zap2docker-stable zap-cli daemon --host 0.0.0.0 --port 8080

    - name: Execute Headless Cypress E2E Security Suite
      uses: cypress-io/github-action@v5
      with:
        browser: chrome
        headless: true
      env:
        HTTP_PROXY: http://localhost:8080
        HTTPS_PROXY: http://localhost:8080

    - name: Trigger OWASP ZAP Full Vulnerability Scan & Generate HTML Report
      run: |
        docker exec zap zap-cli report -o /zap/wrk/ZAP_Vulnerability_Report.html -f html

    - name: Upload Security Assessment Artifacts
      uses: actions/upload-artifact@v3
      if: always()
      with:
        name: vulnerability-assessment-report
        path: ${{ github.workspace }}/zap-reports/ZAP_Vulnerability_Report.html
```

### Expected Test Results

- **Headless CLI Status:** 0 Vulnerabilities/Failures blocking build execution
- **Pipeline Output Status:** Pipeline Completed / Artifact Generated
- **Assertions Evaluated:**
- Execute Headless Cypress E2E Security Suite — PASS
- Route Traffic via Active OWASP ZAP Proxy Engine — PASS
- Generate & Upload `ZAP_Vulnerability_Report.html` Artifact — PASS

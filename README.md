# E2E WEB AUTOMATION & CLIENT-SIDE SECURITY AUDITING (Cypress)

## Overview
This portfolio suite demonstrates an End-to-End (E2E) web automation and client-side security auditing framework using Cypress. It transitions from interactive UI authentication and storage security assertions to headless execution integrated with OWASP ZAP and GitHub Actions for continuous security testing.

## Projects Overview
- **Project 1: E2E Auth Flow & Dynamic Client-Side Vulnerability Assessment (Cypress UI)**  
  *Focus:* Automating E2E login journeys while asserting browser storage safety (`localStorage`), validating session cookie security attributes (`HttpOnly`, `Secure`), and inspecting HTTP security headers.
- **Project 2: Headless Web & API Security Scanning Pipeline (Cypress CLI & OWASP ZAP Integration)**  
  *Focus:* Executing headless Cypress runs routed through an OWASP ZAP proxy engine to capture web traffic, run dynamic security scans, and generate HTML vulnerability reports within GitHub Actions.

---

## Suite Projects
- [Project 1: E2E Auth Flow & Dynamic Client-Side Vulnerability Assessment (Cypress UI)](./PROJECT_1.md)
- [Project 2: Headless Web & API Security Scanning Pipeline (Cypress CLI & OWASP ZAP Integration)](./PROJECT_2.md)
- [Executive Summary: End-to-End E2E & DAST Web Security Architecture](./PROJECT_3_Executive_Summary.md)

---

## Executive Summary: End-to-End E2E & DAST Web Security Architecture
This portfolio section demonstrates a hybrid client-side and application-level web security testing suite, bridging browser-based functional verification with active vulnerability scanning:

- **E2E Authentication & Storage Auditing (Project 1):** Verification of critical user authentication flows alongside real-time inspection of browser storage (`localStorage`/`sessionStorage`), enforcing secure cookie attributes (`HttpOnly`, `Secure`) and security header policies (`CSP`, `X-Content-Type-Options`).
- **Proxy-Routed Headless Execution & DAST Pipeline (Project 2):** Orchestration of headless Cypress runs in Chrome routed through an OWASP ZAP active proxy engine, capturing dynamic web traffic to identify vulnerabilities during automated UI interactions.
- **CI/CD Integration & Automated Artifact Publishing:** Seamless execution within GitHub Actions (`.github/workflows/security-e2e.yml`), automatically generating and archiving structured HTML vulnerability audit reports (`ZAP_Vulnerability_Report.html`) for triage and remediation tracking.

This dual approach ensures both client-side storage security and application-level attack surface coverage within modern continuous integration environments.

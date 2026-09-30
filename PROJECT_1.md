# Project 1: E2E Auth Flow & Dynamic Client-Side Vulnerability Assessment (Cypress UI)

### Objective

Design an automated E2E test suite that validates critical authentication journeys while concurrently auditing the client-side attack surface—ensuring session tokens are not leaked in browser storage and validating response headers against modern web security standards.

### Test Specifications

- **Framework:** Cypress (E2E & Security Assertion Suite)
- **Assessment Target:** Authentication Endpoints & Browser Session Management
- **Scope:** Client-Side Data Exposure, Session Hijacking Prevention, and DOM-based Security Checks

### Setup Instructions

1. Create a root project directory and initialize the file structure:
    - `cypress/e2e/security_auth_audit.cy.js`
    - `cypress.config.js`
2. Configure `cypress.config.js` with target app base URL settings.
3. Open the Cypress Test Runner for interactive execution:Bash
    
    ```powershell
    npx cypress open
    ```
    
4. Select **E2E Testing**, choose **Chrome**, and launch `security_auth_audit.cy.js`.

### Test Script

JavaScript

```jsx
describe('E2E Authentication & Client-Side Vulnerability Assessment', () => {

  it('Executes Secure Login & Asserts Session Token Storage Controls', () => {
    // 1. Intercept auth API call prior to action to avoid network race conditions
    cy.intercept('POST', '/api/v1/auth/login').as('loginReq');

    // 2. Perform authentication flow
    cy.visit('/login');
    cy.get('#username').type('sec_auditor');
    cy.get('#password').type('SuperSecret123!');
    cy.get('#login-btn').click();

    // 3. Verify successful authentication routing
    cy.url().should('include', '/dashboard');

    // Security Assertion 1: Validate Auth Response & Security Headers
    cy.wait('@loginReq').then((interception) => {
      expect(interception.response.statusCode).to.eq(200);
      expect(interception.response.headers).to.have.property('content-security-policy');
      expect(interception.response.headers).to.have.property('x-content-type-options', 'nosniff');
    });

    // Security Assertion 2: Verify Auth Token is NOT exposed unencrypted in LocalStorage
    cy.window().then((win) => {
      expect(win.localStorage.getItem('jwt_token')).to.be.null;
      expect(win.localStorage.getItem('access_token')).to.be.null;
    });

    // Security Assertion 3: Verify Session Cookie carries Secure & HttpOnly flags
    cy.getCookie('session_id').should('exist').and('have.property', 'httpOnly', true);
    cy.getCookie('session_id').should('have.property', 'secure', true);
  });
});
```

### Expected Test Results

- **HTTP Status:** 200 OK (Auth Route)
- **Test Suite Status:** 3/3 Passed (PASS)
- **Assertions Evaluated:**
    - Validate Auth Response & Security Headers — PASS
    - Verify Auth Token is NOT exposed unencrypted in LocalStorage — PASS
    - Verify Session Cookie carries Secure & HttpOnly flags — PASS

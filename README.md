````markdown name=README.md url=https://github.com/dievyak/cypress-test-suite/blob/802a4bd0ffbdc57c6f1b5d40efbe8b2a1d5cdcd8/README.md
# cypress-test
Welcome to my curated list of Cypress tests gathered from my own experience in UI testing. This guide is for QA Automation engineers building scalable and maintainable Cypress frameworks.

🚀 Reliable browser-based tests  
✅ Automated validation of UI components and functional tests  
🔄 Reusable custom commands and fixtures  

---

## 🧩 Core Cypress Practices

- **Accessibility**: Enhance accessibility testing by integrating custom plugins — e.g., the open-source WICK-A11Y Cypress Accessibility plugin.
- **Productivity**: Boost productivity with IDE extensions like VS Code Cypress Snippets for faster test writing.
- **Cypress Cloud**: Use for analytics, test reporting, parallel execution, UI coverage metrics, and AI-powered insights.

---

## 🧩 UI Elements Manipulation

- **Selector Strategies**: Assert element states using partial class selectors (`[class*="disabled-true"]`, `[class*="isError-true"]`) when no unique attributes exist.
- **DOM Traversal**: Locate child elements and access parent:  
  `cy.get('#element').parent()`
- **Off-screen Interaction**: Scroll elements into view before interacting:  
  `cy.get('#element').scrollIntoView().click()`
- **Attribute Validation**:  
  `cy.get('#element').should('have.attr', 'data-state', 'off')`

---

## 🧠 Advanced Cypress Practices

### ⚠️ Alert & Confirmation Handling

Intercept browser alerts:  
```js
cy.on('window:alert', (text) => {
  expect(text).to.equal('Expected alert message');
});
```

Handle confirmation dialogs:  
- Simulate clicking “OK”:
  ```js
  cy.on('window:confirm', (text) => {
    expect(text).to.equal('Are you sure?');
    return true;
  });
  ```
- Simulate clicking “Cancel”:
  ```js
  cy.on('window:confirm', (text) => {
    expect(text).to.equal('Are you sure?');
    return false;
  });
  ```

### 🎯 Input and Assertion Tricks

- **Force-clear inactive inputs**:  
  `cy.get('input[name="email"]').clear({ force: true }).type('test@example.com')`
- **RegEx assertions for dynamic content**:  
  `expect(invokedNormalizedText).to.match(/Min:\s*\d+\sUSD\s*-\s*Max:\s*\d+\sUSD/)`

### 🏷️ Test Tags and Browser Events

- **Test tags** for targeted execution:
  ```js
  // testSuite.spec.js
  describe("Regression Tests", { tags: ["regression"] }, () => {
    it("Should test something", () => {
      // Test code
    });
  });
  ```
- **Trigger browser events**:
  ```js
  cy.get('#passwordInput')
    .clear({ force: true })
    .type(phoneNumber)
    .trigger("input")
    .trigger("change");
  ```

---

## ⚙️ Configuration and Environment Setup

- **Custom timeouts**:
  ```js
  defaultCommandTimeout: 40000,
  requestTimeout: 20000,
  responseTimeout: 20000
  ```
- **Multiple config files**:  
  `npx cypress run --config-file cypress.stage.config.js --headless`
- **Environment variables via CLI**:  
  `--env url=stage`
- **Mobile simulation**:  
  `cy.viewport('iphone-16')`
- **Environment management**:  
  Use `Cypress.env()` to manage variables for dynamic config.

---

## 🌐 Environment-Specific URL Configuration

Centralized module example:
```js
// environmentURLs.js
const URL = {
  stage: { homePage: "https://myhomepage.stage.com" },
  dev:   { homePage: "https://myhomepage.dev.com" }
};
export default URL;

// Usage in tests
const environment = Cypress.env("url") || "stage";
const envConfig = URL[environment];
const homePageURL = envConfig.homePage;
```

---

## 🧪 Running Cypress Tests via npm Scripts

**package.json**
```json
"scripts": {
  "cypress:tests:cloud:stage": "npx cypress run --config-file cypress.stage.config.js --headless --record --key {{CYPRESS_CLOUD_PROJECT_KEY}} --env url=stage"
}
```
Run with:
```
npm run cypress:tests:cloud:stage
```
Features:
- Uses the stage config file
- Headless execution
- Results recorded to Cypress Cloud
- Stage environment injected via CLI

---

## 🔁 Advanced Command Chaining & Flow Control

- **Assertion chaining**:
  `cy.should('be.visible').and('include.text', 'Success')`
- **Time control**:
  ```js
  cy.clock();
  cy.get('#toast-btn').click();
  cy.tick(3000);
  cy.get('.toast').should('not.exist');
  ```
- **Dynamic value access**:
  ```js
  cy.get('#user-email').invoke('text').then((email) => {
    expect(email).to.include('@');
  });
  ```
- **DOM iteration**:
  ```js
  cy.get('.product-item').each(($el) => {
    cy.wrap($el).should('be.visible');
  });
  ```
- **External data wrapping**:
  ```js
  const user = { name: 'John' };
  cy.wrap(user).its('name').should('eq', 'John');
  ```
- **Spread multiple aliases**:
  ```js
  cy.get('.user-name').as('name');
  cy.get('.user-age').as('age');
  cy.get('@name').then((nameElement) => {
    cy.get('@age').then((ageElement) => {
      cy.wrap([nameElement.text(), ageElement.text()])
        .spread((name, age) => {
          expect(name).to.not.be.empty;
          expect(Number(age)).to.be.greaterThan(0);
        });
    });
  });
  ```
- **jQuery integration**:
  `cy.get('#element').invoke('val').should('eq', 'expectedValue')`
- **Nested property access**:
  `cy.window().its('navigator.language').should('eq', 'en-US')`
- **Aliasing elements**:
  `cy.get('#user-profile').as('profile'); cy.get('@profile').should('be.visible')`
- **Utility libraries**:
  ```js
  const items = [1, 2, 3];
  const doubled = Cypress._.map(items, i => i * 2);
  expect(doubled).to.deep.equal([2, 4, 6]);
  ```
- **Async logic**:
  ```js
  return new Cypress.Promise((resolve) => {
    setTimeout(() => resolve('done'), 1000);
  });
  ```
- **Manual retry**:
  ```js
  cy.get('.status').then(($el) => {
    const checkStatus = () => {
      if ($el.text() !== 'Ready') {
        setTimeout(checkStatus, 500);
      } else {
        expect($el.text()).to.equal('Ready');
      }
    };
    checkStatus();
  });
  ```

---

## 🌍 Cross-Origin Testing (Cypress 12+)

```js
cy.visit('http://localhost:3000');
cy.get('#login-button').click();

cy.origin('https://auth.example.com', () => {
  cy.get('#username').type('testuser');
  cy.get('#password').type('testpass');
  cy.get('#submit').click();
});
cy.url().should('include', 'dashboard');
```

---

## 📍 Scoped Actions

```js
cy.get('#login-form').within(() => {
  cy.get('input[name="email"]').type('user@example.com');
  cy.get('input[name="password"]').type('123456');
});
```

---

## ⏳ Smart Waiting

- Use network-aware `cy.wait('@alias')` instead of fixed waits:
  ```js
  cy.intercept('/api/user').as('getUser');
  cy.visit('/dashboard');
  cy.wait('@getUser');
  ```

---

## 🔀 Conditional Logic

- Use [cypress-if plugin](https://github.com/bahmutov/cypress-if) for conditional flows:
  ```js
  cy.get('button#subscribe')
    .if('exists')
    .click();
  ```

---

## 🧪 API Testing Techniques with Cypress

- **Intercept and wait for API calls**:
  ```js
  cy.intercept('GET', '/api/users').as('getUsers');
  cy.wait('@getUsers').its('response.statusCode').should('eq', 200);
  ```
- **Validate API response details**:
  `expect(response.statusCode).to.eq(200);`
- **Node operations**:
  `cy.task('getItem', 'bearerUserToken');`
- **Authenticated requests**:
  ```js
  cy.request({
    method: 'GET',
    url: yourURL,
    headers: { Authorization: `Bearer ${bearerUserToken}` },
  });
  ```
- **Custom commands**, **GraphQL**, **Session/token management**:  
  Write reusable commands and templates to simplify API interactions.
- **WebSockets**, **schema validation**, **chained requests**, **header/cookie assertions**, **pre- and post-test data management**, **mocking errors**, **integrated UI/API flows**.
- **Readability**: Use `cy.log()` in custom commands.
- **Extract values**:
  ```js
  cy.request('/api/users').then((response) => {
    const userId = response.body.data[0].id;
    expect(userId).to.exist;
  });
  ```
- **Fixtures**:
  ```js
  cy.fixture('user.json').then((user) => {
    expect(user.name).to.equal('Jane');
  });
  ```
- Use **WireMock** for advanced API mocking.

---

## 🧭 DOM, Forms & User Interaction

Best practices for simulating and validating user behavior:

- **Typing Simulation**: `.type({ delay: 100 })`
- **Download Testing**: Use `cypress-downloadfile` plugin.
- **Visibility vs. Existence**: Assertions with `should('be.visible')` vs. `should('exist')`.
- **Forced Interactions**: `.click({ force: true })`
- **Viewport Navigation**: `.scrollTo('top')` & `.scrollTo('bottom')`
- **Checkbox Handling**: `.check()`, `.uncheck()`
- **Dropdown Selection**: `.select()`
- **Dynamic Validation**: Assert form errors/messages
- **Date Picker Control**: Use `cy.clock()` + `.type()`
- **Element Iteration**: `.each()`
- **Form Submission**: `cy.submit()`

---

## 🧠 Debugging & Testing Strategy

- **Issue Investigation**: `debug()`, `pause()`, `cy.stop()`
- **Inline Logging**: `console.log()` inside `.then()`
- **Retry Logic**: `Cypress.config('retries', 2)`
- **Test Structure**: Arrange → Act → Assert
- **File Upload Testing**: `.selectFile()`
- **Viewport Management**: `cy.viewportPreset()` or custom
- **Page Object Model**: Architecture pattern for reuse/scalability
- **Test Grouping**: `describe()`, `.only`
- **Conditional Execution**: `.skip()` or inside `it()`
- **Shared Setup**: `beforeEach()`

---

## 🧰 Additional Tools & Plugins

- **Cypress Real Events**: Simulate native browser events
- **Cypress File Upload**: Handle file uploads

---

## 🔐 Auth & Session Management Techniques

- **Session caching**: Use `cy.session()` for persisted login across tests
- **API-based Login**: Authenticate via API; store tokens in localStorage
- **Storage cleanup**: Clear local/sessionStorage between tests (Cypress does this by default)
- **Cookie management & validation**: Use `cy.setCookie()`, `cy.clearCookies()`, and `cy.getCookie()`
- **Role-based Access**: Test with multiple tokens
- **Redirect Verification**: Test unauthenticated redirects
- **Session Simulation**:  
  ```js
  cy.window().then(win => win.sessionStorage.setItem(...));
  ```
- **Credentials**: Store with `Cypress.env()`
- **UI bypass**: Authenticate via API with `cy.request()`

---

## ☁️ CI, Cloud & Reporting

- **Advanced Reporting**: Mochawesome integration
- **Video Debugging**: Enable video recording for easier failure analysis
- **CI/CD Platforms**: Github Actions, Gitlab CI, Jenkins, Argo Workflows, etc.
- **Test Filtering**: cypress-grep plugin for tags/regex
- **Slack Notifications**: Via webhook or Cypress Cloud integration
- **Failure Screenshots**: Only on test failures
- **Parallel Execution**: Use Cypress Cloud
- **Environment Config**: Set `baseUrl` in config or via CLI
- **Dev/Test Automation**: Use `start-server-and-test`

---

## 🧩 Final Touches

- **Localization Testing**: Switch languages via query parameters/UI toggles
- **Feature Flags**: Mock/toggle feature flags for conditional flows
- **Visual Regression**: Percy or Applitools integration
- **Boilerplate Repository**: Maintain a starter repo for team onboarding
- **Changelog Awareness**: Track and respond to Cypress updates

---

### 🔔 Slack Notification Integration (Cypress Cloud)

Get real-time Cypress test results in Slack:

**1. Create Cypress Cloud Project**
- Add your projectId to `cypress.config.js`:
  ```js
  const { defineConfig } = require('cypress');
  module.exports = defineConfig({
    projectId: 'your-project-id',
  });
  ```

**2. Create Slack Channel**
- E.g., `#cypress-tests`

**3. Connect Slack in Cypress Cloud**
- Go to your project → Settings → Integrations → Add Slack → Select channel

**4. Done!**  
Receive automated test run updates in Slack 🚀

---

## 🙌 Contributions Welcome

I'm always looking to expand and improve this Cypress testing guide.  
Open an issue or submit a PR for tips, enhancements, or corrections 🤝  

Whether it’s new testing tricks, plugin recommendations, or CI/CD tweaks—your input helps the whole community!
````

Welcome to my curated list of cypress tests gathered from my own experience in UI tests. This guide is for QA Automation engineers building scalable and maintainable Cypress frameworks
🚀 reliable browser-based tests 
✅ Automated validation of UI components and functional tests
🔄 Reusable custom commands and fixtures 

 
 🧩 Core Cypress Practices:
 
Enhance accessibility testing by integrating custom plugins tailored to your framework — for example, the open-source WICK-A11Y Cypress Accessibility plugin.

Boost productivity with IDE extensions like VS Code Cypress Snippets for faster test writing and improved developer experience.

Leverage Cypress Cloud for advanced capabilities including analytics, test reporting, parallel execution, UI coverage metrics, and AI-powered insights.

🧩 UI Elements Manipulation:

Assert element states using partial class selectors when no unique attributes are available — e.g., [class*="disabled-true"] or [class*="isError-true"].

Traverse DOM hierarchy by locating a child element and accessing its parent via .parent() — e.g., cy.get('#element').parent().

Interact with off-screen elements by scrolling them into view before performing actions — e.g., cy.get('#element').scrollIntoView().click().

Validate UI attributes using Cypress’s .should() assertion — e.g., cy.get('#element').should('have.attr', 'data-state', 'off').

🧠 Advanced Cypress Practices:

⚠️ Alert & Confirmation Handling:

Intercept browser alerts using cy.on('window:alert') to verify alert messages and simulate user interactions.

Handle confirmation dialogs with cy.on('window:confirm') to assert dialog text and control the response:

✅ Simulate clicking “OK”:

\\\ cy.on('window:confirm', (text) => {
  expect(text).to.equal('Are you sure?');
  return true; // Confirms the action
}); \\\

❌ Simulate clicking “Cancel”:

cy.on('window:confirm', (text) => {
  expect(text).to.equal('Are you sure?');
  return false; // Aborts the action
});

🎯 Input and Assertion Tricks:

Force-clear inactive inputs using .clear({ force: true }) when the input field isn't focused or interactable:
cy.get('input[name="email"]').clear({ force: true }).type('test@example.com');
Use regular expressions in assertions for flexible and reliable text validation — ideal for dynamic content like price ranges:
expect(invokedNormalizedText).to.match(/Min:\s*\d+\sUSD\s*-\s*Max:\s*\d+\sUSD/);

🏷️ Test Tags and Browser Events:

Organize test suites using Cypress tags to enable targeted execution and filtering:
// testSuite.spec.js
describe("Regression Tests", { tags: ["regression"] }, () => {
  it("Should test something", () => {
    // Test code
  });
});

Simulate browser events with .trigger() to replicate interactions like drag-and-drop or custom input behaviors not natively supported by Cypress:

cy.get('#passwordInput')
  .clear({ force: true })
  .type(phoneNumber)
  .trigger("input")
  .trigger("change");

⚙️ Configuration and Environment Setup:

Customize timeouts to improve test stability and control:
defaultCommandTimeout: 40000,
requestTimeout: 20000,
responseTimeout: 20000

Create multiple config files to support different environments or test states — e.g., cypress.stage.config.js:

npx cypress run --config-file cypress.stage.config.js --headless

Pass environment-specific variables via CLI flags for flexible URL targeting:

--env url=stage

Simulate mobile devices using cy.viewport() for responsive UI testing:

cy.viewport('iphone-16');

Manage environment variables with Cypress.env() to dynamically adapt tests based on context and configuration.

🌐 Environment-Specific URL Configuration:

Define environment-specific base URLs in a centralized module to streamline testing across multiple environments:
// environmentURLs.js

const URL = {
  stage: {
    homePage: "https://myhomepage.stage.com",
  },
  dev: {
    homePage: "https://myhomepage.dev.com",
  }
};

export default URL;

Use this configuration in your tests by dynamically referencing the appropriate environment via CLI flags or Cypress.env() to ensure flexibility and maintainability across staging, development, and production pipelines.

// Get environment variable and default to 'stage' if none is provided
const environment = Cypress.env("url") || "stage";
// Construct environment specific URL object
const envConfig = URL[environment];
// Construct environment specific the API URL based on the environment
const homePageURL = envConfig.homePage;

🧪 Running Cypress Tests via npm Scripts:

Streamline test execution using custom npm scripts for CLI-based runs. This improves consistency and simplifies command usage across environments:
// package.json
"scripts": {
  "cypress:tests:cloud:stage": "npx cypress run --config-file cypress.stage.config.js --headless --record --key {{CYPRESS_CLOUD_PROJECT_KEY}} --env url=stage"
}

Run your tests with:
npm run cypress:tests:cloud:stage

This command Uses the stage-specific config file

Runs tests in headless mode

Records results to Cypress Cloud

Injects the stage environment URL via CLI flag

🔁 Advanced Command Chaining & Flow Control:

Master Cypress’s powerful chaining and control mechanisms to write expressive, resilient, and efficient tests:

✅ Assertion Chaining:

Combine multiple assertions using .should() and .and():
cy.should('be.visible').and('include.text', 'Success');

⏱️ Time Control:

Use cy.clock() and cy.tick() to simulate time-based behavior (e.g., timers, polling):
cy.clock();
cy.get('#toast-btn').click();
cy.tick(3000); // Fast-forward 3 seconds
cy.get('.toast').should('not.exist');

🔄 Dynamic Value Access:

Chain .then() to extract and assert dynamic values:
cy.get('#user-email').invoke('text').then((email) => {
  expect(email).to.include('@');
});

🔁 DOM Iteration:

Use .each() to loop through elements:
cy.get('.product-item').each(($el) => {
  cy.wrap($el).should('be.visible');
});

📦 External Data Wrapping:

Wrap external objects with cy.wrap():
const user = { name: 'John' };
cy.wrap(user).its('name').should('eq', 'John');

📤 Spread Multiple Aliases:

Use .spread() to handle multiple aliases:
cy.get('.user-name').as('name');
cy.get('.user-age').as('age');

cy.get('@name').then((nameElement) => {
  cy.get('@age').then((ageElement) => {
    cy.wrap([nameElement.text(), ageElement.text()]).spread((name, age) => {
      expect(name).to.not.be.empty;
      expect(Number(age)).to.be.greaterThan(0);
    });
  });
});

🧩 jQuery Integration:

Invoke jQuery functions with .invoke():
cy.get('#element').invoke('val').should('eq', 'expectedValue');

🧬 Nested Property Access:

Use .its() to access deep properties:
cy.window().its('navigator.language').should('eq', 'en-US');

🏷️ Aliasing Elements:

Alias elements with .as() for reuse:
cy.get('#user-profile').as('profile');
cy.get('@profile').should('be.visible');

🛠️ Utility Libraries:

Use Cypress._ (Lodash) for data manipulation:
const items = [1, 2, 3];
const doubled = Cypress._.map(items, (i) => i * 2);
expect(doubled).to.deep.equal([2, 4, 6]);

🔄 Async Logic:

Use Cypress.Promise for custom async flows:
return new Cypress.Promise((resolve) => {
  setTimeout(() => {
    resolve('done');
  }, 1000);
});

🔁 Retry Logic:

Implement manual retry with .then() or use the .retry() plugin:
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

🌍 Cross-Origin Testing (Cypress 12+):

Use cy.origin() to interact with external domains:
cy.visit('http://localhost:3000');
cy.get('#login-button').click();

cy.origin('https://auth.example.com', () => {
  cy.get('#username').type('testuser');
  cy.get('#password').type('testpass');
  cy.get('#submit').click();
});

cy.url().should('include', 'dashboard');

📍 Scoped Actions:

Use .within() to limit DOM traversal to a specific container:
cy.get('#login-form').within(() => {
  cy.get('input[name="email"]').type('user@example.com');
  cy.get('input[name="password"]').type('123456');
});

⏳ Smart Waiting:

Replace fixed waits with network-aware .wait('@alias'):
cy.intercept('/api/user').as('getUser');
cy.visit('/dashboard');
cy.wait('@getUser'); // Waits for the request to complete

🔀 Conditional Logic:

Use the cypress-if plugin for conditional flows:
cy.get('button#subscribe')
  .if('exists') // Executes only if the button exists
  .click();

🧪 API Testing Techniques with Cypress:

Demonstration of advanced API testing strategies integrated with Cypress for robust and maintainable test automation.
Key Practices

Intercept and wait for API calls:

Use cy.intercept() to capture network requests and wait for responses before asserting UI elements — avoid using cy.wait(ms) delays.
cy.intercept('GET', '/api/users').as('getUsers');
cy.wait('@getUsers').its('response.statusCode').should('eq', 200);

Assert API responses using expect:

Validate API response details, such as status codes or payload values:
expect(response.statusCode).to.eq(200);

Leverage cy.task for Node operations:

Execute backend operations (e.g., storing or fetching values):
cy.task('getItem', 'bearerUserToken');

Pass headers in API requests:

Include authentication tokens or custom headers when needed:
cy.request({
  method: 'GET',
  url: yourURL,
  headers: { Authorization: `Bearer ${bearerUserToken}` },
});

Create reusable custom commands:

Define custom Cypress commands for REST API calls and reuse them as templates for consistent testing.

GraphQL testing:

Auto-generate GraphQL commands using schema introspection.
Write custom commands for GraphQL queries and mutations to streamline testing.

Session and token management:

Handle user authentication via API (e.g., login, refresh tokens).

Real-time testing with WebSockets:

Test live event-based APIs (e.g., SignalR) for real-time data validation.

Schema validation:

Validate API response schemas using tools like cypress-ajv-schema-validator
 or zod.

Chain API requests:

Combine multiple cy.request() calls for end-to-end API workflow testing.

Assertions for headers, cookies, and status codes:

Ensure correct response metadata and authentication behavior.

Pre-test and post-test data management:

Use APIs to prepare test data or perform cleanup operations.

Combine UI and API tests:

Chain UI actions with API verifications for complete coverage.

Simulate API failures:

Mock error responses (e.g., 401 Unauthorized, 500 Server Error) to validate UI error handling.

Improve test readability:

Use cy.log() within custom commands for better debugging and traceability.

Extract dynamic values from API responses:

cy.request('/api/users').then((response) => {
  const userId = response.body.data[0].id;
  expect(userId).to.exist;
});

Load mock data using fixtures:

cy.fixture('user.json').then((user) => {
  expect(user.name).to.equal('Jane');
});

Use WireMock for API mocking — Simulate complex or controlled API behaviors.

🧪 Debugging & Testing Strategy:

To ensure robust and maintainable Cypress tests, the following practices are employed:

Issue Investigation: Utilize debug(), pause(), and cy.stop() to inspect and isolate test failures.

Inline Logging: Apply console.log() within .then() blocks for real-time visibility into test flow and data.

Retry Logic: Configure automatic retries using Cypress.config('retries', 2) to handle intermittent failures.

Test Structure: Adopt the Arrange → Act → Assert pattern for clarity and consistency.

File Upload Testing: Use .selectFile() to simulate file uploads effectively.

Viewport Management: Set screen sizes with cy.viewportPreset() or define custom dimensions for responsive testing.

Architecture Pattern: Implement the Page Object Model to promote reusable and scalable test components.

Test Grouping: Organize test suites using describe() and selectively run tests with .only.

Conditional Execution: Skip tests with .skip() or apply conditional logic within it() blocks.

Shared Setup: Leverage beforeEach() to initialize common preconditions across tests.

Absolutely! Here's a polished and professional version of your **DOM, Forms & User Interaction** section for your GitHub README:

---

 🧭 DOM, Forms & User Interaction:

Best practices for simulating and validating user behavior in Cypress tests:

- Typing Simulation: Use `.type({ delay: 100 })` to mimic realistic user input timing.
- Download Testing: Integrate the `cypress-downloadfile` plugin to validate file download functionality.
- Visibility vs. Existence: Differentiate assertions with `should('be.visible')` and `should('exist')` for precise DOM checks.
- Forced Interactions: Apply `.click({ force: true })` to interact with hidden or obstructed elements.
- Viewport Navigation: Use `.scrollTo('top')` or `.scrollTo('bottom')` to control scroll behavior.
- Checkbox Handling: Toggle checkboxes with `.check()` and `.uncheck()` for accurate state testing.
- Dropdown Selection: Use `.select()` to interact with `<select>` elements and validate options.
- Dynamic Validation: Assert form validation messages and behaviors based on user input and conditions.
- Date Picker Control: Combine `cy.clock()` with `.type()` to simulate and control date inputs.
- Element Iteration: Use `.each()` to loop through and interact with multiple elements in a collection.
- Form Submission: Trigger form actions using `cy.submit()` to validate end-to-end workflows.


🧠 Debugging & Testing Strategy:

Techniques for building resilient and readable tests:

Issue Investigation: Use debug(), pause(), cy.stop().

Inline Logging: Use console.log() inside .then().

Retry Logic: Set Cypress.config('retries', 2).

Test Structure: Follow Arrange → Act → Assert.

File Upload Testing: Use .selectFile().

Viewport Management: Use cy.viewportPreset() or custom sizes.

Architecture Pattern: Apply Page Object Model.

Test Grouping: Use describe() and .only.

Conditional Execution: Use .skip() or conditional it().

Shared Setup: Use beforeEach() for common preconditions.

🧰 Additional Tools & Plugins:

Cypress Real Events: Simulate native browser events.
Cypress File Upload: Handle file uploads.

🔐 Auth & Session Management Techniques:

Strategies for handling authentication and session state in Cypress tests:

Session Caching: Use cy.session() to persist login sessions across tests for faster execution.

API-Based Login: Authenticate via API and store access tokens in localStorage to bypass UI login flows.

Storage Cleanup: Clear localStorage and sessionStorage before each test. Cypress handles this automatically unless overridden.

Cookie Management: Set and clear cookies using cy.setCookie() and cy.clearCookies() as needed.

Cookie Validation: Assert cookie presence and values with cy.getCookie().

Role-Based Access: Test multi-role scenarios by dynamically switching authentication tokens.

Redirect Verification: Confirm unauthenticated users are properly redirected to login or error pages.

Session Simulation: Use cy.window().then(win => win.sessionStorage.setItem(...)) to mock session data.

Secure Credentials: Store sensitive data like usernames and passwords using Cypress.env() for secure access.

UI Bypass: Skip login screens entirely by issuing direct API calls with cy.request().

☁️ CI, Cloud & Reporting:

Enhance your Cypress test automation with powerful reporting, cloud execution, and CI/CD integration:

Advanced Reporting: Integrate Mochawesome for rich, interactive test reports.

Video Debugging: Enable video recording to capture test execution and simplify failure analysis.

CI/CD Compatibility: Run tests seamlessly on platforms like GitHub Actions, GitLab CI, Jenkins, Argo Workflows, and more.

Test Filtering: Use the cypress-grep plugin to selectively run tests by tag or regular expression.

Slack Notifications: Send test results to Slack via webhook or through native Cypress Cloud integration.

Failure Screenshots: Configure Cypress to capture screenshots only on test failures for cleaner logs.

Parallel Execution: Accelerate test runs by executing them in parallel using Cypress Cloud.

Environment Configuration: Set baseUrl in your config file or pass via CLI to support multi-environment testing.

Dev/Test Automation: Use start-server-and-test for streamlined one-liner commands
that start your app and run tests.

🧩 Final Touches:

Refinements and advanced practices to elevate your Cypress testing workflow:

Localization Testing: Validate internationalization by switching language via query parameters or UI toggles.

Feature Flag Simulation: Mock or toggle feature flags to test conditional behaviors and rollout scenarios.

Visual Regression: Integrate tools like Percy or Applitools for automated visual testing across environments.

Boilerplate Repository: Maintain a Cypress starter repo with preconfigured patterns, plugins, and best practices for team onboarding.

Changelog Awareness: Track Cypress updates and breaking changes by maintaining a project changelog and regularly reviewing the official Cypress Changelog.

🔔 Slack Notification Integration (Cypress Cloud):

Receive real-time Cypress test results directly in Slack with a Cypress Cloud subscription—set it up in just four steps:

1. Create a Cypress Cloud Project:

Navigate to the Cypress Dashboard and create a new project.

Add your projectId to cypress.config.js
const { defineConfig } = require('cypress');

module.exports = defineConfig({
  projectId: 'your-project-id',
});

2. Create a Slack Channel:

Set up a dedicated Slack channel (e.g., #cypress-tests) to receive notifications.

3. Connect Slack via Cypress Cloud:

In Cypress Cloud: Go to your project → Settings → Integrations → Add Slack → Select your channel.

4. Done!
You’ll now receive automated test run updates directly in Slack 🚀

🙌 Contributions Welcome
I´m always looking to improve and expand this Cypress testing guide. Feel free to suggest new tips, enhancements, or corrections by opening an issue or submitting a pull request 🤝

Whether it’s a clever testing trick, plugin recommendation, or CI/CD tweak—your input helps the whole community!

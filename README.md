# TypeScript Cucumber + Playwright API Automation Framework

This project is a complete API automation framework built using **TypeScript**, **Cucumber (BDD)**, and **Playwright**, following the **Page Object Model** pattern and supporting **Allure**, **ESLint**, **SonarQube**, **.env configuration**, and multiple **Cucumber reports**.

---

## 🚀 Features
- Page Object Model pattern
- Cucumber BDD (Gherkin syntax)
- Hooks for setup/teardown
- Utility functions
- Environment-based configuration using `.env`
- Full API testing with separate classes for each HTTP method
- Reporting with Allure, Trace Viewer, Cucumber HTML
- Code quality via ESLint and SonarQube
- GitHub Actions CI pipeline integration

---

## 📁 Folder Structure
```
.
├── .env
├── .eslintrc.js
├── cucumber.json
├── package.json
├── tsconfig.json
├── /src
│   ├── /features
│   │   ├── example.feature
│   │   ├── post.feature
│   │   ├── put.feature
│   │   ├── delete.feature
│   │   └── /step-definitions
│   │       ├── example.steps.ts
│   │       ├── post.steps.ts
│   │       ├── put.steps.ts
│   │       └── delete.steps.ts
│   ├── /api
│   │   ├── Get.ts
│   │   ├── Post.ts
│   │   ├── Put.ts
│   │   └── Delete.ts
│   ├── /hooks
│   │   └── hooks.ts
│   ├── /pages
│   │   └── ApiPage.ts
│   ├── /utils
│   │   └── apiHelper.ts
│   └── config.ts
├── /reports
│   ├── cucumber-html
│   └── allure-results
├── /allure-results
│   └── environment.properties
└── /.github
    └── /workflows
        └── ci.yml
```

---

## ⚙️ Setup Instructions

### 1. Clone the Repo
```bash
git clone <repo-url>
cd automation-framework
```

### 2. Install Dependencies
```bash
npm install --save-dev \
  @cucumber/cucumber \
  @playwright/test \
  @types/node \
  typescript \
  ts-node \
  dotenv \
  eslint \
  multiple-cucumber-html-reporter \
  allure-commandline
```

### 3. Create tsconfig.json
```bash
npx tsc --init
```

### 4. Install Playwright Browsers
```bash
npx playwright install
```

### 5. (Optional) Install Allure CLI Globally
```bash
npm install -g allure-commandline --save-dev
```

### 6. Configure Environment
Create a `.env` file:
```ini
BASE_URL=https://jsonplaceholder.typicode.com
```

### 7. Run Tests
```bash
npx cucumber-js --require-module ts-node/register --require src/features/**/*.ts
```

### 8. Generate Reports
- **Allure**:
```bash
npx allure generate allure-results --clean -o allure-report && npx allure open allure-report
```
- **Cucumber HTML**:
```bash
npm run cucumber-report
```
- **Playwright Trace**:
  Traces saved as zip in results, open using `npx playwright show-trace trace.zip`

### 9. Allure environment.properties (Optional)
Create `allure-results/environment.properties` file:
```properties
Environment=Staging
BaseURL=https://jsonplaceholder.typicode.com
Tester=YourName
```

---

## ✅ Example Tests

### File: /src/features/post.feature
```gherkin
Feature: POST API Example

  Scenario: Verify POST request
    Given I perform POST request on "/posts" with body:
      | title  | foo     |
      | body   | bar     |
      | userId | 1       |
    Then the response status should be 201
```

### File: /src/features/step-definitions/post.steps.ts
```ts
import { Given, Then } from '@cucumber/cucumber'
import { expect } from '@playwright/test'
import { Post } from '../../api/Post'

let response: any

Given('I perform POST request on {string} with body:', async (endpoint: string, dataTable) => {
  const data = Object.fromEntries(dataTable.rawTable.slice(1))
  response = await Post.call(endpoint, data)
})

Then('the response status should be {int}', async (statusCode: number) => {
  expect(response.status()).toBe(statusCode)
})
```

... (remaining test examples remain unchanged)

---

## ⚙️ GitHub CI

### File: /.github/workflows/ci.yml
```yaml
name: CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm install

      - name: Install Playwright
        run: npx playwright install

      - name: Run tests
        run: npm run test

      - name: Upload Cucumber Report
        uses: actions/upload-artifact@v3
        with:
          name: cucumber-report
          path: reports/
```

---

## 🧠 Next Steps
- Add data validation and schema assertions
- Integrate test results with dashboard (e.g., Allure TestOps or custom dashboard)
- Add contract testing using OpenAPI

# TD Quality Automation Framework

A BDD-driven test automation framework built to practice and demonstrate the
exact skill set called for in TD's **Test Automation / Quality Engineer**
posting (Enterprise Marketing Technology, Toronto): Java, GitHub, RestAssured,
API testing, accessibility testing, BDD/automation frameworks, and CI/CD/CT
pipeline integration.

It uses public demo targets so anyone can clone and run it with no internal
credentials: [Sauce Demo](https://www.saucedemo.com/) for UI tests and
[reqres.in](https://reqres.in) for API tests.

## Why this project

| Job posting requirement | Where it's demonstrated here |
|---|---|
| Hands-on experience in Java, GitHub, RestAssured | Entire suite is Java 17 + Maven; API tests use RestAssured (`UserApiStepDefs`); repo lives on GitHub with a CI workflow |
| API and Accessibility testing | `features/api_users.feature` (RestAssured) and `features/accessibility.feature` (axe-core, `AccessibilityStepDefs`) |
| Build, enhance, and deploy BDD and automation frameworks | Cucumber/Gherkin `.feature` files + Selenium page-object model (`LoginPage`, `DriverFactory`) |
| Develop and automate tests, promote in-sprint automation, integrate with CI/CD/CT | `.github/workflows/ci.yml` runs the suite automatically on every push/PR, split into fast API-only and UI/accessibility jobs |
| Plan, design, and execute test activities aligned to standards | Feature files use `@smoke`, `@api`, `@ui`, `@accessibility` tags so scope can be run/reported independently — mirroring how you'd triage a smoke suite vs. full regression |
| Define and monitor metrics / communicate risk | Cucumber HTML/JSON reports (`target/cucumber-report`) give pass/fail metrics per tag; failed UI scenarios auto-attach a screenshot for fast triage |
| Service virtualization (future extension) | Framework is structured so a WireMock stub server can be dropped in behind `ApiContext.BASE_URL` without touching step definitions — noted as a next step below |

## Project structure

```
td-quality-automation-framework/
├── pom.xml
├── .github/workflows/ci.yml          # CI pipeline
├── src/test/resources/features/
│   ├── ui_login.feature              # Selenium UI scenarios
│   ├── api_users.feature             # RestAssured API scenarios
│   └── accessibility.feature         # axe-core a11y scenarios
└── src/test/java/com/eliasroro/qa/
    ├── runners/RunCucumberTest.java  # Cucumber-JUnit entry point
    ├── hooks/Hooks.java              # driver setup/teardown, screenshot-on-fail
    ├── pages/                        # Page Object Model (DriverFactory, LoginPage)
    ├── api/ApiContext.java           # shared API test state
    └── stepdefs/                     # Given/When/Then implementations
```

## Running locally

Requires JDK 17+, Maven, and Google Chrome installed.

```bash
# whole suite
mvn test

# just the API tests (fastest — no browser)
mvn test -Dcucumber.filter.tags="@api"

# just UI tests, visible (non-headless) browser
mvn test -Dcucumber.filter.tags="@ui" -Dheadless=false

# just the accessibility scan
mvn test -Dcucumber.filter.tags="@accessibility"

# only the smoke subset across all types
mvn test -Dcucumber.filter.tags="@smoke"
```

HTML report is written to `target/cucumber-report/cucumber.html` after each run.

## CI/CD

`.github/workflows/ci.yml` runs on every push/PR to `main`: API tests first
(fast feedback), then headless UI + accessibility tests, uploading the
Cucumber report as a build artifact either way.

## Possible next steps

- **Service virtualization**: introduce [WireMock](https://wiremock.org/) to
  stub the Users API so API tests run fully offline/deterministically —
  directly matching the posting's "service virtualization capabilities" line.
- **Cross-browser matrix**: extend the GitHub Actions job to a matrix over
  Chrome/Firefox using WebDriverManager's multi-browser support.
- **Parallel execution**: switch the JUnit runner to Cucumber's parallel
  JUnit Platform runner for faster CI runs as the suite grows.

## Author

Elias Roro — [github.com/EliasRoro](https://github.com/EliasRoro)

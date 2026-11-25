# UI Testing Lab (`ui-lab`)

This repository contains the automated UI testing lab exercises using **Part 3: Robot Framework** (`robot/`) and **Part 4: Cypress** (`cypress/`).

## Folders

- **`robot/`**: Holds the Robot Framework (`7.5.1`) and SeleniumLibrary (`6.9.0`) test suites (`first.robot`, `web_form.robot`, `pizza_scenario.robot`), the `resources/quickpizza.resource` keyword library, and two full headless Chrome execution reports with screenshots in `results/run1/` and `results/run2/`.
  - Run the tests (from `robot/` with `.venv` activated and `CHROMEDRIVER` set):
    ```powershell
    robot -d results/run1 -v BROWSER:headlesschrome pizza_scenario.robot
    ```
- **`cypress/`**: Holds the Cypress (`16.2.1`) configuration, `web-form.cy.js` and `pizza-scenario.cy.js` specs, the custom `adminLogin` command in `cypress/support/commands.js`, and full-run recorded videos (`cypress/videos/`) and screenshots (`cypress/screenshots/`).
  - Run all specs (from `cypress/`):
    ```powershell
    npx cypress run --browser chrome
    ```

## Results and Reflections

See [RESULTS.md](RESULTS.md) for browser/driver versions, Part 0 locators and manual walkthrough, test execution outputs from both tool parts, and Part B reflections.

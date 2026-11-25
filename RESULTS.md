# UI lab results

Tool parts that I chose: Part 3 (Robot Framework) and Part 4 (Cypress)

## Part 0
- Step 0.1, Chrome version: 157.0.8103.78 (Official Build) (64-bit) (Windows)
- Step 0.3, ChromeDriver version: ChromeDriver 157.0.8103.78 (updated from 156.0.8078.80 at `C:\drivers\chromedriver-win64\chromedriver.exe`)
- Step 0.4, locator of Pizza, Please!: `button[name="pizza-please"]` (count = 1)
- Step 0.4, locator of Excluded tools: `select` (count = 1)
- Step 0.4, locator of Must be vegetarian: `#default-checkbox` (count = 1)
- Step 0.4, locator of Custom Pizza Name: `input#pizza-name` (count = 1)
- Step 0.5, pizza name, tool and calories of the run by hand: `Lab Pizza 20261125-225800`, `Tool: Scissors`, `Calories per slice: 350`

| Step | Actual result | Pass or fail |
|------|---------------|--------------|
| 1 | Heading reads `Looking to break out of your pizza routine?` | Pass |
| 2 | `Max Calories per Slice` (default 1000), `Excluded tools`, `Must be vegetarian`, and `Custom Pizza Name` appear | Pass |
| 3 | Fields show `600`, `Knife`, `Must be vegetarian` checked, and `Lab Pizza 20261125-225800` | Pass |
| 4 | Recommendation displays `Name: Lab Pizza 20261125-225800`, `Tool: Scissors` (not `Knife`), and `Calories per slice: 350` (<= 600) | Pass |
| 5 | Clicking `Love it!` without logging in displays `Please log in first.` | Pass |
| 6 | Opening `/admin` and signing in with `admin` / `admin` displays `Latest pizza recommendations` | Pass |
| 7 | Admin list displays `Lab Pizza 20261125-225800 (tool=Scissors, ingredients_number=6) (newest)` | Pass |

## Part 3: Robot Framework
- robot --version: `Robot Framework 7.5.1 (Python 3.12.10 on win32)`
- Step 3.2, failure message for the wrong title: `Title should have been 'Web Form' but was 'Web form'.`
- Step 3.8, run 1, result, elapsed time and pizza name: `PASS`, `10.753615s` (`00:00:10.754`), `Lab Pizza 20261125-225634` (`Tool: Pizza cutter`, `Calories per slice: 450`)
- Step 3.8, run 2, result, elapsed time and pizza name: `PASS`, `10.270302s` (`00:00:10.270`), `Lab Pizza 20261125-225646` (`Tool: Scissors`, `Calories per slice: 350`)

## Part 4: Cypress
- node --version, npm --version and npx cypress version: `node v24.13.0`, `npm 11.6.2`, `Cypress 16.2.1`
- Step 4.3, failure message for the wrong title: `Timed out retrying after 10000ms` (`-'Web form'` / `+'Web Form'`)
- Step 4.6, tool and calories in the answer of POST /api/pizza: Run 1: `Tool: Scissors`, `Calories per slice: 350`; Run 2: `Tool: Pizza cutter`, `Calories per slice: 550`
- Step 4.7, the line of the admin list that shows your pizza: Run 1: `Lab Pizza 20261125-225800 (tool=Scissors, ingredients_number=6) (newest)`; Run 2: `Lab Pizza 20261125-230106 (tool=Pizza cutter, ingredients_number=8) (newest)`
- Step 4.8, run 1, result, duration and pizza name: `All specs passed!` (2/2 passing), `00:14` total (`pizza-scenario.cy.js` `00:10`, `web-form.cy.js` `00:04`), `Lab Pizza 20261125-225800`
- Step 4.8, run 2, result, duration and pizza name: `All specs passed!` (2/2 passing), `00:14` total (`pizza-scenario.cy.js` `00:11`, `web-form.cy.js` `00:03`), `Lab Pizza 20261125-230106`
- Step 4.9 (optional), browser and result: `Edge 158 (headless)` — `pizza-scenario.cy.js` passed (`1 passing` in `10s`), while `web-form.cy.js` aborted inside the Cypress Edge runner with `Error: * is not a valid URL`.

## Part B
1. QuickPizza assigns the same `id="floating_filled"` to three different numeric inputs (`Max Calories per Slice`, `Min Number of Toppings`, and `Max Number of Toppings`), so selecting `#floating_filled` returns three matches and cannot uniquely identify the calorie box. Locating it relative to its visible `<label>` text (`Max Calories per Slice`) finds only one element. Changing the application HTML to give each field a unique ID such as `id="max-calories-per-slice"` would let tests use `#max-calories-per-slice` directly. Unique IDs help testers keep locators short, fast, and independent of UI label wording changes or surrounding DOM nesting.

2. Comparing the recommended tool shown on the storefront in Step 4 with the entry persisted in `/admin` in Step 7 is a consistency test oracle across two views of the same system. In both our Robot runs (`Lab Pizza 20261125-225634` with `Pizza cutter` and `Lab Pizza 20261125-225646` with `Scissors`) and Cypress runs (`Scissors` and `Pizza cutter`), both pages matched. This oracle would catch a persistence or serialization fault in QuickPizza where the backend saves the wrong tool (or loses the selected tool and falls back to a default) in `/admin` even though the recommendation card on the main page displayed the expected value.

3. `QP-01` currently verifies the pizza name, excluded tool, and calorie limit, but never checks whether the generated pizza actually satisfies the `Must be vegetarian` constraint. A straightforward check would inspect the `POST /api/pizza` JSON response via `cy.intercept` and assert `expect(response.body.vegetarian).to.equal(true)`. In addition, a UI check could read the bullet list of ingredients inside `#recommendations li` and verify none of them belong to a known list of meat ingredients (for example checking against meats shown in non-vegetarian runs such as `Frutti di Mare` or `Prosciutto`). That check would require either an authoritative list of non-vegetarian ingredients from QuickPizza's catalog or relying on the API's `vegetarian` boolean field.

4. Both tools handled QuickPizza's asynchronous rendering without fixed sleeps, though in different ways. Robot Framework relied on explicit wait keywords such as `Wait Until Element Contains` and `Wait Until Page Contains Element` configured with `timeout=10s`, completing `pizza_scenario.robot` in `10.75s` and `10.27s`. Cypress retried `cy.get(...)` and `.should(...)` assertions automatically up to `defaultCommandTimeout: 10000` and let us wait deterministically on `POST /api/pizza` with `cy.intercept` and `cy.wait('@pizza')`, taking `10–11s` for `pizza-scenario.cy.js` (`14s` total per full run including `web-form.cy.js` and `.mp4` recording). Robot's `results/run1/log.html` gave clearer step-by-step pass logs with exact extracted variable values, while Cypress's `.mp4` videos and command log screenshots visually showed both the assertion timeline and the rendered page together.

5. For Project 2, I choose **Robot Framework** (which matches our Group 06 `UI testing/` implementation). First, Project 2 system test cases span separate Staff (`WP Admin`) and Customer storefront sessions, which Robot Framework handles cleanly using multiple named browser aliases (`Open Browser ... alias=staff/customer` and `Switch Browser`) without Cypress's single-browser cookie-clearing behavior or cross-origin restrictions. Second, Robot's keyword-driven `.resource` files map almost one-to-one with written manual system test steps, and `report.html`/`log.html` make every step expectation and screenshot immediately auditable against the manual test case table.

6. (Part 3 — Robot Framework) If QuickPizza changed the DOM structure or label around the `Max Calories per Slice` box, only a single variable definition (`${MAX CALORIES}` at line 10 of `robot/resources/quickpizza.resource`) would need to be updated. The test file `pizza_scenario.robot` only calls high-level business keywords like `Turn On Advanced` and `Fill In The Pizza Request` without referencing any XPath or CSS selector directly. Centralizing selectors and waits inside `quickpizza.resource` isolates test logic from UI markup changes so tests across the suite recover after editing one resource file.

7. (Part 4 — Cypress) `QP-01` must stay inside a single `it(...)` test block in Cypress because Cypress automatically clears cookies and browser state before each test case starts. Furthermore, the `@tool` alias and `pizzaName` variable created during Steps 3 and 4 live within that test closure and would not persist cleanly across independent `it(...)` blocks. If Steps 6 and 7 were moved into a second `it(...)` test, Cypress would clear the `WebSocket visitor` / server session cookie tying the browser to the QuickPizza instance holding `Lab Pizza ...` (and lose `@tool`), causing Step 7's assertion in `/admin` to miss the generated pizza or fail.

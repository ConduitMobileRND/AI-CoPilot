# Test Implementation Guide

## 🎯 Purpose

Implement automated tests based on JSON specifications created from qTest modules.

**This prompt will:**

1. ✅ **Analyze existing project structure** - detect framework, language, patterns
2. ✅ **Learn from existing code** - examine test files, page objects, utilities
3. ✅ **Inspect UI and get DOM locators** - for UI/Web tests, navigate to page and find real selectors
4. ✅ **Match existing patterns exactly** - follow naming, structure, coding conventions
5. ✅ **Reuse existing components** - update existing page objects, don't create new ones
6. ✅ **Update existing files** - add tests to existing spec files
7. ✅ **Update JSON only** - mark implementation status in JSON file

**This prompt will NOT:**

- ❌ Create new README/SUMMARY/IMPLEMENTATION markdown files
- ❌ Create new documentation after implementation
- ❌ Create new test classes (adds to existing)
- ❌ Create new page objects without explicit approval

## 📋 Prerequisites

Before running this prompt, ensure you have:

- ✅ JSON file(s) created in `.qtest/test-cases/{package}/` with automation test specifications
- ✅ Test spec file created (`.spec.ts`, `.java`, `.py`) with placeholders
- ✅ Authentication/setup tests passing (if required)

## 🚀 Usage

```
"Implement tests from {JsonFileName}.json"
"Implement tests in {package} module"
"Help me implement the automation tests"
```

---

## Step 1: Project Detection & Analysis

I will automatically analyze:

### 1.1 Detect Project Type

```bash
# Check workspace structure
ls -la packages/*/tests/           # TypeScript/Playwright (automation-web)
ls -la rest-api/src/test/          # Java/TestNG (automation-comosense)
ls -la tests/                      # Python/Pytest (automation-llm-validation)
```

**Determine:**

- 🔷 **TypeScript/Playwright** → automation-web
- 🔶 **Java/JUnit5** → automation-comosense
- 🔵 **Python/Pytest** → automation-llm-validation

### 1.2 Locate JSON Specifications

```bash
# Find JSON files with test specifications
find .qtest/test-cases/ -name "*.json" -type f
```

**Extract from JSON:**

- Test case names and PIDs (TC-XX)
- Descriptions
- Preconditions
- Test steps (if available)
- Priority levels

### 1.3 Analyze Existing Test Patterns

**TypeScript/Playwright:**

```bash
# Find existing test files to learn patterns
find packages/*/tests/ -name "*.spec.ts" -type f
grep -n "test(" packages/*/tests/*.spec.ts | head -20
grep -n "expect(" packages/*/tests/*.spec.ts | head -20
```

**Java/JUnit5:**

```bash
# Find existing test classes
find rest-api/src/test/ -name "*Test.java" -type f
grep -n "@Test" rest-api/src/test/**/*.java | head -20
grep -n "Assertions" rest-api/src/test/**/*.java | head -20
grep -n "@DisplayName" rest-api/src/test/**/*.java | head -20
```

**Python/Pytest:**

```bash
# Find existing test files
find tests/ -name "test_*.py" -type f
grep -n "def test_" tests/**/*.py | head -20
grep -n "assert " tests/**/*.py | head -20
```

**Learn:**

- Naming conventions
- Import patterns
- Helper methods/utilities
- Assertion styles
- Page Object Models (if used)
- Common patterns (login, navigation, cleanup)

### 1.4 Inspect UI and Get DOM Locators (For UI/Web Tests)

**For TypeScript/Playwright, Java/Selenium, or any UI automation:**

Before implementing Page Object Models, navigate to the actual web page and inspect the DOM to get real, working locators.

**Steps:**

1. **Run existing authentication/setup test** (if required):

   ```bash
   # TypeScript/Playwright example
   npm run test:hub:qa -- hub-smoke --headed

   # This creates auth state and you can see the UI
   ```

2. **Navigate to the target page in headed mode**:

   ```bash
   # Open browser in headed mode to inspect UI
   npm run test:hub:qa -- {test-name} --headed --debug
   ```

3. **Use Browser DevTools to inspect elements**:
   - Right-click element → Inspect
   - Look for reliable selectors in order of preference:
     - `[data-testid="..."]` - Most reliable
     - `[data-automation-id="..."]` - Automation-specific
     - `[aria-label="..."]` - Accessibility labels
     - `#id` - Unique IDs
     - `.class` - CSS classes (less reliable)
     - Text content - `text="button label"` (Playwright)
     - Role selectors - `role=button[name="Submit"]` (Playwright)

4. **Document available locators before creating Page Objects**:

   ```typescript
   // Example findings from DOM inspection:
   // Members page:
   // - Search input: [data-testid="member-search"], input[placeholder="Search members"]
   // - Member list item: [data-testid="member-item"], .member-card
   // - Member profile: [data-testid="member-profile"], #profile-container
   // - Wallet section: [data-automation-id="wallet-section"], .wallet-list
   // - Activity section: [data-testid="member-activities"], .activity-feed
   ```

5. **Test locators in browser console**:

   ```javascript
   // In browser DevTools console, test if locator works:
   document.querySelector('[data-testid="member-search"]');
   document.querySelectorAll('[data-testid="member-item"]').length;

   // For Playwright locators:
   // Use Playwright Inspector (npx playwright codegen) to record interactions
   ```

6. **Create Page Object with multiple selector strategies**:
   ```typescript
   // Use findings to create flexible Page Object with fallback selectors
   private searchInput = [
     '[data-testid="member-search"]',
     '[data-automation-id="search"]',
     'input[placeholder*="Search"]',
     'input[name="search"]',
     'role=searchbox'
   ];
   ```

**Benefits:**

- ✅ Accurate locators from the start (reduces failures)
- ✅ Multiple fallback strategies for resilience
- ✅ Understand actual UI structure before coding
- ✅ Identify missing test IDs and report to dev team
- ✅ See real element states (visible, hidden, disabled)

**Tools:**

- **Playwright Inspector**: `npx playwright codegen {url}` - Records interactions and generates code
- **Browser DevTools**: Chrome/Firefox DevTools for manual inspection
- **Playwright Test UI Mode**: `npm test -- --ui` - Interactive test runner with DOM viewer

**Note:** For API-only tests (Java/REST), skip this step as there's no UI to inspect.

---

## Step 2: Implementation Strategy

Based on project type, I will provide:

### 2.1 TypeScript/Playwright Implementation

**Pattern Analysis:**

```typescript
// Learn from existing tests
import { test, expect, Logger } from "../BaseTest";

test.describe("Module Name", () => {
  test.beforeEach(async ({ page }) => {
    // Common setup
  });

  test("TC-XX: Test description", async ({ page }) => {
    Logger.info("Starting test: TC-XX");

    // Implementation steps here

    Logger.info("Completed test: TC-XX");
  });
});
```

**Implementation Checklist:**

- [ ] Inspect UI in browser and get real DOM locators (Step 1.4)
- [ ] Import correct base test class
- [ ] Use proper Page Object Models (if available)
- [ ] Add Logger statements for traceability
- [ ] Include @QTestCase decorator with PID
- [ ] Add proper assertions with expect()
- [ ] Handle async/await correctly
- [ ] Add appropriate timeouts
- [ ] Clean up test data in afterEach

### 2.2 Java/JUnit5 Implementation

**Pattern Analysis:**

```java
// Learn from existing tests
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.DisplayName;
import static org.junit.jupiter.api.Assertions.*;

public class ModuleTest extends BaseTest {

    @Test
    @DisplayName("TC-XX: Test description")
    @QTestCase(id = "TC-XX")  // Use TC from JSON qTestPID (Workflow A) OR "" empty (Workflows B/C)
    public void testTC_XX() {
        logger.info("Starting test: TC-XX");

        // Implementation steps here

        logger.info("Completed test: TC-XX");
    }
}
```

**@QTestCase Annotation Rules:**

- **Workflow A (qTest-First):** Use `@QTestCase("TC-2415")` - JSON already has qTestPID from qTest
- **Workflow B/C (Code-First/Reverse Eng):** Use `@QTestCase("")` - Empty until synced to qTest
  - After sync to qTest → run reverse-sync → update to `@QTestCase("TC-2415")`

**Implementation Checklist:**

- [ ] Extend proper base test class
- [ ] Use @Test annotation from JUnit5
- [ ] Add @DisplayName for readable test names
- [ ] Add @QTestCase annotation with correct value (based on workflow)
- [ ] Use logger for traceability
- [ ] Add proper JUnit5 assertions (assertEquals, assertTrue, etc.)
- [ ] Use @BeforeEach/@AfterEach for setup/cleanup
- [ ] Use @BeforeAll/@AfterAll for class-level setup
- [ ] Handle test data properly
- [ ] Add proper exception handling
- [ ] Use assertThrows() for exception testing

### 2.3 Python/Pytest Implementation

**Pattern Analysis:**

```python
# Learn from existing tests
import pytest
from utils.logger import Logger

class TestModule:

    def test_tc_xx(self, setup):
        """TC-XX: Test description"""
        Logger.info("Starting test: TC-XX")

        # Implementation steps here

        Logger.info("Completed test: TC-XX")
```

**Implementation Checklist:**

- [ ] Use proper test class structure
- [ ] Add docstrings with TC-XX reference
- [ ] Use Logger for traceability
- [ ] Add pytest assertions
- [ ] Use fixtures for setup/cleanup
- [ ] Handle test data properly
- [ ] Add proper markers (@pytest.mark)

---

## Step 3: Guided Implementation

For each test in the JSON file, I will:

### 3.1 Ask for Missing Information

If test steps are not detailed enough:

```
⚠️ Test TC-XX: "{test_name}" needs more details

Current info:
- Description: {description}
- Precondition: {precondition}

I need to know:
1. What page/URL to navigate to?
2. What actions to perform (click, type, select)?
3. What elements to interact with (selectors)?
4. What to verify (assertions)?
5. Any test data required?

Please provide details or point me to:
- Manual test execution video
- UI mockups/screenshots
- Product documentation
- Similar existing test for reference
```

### 3.2 Generate Implementation Code

Based on learned patterns and provided details:

**Example - TypeScript/Playwright:**

```typescript
test("TC-XX: Verify favorite functionality", async ({ page }) => {
  Logger.info("Starting test: TC-XX - Verify favorite functionality");

  // Navigate to email templates
  await page.goto("/email-templates");
  await expect(page).toHaveURL(/email-templates/);

  // Find first template and click favorite icon
  const firstTemplate = page.locator('[data-testid="template-card"]').first();
  const favoriteBtn = firstTemplate.locator('[data-testid="favorite-btn"]');
  await favoriteBtn.click();

  // Verify template is marked as favorite
  await expect(favoriteBtn).toHaveClass(/favorited/);
  Logger.info("Template marked as favorite successfully");

  // Verify favorite appears in favorites filter
  await page.click('[data-testid="favorites-filter"]');
  await expect(firstTemplate).toBeVisible();

  Logger.info("Completed test: TC-XX");
});
```

### 3.3 Identify Required Page Objects

**Important:** Before creating Page Objects, ensure you've completed Step 1.4 (Inspect UI and Get DOM Locators) to have real, working selectors.

If Page Object Models don't exist:

```
📦 Required Page Objects:

1. EmailTemplatesPage
   Methods needed:
   - navigate()
   - getTemplateCard(index)
   - clickFavorite(templateCard)
   - filterByFavorites()

   Locators found (from Step 1.4 DOM inspection):
   - Template card: [data-testid="template-card"], .template-item
   - Favorite button: [data-testid="favorite-btn"], .favorite-icon
   - Filter: [data-testid="favorites-filter"], button[aria-label="Favorites"]

2. EmailEditorPage
   Methods needed:
   - createTemplate(name, content)
   - saveTemplate()
   - verifyWarning()

   Locators found:
   - Template name input: [data-testid="template-name"], input[name="templateName"]
   - Content editor: [data-testid="editor"], .editor-content
   - Save button: [data-testid="save-btn"], button[type="submit"]

Would you like me to create these Page Objects with the inspected locators?
```

**Best Practice:** Use multiple selector strategies per element for resilience (see Step 1.4).

### 3.4 Handle Test Data Requirements

```
📊 Test Data Needed:

TC-16 requires: 20+ email templates with different names
TC-17 requires: New member data

Options:
1. Create setup script to generate test data
2. Use existing test data (if available)
3. Create data in @BeforeAll/@BeforeEach hooks

Which approach would you prefer?
```

---

## Step 4: Implementation Workflow

### For Each Test Case:

```
1. Read JSON specification          → Extract TC-XX details
2. Check if similar test exists     → Learn from existing patterns
3. Ask for missing details          → If test steps unclear
4. Generate implementation code     → Based on framework patterns
5. Create Page Objects (if needed)  → Reusable components
6. Add test data setup (if needed)  → Setup/cleanup hooks
7. Run test locally                 → Verify it passes
8. Fix any failures                 → Debug and adjust
9. Update JSON with notes           → Document any changes
```

### Batch Implementation Strategy:

**Priority Order:**

1. ⭐ **Simple tests first** (single action, easy verification)
2. ⭐ **Complex tests next** (multiple steps, data setup)
3. ⭐ **Tests with dependencies last** (require other tests to pass)

**Group by:**

- Similar functionality (all CRUD operations together)
- Same page/module (reduce context switching)
- Shared test data (optimize setup/cleanup)

---

## Step 5: Verification & Validation

After implementation:

### 5.1 Run Tests Locally

**TypeScript/Playwright:**

```bash
cd automation-web
npm test -- packages/{package}/tests/*.spec.ts
```

**Java/JUnit5:**

```bash
cd automation-comosense
mvn test -Dtest=*Test
# Or run specific test class
mvn test -Dtest=EmailTemplateOrganizationTest
```

**Python/Pytest:**

```bash
cd automation-llm-validation
pytest tests/test_*.py -v
```

### 5.2 Fix Failures

For each failure:

1. Read error message
2. Check locators/selectors
3. Verify test data
4. Add debug logging
5. Re-run test
6. Update code

### 5.3 Validate PIDs

```bash
cd qtest-mcp-server
npm run validate:pids
```

Ensures:

- JSON PIDs match code annotations
- No duplicate PIDs
- All tests have PIDs

---

## Step 6: Next Steps After Implementation

```
1. ✅ All tests implemented and passing locally
2. ✅ PIDs validated (JSON ↔ Code sync)
3. ✅ JSON file updated with implementation status (see below)
4. ⏭️ Run regression suite (ensure no breaks)
5. ⏭️ Commit code with proper message
6. ⏭️ [Optional] Sync results to qTest
7. ⏭️ Create PR for review
```

**❌ DO NOT CREATE:**

- ❌ README-{MODULE}.md files
- ❌ IMPLEMENTATION-SUMMARY-{MODULE}.md files
- ❌ QUICK-REF-{MODULE}.md files
- ❌ UPDATE-{MODULE}.md files
- ❌ Any other documentation markdown files

**✅ ONLY UPDATE:**

- ✅ The JSON file (`.qtest/test-cases/{package}/{module}.json`) with implementation status
- ✅ Existing test code files (add tests to existing spec files)
- ✅ Existing page object files (update existing pages, don't create new ones)

**Git Commit Message Format:**

```
Add automation tests for {Module Name}

- Implemented TC-XX through TC-YY
- Added Page Objects for {pages}
- All tests passing locally

qTest Module: {moduleId}
```

---

## 🔧 Common Patterns & Solutions

### Pattern 1: Authentication Required

**TypeScript/Playwright:**

```typescript
test.use({ storageState: authFile });

test.beforeEach(async () => {
  if (!HubAuthHelper.authFileExists()) {
    throw new Error("Auth required. Run login test first.");
  }
});
```

**Java/JUnit5:**

```java
@BeforeAll
public static void authenticate() {
    authHelper.login();
}

// Or per-test authentication
@BeforeEach
public void setupAuth() {
    authHelper.ensureAuthenticated();
}
```

### Pattern 2: Dynamic Test Data

**TypeScript/Playwright:**

```typescript
test.beforeEach(async ({ page }) => {
  testData = {
    templateName: `Test_${Date.now()}`,
    folderName: `Folder_${Math.random()}`,
  };
});
```

**Java/JUnit5:**

```java
// Using @ParameterizedTest
@ParameterizedTest
@ValueSource(strings = {"Template_1", "Template_2", "Template_3"})
void testWithDifferentTemplates(String templateName) {
    // Test implementation
}

// Or using @MethodSource
@ParameterizedTest
@MethodSource("templateNameProvider")
void testWithGeneratedNames(String templateName) {
    // Test implementation
}

static Stream<String> templateNameProvider() {
    return Stream.of(
        "Template_" + System.currentTimeMillis(),
        "Template_" + UUID.randomUUID()
    );
}
```

### Pattern 3: Waiting for Elements

**TypeScript/Playwright:**

```typescript
// Wait for element to be visible
await expect(page.locator('[data-testid="element"]')).toBeVisible();

// Wait for navigation
await page.waitForURL(/expected-url/);

// Wait for network idle
await page.waitForLoadState("networkidle");
```

**Java/JUnit5:**

```java
// Explicit wait
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("element")));

// Or using Awaitility for cleaner waits
import static org.awaitility.Awaitility.*;

await().atMost(10, SECONDS)
    .until(() -> driver.findElement(By.id("element")).isDisplayed());
```

### Pattern 4: Error Handling

**TypeScript/Playwright:**

```typescript
try {
  await page.click('[data-testid="button"]');
} catch (error) {
  Logger.error(`Failed to click button: ${error.message}`);
  throw error;
}
```

**Java/JUnit5:**

```java
try {
    driver.findElement(By.id("button")).click();
} catch (Exception e) {
    logger.error("Failed to click button: " + e.getMessage());
    throw e;
}

// Or using JUnit5 assertDoesNotThrow
assertDoesNotThrow(() -> {
    driver.findElement(By.id("button")).click();
}, "Failed to click button");
```

---

## 📚 Reference Materials

I will reference during implementation:

**Project-Specific:**

- Existing test files in the same module
- Page Object Models (if available)
- Test utilities and helpers
- Configuration files
- README files in test directories

**Framework Documentation:**

- Playwright: https://playwright.dev
- TestNG: https://testng.org
- Pytest: https://docs.pytest.org

**Best Practices:**

- Keep tests independent (no inter-test dependencies)
- Use meaningful test data
- Add proper wait conditions
- Clean up after tests
- Add descriptive error messages
- Use Page Object pattern for maintainability

---

## ⚠️ Important Notes

1. **Don't Create New Files** without explicit approval (per IMPORTANT_RULES.md)
   - ❌ NO new README/SUMMARY/IMPLEMENTATION markdown files after implementation
   - ❌ NO new documentation files for test implementation
   - ✅ ONLY update the JSON file with implementation status (see Step 6)
2. **Update Existing Test Files** - add new tests to existing spec files
3. **Analyze Existing Patterns First** - examine existing test files, page objects, and patterns before implementing
4. **Follow Project Conventions** - match existing code style exactly
5. **Test Locally First** - never commit failing tests
6. **Document Assumptions** - if unclear, ask before implementing
7. **Keep Tests Simple** - one test = one scenario
8. **Reuse Code** - use existing Page Objects and utilities

---

## 🎯 Success Criteria

Implementation is complete when:

- [ ] UI inspected and real DOM locators identified (for UI/Web tests - Step 1.4)
- [ ] All tests from JSON implemented
- [ ] All tests passing locally
- [ ] PIDs validated (JSON ↔ Code)
- [ ] Tests added to existing spec files (no new test files created)
- [ ] Page Objects updated with real locators (existing files modified, not new ones created)
- [ ] Code follows project conventions exactly
- [ ] Proper logging in place
- [ ] Test data cleanup implemented
- [ ] **JSON file updated with implementation status** (ONLY file to be updated)
- [ ] **NO README/SUMMARY/IMPLEMENTATION markdown files created**
- [ ] Ready for PR/review

---

## 📊 Update JSON Implementation Status

**After ALL tests in a JSON file pass locally:**

### Update JSON Metadata

Add implementation tracking metadata to the JSON file:

```json
{
  "module": "Email Template Organization",
  "qTestModuleId": "<module-id>",
  "implementationStatus": "completed",
  "implementationDate": "2026-02-09",
  "testsPassed": 10,
  "testsFailed": 0,
  "lastRunDate": "2026-02-09",
  "testExecutor": "automation-web",
  "testCases": [...]
}
```

**Status Values:**

- `"pending"` - Tests created in JSON but not implemented in code
- `"in-progress"` - Some tests implemented, some pending/fixme
- `"completed"` - All tests implemented and passing locally
- `"blocked"` - Implementation blocked by dependencies/issues

### Verification Checklist

Before marking as `"completed"`:

- [ ] No `test.fixme()` or `@Disabled` annotations
- [ ] All assertions passing
- [ ] No console errors or warnings
- [ ] Test data properly cleaned up
- [ ] Tests run in isolation (can run alone or with suite)
- [ ] PIDs match between JSON and code (`@QTestCase`)

### Commit Changes

```bash
# Update JSON file with implementation status
git add .qtest/test-cases/{package}/{Module}.json

# Commit with clear message
git commit -m "Mark {Module} tests as completed

- All {count} tests implemented and passing
- Implementation date: {date}
- Status: completed"

# Example
git commit -m "Mark Email Template Organization tests as completed

- All 10 tests implemented and passing
- Implementation date: 2026-02-09
- Status: completed"
```

**This status tracking enables:**

- Clear progress visibility
- Quick identification of completed vs pending modules
- Audit trail for implementation timeline
- Easy filtering for sync operations

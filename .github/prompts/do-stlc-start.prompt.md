# AI-STLC Master Orchestrator

## 🎯 PRIMARY ENTRY POINT for All Test Automation Workflows

> **⭐ Always start here.** This is the main prompt that routes you to the appropriate workflow and next steps.

**Architecture:**

- **This prompt (`do-stlc-start.prompt.md`)** → Primary entry point (use this)
- **AI-STLC-Triple-Workflow-Strategy.md** → Detailed strategy reference (linked from here)

---

## What This Does

This master orchestrator will:

1. **If you provide a qTest module ID:** Immediately analyze and fetch test cases
   - ✅ **CRITICAL:** Analyze test types first (Automation vs Manual)
   - ✅ Fetch ONLY automation tests (Type=702) for JSON files
   - ✅ Report manual tests separately (stay in STD documentation)
   - ✅ Create JSON file with automation tests only
2. **If you request workflow analysis:** Scan workspace, detect artifacts, and recommend workflow
3. ✅ **Tell you exactly which prompt to run next**
4. ✅ Provide clear navigation throughout the entire process
5. ✅ Reference the Triple Workflow Strategy for detailed guidance

---

## ⚠️ **CRITICAL RULE: Automation Tests Only in JSON**

**When fetching from qTest:**

- ✅ **JSON files** = ONLY automation tests (Type field = "Automation" or value "702")
- 📝 **Manual tests** = Stay in STD/mini-STD documentation ONLY
- ⛔ **NEVER** include manual tests in JSON files

**Why?** JSON files drive code implementation. Manual tests are executed manually, not coded.

---

## How to Use

### **Option 1: Provide Specific qTest Module ID**

If you already know the qTest module ID, provide it directly:

```
"{moduleId}"
"Fetch tests from module {moduleId}"
"Generate JSON from qTest module {moduleId}"
```

**I will:**

- ✅ Skip workspace detection
- ✅ Analyze test types in the module (Automation vs Manual)
- ✅ Fetch ONLY automation tests (Type=702) for JSON file
- ✅ Create JSON file with automation tests only
- ✅ Report manual tests (stay in STD documentation)
- ✅ Show you next steps for implementation

---

### **Option 2: Start General Workflow Analysis**

If you need help determining the workflow:

```
"Start AI-STLC workflow"
"Start AI-STLC for [feature-name]"
"I need to automate tests for [project/module]"
```

**I will:**

- ✅ Scan workspace for artifacts
- ✅ Analyze project state
- ✅ Recommend optimal workflow
- ✅ Guide you through the process

---

## Step 1: Project Context Detection (Only for Option 2)

**Note:** If you provided a specific qTest module ID, I will skip this step and fetch test cases directly.

**⚠️ CRITICAL FIRST STEP: Analyze Existing Module Documentation**

Before recommending any workflow, ALWAYS scan for existing test documentation in the module:

```bash
# Check for existing module documentation
ls docs/{module}/              # e.g., docs/p2c/, docs/hub/, docs/agent/
grep -r "test" docs/{module}/  # Look for test-related docs
```

**If existing STP/STD/QA-Workplan found:**

- ✅ **Read and analyze** existing patterns:
  - Automation strategy (e.g., 80% automation / 20% manual for P2C)
  - Environment structure (e.g., QA + Production only)
  - Test case count baseline
  - Naming conventions and folder structure
- ✅ **Use as template** for new feature documentation
- ✅ **Maintain consistency** with established practices

**Example: P2C Module**

```bash
ls docs/p2c/
# Found: p2c-stp.md, p2c-std.md, qa-workplan.md, design.md, prd.md
# Analysis: 80/20 automation, QA+Prod envs, ~20-30 test cases per feature
# Action: Use same pattern for new P2C features (e.g., Localization)
```

When doing full workflow analysis, I will scan your workspace for:

### Testing Artifacts

- [ ] qTest modules (check for module IDs in docs/README files)
- [ ] qTest test cases (check `.qtest/` folder)
- [ ] PRD documents (search `docs/prd/` or `docs/`)
- [ ] STP documents (search `docs/` for `*stp*.md`)
- [ ] STD documents (search `docs/` for `*std*.md`)
- [ ] Design documents (search `docs/` for `*design*.md`)
- [ ] Existing test code (check for implemented test classes)
- [ ] Change requests / JIRA tickets (for enhancements)
- [ ] lessons-learned.md (for context)

### Project Structure

- [ ] **All available test frameworks** (Playwright/Jest/TestNG/Pytest) — Detect ALL frameworks in workspace
- [ ] **Feature-component framework mapping:**
  - Backend/API components → Java + RestAssured + TestNG
  - UI components (Agent/Hub/Portal) → Playwright/TypeScript
  - Data/Cloud/SQL/MongoDB → automation-llm-validation
- [ ] Programming language (TypeScript/Java/Python)
- [ ] Build tool (npm/Maven/Gradle)
- [ ] Test locations and structure (per component)
- [ ] **Primary framework for this specific feature** (based on component analysis)

### qTest Integration

- [ ] qTest CLI configuration
- [ ] API credentials setup
- [ ] Existing sync scripts (`.qtest/simple_sync.py`)
- [ ] Auto-extraction tools (`.qtest/java_parser.py`, etc.)

---

## Step 2: Workflow Selection Logic

Based on detected artifacts, I will select:

### 🟢 WORKFLOW A: qTest-First

\*\*Selected when:

- ✅ qTest module exists with test cases
- ✅ Test cases already defined and reviewed
- ✅ Need traceability from day one
- ✅ Regulatory/compliance requirements

**Your path will be:**

```
1. Fetch test cases from qTest         → qtest fetch --module {moduleId}
2. [Optional] Generate code skeleton   → qtest generate-code --module {moduleId} --framework {playwright|testng|pytest}
3. Implement test logic                → Write test code based on JSON specs
4. Run tests locally                   → npm test / mvn test / pytest
5. Validate tests pass                 → Fix any failures
6. [Optional] Sync results to qTest    → qtest sync --module {moduleId} --tests-dir ./.qtest/test-cases/
```

**CLI Commands:**

```bash
# Step 1a: FIRST - Analyze module hierarchy and test types
# Determine parent module for correct folder structure
node -e "
import { QTestClient } from './dist/qtest-client.js';
const client = new QTestClient();
const module = await client.makeRequest('GET', '/projects/{projectId}/modules/{moduleId}');
const parent = module.parent_id ?
  await client.makeRequest('GET', '/projects/{projectId}/modules/' + module.parent_id) : null;
const folder = parent ? parent.name.toLowerCase().replace(/[^a-z0-9]/g, '') :
  module.name.toLowerCase().replace(/[^a-z0-9]/g, '');
console.log('Folder:', folder);
"

# Step 1b: Fetch ONLY automation tests (Type=702) and create JSON
# ⚠️ IMPORTANT: This automatically filters for automation tests only (Type=702)
# Manual tests (Type=701) stay in qTest/STD documentation

# RECOMMENDED - Workflow A (qTest-First): Search and sync by module name
cd /path/to/qtest-mcp-server
node search-deep.js "<module-name-to-search>"
# Example: node search-deep.js "agent promo accessibility"
#   - Searches 82 modules by name
#   - Shows module hierarchy path
#   - Auto-syncs if single match found
#   - Creates JSON with ONLY automation tests (Type=702)
# Output: {workspace}/.qtest/test-cases/{parent-folder}/{module-name}/{module-name}.json

# {workspace} = automation-web for UI tests, automation-comosense for API tests

# Step 2: (Optional) Generate test code skeleton
qtest generate-code --module {moduleId} \
  --framework {playwright|testng|pytest} \
  --output packages/{package}/tests/

# Step 3 Implement test logic based on JSON specifications
based on this prompr do-implement-tests.prompt.md
**do-implement-tests.prompt.md** → Detailed prompt reference (linked from here)

# Step 4-5: run, validate
npm test  # or mvn test, node ,pytest

cd {workspace}
qtest sync --module {moduleId} \
  --tests-dir ./.qtest/test-cases/{parent-module-folder}/ \
  --create-submodules
```

**Workspace Paths:**

- **UI/Web Tests:** `/path/to/automation-web/.qtest/test-cases/{parent-folder}/`
- **API Tests:** `/path/to/automation-comosense/.qtest/test-cases/{parent-folder}/`
- **NOT in qtest-mcp-server** - That's just the CLI tool!-create-submodules

**Folder Structure Logic:**

- If module has parent: Use parent module name as folder (e.g., Hub → `hub/`)
- If module is root: Use module name as folder (e.g., P2C → `p2c/`)
- Folder name: lowercase, alphanumeric only
- Example: Module "Email Template Organization" under "Hub" → `.qtest/test-cases/hub/EmailTemplateOrganization.json`

**Key Files:**

- `{workspace}/.qtest/test-cases/{parent-module-folder}/{Module}.json` - Single source of truth (AUTOMATION TESTS ONLY)
  - **{workspace}** = `automation-web` (UI tests) or `automation-comosense` (API tests)
  - **NOT** in `qtest-mcp-server` (that's just the CLI tool)
  - Folder determined by parent module name (e.g., hub/, p2c/, agent/)
  - If root module, use module name as folder
- Test implementation files (`.spec.ts` for web, `.java` for API)

**Important:**

- ⚠️ JSON files contain ONLY automation tests (Type=702)
- 📝 Manual tests remain in STD documentation
- ✅ Always verify test type before adding to JSON
- 📁 JSON folder matches parent module structure
- 🎯 JSON files go in TEST PROJECT workspace, not qtest-mcp-server

**No markdown plans needed** - JSON file contains all automation test specifications

### 🔵 WORKFLOW B: Code-First (New Features & Enhancements)

\*\*Selected when:

- ✅ PRD or requirements documents exist (or need updating)
- ✅ No qTest module yet OR qTest has <10 test cases
- ✅ New feature development OR enhancement to existing feature
- ✅ Need speed and flexibility

**Decision Logic - I will analyze:**

1. **Feature Status:** New Feature vs Existing Feature Enhancement
2. **Feature Scope:** Hotfix/POC vs Full Feature
3. **Test Case Count:** <15 test cases vs >15 test cases
4. **Sprint Type:** Single sprint vs Multi-sprint
5. **Existing Documentation:** None vs Partial vs Complete

**Path Selection:**

#### 🚀 Quick Path (Small Feature/Hotfix/Enhancement)

**When:**

- Single sprint (hotfix/POC/spike/small enhancement)
- Simple feature or change (<15 test cases)
- Limited scope change
- Fast validation needed

**Variant A - New Feature:**

```

1. Requirements (JIRA/PRD) → Input document
2. do-mini-std.prompt.md → Generate minimal STD (all test cases: manual + automation)
3. Extract automation tests → Create JSON files (.qtest/test-cases/) for automation type only
4. [Implement tests] → Write test code (automation tests only)
5. [Verify implementation] → Run tests locally, ensure all pass
6. [Validate before sync] → npm run validate:pids (check PID synchronization)
7. [Sync to qTest] → qtest sync --module {moduleId} --tests-dir ./.qtest/test-cases/{package}/
   - Auto-creates qTest submodules
   - Smart duplicate detection
   - Uploads all test cases from JSON files
   - Auto-updates JSON with new PIDs

```

**CLI Commands:**

```bash
# Step 3: Create JSON specs (manually or AI-assisted)
# .qtest/test-cases/{package}/NewFeature.json

# Step 4: Implement tests
# Write test code based on JSON specs

# Step 5: Run tests locally
npm test  # or mvn test

# Step 6: Validate PID synchronization
cd ../qtest-mcp-server
npm run validate:pids

# Step 7: Sync to qTest
qtest sync --module {moduleId} \
  --tests-dir ./.qtest/test-cases/{package}/ \
  --create-submodules

# JSON files are AUTO-UPDATED with PIDs from qTest!
# Commit the updated JSON files with new PIDs
git add .qtest/test-cases/
git commit -m "Add new feature tests - TC-XXX through TC-YYY"
```

**Variant B - Existing Feature Enhancement:**

```
1. Change request (JIRA)     → Review what changed
2. Update existing docs      → Update PRD/mini-STD (add/modify test cases)
3. [Pre-Sync Checks]         → BEFORE updating JSON:
   a. Check qTest module      → qtest check --module {moduleId} --verbose
   b. Fetch existing tests    → qtest fetch --module {moduleId}
   c. Review current coverage → Compare with local JSON files
4. Update JSON files         → Modify .qtest/test-cases/{package}/{Module}.json
5. Update test code          → Modify existing test classes (copy PIDs to @QTestCase)
6. Run regression tests      → Validate no breaks
7. [Verify implementation]   → Run all tests locally, ensure pass
8. [Validate sync]           → npm run validate:pids (verify JSON ↔ Java sync)
9. [Sync to qTest]           → ONLY AFTER verification - Update qTest with changes
```

**CLI Commands:**

```bash
# Step 3: Pre-sync checks
qtest check --module {moduleId} --verbose
qtest fetch --module {moduleId} --output ./.qtest/test-cases/{package}/

# Step 4-7: Update JSON, code, test
# ... make changes ...

# Step 8: Validate PID synchronization
cd ../qtest-mcp-server
npm run validate:pids

# Step 9: Sync to qTest
qtest sync --module {moduleId} \
  --tests-dir ./.qtest/test-cases/{package}/ \
  --create-submodules
```

**Example:** P2C module small enhancement (see `automation-comosense/docs/p2c/`)

#### 📋 Full Path (Complex/Large Feature)

**When:**

- Multi-sprint feature
- Complex feature or major enhancement (>15 test cases)
- Full project scope
- Requires comprehensive planning

**Variant A - New Feature:**

```
1. Requirements (JIRA/PRD)   → Input document
2. do-stp.prompt.md          → Generate Software Test Plan (strategy)
3. do-std.prompt.md          → Generate Software Test Design (all test cases: manual + automation)
4. do-qa-workplan.prompt.md  → Generate QA Implementation Plan
5. Extract automation tests  → Create JSON files (.qtest/test-cases/) for automation type only
6. [Implement tests]         → Write test code (automation only)
7. [Verify implementation]   → Run full test suite locally, ensure all pass
8. [Validate before sync]    → npm run validate:pids (check synchronization)
9. [Sync to qTest]           → qtest sync - Batch sync with hierarchy
   - Processes all JSON files at once
   - Auto-creates submodule structure
   - Smart UPDATE existing or CREATE new tests
   - Auto-updates JSON with new PIDs
```

**CLI Commands:**

```bash
# Steps 1-5: Generate docs and create JSON specs
# ... AI-assisted documentation generation ...

# Step 6: Implement tests
# Write test code based on JSON specs

# Step 7: Run full test suite
npm test  # or mvn test

# Step 8: Validate PID synchronization
cd ../qtest-mcp-server
npm run validate:pids

# Step 9: Batch sync all test files
qtest sync --module {moduleId} \
  --tests-dir ./.qtest/test-cases/{package}/ \
  --create-submodules

# JSON files auto-updated with PIDs from qTest
git add .qtest/test-cases/
git commit -m "Add large feature tests - TC-XXX through TC-YYY"
```

**Variant B - Existing Feature Major Enhancement:**

```
1. Change request (JIRA)     → Review what changed
2. Update documentation      → Update PRD, review/update STP, update STD
3. [Pre-Sync Checks]         → BEFORE updating JSON:
   a. Check module status     → qtest check --module {moduleId} --verbose
   b. Fetch existing tests    → qtest fetch --module {moduleId} (all subfolders)
   c. Review test structure   → Compare current vs new requirements
4. Extract automation tests  → Update JSON files with new/modified test cases
5. Update test code          → Add new tests, modify existing classes
6. Run regression tests      → Full regression validation
7. [Verify implementation]   → Run complete test suite, ensure all pass
8. [Validate sync]           → npm run validate:pids (check JSON ↔ Java)
9. [Sync to qTest]           → ONLY AFTER verification - Update qTest module with changes
```

**CLI Commands:**

```bash
# Step 3: Pre-sync checks
qtest check --module {moduleId} --verbose
qtest fetch --module {moduleId} --output ./.qtest/test-cases/{package}/

# Steps 4-7: Update JSON, code, test
# ... make changes ...

# Step 8: Validate synchronization
cd ../qtest-mcp-server
npm run validate:pids

# Step 9: Sync to qTest
qtest sync --module {moduleId} \
  --tests-dir ./.qtest/test-cases/{package}/ \
  --create-submodules
```

**Key Points:**

- **New Feature:** Generate/create documentation and code from scratch
- **Enhancement:** Update existing documentation and code incrementally
- STD/mini-STD contains ALL test cases (manual + automation)
- Only automation test cases get JSON files created
- JSON files drive skeleton generation and implementation
- Manual test cases remain in STD documentation
- After implementation, sync everything to qTest (optional)
- **Always run regression tests** when modifying existing features

### 🟠 WORKFLOW C: Reverse Engineering

**Selected when:**

- ❌ No PRD exists
- ❌ No STP/STD exists
- ❌ No qTest module
- ✅ Legacy/undocumented system exists

**Your path will be:**

```
1. do-design.prompt.md       → Reverse engineer system
2. do-prd.prompt.md          → Generate PRD from design
3. do-stp.prompt.md          → Generate Software Test Plan
4. do-std.prompt.md          → Generate Software Test Design (all test cases)
5. do-qa-workplan.prompt.md  → Generate QA Implementation Plan
6. Extract automation tests  → Create JSON files for automation type only
7. [Implement tests]         → Automation tests only
8. [Validate implementation] → Run all tests locally, verify pass
9. [Validate sync]           → npm run validate:pids (check JSON ↔ Java)
10. [Sync to qTest]          → Upload all test cases + automation results
```

**CLI Commands:**

```bash
# Steps 1-6: Generate documentation and create JSON specs
# ... AI-assisted documentation generation ...

# Step 7: Implement tests
# Write test code based on JSON specifications

# Step 8: Run and validate
npm test  # or mvn test

# Step 9: Validate PID synchronization
cd ../qtest-mcp-server
npm run validate:pids

# Step 10: Create qTest module and sync tests
qtest sync --module {moduleId} \
  --tests-dir ./.qtest/test-cases/{package}/ \
  --create-submodules

# Note: JSON files auto-updated with qTestPID for new tests
```

---

## Step 3: Output Format

I will provide:

### 📊 Detection Summary

```markdown
## Project Detection Results

**Workspace:** /path/to/project
**Framework:** Playwright (TypeScript) or Java/API
**Build Tool:** npm or Maven

### Detected Artifacts

- ✅ PRD Document: docs/prd/payment-feature.md
- ❌ qTest Module: Not found
- ✅ Test Framework: Configured and ready
- ❌ STP/STD: Not generated yet

### Feature Analysis

- **Feature Type:** Hotfix / Small Feature / Large Feature
- **Estimated Test Cases:** <15 / >15
- **Sprint Scope:** Single sprint / Multi-sprint
- **Complexity:** Simple / Complex

### Workflow Recommendation: 🔵 WORKFLOW B (Code-First) → Quick Path

**Rationale:**

- PRD exists, no qTest module yet
- Small feature with estimated <15 test cases
- Single sprint scope (hotfix/POC)
- Fast validation needed
- Suitable for mini-STD approach

**OR**

### Workflow Recommendation: 🔵 WORKFLOW B (Code-First) → Full Path

**Rationale:**

- PRD exists, no qTest module yet
- Complex feature with estimated >15 test cases
- Multi-sprint scope
- Requires comprehensive test planning
- Suitable for full STP → STD approach

**OR**

### Workflow Recommendation: 🟣 WORKFLOW D (Change Request / Enhancement)

**Rationale:**

- Existing feature with complete documentation (PRD, STP, STD)
- Existing qTest module and JSON files
- Existing test implementation code
- Product change/enhancement to existing feature
- Need to update tests incrementally

**Example:** P2C module updates (see `automation-comosense/docs/p2c/`)
```

### ✅ NEXT STEP Instructions (Quick Path - New Feature or Enhancement)

```markdown
## ✅ NEXT STEP (Quick Path - New Feature)

**Step 1: Generate Minimal STD**

→ **Run Prompt:** do-mini-std.prompt.md
→ **Input Document:** docs/prd/payment-feature.md OR JIRA requirements
→ **Expected Output:** docs/doc\_{package}/payment-feature-mini-std.md (contains ALL test cases)
→ **Estimated Time:** 15-20 minutes (AI-assisted)

**Step 2: Pre-Sync Checks (BEFORE creating JSON)**

→ **Verify Credentials:** Check qTest API token and project access
→ **Fetch Existing Tests:** Get existing tests from qTest module (if exists)
→ **Review Structure:** Understand existing test organization
→ **Tools:** Use .qtest/test-qtest-connection.sh to verify connectivity

**Step 3: Extract Automation Tests**

→ **Action:** Review mini-STD and identify automation test cases
→ **Create JSON:** .qtest/test-cases/{package}/{Module}.json (automation tests only)
→ **Include:** qTestPID fields for smart sync capability

**Step 4: Implement Tests**

→ **Reference:** JSON file for automation tests
→ **Implement:** Test code for automation only
→ **Manual tests:** Stay in mini-STD documentation

**Step 5: Verify Implementation**

→ **Run Locally:** Execute all tests and ensure they pass
→ **Check Coverage:** Validate all scenarios work correctly
→ **Fix Issues:** Resolve any test failures before sync

**Step 6: Sync to qTest (ONLY AFTER VERIFICATION)**

→ **Timing:** Only after tests are implemented and verified
→ **Upload:** mini-STD test cases + automation results to qTest
→ **Tools:** Use .qtest/simple_sync.py for reliable sync
```

````markdown
## ✅ NEXT STEP (Quick Path - Enhancement)

**Step 1: Review Change Request**

→ **Document:** JIRA ticket / Change request
→ **Identify:** What changed, which test cases affected

**Step 2: Update Existing Documentation**

→ **PRD:** Update docs/{module}/prd.md (if needed)
→ **STD/mini-STD:** Update docs/{module}/{module}-std.md with new/modified test cases
→ **Mark:** Clearly indicate what's new vs modified

**Step 3: Update JSON Files**

→ **Location:** .qtest/test-cases/{package}/{Module}.json
→ **Action:** Add new or modify existing automation test cases

**Step 4: Update Test Code**

→ **Modify:** Existing test classes to handle changes
→ **Add:** New test methods following existing patterns

**Step 5: Run Regression Tests**

→ **Validate:** All existing tests still pass
→ **Fix:** Any regression failures

**Step 6: Sync to qTest**

→ **Pre-Validation:** `npm run validate:pids` (check JSON ↔ Java sync)
→ **Sync Command:**

```bash
qtest sync --module {moduleId} \
  --tests-dir ./.qtest/test-cases/{package}/ \
  --create-submodules
```
````

→ **Validates:** Duplicate detection and auto-submodule creation
→ **Expected:** Test cases sync to qTest with proper hierarchy
→ **Note:** JSON files auto-updated with qTestPID for new tests

````

### ✅ NEXT STEP Instructions (Full Path - New Feature or Major Enhancement)

```markdown
## ✅ NEXT STEP (Full Path - New Feature)

**Step 1: Generate Software Test Plan**

→ **Run Prompt:** do-stp.prompt.md
→ **Input Document:** docs/prd/payment-feature.md
→ **Expected Output:** docs/doc\_{package}/payment-feature-stp.md
→ **Estimated Time:** 30-45 minutes (AI-assisted)

**Step 2: Generate Software Test Design**

→ **Run Prompt:** do-std.prompt.md
→ **Input Document:** docs/doc*{package}/payment-feature-stp.md
→ **Expected Output:** docs/doc*{package}/payment-feature-std.md (contains ALL test cases)
→ **Estimated Time:** 45-60 minutes (AI-assisted)

**Step 3: Generate QA Work Plan**

→ **Run Prompt:** do-qa-workplan.prompt.md
→ **Input Document:** docs/doc\_{package}/payment-feature-std.md
→ **Expected Output:** Implementation plan with epics and stories

**Step 4: Extract Automation Tests**

→ **Action:** Review STD and identify automation test cases
→ **Create JSON:** .qtest/test-cases/{package}/{Module}.json (automation tests only)

**Step 5: Implement Tests**

→ **Reference:** JSON file for automation tests
→ **Implement:** Test code for automation only
→ **Manual tests:** Stay in STD documentation

**Step 6: Validate & Sync to qTest**

→ **Pre-Validation:** `npm run validate:pids` (check JSON ↔ Java sync)
→ **Sync Command:**
```bash
qtest sync --module {moduleId} \
  --tests-dir ./.qtest/test-cases/{package}/ \
  --create-submodules
````

→ **Post-Validation:** `npm run validate:pids` (verify PIDs added)
→ **Verify:** Check qTest for correct test count and submodule hierarchy
→ **Note:** JSON files auto-updated with qTestPID for new tests

````

```markdown
## ✅ NEXT STEP (Full Path - Major Enhancement)

**Step 1: Review Change Request**

→ **Document:** JIRA ticket / Major change request
→ **Identify:** Scope of changes, impact analysis

**Step 2: Update Documentation**

→ **PRD:** Update docs/{module}/prd.md with new requirements
→ **STP:** Review and update test strategy if needed
→ **STD:** Update docs/{module}/{module}-std.md with new/modified test cases

**Step 3: Extract Automation Tests**

→ **Action:** Review updated STD for automation test cases
→ **Update JSON:** .qtest/test-cases/{package}/{Module}.json

**Step 4: Update Test Code**

→ **Add:** New test classes/methods
→ **Modify:** Existing test code

**Step 5: Run Full Regression**

→ **Validate:** Complete regression test suite
→ **Fix:** Any failures

**Step 6: Validate & Sync to qTest**

→ **Pre-Check:** `qtest check --module {moduleId} --verbose` (verify existing tests)
→ **Pre-Validation:** `npm run validate:pids` (check JSON ↔ Java sync)
→ **Sync Command:**
```bash
qtest sync --module {moduleId} \
  --tests-dir ./.qtest/test-cases/{package}/ \
  --create-submodules
````

→ **Post-Validation:** `npm run validate:pids` (verify PIDs updated)
→ **Verify:** Check qTest for correct test count and no duplicates
→ **Note:** JSON files auto-updated with qTestPID for new tests

````

### ✅ NEXT STEP Instructions (qTest-First)

```markdown
## ✅ NEXT STEP (qTest-First)

**Step 1: Fetch from qTest**

→ **Command:** `qtest fetch --module {moduleId} --output ./.qtest/test-cases/{package}/`
→ **Output:** JSON files with test specifications from qTest
→ **Note:** Existing JSON files will be updated, new ones created

**Step 2: Generate Code Skeleton (Optional)**

→ **Command:** `qtest generate-code --spec ./.qtest/test-cases/{package}/{Module}.json`
→ **Output:** Test code skeleton based on JSON specifications
→ **Note:** AI-assisted code generation, requires manual completion

**Step 3: Implement Tests**

→ **Reference:** .qtest/test-cases/{package}/{Module}.json
→ **Implement:** Test logic based on JSON specifications
→ **Run:** `npm run test:{package}:local` (Web) or `mvn test` (API)

**Step 4: Validate**

→ **Test locally** until all tests pass
→ **Validation:** `npm run validate:pids` (verify JSON ↔ Java sync)
→ **No markdown plans needed** - JSON is single source of truth
````

### ✅ NEXT STEP Instructions (Change Request / Enhancement)

````markdown
## ✅ NEXT STEP (Change Request)

**Step 1: Review Change Request**

→ **Document:** JIRA ticket / Change request document
→ **Identify:** What changed (new feature, enhancement, bug fix)
→ **Impact:** Which test cases are affected

**Step 2: Update Documentation (if needed)**

→ **PRD:** Update docs/{module}/prd.md with new requirements
→ **STD:** Update docs/{module}/{module}-std.md with new/modified test cases
→ **Mark:** Clearly indicate what's new vs modified

**Step 3: Pre-Sync Checks**

→ **Check Module:** `qtest check --module {moduleId} --verbose`
→ **Fetch Current:** `qtest fetch --module {moduleId} --output ./.qtest/test-cases/{package}/`
→ **Review Structure:** Compare current vs new requirements

**Step 4: Update JSON Files**

→ **Location:** .qtest/test-cases/{package}/{Module}.json
→ **Action:** Add new automation test cases OR modify existing ones

**Step 5: Update Test Code**

→ **Modify:** Existing test classes to handle changes
→ **Add:** New test methods following existing patterns

**Step 6: Run Regression Tests**

→ **Validate:** All existing tests still pass
→ **Fix:** Any regression failures

**Step 7: Validate & Sync**

→ **Pre-Validation:** `npm run validate:pids` (check JSON ↔ Java sync)
→ **Sync Command:**

```bash
qtest sync --module {moduleId} \
  --tests-dir ./.qtest/test-cases/{package}/ \
  --create-submodules
```
````

→ **Post-Validation:** `npm run validate:pids` (verify PIDs updated)
→ **Note:** JSON files auto-updated with qTestPID for new tests

**Step 8: Git Commit**

→ **Add Changes:**

```bash
git add .qtest/test-cases/{package}/{Module}.json
git add rest-api/src/test/java/.../
git commit -m "feat: Update {Module} tests - {JIRA-ID}"
```

````
→ **Preserve:** Existing test case structure and qTestPID references

**Step 4: Update Test Code**

→ **Existing Tests:** Modify test classes to handle changes
→ **New Tests:** Add new test methods following existing patterns
→ **Maintain:** Same file structure and naming conventions

**Step 5: Run Regression Tests**

→ **Full Suite:** Run all existing tests to ensure no breaks
→ **New Tests:** Run new/modified tests to validate changes
→ **Fix:** Any regression failures before proceeding

**Step 6: Validate & Sync to qTest**

→ **Pre-Validation:** `npm run validate:pids` (check JSON ↔ Java sync)
→ **Sync Command:**
```bash
qtest sync --module {moduleId} \
  --tests-dir ./.qtest/test-cases/{package}/ \
  --create-submodules
````

→ **Post-Validation:** `npm run validate:pids` (verify PIDs updated)
→ **Verify:** Check qTest for correct updates and no duplicates
→ **Traceability:** Link to change request/JIRA ticket in qTest
→ **Note:** JSON files auto-updated with qTestPID for new tests

```

---

## Quick Decision Tree

**Step 3: Implement Tests**

→ **Reference:** .qtest/test-cases/{package}/{Module}.json
→ **Implement:** Test logic based on JSON specifications
→ **Run:** npm run test:{package}:local (Web) or mvn test (API)

**Step 4: Validate**
?}
Q2{PRD or<br/>requirements<br/>exist?}
Q3{Any system<br/>documentation?}

    WF_A[� WORKFLOW A<br/>Code-First<br/><br/>Next: do-stp.prompt.md]
    WF_B[🟢 WORKFLOW B<br/>qTest-First<br/><br/>Next: Create JSON file<br/>.qtest/test-cases/]
    WF_C[🟠 WORKFLOW C<br/>Reverse Engineering<br/><br/>Next: do-design.prompt.md]

    START --> Q1
    Q1 -->|Yes| WF_B
    Q1 -->|No| Q2
    Q2 -->|Yes| WF_A
    Q2 -->|No| Q3
    Q3 -->|Yes| WF_A
    Q3 -->|No| WF_C

```

---

## Workflow A: qTest-First - Detailed Steps

When qTest module exists, follow these steps:

### Step 1: Identify qTest Module

- Get module ID from qTest Manager UI
- Note: Module name, number of test cases
- Filter: Only automation tests (Type 702)

### Step 2: Create JSON Test Definition

**automation-web (TypeScript/Playwright):**

```bash
# Use qTest MCP Server generateTestCode() function
# Or manually create JSON file
# Location: .qtest/test-cases/{package}/{Module}.json
```

**automation-comosense (Java/API):**

```bash
# Use qTest Manager UI to view test cases
# Manually create JSON file
# Location: .qtest/test-cases/{package}/{Module}.json
```

**JSON Template:**

```json
{
  "module": "Module Name",
  "qTestModuleId": "{moduleId}",
  "testCases": [
    {
      "name": "Test Case Name",
      "description": "What this test validates",
      "precondition": "Prerequisites",
      "testSteps": [],
      "qTestId": "{testCaseId}",
      "qTestPID": "TC-XXX",
      "priority": "High",
      "type": "Automation"
    }
  ]
}
```

      "type": "Automation"
    }

]
}

````

### Step 3: Generate Code Skeleton (Optional)

**CLI Command:**

```bash
# Generate code skeleton from JSON specification
qtest generate-code --spec ./.qtest/test-cases/{package}/{Module}.json

# Output: Test code skeleton that needs manual completion
````

**automation-web:**
AI-assisted code generation using qtest CLI, or write manually

**automation-comosense:**
Write test class manually following JSON specification

### Step 4: Implement Tests

- Reference JSON file for specifications
- Implement test logic
- Add page objects as needed
- Follow project patterns

### Step 5: Run & Validate

**automation-web:**

```bash
npm run test:{package}:local
```

**automation-comosense:**

```bash
mvn test -Dtest={TestClass}
```

**Validation:**

```bash
# Check JSON ↔ Java synchronization
cd ../qtest-mcp-server
npm run validate:pids
```

### Step 6: Sync to qTest (Optional)

**All projects use qTest CLI:**

```bash
# Pre-sync validation
npm run validate:pids

# Sync test cases to qTest
qtest sync --module {moduleId} \
  --tests-dir ./.qtest/test-cases/{package}/ \
  --create-submodules

# Verify sync results (expected count, no duplicates)
# Note: JSON files auto-updated with qTestPID for new tests
```

equirements<br/>exist?}
Q3{Any system<br/>documentation?}

    WF_A[🟢 WORKFLOW A<br/>qTest-First<br/><br/>Next: qTest CLI commands]
    WF_B[🔵 WORKFLOW B<br/>Code-First<br/><br/>Next: do-stp.prompt.md]
    WF_C[🟠 WORKFLOW C<br/>Reverse Engineering<br/><br/>Next: do-design.prompt.md]

    START --> Q1
    Q1 -->|Yes| WF_A
    Q1 -->|No| Q2
    Q2 -->|Yes| WF_B
    Q2 -->|No| Q3
    Q3 -->|Yes| WF_B
    Q3 -->|No| WF_C

```

---

## For Simple Features (Quick Path)

If feature is simple (<15 test cases), I may recommend:

- `do-mini-std.prompt.md` instead of full STP → STD
- `do-qa-mini-workplan.prompt.md` instead of full workplan

I'll tell you when this quick path is appropriate.

---

## Execution Instructions

### What I Will Do:

**If you provide a qTest Module ID (e.g., "{moduleId}"):**
1. ✅ Fetch test cases from that specific module immediately
2. ✅ Create JSON file in appropriate directory
3. ✅ Show test cases summary
4. ✅ Provide next steps for implementation
5. ⛔ **SKIP** workspace scanning and detection

**If you say "Start AI-STLC workflow":**
1. ✅ Scan workspace files and structure
2. ✅ Search for test artifacts
3. ✅ Analyze qTest integration status
4. ✅ Select optimal workflow
5. ✅ Provide detection summary
6. ✅ Give you exact next prompt to run with input file

### What You Should Do:

**For qTest Module ID:**
```

Provide just the module ID: "{moduleId}"
Or: "Fetch tests from qTest module {moduleId}"

```

**For General Workflow:**
```

Say: "Start AI-STLC workflow"
Or: "Start AI-STLC for [feature-name]"

```

---

## Override Options

If you want to force a specific workflow:

```

"Use WORKFLOW A (qTest-First) for [feature]"
"Use WORKFLOW B (Code-First) for [feature]"
"Use WORKFLOW C (Reverse Engineering) for [feature]"

```

---

## Example Sessions

### Example 1: Direct qTest Module Fetch

**You say:**
```

"{moduleId}"

```

**I respond:**
```

🔄 Fetching test cases from qTest module {moduleId}...
✅ Found N test cases in "{Module Name}"
✅ JSON file created: .qtest/test-cases/{package}/{Module}.json

📋 Test Cases:

1. TC-XXX: Test case 1
2. TC-YYY: Test case 2
   ... (N more)

✅ NEXT STEP: Implement tests in packages/{package}/tests/{module}.spec.ts

```

---

### Example 2: General Workflow Analysis

**You say:**

```

"Start AI-STLC workflow for payment wallet feature"

```

**I respond:**

```

## Project Detection Results

✅ PRD found: docs/prd/payment-wallet-prd.md
❌ qTest module: Not found
✅ Framework: Playwright

## Selected Workflow: 🔵 WORKFLOW B (Code-First)

## ✅ NEXT STEP

→ Run: do-stp.prompt.md
→ Input: docs/prd/payment-wallet-prd.md
→ Output: docs/doc_cp/payment-wallet-stp.md

```

**You then say:**

```

"Generate STP from docs/prd/payment-wallet-prd.md using do-stp.prompt.md"

```

_(Process continues with clear NEXT STEP at each stage)_

---

## Ready to Start?

### **Option 1: Fetch from qTest Module**
Provide the module ID directly:
```

"{moduleId}"
"Fetch tests from module {moduleId}"

```

### **Option 2: Start Workflow Analysis**
Request general workflow guidance:
```

"Start AI-STLC workflow"
"Start AI-STLC for [feature-name]"

```

---

**I will respond appropriately based on your input:**
- Module ID → Immediate fetch and JSON creation
- General request → Full workspace analysis and workflow recommendation

---

## Reference Documentation

- **⭐ AI-STLC Triple Workflow Strategy:** `.github/prompts/ai-stlc/AI-STLC-Triple-Workflow-Strategy.md` (Detailed workflow documentation)
- **Prompts Index:** `.github/prompts/PROMPTS-INDEX.md` (All available prompts)
- **Complete Guide:** `.github/prompts/ai-stlc/AI-STLC-Complete-Guide.md` (Presentation/training material ONLY - not a prompt)

**Note:** Always start from this prompt (`do-stlc-start.prompt.md`). The Complete Guide is for presentation and documentation purposes only.

---

**Version:** 2.1
**Last Updated:** February 9, 2026
**Purpose:** ⭐ PRIMARY ENTRY POINT for all AI-STLC workflows - Routes to appropriate workflow based on input
**Usage:** Provide either a qTest module ID for immediate fetch OR request general workflow analysis
```

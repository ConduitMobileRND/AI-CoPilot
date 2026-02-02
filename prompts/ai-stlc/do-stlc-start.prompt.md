# AI-STLC Master Orchestrator

## 🎯 Your Starting Point for All Test Automation Workflows

> **Always start here.** This prompt analyzes your project and tells you exactly which workflow and prompts to use next.

---

## What This Does

This master orchestrator will:

1. ✅ Scan your workspace to understand project state
2. ✅ Detect existing test artifacts (qTest modules, PRD, STP/STD docs)
3. ✅ Select the optimal workflow (A, B, or C)
4. ✅ **Tell you exactly which prompt to run next**
5. ✅ Provide clear navigation throughout the entire process

---

## How to Use

Simply say:

```
"Start AI-STLC workflow"
```

Or provide context:

```
"Start AI-STLC for [feature-name]"
"I need to automate tests for [project/module]"
```

---

## Step 1: Project Context Detection

I will automatically scan your workspace for:

### Testing Artifacts

- [ ] qTest modules (check for module IDs in docs/README files)
- [ ] qTest test cases (check `.qtest/` folder)
- [ ] PRD documents (search `docs/prd/` or `docs/`)
- [ ] STP documents (search `docs/` for `*stp*.md`)
- [ ] STD documents (search `docs/` for `*std*.md`)
- [ ] Design documents (search `docs/design/`)
- [ ] Existing test code (check for implemented test classes)
- [ ] Change requests / JIRA tickets (for enhancements)

### Project Structure

- [ ] Test framework (Playwright/Jest/TestNG/Pytest)
- [ ] Programming language (TypeScript/Java/Python)
- [ ] Build tool (npm/Maven/Gradle)
- [ ] Test locations and structure

### qTest Integration

- [ ] qTest CLI configuration
- [ ] API credentials setup
- [ ] Existing sync scripts (`.qtest/simple_sync.py`)
- [ ] Auto-extraction tools (`.qtest/java_parser.py`, etc.)

---

## Step 2: Workflow Selection Logic

Based on detected artifacts, I will select:

### � WORKFLOW A: qTest-First

**Selected when:**

- ✅ qTest module exists with test cases
- ✅ Test cases already defined and reviewed
- ✅ Need traceability from day one
- ✅ Regulatory/compliance requirements

**Your path will be:**

```
1. Fetch qTest module data               → Use qTest Manager or CLI
2. Create JSON test definition           → .qtest/test-cases/{package}/{Module}.json
3. Generate code skeleton (optional)     → Use qTest CLI --generate-code
4. Implement test logic                  → Follow JSON specification
5. Run tests locally                     → Validate implementation
6. [Optional] Sync results to qTest      → When ready to report
```

**Key Files:**

- `.qtest/test-cases/{package}/{Module}.json` - Single source of truth
- Test implementation files (`.spec.ts` for web, `.java` for API)

**No markdown plans needed** - JSON file contains all test specifications

### 🔵 WORKFLOW B: Code-First (New Features & Enhancements)

**Selected when:**

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
1. Requirements (JIRA/PRD)   → Input document
2. do-mini-std.prompt.md     → Generate minimal STD (all test cases: manual + automation)
3. Extract automation tests  → Create JSON files (.qtest/test-cases/) for automation type only
4. [Generate code skeleton]  → Use qTest CLI or manual creation
5. [Implement tests]         → Automation tests only (manual tests stay in mini-STD)
6. [Sync to qTest]           → Upload mini-STD test cases + automation results
```

**Variant B - Existing Feature Enhancement:**

```
1. Change request (JIRA)     → Review what changed
2. Update existing docs      → Update PRD/mini-STD (add/modify test cases)
3. Update JSON files         → Modify .qtest/test-cases/{package}/{Module}.json
4. Update test code          → Modify existing test classes
5. Run regression tests      → Validate no breaks
6. [Sync to qTest]           → Update qTest with changes
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
6. [Generate code skeleton]  → Use qTest CLI or manual creation
7. [Implement tests]         → Automation tests only (manual tests stay in STD)
8. [Sync to qTest]           → Upload STD test cases + automation results to qTest
```

**Variant B - Existing Feature Major Enhancement:**

```
1. Change request (JIRA)     → Review what changed
2. Update documentation      → Update PRD, review/update STP, update STD
3. Extract automation tests  → Update JSON files with new/modified test cases
4. Update test code          → Add new tests, modify existing classes
5. Run regression tests      → Full regression validation
6. [Sync to qTest]           → Update qTest module with changes
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
8. [Sync to qTest]           → Upload all test cases + automation results
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

**Step 2: Extract Automation Tests**

→ **Action:** Review mini-STD and identify automation test cases
→ **Create JSON:** .qtest/test-cases/{package}/{Module}.json (automation tests only)

**Step 3: Implement Tests**

→ **Reference:** JSON file for automation tests
→ **Implement:** Test code for automation only
→ **Manual tests:** Stay in mini-STD documentation

**Step 4: Sync to qTest (Optional)**

→ **Upload:** mini-STD test cases + automation results to qTest
```

```markdown
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

→ **Update:** qTest module with changes
```

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

**Step 6: Sync to qTest (Optional)**

→ **Upload:** STD test cases + automation results to qTest
```

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

**Step 6: Sync to qTest**

→ **Update:** qTest module with all changes
```

### ✅ NEXT STEP Instructions (qTest-First)

```markdown
## ✅ NEXT STEP (qTest-First)

**Step 1: Create JSON Test Definition**

→ **Location:** .qtest/test-cases/{package}/{Module}.json
→ **Command (Web):** npm run qtest:generate -- --module-id {ID} --type-filter 702
→ **Command (API):** Use qTest Manager UI to get test case details
→ **Action:** Manually create JSON file with test specifications

**Step 2: Generate Code Skeleton (Optional)**

→ **Command (Web):** npm run qtest:generate -- --module-id {ID} --type-filter 702 --generate-code
→ **Output:** packages/{package}/tests/{module}.spec.ts

**Step 3: Implement Tests**

→ **Reference:** .qtest/test-cases/{package}/{Module}.json
→ **Implement:** Test logic based on JSON specifications
→ **Run:** npm run test:{package}:local (Web) or mvn test (API)

**Step 4: Validate**

→ **Test locally** until all tests pass
→ **No markdown plans needed** - JSON is single source of truth
```

### ✅ NEXT STEP Instructions (Change Request / Enhancement)

```markdown
## ✅ NEXT STEP (Change Request)

**Step 1: Review Change Request**

→ **Document:** JIRA ticket / Change request document
→ **Identify:** What changed (new feature, enhancement, bug fix)
→ **Impact:** Which test cases are affected

**Step 2: Update Documentation (if needed)**

→ **PRD:** Update docs/{module}/prd.md with new requirements
→ **STD:** Update docs/{module}/{module}-std.md with new/modified test cases
→ **Mark:** Clearly indicate what's new vs modified

**Step 3: Update JSON Files**

→ **Location:** .qtest/test-cases/{package}/{Module}.json
→ **Action:** Add new automation test cases OR modify existing ones
→ **Preserve:** Existing test case structure and qTestPID references

**Step 4: Update Test Code**

→ **Existing Tests:** Modify test classes to handle changes
→ **New Tests:** Add new test methods following existing patterns
→ **Maintain:** Same file structure and naming conventions

**Step 5: Run Regression Tests**

→ **Full Suite:** Run all existing tests to ensure no breaks
→ **New Tests:** Run new/modified tests to validate changes
→ **Fix:** Any regression failures before proceeding

**Step 6: Sync to qTest**

→ **Update Module:** Sync changes to qTest module
→ **Update Results:** Run and report test execution results
→ **Traceability:** Link to change request/JIRA ticket
```

---

## Quick Decision Tree

→ **Command (Web):** npm run qtest:generate -- --module-id {ID} --type-filter 702 --generate-code
→ **Output:** packages/{package}/tests/{module}.spec.ts

**Step 3: Implement Tests**

→ **Reference:** .qtest/test-cases/{package}/{Module}.json
→ **Implement:** Test logic based on JSON specifications
→ **Run:** npm run test:{package}:local (Web) or mvn test (API)

**Step 4: Validate**
?}
Q2{PRD or<br/>requirements<br/>exist?}
Q3{Any system<br/>documentation?}

    WF_A[🟢 WORKFLOW A<br/>qTest-First<br/><br/>Next: Create JSON file<br/>.qtest/test-cases/]
    WF_B[🔵 WORKFLOW B<br/>Code-First<br/><br/>Next: do-stp.prompt.md]
    WF_C[🟠 WORKFLOW C<br/>Reverse Engineering<br/><br/>Next: do-design.prompt.md]

    START --> Q1
    Q1 -->|Yes| WF_A
    Q1 -->|No| Q2
    Q2 -->|Yes| WF_B
    Q2 -->|No| Q3
    Q3 -->|Yes| WF_B
    Q3 -->|No| WF_C

````

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
# Fetch module data (review only)
npm run qtest:generate -- --module-id {MODULE_ID} --type-filter 702

# Manually create JSON file
# Location: .qtest/test-cases/{package}/{Module}.json
````

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
  "qTestModuleId": "67420552",
  "testCases": [
    {
      "name": "Test Case Name",
      "description": "What this test validates",
      "precondition": "Prerequisites",
      "testSteps": [],
      "qTestId": "134717060",
      "qTestPID": "TC-XXX-001",
      "priority": "High",
      "type": "Automation"
    }
  ]
}
```

### Step 3: Generate Code Skeleton (Optional)

**automation-web:**

```bash
npm run qtest:generate -- \
  --module-id {MODULE_ID} \
  --type-filter 702 \
  --generate-code
```

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

### Step 6: Sync to qTest (Optional)

**automation-web:**

```bash
QTEST_REPORTER_ENABLED=true npm run test:{package}:qa
```

**automation-comosense:**

```bash
.qtest/simple_sync.py --module {module-id}equirements<br/>exist?}
    Q3{Any system<br/>documentation?}

    WF_B[🟢 WORKFLOW B<br/>qTest-First<br/><br/>Next: qTest CLI commands]
    WF_A[🔵 WORKFLOW A<br/>Code-First<br/><br/>Next: do-stp.prompt.md]
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

## For Simple Features (Quick Path)

If feature is simple (<15 test cases), I may recommend:

- `do-mini-std.prompt.md` instead of full STP → STD
- `do-qa-mini-workplan.prompt.md` instead of full workplan

I'll tell you when this quick path is appropriate.

---

## Execution Instructions

### What I Will Do:

1. Scan workspace files and structure
2. Search for test artifacts
3. Analyze qTest integration status
4. Select optimal workflow
5. Provide detection summary
6. **Give you exact next prompt to run with input file**

### What You Should Do:

1. Run this prompt: `do-stlc-start.prompt.md`
2. Review my detection results
3. Confirm workflow selection (or override)
4. **Follow the NEXT STEP instructions exactly**
5. After each step, look for "NEXT STEP" at the end of output

---

## Override Options

If you want to force a specific workflow:

```
"Use WORKFLOW A (Code-First) for [feature]"
"Use WORKFLOW B (qTest-First) for [feature]"
"Use WORKFLOW C (Reverse Engineering) for [feature]"
```

---

## Example Session

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

Say: **"Start AI-STLC workflow"** or provide your feature/project name.

I will:

1. Detect your project state
2. Select the workflow
3. Tell you exactly what to do next

---

## Reference

- **Full Strategy Document:** `docs/ai-stlc/AI-STLC-Triple-Workflow-Strategy.md`
- **Prompts Index:** `.github/prompts/PROMPTS-INDEX.md`
- **qTest Integration:** `docs/qtest/` (if exists)

---

**Version:** 1.0  
**Last Updated:** February 2, 2026  
**Purpose:** Master orchestrator for AI-STLC workflows

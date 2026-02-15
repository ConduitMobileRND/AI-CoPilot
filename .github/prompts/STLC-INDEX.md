# AI-STLC Prompts Navigation Guide

> **Quick Reference:** Which prompt to use and when
> **Last Updated:** February 15, 2026 | **Version:** 2.0

---

## 🚀 Quick Start (30 seconds)

### **Option 1: I have a qTest Module ID**
```
"{moduleId}"
```
Example: `"TR-123"` or `"45678"`

### **Option 2: I have requirements/PRD/feature request**
```
"Start AI-STLC workflow for [feature-name]"
```
Example: `"Start AI-STLC workflow for payment cashback feature"`

### **Option 3: I'm not sure where to start**
```
"Start AI-STLC workflow"
```

**All routes go through:** [`do-stlc-start.prompt.md`](do-stlc-start.prompt.md)

---

## 🎯 Master Entry Point

```
do-stlc-start.prompt.md
```

**Always start with this.** It will:
1. ✅ Detect your project artifacts (PRD, qTest, code)
2. ✅ Recommend the best workflow (A, B, or C)
3. ✅ Tell you the exact next prompt to run
4. ✅ Provide expected inputs and outputs

---

## 📋 Complete Workflow Maps

### 🟢 WORKFLOW A: qTest-First

_For existing qTest modules with defined test cases_

```
┌──────────────────────────────────────────────────┐
│  WORKFLOW A: qTest-First (Existing Test Suite)  │
└──────────────────────────────────────────────────┘

Step 1: Generate Implementation Plans
  🔧 qTest CLI command or npm script
  📥 Input:  qTest Module ID
  📤 Output: N markdown files (one per test case)
  ⏱️  Time:   15-30 min (automated)
          ↓
Step 2: Generate Code Skeleton
  🔧 qTest CLI command or npm script
  📥 Input:  qTest Module ID
  📤 Output: 1 test file skeleton with all test cases
  ⏱️  Time:   15-30 min (automated)
          ↓
Step 3: Implement Test Logic
  🛠️  Manual with AI assistance (GitHub Copilot)
  📥 Input:  Code skeleton + markdown plans
  📤 Output: Complete test implementation
  ⏱️  Time:   3-6 days (depending on test count)
          ↓
Step 4: Sync Results Back to qTest
  🔧 .qtest/simple_sync.py (smart sync)
  📤 Output: qTest execution results updated
  ⏱️  Time:   15 min
```

**Commands:**

```bash
# Step 1: Generate plans
npm run qtest:generate -- --module-id <MODULE_ID>

# Step 2: Generate code
npm run qtest:generate -- --module-id <MODULE_ID> --generate-code

# Step 4: Sync results
npm run qtest:sync -- --module <module-name>
```

---

### 🔵 WORKFLOW B: Code-First

_For new features starting from PRD/requirements_

```
┌─────────────────────────────────────────────────────┐
│  WORKFLOW B: Code-First (New Feature Development)  │
└─────────────────────────────────────────────────────┘

Step 1: Generate Software Test Plan
  📄 do-stp.prompt.md
  📥 Input:  PRD document (e.g., docs/prd/feature-prd.md)
  📤 Output: STP document (e.g., docs/doc_module/feature-stp.md)
  ⏱️  Time:   30-45 min (AI-assisted)
          ↓
Step 2: Generate Software Test Design
  📄 do-std.prompt.md
  📥 Input:  STP document from Step 1
  📤 Output: STD document (e.g., docs/doc_module/feature-std.md)
  ⏱️  Time:   45-60 min (AI-assisted)
          ↓
Step 3: Generate QA Implementation Plan
  📄 do-qa-workplan.prompt.md
  📥 Input:  STD document from Step 2
  📤 Output: QA Workplan (e.g., docs/doc_module/feature-workplan.md)
  ⏱️  Time:   30-45 min (AI-assisted)
          ↓
Step 4: Implement Tests
  🛠️  Manual with AI assistance (GitHub Copilot)
  📥 Input:  QA Workplan from Step 3
  📤 Output: Test code file (e.g., packages/module/tests/feature.spec.ts)
  ⏱️  Time:   2-5 days (depending on complexity)
          ↓
Step 5: Auto-Extract & Sync to qTest
  🔧 .qtest/java_parser.py or similar (auto-extract)
  🔧 .qtest/simple_sync.py (smart sync)
  📤 Output: New qTest module created
  ⏱️  Time:   30 min
```

**Quick Path (Simple Features <15 tests):**

```
Step 1: do-mini-std.prompt.md (combines STP+STD)
Step 2: do-qa-mini-workplan.prompt.md
Step 3: Implement
Step 4: Sync
```

---

### 🟠 WORKFLOW C: Reverse Engineering

_For legacy/undocumented systems with no PRD, STP/STD, or qTest_

```
┌────────────────────────────────────────────────────────┐
│  WORKFLOW C: Reverse Engineering (Legacy Systems)     │
└────────────────────────────────────────────────────────┘

Step 1: Reverse Engineer System Design
  📄 do-design.prompt.md
  📥 Input:  Existing codebase, system behavior, observations
  📤 Output: Design document (e.g., docs/design/feature-design.md)
  ⏱️  Time:   4-6 hours (AI-assisted)
          ↓
Step 2: Generate Product Requirements
  📄 do-prd.prompt.md
  📥 Input:  Design document from Step 1
  📤 Output: PRD document (e.g., docs/prd/feature-prd.md)
  ⏱️  Time:   3-4 hours (AI-assisted)
          ↓
Step 3: Generate Software Test Plan
  📄 do-stp.prompt.md
  📥 Input:  PRD document from Step 2
  📤 Output: STP document (e.g., docs/doc_module/feature-stp.md)
  ⏱️  Time:   30-45 min (AI-assisted)
          ↓
Step 4: Generate Software Test Design
  📄 do-std.prompt.md
  📥 Input:  STP document from Step 3
  📤 Output: STD document (e.g., docs/doc_module/feature-std.md)
  ⏱️  Time:   45-60 min (AI-assisted)
          ↓
Step 5: Generate QA Implementation Plan
  📄 do-qa-workplan.prompt.md
  📥 Input:  STD document from Step 4
  📤 Output: QA Workplan (e.g., docs/doc_module/feature-workplan.md)
  ⏱️  Time:   30-45 min (AI-assisted)
          ↓
Step 6: Implement Tests
  🛠️  Manual with AI assistance (GitHub Copilot)
  📥 Input:  QA Workplan from Step 5
  📤 Output: Test code file
  ⏱️  Time:   3-5 days
          ↓
Step 7: Auto-Extract & Sync to qTest
  🔧 Auto-extraction + smart sync
  📤 Output: New qTest module created with full documentation
  ⏱️  Time:   30 min
```

---

## 🔍 Quick Workflow Selection

| You Have                   | You Need                | Use Workflow          | Start With            |
| -------------------------- | ----------------------- | --------------------- | --------------------- |
| PRD exists, no qTest       | Test automation         | 🔵 WORKFLOW A         | do-stp.prompt.md      |
| qTest module (50+ cases)   | Automate existing tests | 🟢 WORKFLOW B         | qTest CLI commands    |
| Legacy system, no docs     | Everything from scratch | 🟠 WORKFLOW C         | do-design.prompt.md   |
| Simple feature (<15 tests) | Quick automation        | 🔵 WORKFLOW A (Quick) | do-mini-std.prompt.md |

---

## 📚 All Available Prompts

### Core Workflow Prompts

| Prompt File                  | Purpose                         | Used In | Input             | Output                         |
| ---------------------------- | ------------------------------- | ------- | ----------------- | ------------------------------ |
| **do-stlc-start.prompt.md**  | Master orchestrator             | All     | Project workspace | Workflow selection + next step |
| **do-stp.prompt.md**         | Generate Software Test Plan     | A, C    | PRD               | STP document                   |
| **do-std.prompt.md**         | Generate Software Test Design   | A, C    | STP               | STD document                   |
| **do-qa-workplan.prompt.md** | Generate QA Implementation Plan | A, C    | STD               | QA Workplan                    |
| **do-design.prompt.md**      | Reverse engineer system         | C       | Codebase/system   | Design document                |
| **do-prd.prompt.md**         | Generate PRD                    | C       | Design doc        | PRD document                   |

### Quick Path Prompts

| Prompt File                       | Purpose                              | Used In    | Input    | Output        |
| --------------------------------- | ------------------------------------ | ---------- | -------- | ------------- |
| **do-mini-std.prompt.md**         | Quick Test Design (STP+STD combined) | A (simple) | PRD      | Combined STD  |
| **do-qa-mini-workplan.prompt.md** | Quick Implementation Plan            | A (simple) | Mini-STD | Mini Workplan |

### Utility Prompts

| Prompt File               | Purpose                  | Used In | Input      | Output    |
| ------------------------- | ------------------------ | ------- | ---------- | --------- |
| **do-learning.prompt.md** | Deep repository learning | All     | Repository | Knowledge |

---

## 🧭 Navigation Pattern

Every prompt file has this structure at the end:

```markdown
---
## 🎯 WORKFLOW POSITION

You are in: WORKFLOW A, Step 2 of 5
---

## ✅ NEXT STEP

After completing this prompt:

→ **Run Prompt:** do-std.prompt.md
→ **Input Document:** [output from this prompt]
→ **Expected Output:** docs/doc_module/feature-std.md
→ **Command:** "Generate STD from [stp-file] using do-std.prompt.md"
```

**Follow the NEXT STEP section at the end of each prompt's output.**

---

## 💡 Usage Tips

### 1. Always Start with Master

```
"Start AI-STLC workflow for [feature-name]"
```

### 2. Follow the Breadcrumbs

Each prompt tells you the next step - just follow them in order.

### 3. Use Quick Path for Simple Features

If your feature has <15 test cases, use mini versions:

- `do-mini-std.prompt.md` instead of do-stp → do-std
- `do-qa-mini-workplan.prompt.md` instead of full workplan

### 4. Don't Skip Steps

Each step builds on the previous - skipping leads to incomplete documentation.

### 5. Verify Outputs

Check that each step produces the expected output file before moving to next step.

---

## 📖 Detailed Documentation

For comprehensive details, see:

- **Strategy Guide:** `docs/ai-stlc/AI-STLC-Triple-Workflow-Strategy.md` (1671 lines)
- **Presentation:** `docs/ai-stlc/AI-STLC-Presentation.marmed.md` (656 lines)

---

## 🆘 When You're Stuck

**Q: I don't know which workflow to use**  
A: Run `do-stlc-start.prompt.md` - it will detect and recommend

**Q: I'm in the middle of a workflow and forgot next step**  
A: Check the "NEXT STEP" section at the end of your last output

**Q: The workflow doesn't fit my situation**  
A: Tell the master orchestrator to override: "Use WORKFLOW A for [feature]"

**Q: I need to start over**  
A: Always safe to re-run `do-stlc-start.prompt.md`

---

## Example Complete Session

```
1. You: "Start AI-STLC workflow for payment wallet"

2. AI runs: do-stlc-start.prompt.md
   Output: "Use WORKFLOW A, next run do-stp.prompt.md"

3. You: "Generate STP from docs/prd/payment-wallet-prd.md using do-stp.prompt.md"

4. AI runs: do-stp.prompt.md
   Output: Creates payment-wallet-stp.md
   Next: "Run do-std.prompt.md with payment-wallet-stp.md"

5. You: "Generate STD from payment-wallet-stp.md using do-std.prompt.md"

6. AI runs: do-std.prompt.md
   Output: Creates payment-wallet-std.md
   Next: "Run do-qa-workplan.prompt.md"

7. [Continue until complete]
```

---

**Version:** 1.0  
**Last Updated:** February 2, 2026  
**Purpose:** Navigation guide for AI-STLC prompt system

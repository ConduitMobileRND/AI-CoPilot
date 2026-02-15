# AI-CoPilot

AI-assisted development workflow prompts and templates for Como automation projects.

> **🎯 New to AI-STLC?** Start here: [`.github/prompts/STLC-INDEX.md`](.github/prompts/STLC-INDEX.md)

---

## 📚 Quick Navigation

| Document | Purpose | Audience |
|----------|---------|----------|
| **[STLC-INDEX.md](.github/prompts/STLC-INDEX.md)** | Navigation guide - which prompt to use when | All users |
| **[AI-STLC-IMPLEMENTATION-GUIDE.md](.github/prompts/AI-STLC-IMPLEMENTATION-GUIDE.md)** | Complete implementation reference | Developers & QA |
| **[AI-STLC-ORGANIZATION.md](.github/prompts/AI-STLC-ORGANIZATION.md)** | Visual organizational structure | Team leads |
| **[INDEX.md](.github/prompts/INDEX.md)** | Master prompt catalog | All users |

---

## 🚀 Quick Start

### For Test Automation (AI-STLC):

```bash
# Option 1: Provide qTest module ID
"{moduleId}"

# Option 2: Start with requirements/feature
"Start AI-STLC workflow for [feature-name]"

# Option 3: Let AI analyze and recommend
"Start AI-STLC workflow"
```

**All routes:** [`.github/prompts/do-stlc-start.prompt.md`](.github/prompts/do-stlc-start.prompt.md)

---

## 📁 Structure

```
AI-CoPilot/
└── .github/
    └── prompts/
        ├── 📚 Navigation & Guides
        │   ├── STLC-INDEX.md                      ← Start here
        │   ├── AI-STLC-IMPLEMENTATION-GUIDE.md    ← Comprehensive guide
        │   ├── AI-STLC-ORGANIZATION.md            ← Visual structure
        │   └── INDEX.md                           ← Prompt catalog
        │
        ├── 🎯 Master Orchestrator
        │   └── do-stlc-start.prompt.md            ← Entry point
        │
        ├── 🔵 Workflow A & C (Code-First & Reverse Engineering)
        │   ├── do-stp.prompt.md                   ← Test Plan
        │   ├── do-std.prompt.md                   ← Test Design
        │   ├── do-qa-workplan.prompt.md           ← Implementation Plan
        │   ├── do-mini-std.prompt.md              ← Quick Test Design
        │   ├── do-qa-mini-workplan.prompt.md      ← Quick Workplan
        │   ├── do-design.prompt.md                ← Reverse Engineering
        │   └── do-prd.prompt.md                   ← Generate PRD
        │
        ├── 🟢 Workflow B (qTest-First) - Uses qtest-mcp-server
        │
        ├── 🛠️ Implementation Support
        │   ├── do-implement-tests.prompt.md
        │   └── do-dev-and-test.prompt.md
        │
        ├── 📚 Utility Prompts
        │   ├── do-learning.prompt.md
        │   ├── do-codereview.prompt.md
        │   ├── do-merge-code.prompt.md
        │   ├── do-merge-docs.prompt.md
        │   ├── do-tests.prompt.md
        │   └── ... (more utilities)
        │
        └── ai-stlc/                               ← Core documentation
            ├── README.md
            ├── AI-STLC-Triple-Workflow-Strategy.md
            └── AI-STLC-Brief-Presentation.md
```

---

## 🎯 Three Workflows

### � Workflow A: qTest-First
**When:** Existing qTest module with 50+ test cases  
**Path:** qTest → Fetch → JSON → Implement → Verify → Sync Results  
**Timeline:** 3-6 days (depending on test count)

### 🔵 Workflow B: Code-First
**When:** New features with PRD, no qTest module yet  
**Path:** PRD → STP → STD → Workplan → JSON → Implement → Verify → Sync  
**Timeline:** 3-8 days (depending on complexity)

### 🟠 Workflow C: Reverse Engineering
**When:** Legacy system with no documentation  
**Path:** Design → PRD → Follow Workflow A  
**Timeline:** 7-10 days

---

## 📊 Decision Tree

```
Do you have a qTest module with test cases?
├── Yes (50+ tests) → 🟢 Workflow B (qTest-First)
└── No → Do you have PRD/requirements?
    ├── Yes → Feature complexity?
    │   ├── Simple (<15 tests) → 🔵 Workflow A (Quick Path)
    │   └── Complex (>15 tests) → 🔵 Workflow A (Full Path)
    └── No → 🟠 Workflow C (Reverse Engineering)
```

**Still unsure?** Run `do-stlc-start.prompt.md` - it will detect and recommend.

---

## 💡 Key Principles

1. **Always start with [`do-stlc-start.prompt.md`](.github/prompts/do-stlc-start.prompt.md)**
2. **Automation tests only in JSON** (manual tests stay in STD/qTest)
3. **Verify before syncing** (100% pass rate required)
4. **Follow the breadcrumbs** (each prompt has NEXT STEP section)
5. **Use Quick Path for simple features** (<15 tests)

---

## 🔗 Project Integration

All Como automation projects reference these prompts:

```bash
automation-web/.github/prompts/          → AI-CoPilot/prompts/
automation-comosense/.github/prompts/    → AI-CoPilot/prompts/
automation-llm-validation/               → (can add symlink)
```

**Benefit:** Update once, applies everywhere

---

## 📖 Comprehensive Documentation

- **[Triple Workflow Strategy](./ai-stlc/AI-STLC-Triple-Workflow-Strategy.md)** (1671 lines) - Complete strategic guide
- **[Brief Presentation](./ai-stlc/AI-STLC-Brief-Presentation.md)** (656 lines) - Visual presentation
- **[Implementation Guide](.github/prompts/AI-STLC-IMPLEMENTATION-GUIDE.md)** - Practical step-by-step reference

---

## 🎓 Learning Path

**Week 1:** Foundation
- Read STLC-INDEX.md (5 min)
- Read AI-STLC-IMPLEMENTATION-GUIDE.md (15 min)
- Try Workflow A Quick Path with simple feature

**Week 2:** Advanced
- Study Workflow A Full Path
- Learn Workflow B (qTest-First)
- Practice with real feature

**Week 3:** Mastery
- Workflow C (Reverse Engineering)
- Custom scenarios
- Tool integration

---

## 🆘 Need Help?

| Question | Answer |
|----------|--------|
| Which workflow to use? | Run `do-stlc-start.prompt.md` |
| Forgot next step? | Check NEXT STEP section in last output |
| Workflow doesn't fit? | Override: "Use WORKFLOW A for [feature]" |
| PID validation fails? | Run `npm run validate:pids` |
| Tests fail during sync? | Fix failures, verify 100% pass, then sync |

---

## 📊 Success Metrics

Track these for each workflow:

- ✅ Test Coverage: >80%
- ✅ Pass Rate: 100% before sync
- ✅ Documentation Time: <2 hours (AI-assisted)
- ✅ Sync Success Rate: 100%
- ✅ PID Validation: 100% accuracy

---

## 🔧 Maintenance

**Adding new prompts:**
1. Add to `.github/prompts/`
2. Update INDEX.md
3. Update STLC-INDEX.md
4. Update AI-STLC-IMPLEMENTATION-GUIDE.md (if applicable)

**Updating workflows:**
1. Edit specific prompt file
2. Update breadcrumbs (NEXT STEP sections)
3. Update workflow diagrams (if needed)

---

**Version:** 2.0  
**Last Updated:** February 15, 2026  
**Maintained by:** QA Automation Team

**For Questions:** See [STLC-INDEX.md](.github/prompts/STLC-INDEX.md) or run the AI-STLC Master Orchestrator

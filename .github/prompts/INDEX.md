# GitHub Copilot Prompts - Master Index

> **Centralized prompt repository for all Como automation projects**

---

## 📁 Directory Structure

```
AI-CoPilot/
  prompts/
    ├── ai-stlc/                    ← AI-STLC Testing Workflows (8 files)
    ├── do-learning.prompt.md       ← Repository deep learning
    ├── do-codereview.prompt.md     ← Code review assistant
    ├── do-design.prompt.md         ← System design (reverse engineering)
    ├── do-dev-and-test.prompt.md   ← Development + testing
    ├── do-feature-design.prompt.md ← Feature design
    ├── do-merge-code.prompt.md     ← Code merge assistant
    ├── do-merge-docs.prompt.md     ← Documentation merge
    ├── do-prd.prompt.md            ← PRD generation
    ├── do-project-design.prompt.md ← Project architecture
    ├── do-std.prompt.md            ← Software Test Design (legacy - moved to ai-stlc/)
    ├── do-stp.prompt.md            ← Software Test Plan (legacy - moved to ai-stlc/)
    ├── do-tests.prompt.md          ← Test generation
    └── do-workplan.prompt.md       ← Work planning
```

---

## 🎯 AI-STLC Testing Workflows

**Location:** [`do-stlc-start.prompt.md`](do-stlc-start.prompt.md) (Master Entry Point)

**Purpose:** Complete Software Testing Life Cycle automation with three complementary workflows

**Quick Commands:**
```bash
# Option 1: Auto-detect and recommend
"Start AI-STLC workflow"

# Option 2: Provide qTest module ID
"{moduleId}"

# Option 3: Specific feature
"Start AI-STLC workflow for [feature-name]"
```

---

### Master Entry Point

- **[do-stlc-start.prompt.md](do-stlc-start.prompt.md)** ⭐ PRIMARY ENTRY - Always start here for test automation

### Workflow A & C Prompts (Code-First / Reverse Engineering)

- **[do-stp.prompt.md](ai-stlc/do-stp.prompt.md)** - Generate Software Test Plan
- **[do-std.prompt.md](ai-stlc/do-std.prompt.md)** - Generate Software Test Design
- **[do-qa-workplan.prompt.md](ai-stlc/do-qa-workplan.prompt.md)** - Generate QA Implementation Plan
- **[do-implement-tests.prompt.md](do-implement-tests.prompt.md)** - Implement Automation Tests (NEW)

### Quick Path (Simple Features)

- **[do-mini-std.prompt.md](ai-stlc/do-mini-std.prompt.md)** - Quick Test Design (STP+STD combined)
- **[do-qa-mini-workplan.prompt.md](ai-stlc/do-qa-mini-workplan.prompt.md)** - Quick Implementation Plan

### Documentation

- **[PROMPTS-INDEX.md](ai-stlc/PROMPTS-INDEX.md)** - Complete AI-STLC workflow maps
- **[README.md](ai-stlc/README.md)** - Quick start guide

**See:** [ai-stlc/README.md](ai-stlc/README.md) for usage instructions

---

## 📚 General Development Prompts

### Learning & Architecture

- **[do-learning.prompt.md](do-learning.prompt.md)** - Deep repository learning methodology
- **[do-design.prompt.md](do-design.prompt.md)** - Reverse engineer system design
- **[do-project-design.prompt.md](do-project-design.prompt.md)** - Project architecture design
- **[do-feature-design.prompt.md](do-feature-design.prompt.md)** - Feature-level design

### Requirements & Planning

- **[do-prd.prompt.md](do-prd.prompt.md)** - Generate Product Requirements Document
- **[do-workplan.prompt.md](do-workplan.prompt.md)** - Generate development work plan

### Development

- **[do-dev-and-test.prompt.md](do-dev-and-test.prompt.md)** - Development + testing workflow
- **[do-tests.prompt.md](do-tests.prompt.md)** - Test generation

### Code Review & Merge

- **[do-codereview.prompt.md](do-codereview.prompt.md)** - AI code review assistant
- **[do-merge-code.prompt.md](do-merge-code.prompt.md)** - Code merge conflict resolution
- **[do-merge-docs.prompt.md](do-merge-docs.prompt.md)** - Documentation merge

---

## 🔗 Project References

All projects reference these prompts via symlinks:

```bash
automation-web/.github/prompts/          → AI-CoPilot/prompts/ai-stlc/
automation-comosense/.github/prompts/    → AI-CoPilot/prompts/ai-stlc/
automation-llm-validation/               → (can add symlink as needed)
```

**Benefit:** Update once, applies everywhere

---

## 🚀 Quick Start

### For AI-STLC Testing Workflows:

```
"Start AI-STLC workflow for [feature-name]"
```

Uses: [ai-stlc/do-stlc-start.prompt.md](ai-stlc/do-stlc-start.prompt.md)

### For Repository Learning:

```
"Learn this repository using do-learning.prompt.md"
```

### For Code Review:

```
"Review this code using do-codereview.prompt.md"
```

---

## 📖 Documentation

- **AI-STLC Strategy:** See `automation-web/docs/ai-stlc/AI-STLC-Triple-Workflow-Strategy.md` (1671 lines)
- **AI-STLC Presentation:** See `automation-web/docs/ai-stlc/AI-STLC-Presentation.marmed.md` (656 lines)

---

## 🔧 Maintenance

### Adding New Prompts

1. Add to `AI-CoPilot/prompts/` (general) or `AI-CoPilot/prompts/ai-stlc/` (testing)
2. Update this INDEX.md
3. Prompts automatically available to all projects via symlinks

### Updating Prompts

1. Edit files in `AI-CoPilot/prompts/`
2. Changes immediately available to all projects (no copy needed)

---

**Version:** 1.0  
**Last Updated:** February 2, 2026  
**Maintained by:** QA Automation Team

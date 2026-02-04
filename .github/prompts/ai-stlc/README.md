# AI-STLC Prompts - Quick Start

## 🎯 Always Start Here

```bash
"Start AI-STLC workflow for [your-feature-name]"
```

Use prompt: **[do-stlc-start.prompt.md](do-stlc-start.prompt.md)**

This master orchestrator will:

- ✅ Scan your workspace
- ✅ Detect test artifacts
- ✅ Select workflow (A, B, or C)
- ✅ **Tell you exactly which prompt to run next**

---

## 📚 Complete Documentation

- **Navigation Guide:** [PROMPTS-INDEX.md](PROMPTS-INDEX.md) - Complete workflow maps
- **Full Strategy:** `docs/ai-stlc/AI-STLC-Triple-Workflow-Strategy.md` - 1671 lines

---

## 🔄 The Three Workflows

### � WORKFLOW A: qTest-First

_Existing qTest modules_

```
qTest Module → JSON (automation only) → Implement → Sync back
```

**Key:** Manual tests stay in qTest, only automation tests get JSON files

### 🔵 WORKFLOW B: Code-First

_New features with PRD_

```
do-stp.prompt.md → do-std.prompt.md (all tests) → Extract automation → JSON files → Implement → Sync
```

**Key:** STD contains ALL tests (manual + automation), extract automation for JSON

### 🟠 WORKFLOW C: Reverse Engineering

_Legacy undocumented systems_

```
do-design.prompt.md → do-prd.prompt.md → do-stp.prompt.md → do-std.prompt.md (all tests) → Extract automation → JSON files → Implement
```

---

## ✅ Each Prompt Tells You What's Next

Every prompt ends with a **NEXT STEP** section showing:

- Which prompt to run next
- What input file to use
- What output to expect
- Estimated time

**Just follow the breadcrumbs!**

---

## 📖 Need Help?

1. **Lost?** → Re-run `do-stlc-start.prompt.md`
2. **Confused?** → Check [PROMPTS-INDEX.md](PROMPTS-INDEX.md)
3. **Deep dive?** → Read `docs/ai-stlc/AI-STLC-Triple-Workflow-Strategy.md`

---

**Version:** 1.0  
**Updated:** February 2, 2026

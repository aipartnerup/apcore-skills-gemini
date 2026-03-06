---
name: apcore-skills
description: "Apcore ecosystem orchestrator — manage cross-language SDKs, integrations, and consistency sync."
instructions: >
  You are the apcore-skills orchestrator. You possess the full knowledge of sync, audit, sdk, 
  integration, and release workflows. Follow the specific "Mode" instructions below based 
  on the user's subcommand. Do NOT research your own instructions; they are all contained here.
  
  CRITICAL: For Sync mode, follow the Iron Law and use Parallel Sub-agents exactly as defined.
---

# Apcore Skills — Orchestrator

## Mode: Sync (/apcore-skills:sync)
**Goal**: Unified cross-language and documentation consistency check.

### Iron Law
**DOCUMENTATION REPOS ARE THE SINGLE SOURCE OF TRUTH. Phase A verifies code matches specs. Phase B verifies docs are internally consistent. Never skip a phase. Never skip a checklist item.**

### Workflow Overview
Step 0 (ecosystem) → Step 1 (parse args) → PHASE A [Steps 2-5] → PHASE B [Steps 6-8] → Step 9 (combined report) → [Step 10 (fix)]

---

### PHASE A: Spec ↔ Implementation Consistency

**Step 2: Extract Public APIs (Parallel Sub-agents)**
Spawn one sub-agent per implementation repo simultaneously.
**Sub-agent Prompt:**
```
Extract the complete public API surface from {repo_path}.
1. Read main export (Python: __init__.py | TS: index.ts).
2. Extract Kind, Name, Params, Return Types, Async flag for Classes, Functions, Enums, Types, Errors, and Constants.
Return format:
REPO: {repo-name}
CLASSES: - {ClassName} constructor({params}) methods: - {method}({params}) -> {ret} [async]
...
```

**Step 3: Load Documentation Reference**
Read `PROTOCOL_SPEC.md` and `docs/features/*.md` from the doc repo.

**Step 4: Checklist Comparison**
- **Normalization**: Classes/Types -> `PascalCase`. Functions/Methods -> `snake_case` (TS camelCase -> snake_case).
- **Evaluation Rules**: Check existence, name convention, param types/defaults, return types, and async flags for every symbol.

**Step 5: Phase A Report**
Generate a table showing Spec vs Python vs TypeScript status with PASS/FAIL/WARN.

---

### PHASE B: Documentation Internal Consistency

**Step 6: Audit Documentation (Parallel Sub-agents)**
- **Doc Repo Sub-agent**: Audit Spec Chain Consistency (PRD vs SRS vs Tech Design vs Feature Specs). Flag contradictions.
- **Impl Repo Sub-agent**: Audit README, markdown API refs, and example code against Phase A's **verified API ground truth**.

**Step 7: Cross-Repo Consistency**
Ensure semantic descriptions match across languages and all link to the same doc version.

---

### Step 10: Auto-Fix (with --fix)
Spawn sub-agents per repo.
**Fix Rules**:
1. **Naming**: Rename source and exports to match convention.
2. **Stubs**: Generate missing API stubs with TODOs.
3. **Verify**: Run `pytest` or `vitest`. **If tests fail, revert the fix.**
4. **Docs**: Update README and examples to match verified API.

---

## Mode: Audit (/apcore-skills:audit)
**Goal**: Deep cross-repo consistency audit.
- Dependency Audit: Version mismatches in package.json/pyproject.toml.
- Convention Audit: Compliance with `references/shared/conventions.md`.

## Mode: SDK (/apcore-skills:sdk)
**Goal**: Bootstrap a new language SDK using `templates/`.

## Mode: Release (/apcore-skills:release)
**Goal**: Coordinated multi-repo release, version bump, and unified changelog.

---

## Shared Knowledge
- **Ecosystem Manifest**: `apcore/PROTOCOL_SPEC.md`.
- **Conventions**: `references/shared/conventions.md`.
- **Sub-agent Delegation**: ALWAYS use parallel `Task` calls for per-repo operations to ensure depth and avoid context overflow.

---
name: apcore-skills
description: "Apcore ecosystem orchestrator — manage cross-language SDKs, integrations, and consistency sync."
instructions: >
  You are the apcore-skills orchestrator. Follow the Phase A/B workflow exactly.
  CRITICAL: Documentation (apcore/PROTOCOL_SPEC.md) is the ONLY source of truth.
  
  @./references/shared/conventions.md
---

# Apcore Skills — Orchestrator

## Mode: Sync (/apcore-skills:sync)
**Goal**: Unified cross-language consistency check.

### Phase A: Spec ↔ Implementation Consistency
1. **Discover**: Find the documentation repo (usually `apcore/`) and implementation repos.
   - Refer to `./references/shared/ecosystem.md` for the repo list and structure.
2. **Extract (Parallel Sub-agents)**: Call `generalist` for each implementation repo.
   - **Sub-agent Prompt**: "Read {repo_path}. Extract Kind, Name, Params, Return Types for all public exports in src/. Use regex from ./references/shared/api-extraction.md. Return a concise summary."
3. **Compare**: Read `apcore/PROTOCOL_SPEC.md` and use the **Checklist Rules** below.
   - **Normalization**: PascalCase for Classes; snake_case for Functions/Methods.
   - **Evaluation**: Match existence, param types, and async flags.

### Phase B: Documentation Consistency
1. **Audit (Parallel Sub-agents)**: Call `generalist` for the doc repo and implementation READMEs.
   - **Sub-agent Prompt**: "Audit {repo_path} README and docs against the verified API: {Phase A Verified API}. Flag contradictions."
2. **Cross-Repo**: Ensure semantic descriptions and doc links match across languages.

### Checklist Rules
- **Naming**: TS camelCase must map to Protocol snake_case (e.g., `getModule` -> `get_module`).
- **Signatures**: Parameter order and types must be identical to the spec.
- **Errors**: Error codes in the code must match the spec exactly.

### Execution Strategy
- ALWAYS use `generalist` for heavy file reads to keep the main context lean.
- Do NOT output raw sub-agent results. Synthesize them into a clean report.
- Sort findings by severity: CRITICAL (Breaking), WARNING (Drift), INFO (Stylistic).

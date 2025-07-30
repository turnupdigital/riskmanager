# Next Fixes Execution Plan & O3 Audit Preparation

## Overview

We have identified **5 Critical** and **7 High-Priority** fixes that must be applied in sequence to ensure **EZAlgoTrader.pine** is truly production-ready. This document outlines the prioritized order, detailed plan for each fix, and instructions for recording execution details for a subsequent o3 technical audit.

---

## Prioritized Fix Order

1. **Restructure Filter Pipeline** (Tasks: `fix_pipeline_structure`)
2. **Prevent Default-Close Fallthrough** (Tasks: `fix_default_close_fallthrough`)
3. **Guard Hybrid Exit Race Condition** (Tasks: `fix_hybrid_exit_mutex`)
4. **Enforce Dynamic Ticker Data Source** (Tasks: `fix_ticker_data_source`)
5. **Limit Debug Label Creation** (Tasks: `limit_label_creation`)
6. **Reuse Backtest Table** (Tasks: `table_clear_usage`)
7. **Debounce Pulse Signals** (Tasks: `debounce_signal_pulses`)
8. **Consolidate Duplicate Inputs** (Tasks: `consolidate_duplicate_inputs`)
9. **Refactor Virtual Account Structure** (Tasks: `refactor_virtual_account_struct`)

---

## Detailed Execution Plan

For each fix, follow this template:

### [#] Fix Title
- **Description:** Brief summary of the issue and intended behavior.
- **Target Location:** File(s) and approximate line ranges to update.
- **Implementation Steps:**
  1. Code edits (diff snippet) or function to replace.
  2. Remove or refactor conflicting blocks.
  3. Add new helper functions or variable guards as needed.
- **Verification:** How to test the fix (compilation + behavior tests).
- **Commit Message Template:** Suggestion for the git commit summary.

**Example:**

#### 1. Restructure Filter Pipeline
- **Description:** Ensure Bollinger Band filter variables are computed *before* entry-signal assembly to avoid undefined or stale values blocking multiple signals.
- **Target Location:** `EZAlgoTrader.pine` around lines 885–960.
- **Implementation Steps:**
  1. Move BB calculations (`bbLongFilterOK`, `bbShortFilterOK`) above the final entry logic.
  2. Refactor `longEntrySignal` to use explicit counts: require `sigCountLong > 0`.
- **Verification:**
  - Compile without errors.
  - Enable ≥2 signals, verify backtest + chart show entries when both filters pass.
- **Commit Message:**
  `🔧 FIX: Restructure filter pipeline – BB filters moved before entry logic, longEntrySignal refactored to support multi-signal`  

---

## Recording Execution for o3 Audit

After applying each fix:
1. Record the **commit hash** and include a short description under the fix section.
2. Update this file by appending:
   - `**Executed:** <CommitHash> – Summary of changes applied`
3. Tag any remaining risks or edge-cases for o3 to analyze.

Example update after executing Fix #1:
```markdown
#### 1. Restructure Filter Pipeline
- **Executed:** `abc1234` – BB filters moved to lines 880–900; `longEntrySignal` refactored at line 910.
```

---

## Next Steps

1. Execute Fix #1 and update this document with the commit hash.
2. Proceed through the list in order, updating after each.
3. Once all fixes are executed, hand off this file to the o3 model for a comprehensive deep audit.

*Ensure clarity and traceability at every step. This file will serve as both the execution plan and the audit package for expert review.*
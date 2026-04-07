---
inclusion: manual
description: Verify that every icon name used in SAIL code matches a valid icon alias from the documentation.
---

# SAIL Icon Validator

Verify that every icon name used in SAIL code matches a valid icon alias from the documentation.

## VALIDATION PROCESS

### STEP 1: Extract All Icon Names
Scan the SAIL code for every `icon: "icon-name"` parameter in any component (richTextIcon, buttonWidget, stampField, etc.). Create a list with line numbers.

### STEP 2: Validate Each Icon
For EACH icon name found, search `/ui-guidelines/reference/rich-text-icon-aliases.md` for an exact match.

**Two-Phase Validation:**
- **Phase 1 (Exact):** Search for exact icon name. If found → VALID.
- **Phase 2 (Suggestions):** If not found → search for partial patterns to find similar valid alternatives.

### STEP 3: Report Results
For each invalid icon, provide: Location (line), the invalid icon name, verification evidence, 3-5 similar valid alternatives, and recommended fix.

## COMMON MISTAKES TO AVOID
- ❌ Fuzzy matching: "circle-question" ≠ "question-circle"
- ❌ Accepting partial matches as valid
- ✅ Each line in the aliases file is exactly one icon name — match the full name

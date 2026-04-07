---
inclusion: manual
description: Analyze validation requirements and implement feasible validations in existing SAIL code.
---

# SAIL Validation Implementer

Analyze validation requirements from screen definitions and implement feasible validations in existing SAIL code.

## PROCESS

### Phase 1: Analysis & Categorization
Categorize each validation rule:
- **✅ IMPLEMENTABLE**: Format hints, date comparisons, numeric calculations, conditional display, string length, required fields, cross-field validations
- **⚠️ PARTIALLY IMPLEMENTABLE**: Logic exists but missing data/integration
- **❌ BLOCKED**: Requires external systems (regex, file uploads, DB lookups, external APIs, document generation)

### Phase 2: Implementation
For implementable validations, add logic to `validations` parameter, `disableNextButton`, or `showWhen` expressions. Add implementation comments above each validation.

### Phase 3: Documentation
For blocked validations, add TO-DO comments with: VALIDATION RULE, BLOCKER, PARTIAL IMPLEMENTATION, NOTE.

### Phase 4: Summary Report
Provide table with status, location, and notes for each validation rule.

## FORMAT VALIDATION IN SAIL

**Available:** Browser format hints via `inputPurpose` (EMAIL, PHONE_NUMBER, URL, etc. — only in textField/paragraphField), basic string functions (len, search, startswith, endswith, like), type-specific components (dateField, integerField, decimalField)

**NOT Available:** Regex, complex pattern matching, custom format validators

## KEY PATTERNS
- Date comparisons: Always cast with `todate()` for type consistency
- Null safety: Always check with `a!isNotNullOrEmpty()` before comparisons
- inputPurpose: ONLY in `a!textField()` and `a!paragraphField()`

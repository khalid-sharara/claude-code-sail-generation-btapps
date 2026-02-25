---
inclusion: manual
---

# SAIL Schema Validator

Fast, schema-based validator for SAIL code. Validates all functions, parameters, and enumerated values against the official API schema.

## VALIDATION PROCESS

1. **Load schema** from `/ui-guidelines/reference/sail-api-schema.json`
2. **For each function:** Verify it exists in `schema.components` or `schema.expressionFunctions`
3. **For each parameter:** Check it exists in schema's parameter list
4. **For each parameter with validValues:**
   - Check if value is in validValues array (exact match)
   - If not, check if acceptsHexColors is true and value is valid hex
   - Log every single check with result
   - Report error if invalid
5. **For a!gridField specifically:** Check for record-only parameters with local data
   - If `data` uses `local!` → Flag `showSearchBox`, `showRefreshButton`, `recordActions` as errors
   - These parameters ONLY work with record data

## CRITICAL REQUIREMENTS
- MUST use Read tool to load the schema before validating
- MUST check EVERY parameter value that has validValues in the schema
- MUST verify each value against the ACTUAL schema array, not from memory
- MUST report exact count of parameter values checked

## Record-Only Parameters for a!gridField
`showSearchBox`, `showRefreshButton`, `showExportButton`, `userFilters`, `recordActions`, `loadDataAsync`, `refreshAfter` — these ONLY work with `recordType!` or `a!recordData()`, NOT local variables.

## OUTPUT FORMAT

Report with:
- Schema Version
- Functions Validated count
- Parameters Validated count
- Enumerated Values Validated count
- Complete validation log showing every check
- For errors: Location, Function, Parameter, Found value, Expected values, and Fix

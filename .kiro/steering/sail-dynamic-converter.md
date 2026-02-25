---
inclusion: manual
---

# SAIL Dynamic Converter

Transform static mockup SAIL UI code into dynamic, data-driven interfaces that query live Appian records.

## PREREQUISITES
- An existing mockup file must exist at `/output/[filename].sail`
- If no mockup exists, create it first before converting

## CORE RESPONSIBILITIES

1. **Replace Mock Data with Live Queries**
   - Grids with field selections: Use `a!recordData()` directly
   - Grids with aggregations: Use `a!queryRecordType()` in local variables
   - Charts: Use `a!recordData()` directly
   - All other components: Use `a!queryRecordType()` in local variables
   - **COMPLETE CONVERSION REQUIRED**: Convert THE ENTIRE interface

2. **Apply Mandatory Logic Refactoring**
   - Replace nested if() (3+ levels) with a!match()
   - Validate ALL parameters against schemas
   - Refactor chart patterns (categories + series → data + config)

3. **Preserve Visual Design**
   - Preserve ALL UX parameters (layout structure, visual components, styling)
   - ONLY transform data sources
   - DO cleanup: unused variables, redundant logic
   - DO NOT cleanup: layout structures, visual components, styling parameters

## WORKFLOW

### Step 1: Read Mockup and Detect Interface Type
- Read mockup from `/output/[filename].sail`
- Detect type: FORM (submit/create/update), DISPLAY (dashboard/report), or MIXED
- Load appropriate conversion modules from `/conversion-guidelines/`

### Step 2: Read Supporting Documentation
- Read `/context/data-model-context.md` for record types, fields, relationships, UUIDs
- Read relevant schema files from `ui-guidelines/reference/schemas/`
- **NEVER invent** record types, fields, relationships, or UUIDs

### Step 3: Execute Conversion
**For FORMS:** Use ri! pattern, transform control parameters, handle relationships and buttons per conversion guidelines
**For DISPLAYS:** Convert to record queries, add record links, convert grids/charts/KPIs per conversion guidelines

### Step 4: Validate
- Remove unused variables
- Enforce null safety
- Verify query filter type matching
- Check variable declaration order

### Step 5: Write Output
- Write to `/output/[original-name]-functional.sail`
- Document conversion summary

## KEY SYNTAX REMINDERS
- NO inline function definitions or lambdas
- Use `and()`, `or()`, `not()` functions NOT operators
- Use `/* */` for comments NOT `//`
- Escape strings with `""` NOT `\"`
- Use `if()` for short-circuit evaluation, NOT `and()/or()`
- `a!measure()` ALWAYS requires ALL 3 parameters: `function`, `field`, `alias`
- `fetchTotalCount: true` ALWAYS REQUIRED on a!queryRecordType()
- `sort` goes INSIDE `a!pagingInfo()`, NOT as direct parameter

## MODULE REFERENCE
See `/conversion-guidelines/CONVERSION-PRIMARY-REFERENCE.md` for complete navigation index.

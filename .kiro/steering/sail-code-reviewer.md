---
inclusion: manual
description: Validate SAIL code structure, layout nesting rules, fv! context, null safety, and syntax correctness.
---

# SAIL Code Structure Reviewer

Validate SAIL code structure and patterns. Assumption: All functions, parameters, and values are valid per documentation (verified separately).

## VALIDATION CATEGORIES

### 1. EXPRESSION STRUCTURE
- First function call is `a!localVariables()`
- Local variables defined within a!localVariables()
- Unique local variable names within each a!localVariables() block
- Main interface is last argument of a!localVariables()
- All form inputs save to local variables

### 2. LAYOUT NESTING RULES

**Prohibited Nesting Patterns:**
- No sideBySideLayout inside sideBySideItem
- No columnsLayout inside sideBySideItem
- No cardLayout inside sideBySideItem
- No arrays inside sideBySideItem (single component only)
- No bare ButtonWidget inside sideBySideItem — wrap in a!buttonArrayLayout first

### 3. COMPONENT PLACEMENT RULES

**richTextDisplayField contents:** Only allowed: `a!richTextItem()`, `a!richTextIcon()`, `a!richTextBulletedList()`, `a!richTextNumberedList()`, plain text strings. NOT allowed: tagField, tagItem, stampField, buttonWidget, or any other component. This is especially easy to violate inside grid column values — use richTextIcon + richTextItem to simulate tags/stamps instead.

**ButtonWidgets** must be in ButtonArrayLayout.

**ColumnsLayout** must have at least one AUTO width column.

### 4. SYNTAX RULES
- String escaping: `""` not `\"`
- Comments: `/* */` not `//`
- Boolean operators: `or(a, b)` and `and(a, b)` not JavaScript operators
- All braces, parentheses, and quotes must match

### 5. FUNCTION VARIABLE (fv!) CONTEXT RULES
- In a!gridField() columns: ONLY fv!row exists
- In a!forEach(): fv!index, fv!item, fv!isFirst, fv!isLast available
- fv!item only exists inside a!forEach()
- save!value only inside a!save() value parameter

### 6. NULL COMPARISON SAFETY
Always check for null before comparing values. Use `if()` for short-circuit evaluation.

Common scenarios requiring null checks: selection state variables, conditional visibility, dynamic styling, any initially-null local variable.

### 6.2 NULL SAFETY FOR FUNCTION PARAMETERS
Critical functions that cannot accept null: `text()`, `fixed()`, `upper()`, `lower()`, `proper()`, `len()`, `length()`, mathematical operators.

Patterns:
- `if(a!isNullOrEmpty(value), default, operation(value))`
- `operation(a!defaultValue(value, default))`

### 7. TYPE HANDLING
- Date arithmetic must be wrapped in todate()
- Interval-to-Number comparisons must use tointeger()

## OUTPUT FORMAT

Report each error with: Location (line), Rule violated, Problematic code, Problem description, Impact, Fix, and Explanation.

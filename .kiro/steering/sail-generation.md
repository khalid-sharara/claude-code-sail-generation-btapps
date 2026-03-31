---
inclusion: auto
---

# PROJECT INSTRUCTIONS - SAIL UI GENERATION

## PURPOSE AND GOALS
- Given a request generate an Appian SAIL UI mockup
- Write generated output to a .sail file in the /output folder
- Use only valid SAIL components and the allowed parameter values for each
- Use modern, but business-appropriate styling
- Don't worry about querying live data, just hard-code sample content using local variables and a!map
- NEVER use `ri!` or `recordtype!` references in mockups - mockups are pure UX prototypes
- Use `local!` variables for ALL data AND control parameters (isUpdate, cancel)
- Initialize control parameters: `local!isUpdate: false()`, `local!cancel: false()`
- The sail-dynamic-converter steering will guide transformation of local! → ri! in Phase 2
- Inline ALL logic - no `rule!` or `cons!` references unless explicitly specified!
  - ❌ SAIL does not support inline function definitions or helper expressions stored in variables
  - ❌ CANNOT create reusable logic: `local!calculateColor: rule!helper` (syntax error - rule references cannot be stored in variables)
  - ❌ CANNOT define inline helper functions or lambdas: `local!helper: function(x, y)(...)` or `local!helper: expression` (invalid SAIL syntax)
  - ✅ Repeat logic inline wherever needed (even if duplicative across multiple columns/components)
  - ✅ For complex repeated logic, use if()/a!match() patterns directly in each location
- 💡 **When logic is repeated 3+ times**: Add TODO comment for future extraction
- ‼️Syntax errors are DISASTROUS and MUST BE AVOIDED at any cost! Be METICULOUS about following instructions to avoid making mistakes!
- ❌Don't assume that a parameter or parameter value exists - ✅ONLY use values specifically described in the appropriate schema files (`/ui-guidelines/reference/schemas/*.json`)

## ⚠️ BEFORE YOU BEGIN - MANDATORY RULES
1. ❌ NEVER nest sideBySideLayouts inside sideBySideLayouts
2. ❌ NEVER put arrays of components inside sideBySideLayouts
3. ❌ NEVER put columnsLayouts or cardLayouts inside sideBySideLayouts
4. ❌ NEVER put a bare ButtonWidget inside a sideBySideItem — always wrap in a!buttonArrayLayout
5. ✅ ONLY richTextItems, richTextIcons, richTextBulletedList, richTextNumberedList, or plain text strings are allowed inside richTextDisplayField value — NEVER nest tagField, stampField, buttonWidget, or any other component inside richTextDisplayField
6. ✅ Each columnsLayout must have at least one AUTO-width columnLayout
7. ❌ choiceValues CANNOT be null or empty strings
8. ⚠️ ALWAYS check for null before comparisons/property access - use if() NOT and() (see NULL SAFETY RULES section)
9. ⚠️ Grid record-only parameters (`showSearchBox`, `userFilters`, `recordActions`) cause runtime errors with local data - use custom search/filter UX with TODO comments for mockups instead
10. ❌ NEVER use runtime generators (rand(), now(), today()) for sample data - use hardcoded static values instead
11. ⚠️ Grid column `value` accepts ONE component — to show multiple component types (e.g., rich text AND a tag), use a single richTextDisplayField with richTextIcon + richTextItem to simulate tags/stamps, since tagField/stampField CANNOT be nested inside richTextDisplayField
12. ⚠️ ALL local variables MUST follow naming conventions with type suffixes: `local!{descriptiveName}_{typeSuffix}` — e.g., `local!customerName_txt`, `local!isApproved_bool`, `local!crmOrders_rt`, `local!selectedIds_list`. NEVER use generic names like `local!data` or `local!items` without a type suffix. See "LOCAL VARIABLE NAMING CONVENTIONS" section for full rules.

If you violate any of these rules, STOP and reconsider your approach.

---

# PHASE 1: UNDERSTAND THE TASK

## INITIAL REQUEST CATEGORIZATION

Determine if the user wants a full page or just a component.

### Decision Criteria:

**Generate a SINGLE COMPONENT if the request:**
- Asks for "a grid", "a card", "a form", "a chart", etc. (note: "a" = one thing)
- Names a single component (grid, KPI, etc.) that's not a top-level layout
- Specifies columns, fields, or content WITHOUT mentioning "page" or "interface"

**Generate a FULL PAGE if the request:**
- Asks for "a page", "a dashboard", "an interface", "a screen"
- Mentions multiple distinct sections or areas
- Describes a complete user experience or workflow
- Names a top-level layout (header-content, form, wizard, panes)

---

# PHASE 2: PLAN THE UI

## PAGE UI DESIGN PLANNING STEPS
When designing a full page, follow these planning steps (not necessary if user requests a single component):

1. Decide which top-level layout to use:
  - [ ] Pane layout - if the page features full-height (100vh) panes that might scroll independently, or,
  - [ ] Form layout - for single-step forms, or,
  - [ ] Wizard layout - for multi-step forms, or,
  - [ ] Header-content layout - for everything else
2. Read primary layout docs
  - [ ] If using FormLayout → Read `ui-guidelines/layouts/form-layout-instructions.md`
  - [ ] If using HeaderContentLayout → Read `ui-guidelines/layouts/header-content-layout-instructions.md`
  - [ ] If using PaneLayout → Read `ui-guidelines/layouts/pane-layout-instructions.md`
  - [ ] If using WizardLayout → Read `ui-guidelines/layouts/wizard-layout-instructions.md`
3. Plan the main page content layout using columnsLayout → Always read `ui-guidelines/layouts/columns-layout-instructions.md`
4. Use sideBySideLayout as needed → Always read `ui-guidelines/layouts/sidebyside-layout-instructions.md`
5. Avoid redundant card nesting for sections containing card collections

## LAYOUT SELECTION GUIDE

### Layout Hierarchy (Top to Bottom):
1. **Page Structure**: HeaderContentLayout/FormLayout/PaneLayout
2. **Content Sections**: ColumnsLayout → CardLayout/SectionLayout
3. **Component Arrangement**: SideBySideLayout (components only!)

### When to Use Each Layout:
- **ColumnsLayout**: Page structure, fixed pixel widths
- **SideBySideLayout**: Icon + text, label + value, minimized (flex 0) layouts

## COMPONENT SELECTION GUIDE

### Form Inputs
- Use `radioButtonField` or `checkboxField` for short lists of options
- Use `cardChoiceField` for visually interesting short option lists
- Use `dropdownField` for longer lists of options
- Use `styledTextEditorField` for formatted text entry

### List Display
- `gridField` for tabular data
- Custom tabular display pattern (`ui-guidelines/components/tabular-data-display-pattern.md`) for complex cells
- `cardGroupLayout` for responsive card grids

### Decorative Data Display
- `stampField` for colored circles/squares with icons or initials
- `tagField` for tag/chip styled elements
- `richTextDisplayField` for styled text and icons

### Common Patterns
Browse `/ui-guidelines/patterns` for composing common UI elements:
- `card_lists.md` for list items shown as cards
- `kpis.md` for key performance indicator cards
- `messages.md` for message banners
- `tabs.md` for tab bars

### Button Quick Rules
- Style is ONLY: `"OUTLINE"` | `"GHOST"` | `"LINK"` | `"SOLID"`
- Colors: `"ACCENT"` | `"SECONDARY"` | `"NEGATIVE"` | hex codes
- Primary action = `style: "SOLID"` + `color: "ACCENT"`
- Always wrapped in `a!buttonArrayLayout`

### Grid Search and Filters
**Mockups:** Create custom search/filter UX with TODO-CONVERTER comments.
**Exception:** Multi-grid filters remain as custom UX.

### Comment Types

| Prefix | Use For |
|--------|---------|
| `TODO-CONVERTER:` | Set field to X, Transform to ri!, Convert to showSearchBox/userFilters |
| `TODO:` | Send email, Trigger process, Configure webhook |
| `TODO-DATA-MODEL:` | Add field to table, Create relationship |
| `REQUIREMENT:` | User-specified business rules |

### Mockup Boundaries

**❌ NEVER in mockups:** `ri!`, `recordtype!`, `a!recordData()`, `a!queryRecordType()`, grid record-only params
**✅ ALWAYS in mockups:** `local!` variables with `a!map()` sample data, simple property names, static hardcoded values

## STYLING
- #F5F6F8: page background color
- #1C2C44: (optional) page header bar background color
- #FFFFFF: content card background color
- `ACCENT`: themed accent color
- `STANDARD`: text and heading color

---

# PHASE 3: WRITE THE CODE

## 📚 DOCUMENTATION REQUIREMENT
**ALWAYS read component docs from `/ui-guidelines/` BEFORE writing code.** Never assume you know how a component works.

## 🎯 PARAMETER VALIDATION WORKFLOW

### Step 1: Load Required Schema Files

**For ALL interfaces:**
```
✅ ui-guidelines/reference/schemas/layouts-schema.json (always needed)
```

**Additional schemas based on components:**
- Forms with inputs? → `schemas/input-components-schema.json`
- Action buttons? → `schemas/button-components-schema.json`
- Read-only displays? → `schemas/display-components-schema.json`
- Grids? → `schemas/grid-components-schema.json`
- Charts? → `schemas/chart-components-schema.json`
- Complex logic? → `schemas/expression-functions-schema.json`

### Step 2: Read Component-Specific Instructions (When Available)

**Layouts:** header-content, columns, sidebyside, form, pane, wizard, card layout instructions
**Components:** button, grid-field, grid-layout, rich-text, stamp-field, card-choice-field, chart, image-field, tabular-data-display-pattern instructions
**Icons:** ⚠️ **MUST READ before using ANY icons:** `ui-guidelines/reference/rich-text-icon-aliases.md`

### Step 3: Follow the Pattern
1. Load schema files → Understand allowed parameters and values
2. Read instruction file (if exists) → Get templates, patterns, checklists
3. Write code → Follow templates exactly
4. Validate → Check against schema + instruction checklist

## 🔄 DYNAMIC SAIL EXPRESSIONS

| Need | Read This File |
|------|----------------|
| Navigation index | `/logic-guidelines/LOGIC-PRIMARY-REFERENCE.md` |
| Email validation | `/logic-guidelines/functions-reference.md#email-validation-pattern` |
| Arrays, loops, forEach | `/logic-guidelines/foreach-patterns.md` |
| Array manipulation | `/logic-guidelines/array-manipulation-patterns.md` |
| Null safety | `/logic-guidelines/null-safety-quick-ref.md` |
| Grid selections | `/logic-guidelines/grid-selection-patterns.md` |
| Checkbox initialization | `/logic-guidelines/choice-field-patterns.md` |
| Pattern matching | `/logic-guidelines/pattern-matching.md` |
| Date/time handling | `/logic-guidelines/datetime-handling.md` |
| Chart configuration | `/logic-guidelines/chart-configuration.md` |

## EXPRESSION STRUCTURE RULES
- All expressions must begin with a!localVariables() as the parent element
- Place the main interface as the last argument of a!localVariables()
- Define any local variables within the a!localVariables() function
- All form inputs should save into a corresponding local variable
- ButtonWidgets must be inside a ButtonArrayLayout
- Use cardLayout for content blocks, EXCEPT for card collections

## 📛 LOCAL VARIABLE NAMING CONVENTIONS (MANDATORY)

### General Variables — suffix with type abbreviation
- Plurality must match the variable content (singular vs plural)
- Name must convey context (type/purpose)
- Format: `local!{descriptiveName}_{typeSuffix}`

| Suffix | Type | Example |
|--------|------|---------|
| `_int` | Integer | `local!totalLineItems_int` |
| `_dec` | Decimal | `local!averageCost_dec` |
| `_txt` | Text | `local!customerName_txt` |
| `_bool` | Boolean | `local!isApproved_bool` |
| `_dt` | Date/DateTime | `local!submissionDate_dt` |
| `_list` | List/Array | `local!selectedIds_list` |
| `_map` | Map | `local!filterCriteria_map` |

### Record Type Variables — suffix with `_rt`
- Format: `local!{contextName}_rt`
- Example: `local!crmOrders_rt`
- When multiple variables reference the same record type, add a differentiator segment after the record name:
  - `local!crmOrders_archived_rt`
  - `local!crmOrders_unarchived_rt`

### Rules
- ✅ Plurality matches content: `local!lineItems_list` (plural, it's a list), `local!customerName_txt` (singular)
- ✅ Context is clear: `local!totalLineItems_int` not `local!count_int`
- ❌ NEVER use generic names without type suffix: `local!data`, `local!items`, `local!value`
- ❌ NEVER omit the differentiator when multiple variables reference the same record type

## SYNTAX REQUIREMENTS
- Never use JavaScript syntax or operators (if, or, and)
- Use a!forEach() instead of apply()
- Double check braces, parentheses, and quotes are matched
- Use /* */ for comments, not //
- Use "" to escape a double quote, not \"
- Choice values cannot be null or empty strings
- Choice field value initialization: leave uninitialized for unchecked state
- **SAIL has NO regex support** - use pattern from `/logic-guidelines/functions-reference.md#email-validation-pattern`

## ⚠️ NULL SAFETY RULES (CRITICAL)

> **📖 Complete Reference:** `/logic-guidelines/null-safety-quick-ref.md`

### Core Problem
SAIL cannot handle null values in most operations.

### Universal Pattern: Use if() for Short-Circuit Evaluation
```sail
/* ✅ RIGHT - if() short-circuits (safe) */
showWhen: if(a!isNotNullOrEmpty(local!selectedId), local!selectedId = fv!item.id, false())

/* ❌ WRONG - and() does NOT short-circuit (crashes) */
showWhen: and(a!isNotNullOrEmpty(local!data), local!data.type = "Contract")
```

### Essential Patterns

| Scenario | Pattern |
|----------|---------|
| Comparison with nullable | `if(a!isNotNullOrEmpty(var), comparison, false)` |
| Property access | `if(a!isNotNullOrEmpty(obj), obj.prop, default)` |
| Function parameter | `function(a!defaultValue(var, default))` |
| Grid selection | `index(selection, 1, null)` then check |
| Boolean with not() | `not(a!defaultValue(var, false()))` |

## ⚠️ FUNCTION VARIABLES (fv!) - CRITICAL RULES
- In grid columns: ONLY use `fv!row` (NOT fv!index, NOT fv!item)
- In a!forEach(): Use `fv!index`, `fv!item`, `fv!isFirst`, `fv!isLast`
- `save!value` can ONLY be used inside `a!save()` value parameter

## TYPE HANDLING FOR DATE/TIME CALCULATIONS
- Cast date arithmetic: `todate(today() + 1)`
- Interval comparisons: `tointeger(now() - timestamp)` before comparing to numbers

## PARAMETER RESTRICTIONS
- Only use parameters explicitly defined in the documentation
- Color values must use 6-character hex codes (#RRGGBB) or documented enumeration values
- Icons must reference valid aliases
- RichTextItem align: "LEFT", "CENTER", or "RIGHT" only
- choiceValues CANNOT be null or empty strings

### Common Enum Value Constraints (Frequently Misused)
| Component | Parameter | Valid Values |
|-----------|-----------|-------------|
| `a!stampField` | `shape` | `"SQUARED"`, `"SEMI_ROUNDED"`, `"ROUNDED"` (NO `"CIRCLE"`) |
| `a!radioButtonField` | `choiceLayout` | `"STACKED"`, `"COMPACT"` (NO `"HORIZONTAL"`) |
| `a!sideBySideItem` | `width` | `"AUTO"`, `"MINIMIZE"`, `"1X"` through `"10X"` (NO `"NARROW"`, `"NARROW_PLUS"`, `"WIDE"` — those are for `a!columnLayout` only) |
| `a!buttonWidget` | `style` | `"OUTLINE"`, `"GHOST"`, `"LINK"`, `"SOLID"` |
| `a!columnLayout` | `width` | `"AUTO"`, `"EXTRA_NARROW"`, `"NARROW"`, `"NARROW_PLUS"`, `"MEDIUM"`, `"MEDIUM_PLUS"`, `"WIDE"`, `"1X"` through `"10X"` |
| `a!cardLayout` | `shape` | `"SQUARED"`, `"SEMI_ROUNDED"`, `"ROUNDED"` |

---

# PHASE 4: VALIDATE & DOCUMENT

## CAPTURING USER REQUIREMENTS IN GENERATED CODE
> **📖 Complete Patterns:** `/logic-guidelines/documentation-patterns.md`

Use three-tier comment structure:
1. **Interface-level header** - Overall purpose and key requirements
2. **Section-level comments** - Business purpose + field requirements
3. **Inline comments** - Complex logic explanation

**Rules:** ✅ ONLY capture requirements explicitly stated by the user. ❌ DO NOT add requirement comments for standard UI patterns.

## 🚨 UNIVERSAL SAIL VALIDATION CHECKLIST

### Before Writing ANY Code:
**READ `/logic-guidelines/LOGIC-PRIMARY-REFERENCE.md` if your code uses arrays, index access, property access, null checking, looping, data aggregation, or multiple data items.**

### Dynamic Form Field Validation:
- **Array of Maps** (PREFERRED): For multi-instance data entry → `saveInto: fv!item.propertyName`
- **Parallel Arrays**: Only when iterating a fixed source list
- Parallel arrays type-initialized based on data type
- NO `value: null, saveInto: null` in input fields
- Multi-select checkbox fields use single array variable
- DO NOT use `local!showValidation` flags
- Email fields: MUST use the email validation pattern from functions-reference.md
- Control parameters use `local!` NOT `ri!`

### Syntax Validation:
- Starts with a!localVariables()
- All braces/parentheses matched
- All strings in double quotes, escape with ""
- Comments use /* */ not //
- `or(a,b)` NOT `a or b`
- Pattern matching (3+ cases) use `a!match()` NOT `if()`
- Empty arrays type-initialized
- Text arrays use `touniformstring({})` NOT `tostring({})`
- All null-unsafe operations protected
- Date arithmetic wrapped in todate()
- NO inline function definitions or lambdas

### Function Variable Validation:
- In grid columns: ONLY `fv!row`
- In a!forEach(): `fv!index`, `fv!item`, `fv!isFirst`, `fv!isLast`
- `save!value` ONLY inside `a!save()` value parameter

### Parameter Validation:
- Every parameter and value checked against documentation
- Grid columns: sortField must match primary field and be unique across columns
- Computed columns must NOT have sortField

### Layout Validation:
- One top-level layout
- No nested sideBySideLayouts
- No columns or card layouts inside sideBySideItems
- Only richTextItems, richTextIcons, richTextBulletedList, richTextNumberedList, or plain text inside richTextDisplayField — NEVER tagField, stampField, buttonWidget, or other components
- Grid column value: if you need mixed component types (text + tag), use richTextIcon + richTextItem to simulate — do NOT nest tagField/stampField inside richTextDisplayField
- At least one AUTO width column in each columnsLayout

### Unused Variable Check:
- No unused local variables

## 🔄 GENERATING FUNCTIONAL INTERFACES

**When user requests a "functional interface" or "dynamic interface":**

**STEP 1:** Create static mockup FIRST → Write to /output/[name].sail
**STEP 2:** THEN follow the conversion steering guide (#[[file:.kiro/steering/sail-dynamic-converter.md]]) to transform to functional code → Write to /output/[name]-functional.sail

## ✅ VALIDATING SAIL EXPRESSIONS

After generating SAIL expressions, validate using:
1. If MCP appian-mcp-server is available, use it for syntax validation
2. Otherwise, follow the validation steering guides:
   - #[[file:.kiro/steering/sail-schema-validator.md]] for function/parameter validation
   - #[[file:.kiro/steering/sail-icon-validator.md]] for icon name validation
   - #[[file:.kiro/steering/sail-code-reviewer.md]] for structure/syntax validation

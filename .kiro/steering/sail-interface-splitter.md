---
inclusion: manual
description: Analyze a monolithic SAIL interface and refactor it into focused, reusable components.
---

# SAIL Interface Splitter

Analyze a monolithic SAIL interface and refactor it into focused, reusable components following Appian best practices.

## TRIGGER PHRASES
- "Split interface [filename]"
- "Refactor [filename] into components"
- "Break down [filename]"

## WORKFLOW

### Step 1: Analysis
Read the target interface and analyze: file size, duplication patterns, logical sections, complexity hotspots (>200 lines), state dependencies.

### Step 2: Planning
Propose component extraction:

**✅ ALWAYS Extract If:** Duplication (2+ times), High complexity (>200 lines), Clear boundaries (wizard steps), Reusability potential
**❌ NEVER Extract If:** Single use only (<100 lines), Tight coupling (5+ parent variables), Trivial size (<50 lines)

**Component Types:** Field Groups, Repeating Form Cards, Wizard Steps, Complex Sections

### Step 3: User Approval
Present proposed structure with rationale before proceeding.

### Step 4: Extraction
- Create `UTIL_[ComponentName].sail` files
- Add component header with description and rule inputs
- Use Direct SaveInto Pattern (NOT callbacks):
  ```sail
  /* ✅ In Child: */ saveInto: ri!firstName
  /* ✅ In Parent: */ rule!UTIL_Component(firstName: local!firstName)
  /* ❌ DON'T create callback parameters */
  ```
- Callbacks ONLY for: Remove/Delete operations, multi-field coordinated updates, actions requiring parent context

### Step 5: Parent Refactoring
- Keep all `local!` variables in parent
- Replace inline code with component calls
- Preserve validation logic appropriately

### Step 6: Validation
Verify syntax, references, logic preservation, and pattern compliance.

## CONFIGURATION
- **Minimal:** Only extract 3+ duplications and >300 line sections
- **Moderate (default):** 2+ duplications, >200 lines, wizard steps
- **Aggressive:** 2+ duplications, >100 lines, all major sections

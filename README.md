# SAIL UI Generation (Kiro)

This tool generates Appian SAIL UI expressions from natural language requests.

## Getting Started
1. [Clone this repo](https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository)
2. Open the repo folder in [Kiro](https://kiro.dev)
3. For best performance, connect to the Appian VPN while using this project ([See why](#validation-approaches))
4. Start chatting with Kiro to generate SAIL interfaces

## Project Structure

### Steering Files (`.kiro/steering/`)
Kiro uses steering files to guide its behavior. This project includes:

- **`sail-generation.md`** (auto-included) — Core SAIL generation rules, syntax requirements, validation checklists, and component guidelines. Always active.
- **`sail-dynamic-converter.md`** (manual) — Guide for converting static mockups to dynamic, data-driven interfaces with live record queries.
- **`sail-code-reviewer.md`** (manual) — Structure and syntax validation rules for SAIL code review.
- **`sail-schema-validator.md`** (manual) — Schema-based parameter and value validation against the API schema.
- **`sail-icon-validator.md`** (manual) — Icon name validation against the allowed aliases list.
- **`sail-interface-splitter.md`** (manual) — Guide for refactoring large interfaces into reusable components.
- **`sail-validation-implementer.md`** (manual) — Guide for implementing validation rules from screen definitions.

Manual steering files are pulled in automatically when relevant, or you can reference them with `#` in chat.

### MCP Server
The Appian MCP server is configured in `.mcp.json` for SAIL expression validation. Requires VPN connection.

## Generating a SAIL Mockup

This project generates SAIL expressions with hard-coded sample data that you can paste into Appian Interface Designer.

### Making Requests

You can be as vague or as specific as you'd like:

**Vague:**
> "Make a case management dashboard"

**Detailed:**
> "A large insurance provider seeks a case management UI for handling customer claims. The target personas are claims adjusters and supervisors who need to review case files, track progress, and approve settlements."

**Specific Layout Instructions:**
> "Create an alerts inbox page. Use a pane layout: a MEDIUM-width left pane for the list of alerts and an AUTO-width pane for viewing the selected alert."

### Testing and Iteration

1. Copy the generated expression from `/output` folder
2. Paste into Appian Interface Designer
3. If errors occur, paste the error message back into Kiro for automatic fixes
4. Request adjustments through chat

## Converting Mockup to Functional Interface

Once satisfied with your static mockup, convert it to use live Appian record data.

### Conversion Commands

> "Now, make this functional"

> "Convert the mock data to record queries"

> "Connect this to our Case record type"

### Pre-Requisite: Data Model Context

Edit `/context/data-model-context.md` with your data model details:
- Record type names and UUIDs
- Field names and UUIDs
- Relationships between record types
- Field data types

Try using [@tokyojordan's tool](https://github.com/tokyojordan/data-model-context) to generate the data model context from your app.

## Validation Approaches

### With VPN Connection (Recommended)
Uses the Appian MCP server configured in `.mcp.json` for fast, accurate SAIL syntax validation.

### Without VPN Connection
Kiro uses the validation steering files to check:
1. Schema validation — function syntax and parameters
2. Icon validation — valid icon names
3. Code review — structure and best practices

---
inclusion: auto
---

# BT Apps Visual Style Guide

All SAIL interfaces built for Business Technology (BT) applications must follow this style guide to ensure a consistent, modern, enterprise-grade look and feel.

## Color Palette

### Primary Colors
| Token | Hex | Usage |
|-------|-----|-------|
| Dark Blue | `#020A51` | Page titles, section headings, primary text, strong labels |
| Accent Blue | `#2322F0` | Primary buttons, links, active states, milestone indicators, decorative borders |
| Cloud Mist | `#E5EDF6` | Light backgrounds, secondary button fills, tag backgrounds, subtle highlights |
| Interface Gray | `#B3C3DA` | Borders, dividers, inactive elements |

### Semantic Colors
| Token | Hex | Usage |
|-------|-----|-------|
| Green | `#21E9BE` | Success states, positive indicators, checkmarks, existing-role banners |
| Green Light | `#DEFCF5` | Success banner backgrounds, positive card fills |
| Amber | `#FCB858` | Warning states, caution indicators, new-role banners |
| Amber Light | `#FFF5E6` | Warning banner backgrounds |
| Red/Orange | `#FF7F39` | Error states, destructive actions, required field indicators |
| Red Light | `#FFECE2` | Error banner backgrounds |
| Violet | `#9F38FA` | Accent for special categories (optional) |
| Cyan | `#2AE3FF` | Queued/pending status indicators |
| Cyan Light | `#DFFBFF` | Queued status tag backgrounds |

### Neutral Colors
| Token | Hex | Usage |
|-------|-----|-------|
| White | `#FFFFFF` | Card backgrounds, page content areas |
| Page Background | `#F5F6F8` | Page-level background (headerContentLayout backgroundColor) |
| Secondary Text | `#4F5C75` | Descriptions, helper text, secondary labels, muted content |

## Typography Hierarchy

| Element | Size | Style | Color |
|---------|------|-------|-------|
| Page title | `LARGE` | `STRONG` | `#020A51` |
| Page subtitle / tagline | `SMALL` + `STRONG` + uppercase | `STRONG` | `#4F5C75` |
| Section heading (in cards) | `MEDIUM_PLUS` via sectionLayout | `labelColor: "#020A51"` | `#020A51` |
| Card title | `MEDIUM` | `STRONG` | `#020A51` |
| Body text | `STANDARD` | `PLAIN` | `#020A51` |
| Secondary/helper text | `STANDARD` | `PLAIN` | `#4F5C75` |
| Small labels / metadata | `SMALL` | `PLAIN` or `STRONG` | `#4F5C75` |
| Summary grid labels | `STANDARD` | `STRONG` | `#4F5C75` |

## Component Styling

### Cards
- Background: `#FFFFFF`
- Shape: `SEMI_ROUNDED`
- Padding: `STANDARD` (content cards) or `MORE` (KPI/summary cards)
- Border: `showBorder: true` for content cards
- Shadow: `showShadow: false` (flat design, no drop shadows)
- Accent border: Use `borderColor: "#2322F0"` for highlighted/primary cards

### Buttons
| Type | Style | Color | Usage |
|------|-------|-------|-------|
| Primary action | `SOLID` | `#2322F0` | Submit, Continue, Generate, Approve |
| Secondary action | `OUTLINE` | `SECONDARY` | Back, Cancel, Skip |
| Tertiary / inline | `LINK` | `SECONDARY` or `#2322F0` | Toggle links, minor actions |
| Destructive | `LINK` | `NEGATIVE` | Delete (always with `confirmMessage`) |
| Navigation forward | Add `icon: "arrow-right"` + `iconPosition: "END"` | — | Next step buttons |
| Navigation back | Add `icon: "arrow-left"` | — | Previous step buttons |

### Tags
- Status/category tags: `backgroundColor: "#E5EDF6"`, `textColor: "#4F5C75"`
- Accent tags: `backgroundColor: "#2322F0"` (white text auto)
- Success tags: `backgroundColor: "#DEFCF5"`, `textColor: "#020A51"`
- Warning tags: `backgroundColor: "#FFF5E6"`, `textColor: "#020A51"`
- Queued/pending tags: `backgroundColor: "#DFFBFF"`, `textColor: "#020A51"`
- Size: `SMALL` for inline metadata, `STANDARD` for standalone

### Stamps
- Shape: `ROUNDED` for avatar/decorative circles
- Size: `TINY` for inline icons alongside text
- Use brand colors for backgrounds with `contentColor: "#FFFFFF"`

### Banners / Alert Cards
| Type | Background | Icon | Icon Color |
|------|-----------|------|------------|
| Success | `#DEFCF5` | `check-circle` | `#21E9BE` |
| Warning | `#FFF5E6` | `exclamation-triangle` | `#FCB858` |
| Error | `#FFECE2` | `exclamation-triangle` | `#FF7F39` |
| Info | `#E5EDF6` | `info-circle` | `#2322F0` |

Banner pattern: `a!cardLayout` with `style: [bg color]`, `shape: "SEMI_ROUNDED"`, `showBorder: false`, containing a `sideBySideLayout` with icon + text.

### Progress / Milestones
- Use `a!milestoneField` with `stepStyle: "DOT"` and `color: "#2322F0"`
- Place in page header area above content

### Editable Grids (List Editors)
- Use `a!gridLayout` for editable lists (responsibilities, qualifications, etc.)
- Columns: `#` (ICON width), content (DISTRIBUTE), actions (NARROW_PLUS)
- Content field: `a!paragraphField` with `height: "SHORT"` for wrapping long text
- Action buttons: up arrow, down arrow, trash — all horizontal in one `a!buttonArrayLayout`
- Delete buttons must have `confirmHeader` and `confirmMessage` for confirmation dialog
- Spacing: `STANDARD`
- Add row: use `addRowLink` with `"+ Add [item type]"` label

## Layout Patterns

### Page Structure
```
a!headerContentLayout
  header: card with title + subtitle + milestoneField
  contents: columnsLayout with single AUTO column
  backgroundColor: "#F5F6F8"
```

### Form Sections
- Wrap each logical section in `a!cardLayout` with `a!sectionLayout` inside
- Section label uses `labelSize: "MEDIUM_PLUS"`, `labelColor: "#020A51"`
- Two-column layouts for short fields (name + title, level + department)
- Full-width for long fields (descriptions, strategy text)

### Navigation Buttons
- Use `a!sideBySideLayout` with back button on left (`width: "MINIMIZE"`) and forward button on right (`align: "END"`)
- Or use single `a!buttonArrayLayout` with `align: "END"` when all buttons go right

### Conditional Banners
- Place above the content they relate to
- Use `showWhen` or `if()` for conditional display
- Always include an icon + text pattern via `sideBySideLayout`

## AI Chat Interfaces
- Use `a!chatField` with `height: "TALL"` or `"EXTRA_TALL"`, `shape: "SEMI_ROUNDED"`, `showBorder: true`
- Title: `sideBySideLayout` with a `TINY` `ROUNDED` stamp (icon: `comments`, bg: `#2322F0`) + bold title text
- Seed with an initial ASSISTANT greeting that includes known context
- System prompts must explicitly list allowed topics and forbidden topics
- Action buttons below chat aligned to the right (`align: "END"`)

## General Principles
- **Flat design**: No drop shadows on cards (`showShadow: false`)
- **Rounded corners**: Use `SEMI_ROUNDED` for cards, banners, and inputs
- **Whitespace**: Use `MORE` padding on summary/KPI cards, `STANDARD` on form cards
- **Consistency**: Same color for the same semantic meaning across all interfaces
- **Enterprise tone**: Clean, professional, no decorative flourishes — let the content breathe
- **Confirmation dialogs**: Always confirm destructive actions (delete, remove) with `confirmHeader` + `confirmMessage`
- **Loading indicators**: Use `loadingIndicator: true` on buttons that trigger LLM calls or long operations

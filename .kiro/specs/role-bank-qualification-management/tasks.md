# Implementation Plan: Role Bank & Qualification Management

## Overview

Build three SAIL mockup interfaces for the Role Bank & Qualification Management feature. Each `.sail` file uses `local!` variables with `a!map()` sample data — no server-side logic, no TypeScript, no external dependencies. The Qualification Bank is built inline within the wizard's Step 2.

## Tasks

- [x] 1. Build Dashboard / Role Bank Overview interface
  - [x] 1.1 Create `output/role-bank-dashboard.sail`
    - Define `local!roles` as a list of `a!map()` sample role objects covering all statuses (Draft, Pending Approval, Approved, On Hold)
    - Define `local!selectedStatus`, `local!searchText`, and filter locals (`local!filterStatus`, `local!filterDepartment`, `local!filterLocation`, `local!filterHiringManager`)
    - Implement `a!headerContentLayout` with navigation header
    - Build status cards section using `a!columnsLayout` with 4 clickable `a!cardLayout` cards showing counts per status
    - Build Quick Actions `a!buttonArrayLayout` with "Create New Role" (SOLID/ACCENT) and "View All Roles" (OUTLINE)
    - Build "My Pending Tasks" widget as `a!cardLayout` with filtered task list
    - Build Role Bank Table using `a!gridField` with columns: Role Title, Department, Hiring Manager, Status (tag), Openings, Filled, Pending Approval From, Date Created, Actions (Edit, View History, Duplicate, Archive)
    - Build search bar (`a!textField`) and filter dropdowns (`a!dropdownField`) with TODO-CONVERTER comments
    - Wire status card clicks to update `local!selectedStatus` and filter the grid
    - Wire search and filter inputs to filter the grid data using `a!queryFilter` patterns
    - _Requirements: 1.1, 1.2, 1.3, 1.4, 2.1, 2.2, 2.3, 2.4, 2.5, 2.6, 2.7_

- [x] 2. Build Create New Role Wizard interface
  - [x] 2.1 Create `output/role-wizard.sail` with Step 1 (Role Details)
    - Define all `local!` variables for wizard state: `local!currentStep`, `local!roleTitle`, `local!department`, `local!location`, `local!hiringManager`, `local!numberOfOpenings`, `local!employmentType`, `local!jobDescription`, `local!salaryRangeMin`, `local!salaryRangeMax`, `local!status`
    - Implement `a!wizardLayout` with 4 steps and built-in progress indicator
    - Build Step 1 form: `a!textField` (Role Title, required), `a!dropdownField` (Department, Location, Employment Type), `a!textField` (Hiring Manager), `a!integerField` (Number of Openings), `a!styledTextEditorField` (Job Description), `a!sideBySideLayout` (Salary Range Min/Max)
    - Add required field validation with inline error messages using `validations` parameter
    - Wire Save Draft button to set `local!status: "Draft"` with confirmation logic
    - Wire Cancel button with unsaved changes confirmation prompt
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5, 3.6_
  - [x] 2.2 Add Step 2 (Qualifications Management) with inline Qualification Bank
    - Define `local!minQualifications` and `local!preferredQualifications` as lists of `a!map()` qualification objects
    - Define `local!qualificationTemplates` as a list of `a!map()` predefined templates for the Qualification Bank
    - Define `local!showQualBank` toggle and `local!qualBankSearch` for bank filtering
    - Build Minimum Qualifications `a!sectionLayout` with `a!forEach` rendering each qualification: `a!dropdownField` (Type) + `a!textField` (Detail) + up/down/remove icon buttons
    - Build Preferred Qualifications `a!sectionLayout` with same pattern
    - Add "Add Qualification" `a!buttonWidget` per section that appends a new `a!map()` entry
    - Build inline Qualification Bank sidebar: `a!cardLayout` with `a!textField` search, `a!forEach` over filtered templates, each with Type tag + Detail + "Add" button
    - Add zero-minimum-qualifications warning using `showWhen` condition
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5, 4.6, 4.7, 4.8_
  - [x] 2.3 Add Step 3 (Approval Workflow) to wizard
    - Define `local!useDefaultChain` toggle and `local!approvers` as list of `a!map()` approver objects
    - Define `local!defaultApprovers` as a predefined approver sequence
    - Build `a!radioButtonField` toggle for Default Chain vs Custom Chain
    - Build custom chain editor with `a!forEach` over `local!approvers`: order number display + `a!textField` (user picker pattern) + remove `a!buttonWidget`
    - Add "Add Approver" button that appends with auto-incremented order
    - Build visual chain representation using `a!richTextDisplayField` with `a!richTextIcon` stamps and arrow connectors
    - Wire default chain toggle to populate `local!approvers` from `local!defaultApprovers`
    - _Requirements: 5.1, 5.2, 5.3, 5.4, 5.5, 5.6_
  - [x] 2.4 Add Step 4 (Review & Submit) to wizard
    - Build read-only summary `a!sectionLayout` blocks for Role Details, Qualifications, and Approval Chain using `a!textField(readOnly: true)` and `a!richTextDisplayField`
    - Wire Submit button to set `local!status: "Pending Approval"`
    - Wire Previous button to navigate back via `local!currentStep` while preserving all data
    - _Requirements: 6.1, 6.2, 6.3, 6.4_

- [ ] 3. Build Role Detail View interface
  - [ ] 3.1 Create `output/role-detail-view.sail`
    - Define `local!role` as a fully populated `a!map()` role object with nested qualifications, approval chain, and activity log
    - Define `local!activeTab` for tab navigation
    - Implement `a!headerContentLayout` with role title and color-coded status `a!tagField` in header
    - Build context-sensitive action buttons using `showWhen` based on `local!role.status` (Edit for Draft, Approve/Reject for Pending)
    - Build Key Metrics card using `a!columnsLayout` with 3 columns: Openings, Filled, Remaining (computed as openings - filled)
    - Build tabbed content area using tab pattern with `local!activeTab`:
      - Overview tab: read-only role details (department, location, hiring manager, employment type, salary range, job description)
      - Qualifications tab: Minimum and Preferred qualification lists with type `a!tagField` badges
      - Approval Status tab: vertical timeline using `a!forEach` over approval chain with stamp icons, name, status tag, date, comments
    - Build Activity Log section using `a!forEach` with timestamp + action description entries
    - _Requirements: 8.1, 8.2, 8.3, 8.4, 8.5_

- [ ] 4. Final checkpoint - Review all SAIL interfaces
  - Verify all three SAIL files exist in `/output` and use valid SAIL syntax
  - Verify all `local!` variables use `a!map()` sample data with no `ri!` or `recordType!` references
  - Verify all requirements (1–11) are covered by at least one interface
  - Ask the user if questions arise

## Notes

- All SAIL files use `local!` variables with `a!map()` sample data — no server-side references
- The Qualification Bank is built inline within the wizard's Step 2, not as a separate file
- Each task references specific requirements for traceability
- SAIL interfaces follow the project's established mockup patterns

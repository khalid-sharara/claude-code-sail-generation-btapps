# Requirements Document

## Introduction

This document defines the requirements for the Role Bank & Qualification Management feature within an Early Stage Recruiting system. The feature provides HR Admins with an interface to define, manage, and track job roles through their lifecycle — from draft creation through qualification definition, approval workflow, and ongoing status tracking.

## Glossary

- **Role_Bank**: The centralized repository of all job roles in the recruiting system
- **Role**: A job position definition containing details, qualifications, and approval status
- **Qualification**: A requirement (experience, education, skill, certification, or other) associated with a role, categorized as minimum or preferred
- **Qualification_Bank**: A predefined library of reusable qualification templates
- **Approval_Chain**: An ordered sequence of approvers who must review and approve a role before it becomes active
- **Approver**: A user designated to review and approve or reject a role submission
- **Role_Wizard**: The multi-step form used to create or edit a role (Details → Qualifications → Approval → Review)
- **HR_Admin**: The primary user who creates, edits, and manages roles
- **Hiring_Manager**: The person responsible for a specific role's hiring process
- **Dashboard**: The main overview page displaying role status summaries, pending tasks, and the role bank table
- **Activity_Log**: A chronological record of all actions taken on a role

## Requirements

### Requirement 1: Dashboard Role Status Overview

**User Story:** As an HR_Admin, I want to see a summary of all roles grouped by status on the dashboard, so that I can quickly understand the current state of the role pipeline.

#### Acceptance Criteria

1. WHEN the HR_Admin navigates to the Dashboard, THE Dashboard SHALL display status cards showing counts for Draft, Pending Approval, Approved, and On Hold roles
2. WHEN a role's status changes, THE Dashboard SHALL update the corresponding status card count to reflect the current totals
3. THE Dashboard SHALL display a "My Pending Tasks" widget listing roles that require action from the logged-in HR_Admin
4. WHEN the HR_Admin clicks a status card, THE Dashboard SHALL filter the Role_Bank table to show only roles matching that status

### Requirement 2: Role Bank Table Display and Interaction

**User Story:** As an HR_Admin, I want to view all roles in a searchable, filterable table, so that I can find and manage specific roles efficiently.

#### Acceptance Criteria

1. THE Role_Bank table SHALL display columns for Role Title, Department, Hiring Manager, Status, Openings, Filled, Pending Approval From, Date Created, and Actions
2. WHEN the HR_Admin types in the global search bar, THE Role_Bank table SHALL filter roles whose title, department, or hiring manager name contains the search text
3. WHEN the HR_Admin applies filters for Role Status, Department, Location, or Hiring Manager, THE Role_Bank table SHALL display only roles matching all selected filter criteria
4. WHEN multiple filters are applied simultaneously, THE Role_Bank table SHALL apply all filters using AND logic, showing only roles that satisfy every active filter
5. THE Role_Bank table SHALL provide action buttons for Edit, View History, Duplicate, and Archive on each role row
6. WHEN the HR_Admin clicks Duplicate on a role, THE System SHALL create a new role in Draft status pre-populated with the original role's details, qualifications, and approval chain
7. WHEN the HR_Admin clicks Archive on a role, THE System SHALL prompt for confirmation before moving the role to an archived state

### Requirement 3: Create New Role — Role Details (Step 1)

**User Story:** As an HR_Admin, I want to enter core details for a new role through a guided wizard, so that I can define the position accurately.

#### Acceptance Criteria

1. WHEN the HR_Admin clicks "Create New Role," THE Role_Wizard SHALL open at Step 1 displaying fields for Role Title, Department, Location, Hiring Manager, Number of Openings, Employment Type, Job Description (rich text), and Salary Range
2. WHEN the HR_Admin leaves a required field empty and attempts to proceed, THE Role_Wizard SHALL display an inline validation error on the empty field and prevent navigation to the next step
3. THE Role_Wizard SHALL display a progress indicator showing the current step and total steps (4 steps)
4. WHEN the HR_Admin clicks "Save Draft" at any step, THE System SHALL persist the current role data with a Draft status and return the HR_Admin to the Dashboard
5. WHEN the HR_Admin clicks "Cancel," THE System SHALL prompt for confirmation if unsaved changes exist, then discard changes and return to the Dashboard
6. WHEN the HR_Admin clicks "Next" with all required fields valid, THE Role_Wizard SHALL navigate to Step 2 (Qualifications)

### Requirement 4: Qualifications Management (Step 2)

**User Story:** As an HR_Admin, I want to define minimum and preferred qualifications for a role, so that candidates can be evaluated against clear criteria.

#### Acceptance Criteria

1. THE Role_Wizard Step 2 SHALL display separate sections for Minimum Qualifications and Preferred Qualifications
2. WHEN the HR_Admin clicks "Add Qualification" in either section, THE System SHALL add a new qualification entry with fields for Type (Experience, Education, Skills, Certification, Other) and Detail text
3. WHEN the HR_Admin drags a qualification to a new position within its section, THE System SHALL reorder the qualifications and persist the new order
4. WHEN the HR_Admin edits a qualification's Type or Detail inline, THE System SHALL update the qualification immediately without requiring a separate save action
5. WHEN the HR_Admin opens the Qualification_Bank, THE System SHALL display a searchable list of predefined qualification templates
6. WHEN the HR_Admin selects a template from the Qualification_Bank, THE System SHALL add a copy of that qualification to the current section with the template's Type and Detail pre-filled
7. WHEN the HR_Admin removes a qualification, THE System SHALL prompt for confirmation before deleting the entry
8. IF the HR_Admin attempts to proceed to Step 3 with zero minimum qualifications, THEN THE Role_Wizard SHALL display a warning indicating that at least one minimum qualification is recommended

### Requirement 5: Approval Workflow Configuration (Step 3)

**User Story:** As an HR_Admin, I want to configure an approval chain for a role, so that the appropriate stakeholders review and approve the role before it goes live.

#### Acceptance Criteria

1. THE Role_Wizard Step 3 SHALL provide a toggle to use the default approval chain or configure a custom chain
2. WHEN the HR_Admin selects the default approval chain, THE System SHALL populate the approval chain with the organization's predefined approver sequence
3. WHEN the HR_Admin adds an approver to a custom chain, THE System SHALL display a searchable user select and assign the next sequential order number automatically
4. WHEN the HR_Admin removes an approver from the chain, THE System SHALL re-sequence the remaining approvers to maintain consecutive order numbers
5. THE Role_Wizard Step 3 SHALL display a visual representation of the approval chain showing approver avatars connected by directional arrows in sequence order
6. WHEN the HR_Admin reorders approvers in the custom chain, THE System SHALL update the visual representation and order numbers to reflect the new sequence

### Requirement 6: Review and Submit (Step 4)

**User Story:** As an HR_Admin, I want to review all role details before submission, so that I can verify accuracy and completeness.

#### Acceptance Criteria

1. THE Role_Wizard Step 4 SHALL display a read-only summary of all data entered in Steps 1 through 3, including role details, qualifications, and the approval chain
2. WHEN the HR_Admin clicks "Submit," THE System SHALL change the role status from Draft to Pending Approval and notify the first approver in the chain
3. WHEN the HR_Admin identifies an error during review, THE Role_Wizard SHALL allow navigation back to any previous step using the progress indicator or "Previous" button
4. WHEN the HR_Admin navigates back to a previous step and makes changes, THE Role_Wizard SHALL preserve all data entered in subsequent steps

### Requirement 7: Approval Processing

**User Story:** As an Approver, I want to review, approve, or reject roles assigned to me, so that the hiring process can proceed with proper authorization.

#### Acceptance Criteria

1. WHEN a role is submitted for approval, THE System SHALL notify the first Approver in the chain and set the role status to Pending Approval
2. WHEN an Approver approves a role, THE System SHALL record the approval with timestamp and optional comments, then advance the role to the next Approver in the chain
3. WHEN the final Approver in the chain approves a role, THE System SHALL change the role status to Approved
4. WHEN an Approver rejects a role, THE System SHALL change the role status to Draft, record the rejection with timestamp and comments, and notify the HR_Admin
5. WHEN an Approver adds comments during approval or rejection, THE System SHALL store the comments and display them in the approval history
6. THE System SHALL display an approval progress bar or timeline showing which approvers have acted and which are pending

### Requirement 8: Role Detail View

**User Story:** As an HR_Admin, I want to view complete details of a role after creation, so that I can monitor its progress and take appropriate actions.

#### Acceptance Criteria

1. THE Role Detail View SHALL display a header with the role title, a color-coded status chip, and context-sensitive action buttons based on the current role status
2. THE Role Detail View SHALL display a key metrics card showing Openings, Filled, and Remaining counts
3. THE Role Detail View SHALL organize content into collapsible or tabbed sections for Overview, Qualifications, and Approval Status/History
4. WHEN viewing the Approval Status section, THE Role Detail View SHALL display a timeline or flowchart showing each approver's status, action date, and comments
5. THE Role Detail View SHALL include an Activity_Log section displaying a chronological list of all actions taken on the role

### Requirement 9: Role Data Persistence and Serialization

**User Story:** As a developer, I want role data to be reliably persisted and retrievable, so that no data is lost during the role lifecycle.

#### Acceptance Criteria

1. WHEN a role is saved (draft or submitted), THE System SHALL serialize the complete role object including details, qualifications, and approval chain into a storable format
2. WHEN a role is loaded for viewing or editing, THE System SHALL deserialize the stored data and reconstruct the complete role object with all associated qualifications and approval chain intact
3. FOR ALL valid Role objects, serializing then deserializing SHALL produce an equivalent Role object (round-trip property)

### Requirement 10: Role Duplication Integrity

**User Story:** As an HR_Admin, I want duplicated roles to be independent copies, so that editing a duplicate does not affect the original role.

#### Acceptance Criteria

1. WHEN a role is duplicated, THE System SHALL create a deep copy of the role's details, qualifications, and approval chain as a new Draft role
2. WHEN the HR_Admin edits a duplicated role, THE System SHALL leave the original role unchanged
3. THE System SHALL assign a new unique identifier to the duplicated role distinct from the original

### Requirement 11: Qualification Reordering Consistency

**User Story:** As an HR_Admin, I want qualification ordering to be preserved accurately, so that the priority of qualifications is maintained as I arrange them.

#### Acceptance Criteria

1. WHEN qualifications are reordered via drag-and-drop, THE System SHALL update the order indices so that each qualification has a unique, consecutive index starting from 1
2. FOR ALL reorder operations on a qualification list, the total number of qualifications SHALL remain unchanged after reordering
3. WHEN a qualification is moved from position A to position B, THE System SHALL place it at position B and shift other qualifications accordingly without duplicating or losing any entry

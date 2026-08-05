# Mortasheen Monster Builder

## UI, Export & Development Workflow Specification

Version 1.0

> Canonical specification defining user interaction, export behavior and development workflow for the Mortasheen Monster Builder.

This document defines how users interact with the application, how project data is exchanged with external systems, and how developers should organize, implement and maintain the project.

This specification complements, but does not replace:

- Master Software Design Specification
- Data Model Specification
- Rule Engine Specification
- Rules Asset Specification
- Validation and Testing Specification

---

# Part I — User Interface

---

## Chapter 1 — Purpose, Scope and Philosophy

### 1.1 Purpose

This chapter defines the guiding principles governing the application's user interface.

The user interface SHALL expose the functionality defined by the project specifications without altering application behavior.

The user interface SHALL remain independent from the Rule Engine, Validation Engine and persistence implementation.

---

### 1.2 Scope

This chapter governs:

- interaction philosophy;
- editor behavior;
- workflow consistency;
- usability principles.

This chapter SHALL NOT define visual appearance.

---

### 1.3 Design Philosophy

The Monster Builder SHALL prioritize:

- clarity;
- predictability;
- discoverability;
- consistency;
- efficiency.

User workflows SHOULD minimize unnecessary interaction while preserving explicit user control.

---

### 1.4 Separation of Responsibilities

The user interface SHALL:

- present information;
- collect user input;
- visualize Validation Reports;
- invoke application services.

The user interface SHALL NOT:

- perform gameplay calculations;
- implement validation logic;
- modify Rule Assets.

---

### 1.5 UI Actions

A **UI Action** is an atomic user interaction initiated by the user.

Examples include:

- Create Monster
- Save
- Undo
- Redo
- Add Ability Block
- Remove Part
- Search
- Export

Menus, buttons, keyboard shortcuts and context menus SHALL invoke UI Actions rather than directly modifying application state.

---

### 1.6 Workflow First

The interface SHALL be organized around user workflows rather than internal implementation details.

Users SHOULD interact with the application through logical construction steps instead of manipulating raw data structures.

---

### 1.7 Deterministic Interaction

Identical UI Actions performed under identical conditions SHALL produce identical observable results.

---

### 1.8 Outputs

This chapter defines the interaction philosophy governing every subsequent chapter.

---

## Chapter 2 — Application Layout

### 2.1 Purpose

This chapter defines the logical organization of the application interface.

---

### 2.2 Workspace

A **Workspace** represents the complete interactive environment presented to the user.

A Workspace consists of:

- interface layout;
- visible interface areas;
- UI State;
- active Monster Document.

The Workspace SHALL remain independent from Monster Document data.

---

### 2.3 Layout Philosophy

The Workspace SHALL consist of logical functional areas rather than fixed windows.

Implementations MAY rearrange visual layout provided workflow remains unchanged.

---

### 2.4 Primary Interface Areas

The Workspace SHOULD provide dedicated areas for:

- Monster Overview;
- Construction Editor;
- Rule Browser;
- Properties;
- Validation;
- Inspector;
- Search.

---

### 2.5 UI State

**UI State** represents temporary interface information.

Examples include:

- selected object;
- expanded panels;
- search text;
- active tab;
- panel visibility.

UI State SHALL remain separate from Monster Documents.

Saving a Monster Document SHALL NOT persist UI State unless explicitly configured by the user.

---

### 2.6 Navigation

Navigation SHALL remain consistent throughout the application.

Users SHALL always know:

- current Monster Document;
- active Rule Pack;
- validation status;
- Dirty State.

---

### 2.7 Context Awareness

Displayed information SHOULD adapt to the current context.

Context-sensitive presentation SHALL NEVER perform implicit modifications to Monster Documents.

Viewing information SHALL remain distinct from editing information.

---

### 2.8 Dirty State

The interface SHALL distinguish between:

- saved documents;
- modified documents.

Dirty State SHALL be visible to the user whenever unsaved changes exist.

---

### 2.9 Status Information

The Workspace SHOULD continuously display:

- active Rule Pack;
- validation summary;
- document state;
- application status.

---

### 2.10 Outputs

This chapter defines the logical Workspace used by every editing workflow.

---

## Chapter 3 — Monster Editor Workflow

### 3.1 Purpose

This chapter defines the standard workflow for constructing and modifying Monster Documents.

---

### 3.2 Workflow Overview

The recommended workflow is:

```
Create Monster
        │
        ▼
Select Rule Pack
        │
        ▼
Select Monster Class
        │
        ▼
Select Size
        │
        ▼
Configure Attributes
        │
        ▼
Add Parts
        │
        ▼
Assign Ability Blocks
        │
        ▼
Resolve Diagnostics
        │
        ▼
Repeat Editing
        │
        ▼
Save
        │
        ▼
Export
```

Applications MAY provide alternative navigation while preserving the logical workflow.

---

### 3.3 Editing Principles

Editing SHALL:

- preserve determinism;
- preserve document consistency;
- support incremental validation;
- minimize unnecessary interruption.

---

### 3.4 Selection

**Selection** identifies the object currently being edited or inspected.

Selection MAY include:

- Monster Document;
- Part;
- Ability Block;
- Descriptor;
- Rule Asset;
- Diagnostic.

Only one primary Selection SHOULD exist at any time.

---

### 3.5 Undo and Redo

All UI Actions modifying Monster Documents SHOULD support:

- Undo;
- Redo.

Undo SHALL restore complete document state.

Redo SHALL restore the immediately reversed operation.

---

### 3.6 Incremental Editing

Following each UI Action the application SHOULD update:

- derived values;
- Validation Reports;
- dependent interface elements.

Only affected subsystems SHOULD be refreshed whenever practical.

---

### 3.7 Saving

Saving SHALL preserve:

- Monster Document;
- Build Context;
- document metadata.

Saving SHALL NOT modify:

- Rule Assets;
- Validation Rules;
- UI State.

---

### 3.8 Outputs

The Monster Editor SHALL produce Monster Documents suitable for validation, persistence and export.

---

## Chapter 4 — Rule Browsing

### 4.1 Purpose

This chapter defines how users browse and inspect Rule Assets.

---

### 4.2 Scope

Users SHALL be able to browse:

- Monster Classes;
- Sizes;
- Ability Blocks;
- Descriptors;
- Damage Types;
- Conditions;
- Movement Types;
- Core Features;
- Weaknesses.

---

### 4.3 Search

Search SHOULD support:

- names;
- identifiers;
- keywords;
- categories.

Search SHALL remain deterministic.

---

### 4.4 Filtering

Filtering MAY include:

- Rule Pack;
- category;
- construction relevance;
- availability.

Filtering SHALL NOT modify Rule Assets.

---

### 4.5 Inspection

Users SHOULD be able to inspect:

- descriptions;
- relationships;
- dependencies;
- applicable rules.

Inspection SHALL remain read-only.

---

### 4.6 Navigation History

Rule Browsing SHOULD preserve browsing history.

Users SHOULD be able to navigate:

- backward;
- forward;
- recently viewed Rule Assets.

---

### 4.7 Cross References

The interface SHOULD allow direct navigation between related Rule Assets.

Cross References SHALL follow stable Rule Asset identifiers.

---

### 4.8 Outputs

Rule Browsing SHALL facilitate discovery without exposing implementation details.

---

## Chapter 5 — Validation User Experience

### 5.1 Purpose

This chapter defines how Validation Reports are presented to users.

---

### 5.2 Scope

This chapter governs:

- Diagnostics;
- Validation Reports;
- navigation;
- filtering.

---

### 5.3 Presentation

Diagnostics SHOULD display:

- severity;
- title;
- explanation;
- affected object;
- suggested correction.

---

### 5.4 Navigation

Users SHOULD be able to navigate directly from a Diagnostic to the affected object.

The interface SHOULD support:

- Next Diagnostic;
- Previous Diagnostic;
- navigation by Validation Category.

---

### 5.5 Grouping

Diagnostics MAY be grouped by:

- severity;
- Validation Category;
- affected subsystem;
- affected object.

---

### 5.6 Filtering

Users MAY filter Diagnostics.

Filtering SHALL NOT modify Validation Reports.

---

### 5.7 Live Validation

Validation SHOULD update automatically following UI Actions affecting Monster Documents.

Implementations MAY delay updates briefly to preserve responsiveness.

---

### 5.8 Outputs

Validation User Experience SHALL emphasize:

- clarity;
- traceability;
- efficient navigation;
- efficient problem resolution.

---

## Chapter 6 — Accessibility

### 6.1 Purpose

This chapter defines accessibility requirements for the application.

Accessibility SHALL be considered a core requirement rather than an optional enhancement.

---

### 6.2 General Principles

Accessibility features SHALL preserve application behavior while improving usability.

---

### 6.3 Keyboard Accessibility

The application SHOULD support complete operation using the keyboard.

---

### 6.4 Visual Accessibility

Implementations SHOULD support:

- scalable interface;
- scalable fonts;
- high-contrast themes;
- color-independent status indicators;
- configurable font scaling;
- reduced motion where practical;
- visible focus indication.

---

### 6.5 Interaction Accessibility

Implementations SHOULD support:

- large interaction targets where practical;
- consistent focus traversal;
- configurable interaction speed where supported.

---

### 6.6 Screen Readers

Where supported by the platform, interface elements SHOULD expose accessible names and descriptions.

---

### 6.7 Localization

Applications SHOULD support localization of:

- interface text;
- Validation Reports;
- documentation links.

Rule Asset identifiers SHALL remain language-independent.

---

### 6.8 User Preferences

Persistent user preferences MAY include:

- Workspace layout;
- accessibility settings;
- recent Rule Packs;
- editor preferences.

Temporary session preferences MAY include:

- active search text;
- current Selection;
- expanded panels;
- current navigation history.

---

### 6.9 Outputs

Accessibility SHALL improve usability without modifying application behavior.

# Part II — Export & Import

---

## Chapter 7 — Export Philosophy

### 7.1 Purpose

This chapter defines the principles governing the export of project data.

Export SHALL produce external representations of project information without modifying the underlying project state.

---

### 7.2 Scope

This chapter governs:

- export philosophy;
- Export Profiles;
- document lifecycle;
- export workflows;
- export consistency.

This chapter SHALL NOT define specific file formats.

---

### 7.3 Document Lifecycle

A **Monster Document** progresses through the following conceptual lifecycle:

```
Create
    │
    ▼
Edit
    │
    ▼
Validate
    │
    ▼
Save
    │
    ▼
Export
    │
    ▼
Import
    │
    ▼
Archive
```

Applications MAY provide additional lifecycle stages provided the existing semantics remain unchanged.

---

### 7.4 Export Principles

Export SHALL prioritize:

- determinism;
- reproducibility;
- interoperability;
- traceability;
- readability where practical.

Export SHALL preserve semantic meaning regardless of representation.

---

### 7.5 Export Profiles

An **Export Profile** defines a complete export configuration.

An Export Profile specifies:

- exported content;
- presentation strategy;
- output format;
- intended audience;
- included metadata;
- formatting options.

Multiple Export Profiles MAY exist for the same Monster Document.

---

### 7.6 Content and Presentation

Export SHALL distinguish between:

**Content**

The project information being exported.

**Presentation**

The visual or textual representation of that information.

Equivalent content MAY be presented using multiple Presentation Profiles without altering semantic meaning.

---

### 7.7 Export Workflow

The recommended workflow is:

```
Select Monster Document
        │
        ▼
Select Export Profile
        │
        ▼
Configure Export Options
        │
        ▼
Generate Output
        │
        ▼
Validate Output
        │
        ▼
Save or Share
```

Applications MAY simplify this workflow provided equivalent exported information is produced.

---

### 7.8 Round-Trip Guarantee

Where supported by the selected Export Profile:

```
Monster Document
        │
        ▼
Export
        │
        ▼
Import
        │
        ▼
Equivalent Monster Document
```

Round-trip operations SHOULD preserve semantic information.

---

### 7.9 Deterministic Export

Equivalent Monster Documents exported using identical Export Profiles SHALL produce semantically equivalent exported data.

Byte-equivalent output SHOULD be preserved whenever practical.

Platform-specific differences SHOULD be minimized.

---

### 7.10 Outputs

This chapter establishes the principles governing every export operation.

---

## Chapter 8 — Supported Export Formats

### 8.1 Purpose

This chapter defines the categories of export supported by the project.

---

### 8.2 Scope

The application SHOULD support exporting project data into one or more external representations.

Supported representations MAY evolve independently of application architecture.

---

### 8.3 Structured Formats

Structured formats SHOULD support:

- Monster Documents;
- Rule Packs;
- Validation Reports;
- Build Context.

Structured formats SHOULD prioritize machine readability.

---

### 8.4 Human-Readable Formats

Human-readable exports SHOULD emphasize:

- readability;
- presentation;
- documentation;
- sharing.

Human-readable representations SHALL preserve semantic meaning.

---

### 8.5 Visual Formats

Visual exports MAY include:

- printable layouts;
- reference cards;
- presentation assets;
- images.

Visual appearance SHALL NOT modify exported content.

---

### 8.6 Clipboard Export

The application MAY support exporting selected information directly to the system clipboard.

Where supported by the platform, clipboard export SHOULD preserve semantic richness using structured or rich-text representations.

---

### 8.7 Export Metadata

Exported data SHOULD identify:

- Rule Pack;
- document version;
- Export Profile;
- Export Profile version;
- export timestamp where applicable;
- application version where practical.

---

### 8.8 Outputs

Supported export formats SHALL provide consistent representations of project information.

---

## Chapter 9 — Import Workflow

### 9.1 Purpose

This chapter defines how external project data is imported into the application.

---

### 9.2 Scope

Import SHALL support:

- Monster Documents;
- Rule Packs;
- exported project information.

Import SHALL preserve project integrity.

---

### 9.3 Import Profiles

An **Import Profile** defines how external information is interpreted.

An Import Profile specifies:

- accepted formats;
- migration strategy;
- conflict policy;
- validation behavior;
- supported features.

Multiple Import Profiles MAY exist.

---

### 9.4 Import Principles

Import SHALL prioritize:

- validation;
- compatibility;
- traceability;
- deterministic reconstruction.

Imported data SHALL remain subject to Validation Rules.

---

### 9.5 Import Workflow

The recommended workflow is:

```
Select Source
        │
        ▼
Identify Format
        │
        ▼
Load Data
        │
        ▼
Preview
        │
        ▼
Validate
        │
        ▼
Resolve Issues
        │
        ▼
Import
```

Applications MAY automate selected workflow stages.

---

### 9.6 Import Compatibility

Import SHALL determine:

- document version;
- Rule Pack compatibility;
- migration requirements;
- unsupported features.

Unsupported information SHALL be reported explicitly.

---

### 9.7 Imported Session

Imported information MAY be:

- opened for editing;
- previewed;
- compared;
- inspected without becoming the active Monster Document.

Implementations SHOULD distinguish imported data from the active editing session.

---

### 9.8 Conflict Policies

Where imported information conflicts with existing project data, implementations SHOULD support one or more conceptual policies:

- Replace
- Merge
- Duplicate
- Cancel

Conflict resolution SHALL avoid silent data loss.

---

### 9.9 Imported State

Successful import SHALL reconstruct:

- Monster Document;
- Build Context;
- metadata;
- Validation Report where appropriate.

UI State SHALL NOT be imported.

---

### 9.10 Outputs

Import SHALL produce validated Monster Documents suitable for editing.

---

## Chapter 10 — Printing and Presentation

### 10.1 Purpose

This chapter defines requirements governing printed and presentation-oriented output.

---

### 10.2 Scope

Presentation output includes:

- printable reference sheets;
- Monster summaries;
- presentation layouts;
- presentation images.

---

### 10.3 Presentation Principles

Presentation SHALL prioritize:

- readability;
- clarity;
- compactness;
- consistent organization.

Presentation SHALL remain independent from editing workflows.

---

### 10.4 Presentation Profiles

A **Presentation Profile** is a specialized Export Profile intended for human consumption.

Presentation Profiles MAY define:

- layout;
- typography;
- level of detail;
- target audience.

Presentation Profiles SHALL NOT modify project data.

---

### 10.5 Print Layout

Printable output SHOULD:

- preserve logical organization;
- maintain consistent typography;
- preserve logical pagination;
- avoid unnecessary page breaks;
- remain readable in monochrome where practical.

Logical sections SHOULD NOT be split across pages unnecessarily.

---

### 10.6 Validation Presentation

Printed and exported Validation Reports SHOULD preserve:

- Diagnostic ordering;
- severity;
- affected objects;
- suggested corrections.

---

### 10.7 Reproducibility

Repeated generation using identical Presentation Profiles SHALL produce semantically equivalent presentation output.

---

### 10.8 Outputs

Presentation output SHALL provide accurate representations suitable for printing, sharing and long-term reference.

# Part III — Development Workflow

---

## Chapter 11 — Repository Structure

### 11.1 Purpose

This chapter defines the logical organization of the project repository.

The repository SHALL reflect the architectural separation established by the project specifications.

---

### 11.2 Scope

This chapter governs:

- repository organization;
- Project Modules;
- documentation;
- assets;
- generated artifacts.

---

### 11.3 Project Modules

A **Project Module** is a logical subsystem representing one architectural responsibility.

Examples include:

- User Interface;
- Rule Engine;
- Validation Engine;
- Rules Assets;
- Export & Import;
- Testing.

Every Project Module SHALL possess a clearly defined responsibility.

Project Modules SHALL remain independent from physical folder layout.

---

### 11.4 Logical vs Physical Organization

The project distinguishes between:

**Logical Organization**

The architectural separation defined by the project specifications.

**Physical Organization**

The directory structure used by the implementation.

The specification defines logical organization only.

Implementations MAY organize files differently provided logical module boundaries remain preserved.

---

### 11.5 Repository Principles

The repository SHALL prioritize:

- clarity;
- modularity;
- traceability;
- reproducibility.

Repository organization SHALL reflect software architecture rather than implementation convenience.

---

### 11.6 Repository Areas

The repository SHOULD distinguish between:

- application source code;
- Rule Assets;
- documentation;
- automated tests;
- fixtures;
- generated artifacts;
- tooling.

Each area SHALL possess one clearly defined responsibility.

---

### 11.7 Generated Artifacts

Generated artifacts SHALL remain distinct from manually maintained project files.

Generated content SHOULD be reproducible.

Generated artifacts SHALL NOT become authoritative project data.

---

### 11.8 Module Ownership

Every Project Module SHALL identify:

- authoritative specification;
- implementation;
- associated tests;
- responsible documentation.

No implementation SHALL become the authoritative definition of project behavior.

---

### 11.9 Documentation Organization

Project specifications SHALL remain version-controlled alongside implementation.

Documentation SHALL evolve together with implementation.

---

### 11.10 Outputs

The repository SHALL provide a deterministic organizational structure supporting long-term maintenance.

---

## Chapter 12 — Coding Standards

### 12.1 Purpose

This chapter defines project-wide coding conventions.

---

### 12.2 Scope

Coding standards govern:

- source code;
- identifiers;
- documentation;
- formatting;
- interfaces.

---

### 12.3 General Principles

Source code SHOULD prioritize:

- readability;
- maintainability;
- explicitness;
- deterministic behavior.

Implementation SHALL reflect project architecture.

---

### 12.4 Architecture Enforcement

Implementation SHALL preserve:

- Project Module boundaries;
- documented interfaces;
- architectural responsibilities.

Architectural shortcuts SHALL NOT become permanent implementation.

---

### 12.5 Naming

Identifiers SHOULD be:

- descriptive;
- consistent;
- stable.

Project terminology SHALL remain consistent with the specifications.

---

### 12.6 Public and Internal Interfaces

The implementation distinguishes between:

**Public Interfaces**

Stable interfaces intended for use by other Project Modules.

**Internal Interfaces**

Implementation details whose behavior MAY evolve without affecting other Project Modules.

Internal implementation SHALL remain encapsulated whenever practical.

---

### 12.7 Documentation

Public interfaces SHOULD include documentation describing:

- purpose;
- inputs;
- outputs;
- side effects;
- assumptions.

Documentation SHALL describe intent rather than implementation mechanics.

---

### 12.8 Responsibility

Each implementation unit SHOULD possess one clearly defined responsibility.

Implementation SHALL minimize duplication.

---

### 12.9 Refactoring

Refactoring SHALL preserve observable project behavior unless explicitly documented otherwise.

Refactoring SHOULD improve maintainability while preserving architectural intent.

---

### 12.10 Outputs

Coding standards SHALL promote consistent implementation across the project.

---

## Chapter 13 — Branching and Versioning

### 13.1 Purpose

This chapter defines versioning principles governing project development.

---

### 13.2 Scope

This chapter governs:

- software versions;
- Rule Pack versions;
- document versions;
- development branches.

---

### 13.3 Versioning Principles

Version identifiers SHALL uniquely identify released project states.

Versioning SHOULD preserve compatibility information.

---

### 13.4 Semantic Versioning

Software releases SHOULD follow semantic versioning principles.

Rule Packs MAY evolve independently from application versions.

---

### 13.5 Development Branches

Development MAY occur using multiple branches.

Every branch SHOULD possess a clearly defined purpose.

Long-lived branches SHOULD remain synchronized with project architecture.

---

### 13.6 Release Branches

Release branches SHOULD prioritize:

- stability;
- regression verification;
- documentation completeness.

---

### 13.7 Outputs

Versioning SHALL provide traceable project evolution.

---

## Chapter 14 — Documentation Workflow

### 14.1 Purpose

This chapter defines how project documentation evolves alongside implementation.

---

### 14.2 Scope

Documentation workflow governs:

- specifications;
- implementation notes;
- generated documentation;
- revision history.

---

### 14.3 Documentation Principles

Documentation SHALL remain authoritative.

Implementation SHALL conform to approved specifications.

Documentation SHALL NOT knowingly contradict implementation.

---

### 14.4 Documentation Lifecycle

The recommended workflow is:

```
Architecture
        │
        ▼
Specification
        │
        ▼
Implementation
        │
        ▼
Validation
        │
        ▼
Documentation Update
        │
        ▼
Specification Review
        │
        ▼
Approval
```

---

### 14.5 Cross References

Specifications SHOULD reference related specifications rather than duplicate information.

Terminology SHALL remain consistent across all project documents.

---

### 14.6 Revision History

Documentation revisions SHOULD identify:

- affected sections;
- rationale;
- version;
- review status.

---

### 14.7 Authoritative Sources

Each project concept SHALL possess exactly one authoritative specification.

Supporting documents SHALL reference that specification rather than redefine it.

---

### 14.8 Outputs

Documentation workflow SHALL preserve consistency between specifications and implementation.

---

## Chapter 15 — Implementation Workflow

### 15.1 Purpose

This chapter defines the recommended workflow for implementing new functionality.

---

### 15.2 Scope

Implementation workflow governs:

- new features;
- architectural changes;
- Rule Assets;
- Validation Rules;
- Export Profiles.

---

### 15.3 Development Tasks

A **Development Task** is the smallest independently implementable unit of project work.

Examples include:

- implementing a Rule Asset type;
- implementing a Validation Rule family;
- adding an Export Profile;
- implementing a Rule Engine subsystem;
- adding automated tests.

---

### 15.4 Task States

Development Tasks SHOULD progress through:

```
Planned
      │
      ▼
In Progress
      │
      ▼
Under Review
      │
      ▼
Completed
      │
      ▼
Archived
```

Tasks MAY additionally enter the **Blocked** state when prerequisite work remains incomplete.

---

### 15.5 Recommended Workflow

The recommended workflow is:

```
Review Specification
        │
        ▼
Architecture Check
        │
        ▼
Create Development Task
        │
        ▼
Implement
        │
        ▼
Run Automated Tests
        │
        ▼
Review
        │
        ▼
Merge
```

---

### 15.6 Architecture Check

Before implementation begins, developers SHOULD verify that:

- architectural responsibilities remain unchanged;
- module boundaries remain respected;
- affected specifications remain identified.

---

### 15.7 Definition of Done

A Development Task SHALL be considered complete when:

- implementation succeeds;
- automated tests pass;
- regression testing succeeds;
- documentation is updated;
- architectural compliance is verified;
- review is complete.

---

### 15.8 Outputs

Implementation workflow SHALL produce reviewed, documented and validated project changes.

---

## Chapter 16 — Testing Workflow

### 16.1 Purpose

This chapter defines how testing integrates into project development.

---

### 16.2 Scope

Testing workflow governs:

- developer testing;
- Continuous Integration;
- release testing;
- regression testing;
- Golden Fixtures.

---

### 16.3 Testing Principles

Testing SHALL accompany implementation.

Testing SHALL NOT be deferred until project completion.

---

### 16.4 Development Testing

Developers SHOULD execute relevant tests whenever Development Tasks modify:

- Rule Engine behavior;
- Validation Rules;
- Rule Assets;
- export behavior;
- import behavior.

---

### 16.5 Continuous Integration

Continuous Integration SHOULD verify:

- successful build;
- automated testing;
- regression testing;
- fixture verification.

---

### 16.6 Release Testing

Release Testing SHOULD verify:

- release candidates;
- Golden Fixtures;
- documentation completeness;
- project readiness.

---

### 16.7 Failure Handling

Test failures SHOULD be investigated before continuing implementation.

Unexpected regressions SHALL be treated as implementation defects until resolved.

Blocked Development Tasks SHALL remain unresolved until prerequisite failures have been addressed.

---

### 16.8 Technical Debt

Known implementation compromises SHOULD be recorded within a **Technical Debt Register**.

Technical Debt SHALL remain visible and traceable throughout project development.

---

### 16.9 Outputs

Testing workflow SHALL provide continuous confidence that project behavior remains correct throughout development.

# Part IV — Tooling, Automation & Project Maintenance

---

## Chapter 17 — Build System

### 17.1 Purpose

This chapter defines the principles governing project builds.

The Build System SHALL transform project sources into distributable application artifacts.

---

### 17.2 Scope

This chapter governs:

- build configuration;
- build reproducibility;
- build artifacts;
- build verification.

---

### 17.3 Build Principles

Builds SHALL prioritize:

- determinism;
- reproducibility;
- traceability;
- automation.

---

### 17.4 Build Configurations

The Build System MAY support multiple build configurations including:

- development;
- testing;
- release.

Build configuration SHALL NOT alter application behavior unless explicitly documented.

---

### 17.5 Build Verification

Every successful build SHOULD verify:

- successful compilation;
- generated artifacts;
- dependency integrity;
- version information.

---

### 17.6 Build Artifacts

Generated artifacts SHALL remain distinct from source material.

Artifacts SHALL be reproducible whenever practical.

Artifacts SHALL NOT become authoritative project data.

---

### 17.7 Outputs

The Build System SHALL produce verified application artifacts suitable for testing or release.

---

## Chapter 18 — Documentation Generator

### 18.1 Purpose

This chapter defines tooling responsible for producing project documentation.

---

### 18.2 Scope

Documentation tooling governs:

- generated documentation;
- reference documentation;
- specification publishing;
- documentation consistency.

---

### 18.3 Documentation Principles

Generated documentation SHALL remain consistent with authoritative project specifications.

Generated documentation SHALL NOT replace authoritative specifications.

---

### 18.4 Documentation Generation

Documentation generation MAY produce:

- developer reference;
- API reference;
- Rule Asset reference;
- release notes;
- specification exports.

---

### 18.5 Consistency Verification

Documentation tooling SHOULD detect:

- broken references;
- inconsistent terminology;
- outdated links;
- missing documentation.

---

### 18.6 Outputs

Documentation tooling SHALL produce synchronized project documentation.

---

## Chapter 19 — Rule Pack Tooling

### 19.1 Purpose

This chapter defines tooling supporting Rule Pack development.

---

### 19.2 Scope

Rule Pack tooling governs:

- Rule Pack creation;
- Rule Pack validation;
- Rule Pack migration;
- Rule Pack packaging.

---

### 19.3 Project Tools

A **Project Tool** is a software component intended primarily to support project development rather than end-user workflows.

Examples include:

- Rule Pack validators;
- documentation generators;
- fixture generators;
- migration utilities.

---

### 19.4 Rule Pack Validation

Rule Pack tooling SHOULD verify:

- Rule Asset integrity;
- dependency consistency;
- version compatibility;
- identifier uniqueness.

---

### 19.5 Rule Pack Migration

Migration tools SHOULD assist with:

- version upgrades;
- compatibility analysis;
- migration verification.

Migration tooling SHALL preserve documented project behavior.

---

### 19.6 Outputs

Rule Pack tooling SHALL assist developers while preserving project integrity.

---

## Chapter 20 — Developer Utilities

### 20.1 Purpose

This chapter defines optional utilities supporting project development.

---

### 20.2 Scope

Developer utilities MAY support:

- fixture generation;
- diagnostics;
- migration;
- debugging;
- profiling.

---

### 20.3 Utility Principles

Developer utilities SHALL improve productivity without becoming mandatory implementation dependencies.

---

### 20.4 Diagnostic Utilities

Utilities MAY support:

- Validation Rule inspection;
- Validation Report visualization;
- diagnostic filtering;
- Rule Asset inspection.

---

### 20.5 Fixture Utilities

Utilities MAY support:

- fixture generation;
- fixture comparison;
- Golden Fixture maintenance;
- regression baseline generation.

---

### 20.6 Outputs

Developer utilities SHALL support efficient project maintenance.

---

## Chapter 21 — Performance Monitoring

### 21.1 Purpose

This chapter defines project-wide performance monitoring.

---

### 21.2 Scope

Performance monitoring governs:

- execution performance;
- memory usage;
- responsiveness;
- scalability.

---

### 21.3 Monitoring Principles

Performance monitoring SHOULD identify regressions before release.

Performance optimization SHALL preserve documented behavior.

---

### 21.4 Performance Analysis

Monitoring MAY analyze:

- Rule Engine execution;
- Validation Engine execution;
- export performance;
- import performance;
- interface responsiveness.

---

### 21.5 Outputs

Performance monitoring SHALL provide actionable performance information.

---

## Chapter 22 — Release Workflow

### 22.1 Purpose

This chapter defines the workflow governing software releases.

---

### 22.2 Scope

Release workflow governs:

- release preparation;
- release verification;
- publication;
- post-release review.

---

### 22.3 Release Workflow

The recommended workflow is:

```
Complete Development
        │
        ▼
Run Full Test Suite
        │
        ▼
Verify Golden Fixtures
        │
        ▼
Documentation Review
        │
        ▼
Create Release
        │
        ▼
Publish
```

---

### 22.4 Release Verification

A release SHOULD verify:

- build reproducibility;
- automated testing;
- regression stability;
- documentation completeness.

---

### 22.5 Outputs

Release workflow SHALL produce verified software releases.

---

## Chapter 23 — Future Automation

### 23.1 Purpose

This chapter defines principles governing future project automation.

---

### 23.2 Scope

Automation MAY support:

- documentation;
- testing;
- Rule Pack generation;
- project analysis;
- migration.

---

### 23.3 Automation Principles

Automation SHALL preserve deterministic project behavior.

Automation SHALL remain subordinate to authoritative project specifications.

---

### 23.4 Automation Opportunities

Future automation MAY include:

- fixture generation;
- project auditing;
- consistency verification;
- documentation synchronization;
- automated migrations.

---

### 23.5 Outputs

Automation SHALL reduce repetitive work while preserving project correctness.

---

## Chapter 24 — Project Maintenance

### 24.1 Purpose

This chapter defines long-term project maintenance principles.

---

### 24.2 Scope

Project maintenance governs:

- long-term evolution;
- architectural preservation;
- documentation maintenance;
- technical debt.

---

### 24.3 Maintenance Principles

Project evolution SHALL preserve:

- architectural separation;
- deterministic behavior;
- documentation integrity;
- Rule Pack compatibility where supported.

---

### 24.4 Specification Maintenance

Project specifications SHALL remain the authoritative source describing project behavior.

Implementation SHALL evolve to match approved specifications.

Architectural changes SHALL update affected specifications before implementation proceeds.

---

### 24.5 Technical Debt

Technical Debt SHALL remain documented within the Technical Debt Register.

Technical Debt SHOULD be periodically reviewed.

Long-term maintenance SHOULD reduce Technical Debt whenever practical.

---

### 24.6 Long-Term Evolution

Future project evolution SHOULD prioritize:

- maintainability;
- extensibility;
- backwards compatibility where appropriate;
- implementation clarity.

---

### 24.7 Project Sustainability

Project maintenance SHALL encourage:

- consistent terminology;
- reproducible builds;
- automated verification;
- comprehensive documentation;
- modular implementation.

---

### 24.8 Outputs

Project maintenance SHALL preserve the long-term health, consistency and sustainability of the Mortasheen Monster Builder.
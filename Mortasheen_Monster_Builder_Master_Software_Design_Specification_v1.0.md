# Mortasheen Monster Builder

## Master Software Design Specification

Version 1.0

> Canonical architecture document for the Mortasheen Monster Builder.

This document is the authoritative architectural specification for the Mortasheen Monster Builder documentation suite.

Unless explicitly stated otherwise, architectural concepts are defined here and referenced by all subordinate specifications.

This document defines the overall architecture, scope, development philosophy and organization of the Mortasheen Monster Builder project.

Unlike the accompanying technical specifications, this document intentionally avoids implementation details. Its purpose is to describe how the project's components fit together and where each aspect of the implementation is specified.

---

## Chapter 1 — Introduction

### 1.1 Purpose

The purpose of the Mortasheen Monster Builder is to provide a deterministic, extensible and data-driven application for constructing legal Mortasheen monsters.

The application SHALL implement the official construction rules while remaining independent from any specific user interface or storage technology.

This document serves as the architectural overview of the project.

---

### 1.2 Scope

The application SHALL support:

- creation of new monsters;
- editing existing monsters;
- validation of monster legality;
- automatic point calculations;
- export of completed monsters;
- support for official Rule Packs;
- future support for community Rule Packs.

The application SHALL remain data-driven wherever practical.

---

### 1.3 Project Goals

The project has five primary goals.

- faithfully implement official Mortasheen construction rules;
- separate rules from algorithms;
- provide deterministic calculations;
- maximize maintainability;
- support future expansion without architectural redesign.

---

### 1.4 Non-Goals

The project is not intended to become:

- a virtual tabletop;
- a combat simulator;
- a campaign manager;
- a monster database;
- a rulebook replacement.

Official Mortasheen publications remain the authoritative source of gameplay rules.

---

## Chapter 2 — Design Philosophy

### 2.1 Data-Driven Architecture

Gameplay information SHALL be represented as structured data rather than hardcoded values.

Algorithms operate upon Rule Assets rather than embedding game content.

---

### 2.2 Separation of Responsibilities

The project separates:

- Rule Assets
- Rule Engine
- Validation Engine
- Data Model
- User Interface
- Storage
- Export

Each subsystem SHALL have one clearly defined responsibility.

---

### 2.3 Immutable Rule Definitions

Rule Assets SHALL remain immutable after publication.

Monster Documents contain mutable user data.

---

### 2.4 Deterministic Calculations

Given identical inputs, the Rule Engine SHALL always produce identical outputs.

No calculation SHALL depend upon hidden state.

---

### 2.5 Extensibility

Future Rule Packs SHALL extend the application through data rather than engine modifications whenever practical.

---

## Chapter 3 — High-Level Architecture

### 3.1 Layered Architecture

The application follows a layered architecture.

```text
User Interface
        │
        ▼
Application Layer
        │
        ▼
Rule Engine
        │
        ▼
Validation Engine
        │
        ▼
Rule Assets
        │
        ▼
Storage Layer
```

Each layer communicates only through documented interfaces.

---

### 3.2 Layer Responsibilities

**User Interface**

Displays information and accepts user input.

**Application Layer**

Coordinates application workflow.

**Rule Engine**

Performs gameplay calculations.

**Validation Engine**

Determines construction legality.

**Rule Assets**

Represent official gameplay definitions.

**Storage Layer**

Persists Monster Documents and Rule Packs.

---

## Chapter 4 — Major Components

The application consists of the following major components:

- Monster Builder
- Rule Engine
- Validation Engine
- Rules Asset Library
- Rule Pack Loader
- Persistence Layer
- Export Layer
- Search System
- Documentation Generator

Each component SHALL expose a well-defined public interface.

---

## Chapter 5 — Data Flow

Monster creation follows the general workflow below.

```text
User Input
      │
      ▼
Monster Document
      │
      ▼
Validation
      │
      ▼
Rule Engine
      │
      ▼
Derived Results
      │
      ▼
User Interface
```

Export operations SHALL use the validated Monster Document as their source.

---

## Chapter 6 — Project Structure

The recommended repository structure is:

```text
docs/
src/
tests/
rulepacks/
assets/
exports/
tools/
```

Additional directories MAY be introduced provided responsibilities remain clearly separated.

---

## Chapter 7 — Technology Principles

The implementation SHALL prioritize:

- strongly typed code;
- deterministic serialization;
- structured JSON data;
- Markdown documentation;
- automated testing;
- reproducible builds.

Specific technologies MAY evolve without changing the architecture.

---

## Chapter 8 — Relationship Between Specifications

The project documentation is divided into independent specifications.

### Master Software Design Specification

Defines overall architecture.

### Data Model Specification

Defines every persistent data structure.

### ****Rule Engine Specification****

Defines gameplay calculations.

### Rules Asset Specification

Defines rule representation.

### Validation and Testing Specification

Defines validation codes and testing strategy.

### UI and Development Workflow Specification

Defines user experience, export formats and development workflow.

Each document SHALL define a unique aspect of the project.

No specification SHALL duplicate another.

---

## Chapter 9 — Coding Standards

The implementation SHOULD follow these principles:

- descriptive names;
- immutable Rule Assets;
- deterministic algorithms;
- explicit interfaces;
- stable identifiers;
- minimal duplication;
- documented public APIs.

Generated code SHOULD remain human-readable.

---

## Chapter 10 — Performance Goals

The application SHOULD provide:

- immediate editing feedback;
- incremental validation;
- efficient Rule Pack loading;
- minimal memory duplication;
- cached derived calculations.

Performance optimizations SHALL preserve deterministic behavior.

---

## Chapter 11 — Error Handling

The application distinguishes between:

- validation errors;
- import errors;
- serialization errors;
- internal exceptions.

User-facing diagnostics SHOULD explain:

- the problem;
- its location;
- the applicable rule;
- suggested corrections.

---

## Chapter 12 — Extensibility

Future versions of the application MAY support:

- additional Rule Packs;
- official expansions;
- localized Rule Packs;
- plugin-based exporters;
- alternative editor layouts.

Extensions SHOULD require minimal engine modification.

---

## Chapter 13 — Security and Data Integrity

The application SHALL:

- validate imported Rule Packs;
- validate imported Monster Documents;
- reject malformed serialized data;
- preserve deterministic save files.

The application SHALL never execute arbitrary code contained within Rule Packs.

---

## Chapter 14 — Development Phases

Development is expected to proceed through the following phases.

### Phase 1

Architecture and specifications.

### Phase 2

Core implementation.

### Phase 3

User interface.

### Phase 4

Validation and testing.

### Phase 5

Release preparation.

Each phase SHOULD conclude with updated documentation.

---

## Chapter 15 — Definition of Done

A feature SHALL be considered complete only when:

- implementation is complete;
- validation rules are implemented;
- automated tests pass;
- documentation is updated;
- exports function correctly;
- no unresolved TODO items remain.

Completion SHALL be evaluated against the relevant specification documents.

---

# Part V — Documentation Standards

## Chapter 16 — Project Glossary

The following terms have consistent meanings throughout every project specification.

Terms defined in this glossary SHALL retain identical meanings across the documentation suite.

Specifications SHALL reference these definitions rather than redefining established terminology.

---

### Authoritative Specification

The single project document responsible for defining a particular concept.

No project concept SHALL possess more than one authoritative specification.

---

### Build Context

The collection of scientist information, permissions and construction settings used when validating a Monster Document.

---

### Derived Data

Information calculated by the Rule Engine rather than authored directly by the user.

Derived Data SHALL NOT become the authoritative source of project information.

---

### Development Task

The smallest independently implementable unit of project work.

Development Tasks progress through defined workflow states and possess explicit completion criteria.

---

### Dirty State

The condition in which a Monster Document contains unsaved changes.

---

### Export Profile

A complete configuration defining how project information is exported.

An Export Profile specifies:

- exported content;
- presentation strategy;
- metadata;
- intended audience;
- output format.

---

### Import Profile

A complete configuration defining how external information is reconstructed into project data.

---

### Monster Document

The canonical persisted representation of a user-created monster.

A Monster Document contains authored project data rather than calculated results.

---

### Presentation Profile

A specialized Export Profile intended for human-readable output.

---

### Project Module

A logical subsystem possessing one architectural responsibility.

Project Modules remain independent from physical repository organization.

---

### Project Tool

A software component intended primarily to support project development rather than end-user workflows.

---

### Rule Asset

An immutable definition representing official gameplay information.

---

### Rule Engine

The subsystem responsible for deterministic gameplay calculations.

---

### Rule Pack

A versioned collection of Rule Assets implementing one ruleset.

---

### Technical Debt Register

The authoritative record of known implementation compromises intended for future resolution.

---

### UI Action

An atomic user interaction initiated through the user interface.

---

### UI State

Temporary interface information that does not belong to a Monster Document.

---

### Validation Engine

The subsystem responsible for determining construction legality.

---

### Validation Rule

A deterministic rule responsible for producing Validation Diagnostics.

---

### Workspace

The complete interactive editing environment presented to the user.

## Chapter 17 — Specification Conventions

### 17.1 Purpose

This chapter defines conventions governing every project specification.

These conventions SHALL be interpreted consistently throughout the documentation suite.

---

### 17.2 Normative Language

The following keywords indicate requirement strength.

| Keyword | Meaning |
|----------|---------|
| **SHALL** | Mandatory requirement. |
| **SHALL NOT** | Prohibited behavior. |
| **SHOULD** | Strong recommendation. Deviations require justification. |
| **SHOULD NOT** | Generally discouraged behavior. |
| **MAY** | Optional behavior consistent with the project architecture. |

Unless explicitly stated otherwise, examples are illustrative rather than normative.

---

### 17.3 Determinism

Throughout the project, **determinism** means:

> Identical inputs, Rule Packs, Build Context and application versions SHALL produce semantically identical outputs.

Determinism applies to:

- Rule Engine calculations;
- Validation;
- serialization;
- import;
- export;
- generated documentation;
- automated testing.

Determinism does not require byte-identical output when explicitly permitted metadata (for example timestamps) differs.

---

### 17.4 Authoritative Specifications

Every project concept SHALL possess exactly one authoritative specification.

Supporting specifications SHALL reference the authoritative source rather than redefining concepts.

---

### 17.5 Cross-Document Terminology

Project terminology SHALL remain consistent throughout the documentation suite.

Specifications SHALL NOT redefine established terms.

Where clarification is required, specifications SHOULD reference the Project Glossary.

---

### 17.6 Illustrative Examples

Examples, diagrams and sample data are provided solely to clarify project behavior.

Unless explicitly stated otherwise, examples SHALL NOT be interpreted as exhaustive implementations.

Illustrative examples MAY omit optional implementation details for clarity.

---

### 17.7 Implementation Independence

Project specifications define architecture, responsibilities and observable behavior.

Specifications SHALL avoid prescribing:

- programming languages;
- frameworks;
- directory structures;
- libraries;
- third-party services;

unless those technologies are themselves project requirements.

## Appendix A — Documentation Hierarchy

The Mortasheen Monster Builder documentation consists of complementary specifications.

```text
Master Software Design Specification
                │
        ┌───────┼─────────────────────────────┐
        │       │                             │
        ▼       ▼                             ▼
 Data Model   Rule Engine             Rules Asset
        │            │                     │
        └────────────┼─────────────────────┘
                     ▼
      Validation & Testing Specification
                     │
                     ▼
UI, Export & Development Workflow Specification
```

The Master Software Design Specification defines the project's overall architecture.

Each subordinate specification defines one architectural responsibility.

No subordinate specification supersedes the Master Software Design Specification.

## Appendix B — Specification Dependencies

The documentation suite follows the dependency relationships below.

| Specification | Depends On |
|---------------|------------|
| Master Software Design Specification | None |
| Data Model Specification | Master Software Design Specification |
| ****Rule Engine Specification**** | Master Software Design Specification, Data Model Specification |
| Rules Asset Specification | Master Software Design Specification |
| Validation & Testing Specification | Master Software Design Specification, Data Model Specification, ****Rule Engine Specification****, Rules Asset Specification |
| UI, Export & Development Workflow Specification | Master Software Design Specification, Data Model Specification, ****Rule Engine Specification****, Validation & Testing Specification |

Dependencies describe conceptual relationships rather than implementation order.

Lower-level specifications SHALL reference higher-level concepts rather than redefining them.
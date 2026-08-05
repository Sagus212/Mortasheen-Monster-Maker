# Mortasheen Monster Builder

# Validation and Testing Specification

Version 1.0

> Canonical specification defining validation architecture, diagnostic reporting and testing requirements for the Mortasheen Monster Builder.

This document defines how the Monster Builder validates Monster Documents, Rule Packs, Rule Assets and generated data. It also defines the testing strategy required to ensure deterministic, correct and maintainable behavior.

This specification complements, but does not replace:

- Master Software Design Specification
- Data Model Specification
- Rule Engine Specification
- Rules Asset Specification

---

## Chapter 1 — Purpose, Authority and Scope

### 1.1 Purpose

The Validation and Testing Specification defines how the Monster Builder determines whether data complies with the official Mortasheen construction rules and whether the software implementation behaves correctly.

Validation determines legality.

Testing determines implementation correctness.

These responsibilities SHALL remain distinct.

---

### 1.2 Authority

This specification derives its authority from the following sources, in order of precedence.

1. Official Mortasheen publications.
2. Master Software Design Specification.
3. Data Model Specification.
4. Rule Engine Specification.
5. Rules Asset Specification.

Where conflicts occur:

- official Mortasheen publications define gameplay;
- technical specifications define implementation;
- validation interprets gameplay through the architecture defined by the technical specifications.

---

### 1.3 Scope

This document defines:

- validation architecture;
- Validation Rules;
- diagnostics;
- validation execution order;
- Validation Codes;
- testing strategy;
- regression strategy;
- acceptance criteria.

This specification SHALL NOT redefine gameplay rules.

---

### 1.4 Separation of Responsibilities

The project separates responsibilities between the following systems.

**Rule Engine**

Responsible for:

- gameplay calculations;
- derived values;
- Rule Asset interpretation.

**Validation Engine**

Responsible for:

- evaluating legality;
- executing Validation Rules;
- producing Diagnostics.

**Testing Framework**

Responsible for:

- verifying implementation correctness;
- detecting regressions.

No subsystem SHALL duplicate another's responsibilities.

---

### 1.5 Guiding Principles

Validation SHALL be:

- deterministic;
- reproducible;
- idempotent;
- traceable;
- explainable;
- independent of user interface.

Testing SHALL be:

- automated whenever practical;
- deterministic;
- repeatable.

---

## Chapter 2 — Validation Architecture

### 2.1 Overview

Validation is performed exclusively by the Validation Engine.

The Validation Engine SHALL inspect:

- Monster Documents;
- Rule Packs;
- Rule Assets;
- derived Rule Engine values;
- serialized data.

Validation SHALL NOT modify application state.

---

### 2.2 Validation Rules

A **Validation Rule** is an immutable unit of validation logic responsible for evaluating exactly one requirement.

A Validation Rule SHALL:

- inspect read-only data;
- evaluate one rule;
- produce zero or more Diagnostics.

Validation Rules SHALL NOT modify application state.

---

### 2.3 Validation Inputs

Validation consumes:

- Monster Document;
- Build Context;
- Rule Pack;
- Rule Assets;
- Rule Engine derived values.

All validation inputs SHALL be treated as immutable.

---

### 2.4 Validation Outputs

Validation produces a Validation Report.

A Validation Report SHALL contain:

- Diagnostics;
- validation summary;
- severity totals;
- execution metadata.

---

### 2.5 Validation Purity

Validation SHALL have no observable side effects.

Executing validation SHALL NOT modify:

- Monster Documents;
- Rule Packs;
- Rule Assets;
- editor state;
- caches;
- serialized output.

Running validation repeatedly without modifying its inputs SHALL produce identical Validation Reports.

---

### 2.6 Validation Phases

Validation SHALL execute in the following order.

1. Document integrity.
2. Rule Pack integrity.
3. Structural validation.
4. Rule validation.
5. Derived-value validation.
6. Cross-reference validation.
7. Final consistency validation.

Each phase MAY assume previous phases succeeded.

---

### 2.7 Validation Categories

Validation Rules SHALL belong to one execution category.

Recommended categories include:

- Structural
- Rule Pack
- Rule Asset
- Construction
- Calculation
- Serialization
- Migration
- Cross Reference
- Internal

Categories determine execution organization.

They SHALL NOT determine diagnostic identifiers.

---

### 2.8 Extensibility

Rule Packs MAY introduce additional Validation Rules.

The Validation Engine SHALL discover Validation Rules dynamically whenever practical.

---

## Chapter 3 — Diagnostic Data Model

### 3.1 Purpose

Diagnostics communicate validation results to both users and developers.

Every detected issue SHALL be represented as a Diagnostic.

---

### 3.2 Diagnostic Components

Each Diagnostic SHALL include:

- Validation Code;
- severity;
- title;
- message;
- affected object;
- originating Validation Rule;
- originating specification.

Diagnostics MAY additionally include:

- suggested correction;
- related Diagnostics;
- documentation link;
- source location.

---

### 3.3 Validation Codes

Every Validation Rule SHALL own exactly one stable Validation Code.

Validation Codes SHALL remain stable across software versions whenever practical.

Example:

```
ABILITY_LIMIT_EXCEEDED
```

---

### 3.4 Diagnostic Messages

Messages SHOULD:

- explain the problem;
- identify the affected object;
- suggest corrective action.

Messages SHALL NOT expose implementation details.

---

### 3.5 Diagnostic Stability

Equivalent validation failures SHALL always generate identical Validation Codes.

Equivalent Validation Reports SHALL preserve diagnostic ordering.

This guarantees deterministic testing.

---

### 3.6 Source Traceability

Every Validation Rule SHOULD reference:

- originating specification;
- originating Rule Asset;
- official rules source where practical.

This allows every Diagnostic to be traced back to its architectural and gameplay origin.

---

## Chapter 4 — Severity Levels and Strictness Modes

### 4.1 Severity Levels

The Validation Engine recognizes four severity levels.

#### Error

Violates official construction rules.

The Monster Document SHALL be considered illegal.

#### Warning

Represents suspicious or discouraged construction.

Warnings SHALL NOT invalidate the Monster Document.

#### Information

Provides useful contextual information.

Informational diagnostics SHALL never indicate illegal construction.

#### Internal

Represents application, Rule Pack or implementation failures.

Internal diagnostics SHOULD be logged separately.

---

### 4.2 Strictness Modes

Validation SHALL support multiple strictness modes.

#### Official Mode

Applies official gameplay rules.

#### Development Mode

Reports additional implementation diagnostics.

#### Rule Pack Author Mode

Reports Rule Pack authoring issues.

---

### 4.3 Filtering

Applications MAY filter Diagnostics by:

- severity;
- Validation Category;
- Validation Code namespace;
- affected object.

Filtering SHALL NOT alter validation results.

---

### 4.4 Escalation

Rule Packs MAY elevate warnings to errors.

Official errors SHALL NOT be downgraded.

---

## Chapter 5 — Validation Execution Order

### 5.1 Purpose

Validation SHALL execute in a deterministic order.

Earlier phases establish prerequisites required by later phases.

---

### 5.2 Execution Pipeline

Recommended pipeline:

1. Deserialize document.
2. Validate serialization.
3. Load Rule Pack.
4. Validate Rule Pack.
5. Resolve Rule Assets.
6. Execute structural Validation Rules.
7. Execute Rule Engine.
8. Execute calculation Validation Rules.
9. Execute gameplay Validation Rules.
10. Produce Validation Report.

---

### 5.3 Fatal Validation Failures

A validation failure is **fatal** when validation cannot safely continue.

Examples include:

- Rule Pack failed to load;
- serialization failed;
- required Rule Assets cannot be resolved.

Illegal Monster Documents are **not** fatal.

Validation SHOULD continue whenever practical to maximize diagnostic usefulness.

---

### 5.4 Dependency Resolution

Validation SHALL resolve Rule Asset dependencies before evaluating gameplay rules.

Unresolved dependencies SHALL prevent later validation phases.

---

### 5.5 Incremental Validation

Editors SHOULD perform incremental validation.

Only Validation Rules affected by user changes SHOULD be re-executed.

---

### 5.6 Rule Interpretation

Where official Mortasheen publications permit multiple plausible interpretations, the active Rule Pack SHALL explicitly identify the interpretation implemented.

Validation SHALL evaluate legality according to the interpretation supplied by the active Rule Pack.

---

### 5.7 Final Validation Report

The Validation Report SHALL summarize:

- total Diagnostics;
- Errors;
- Warnings;
- Information;
- Internal diagnostics;
- validation duration;
- Rule Pack version.

---

## Chapter 6 — Validation Code Catalogue

### 6.1 Purpose

Every Validation Rule SHALL correspond to exactly one Validation Code.

Validation Codes provide stable identifiers used by:

- Diagnostics;
- automated tests;
- localization;
- logging;
- documentation.

---

### 6.2 Validation Categories

Validation Codes SHALL be grouped into namespaces.

Recommended namespaces include:

- DOC
- PACK
- CLASS
- SIZE
- ATTR
- PART
- ABILITY
- DESC
- WEAKNESS
- CORE
- ASSET
- SERIALIZE
- IMPORT
- MIGRATION
- INTERNAL

---

### 6.3 Naming Convention

Validation Codes SHOULD use uppercase snake case.

Examples:

```
PART_LIMIT_EXCEEDED

ABILITY_UNKNOWN_DESCRIPTOR

PACK_DUPLICATE_IDENTIFIER
```

Validation Codes SHALL remain stable once published.

---

### 6.4 Validation Rule Documentation

Every Validation Rule SHALL define:

- Validation Code;
- category;
- severity;
- trigger condition;
- affected object;
- suggested correction;
- originating specification;
- originating Rule Asset where applicable.

---

### 6.5 Registry

The Validation Code Catalogue SHALL function as the authoritative registry of every Validation Rule implemented by the application.

Future chapters SHALL populate this catalogue.

---

### 6.6 Extensibility

Rule Packs MAY define additional Validation Codes.

Custom Validation Codes SHALL use Rule Pack-specific namespaces to avoid collisions.

Example:

```
HOMEBREW_DUPLICATE_ORGAN
```

Applications SHALL preserve unknown Validation Codes without modification.

## Chapter 7 — Monster Document and Build Context Validation

### 7.1 Purpose

This chapter defines the Validation Rules responsible for verifying the structural integrity and internal consistency of Monster Documents and their associated Build Contexts.

These Validation Rules SHALL execute before gameplay validation.

---

### 7.2 Scope

This chapter validates:

- Monster Document structure;
- document identifiers;
- metadata;
- provenance;
- Build Context;
- serialization consistency.

This chapter SHALL NOT validate gameplay legality.

---

### 7.3 Preconditions

Before executing these Validation Rules:

- the Monster Document SHALL have been successfully deserialized;
- the active Rule Pack SHALL have been loaded;
- required Rule Assets SHALL be available.

Fatal failures occurring before these conditions SHALL terminate validation.

---

### 7.4 Structural Validation

Validation SHALL verify:

- required fields exist;
- field types are correct;
- mandatory collections are present;
- required identifiers exist;
- identifiers are unique where required;
- unsupported fields are handled appropriately.

---

### 7.5 Semantic Validation

Validation SHALL verify:

- provenance consistency;
- creator metadata;
- ownership metadata;
- construction history;
- alteration eligibility;
- Build Context consistency;
- selected Rule Pack;
- active interpretation;
- construction mode.

---

### 7.6 Serialization Validation

Validation SHALL verify:

- document version;
- serialization integrity;
- migration requirements;
- deterministic deserialization.

---

### 7.7 Validation Order

Validation Rules defined in this chapter SHOULD execute in the following order:

1. Structure
2. Metadata
3. Provenance
4. Build Context
5. Serialization

---

### 7.8 Cross-Validation

Cross-validation MAY compare:

- Build Context
- Rule Pack
- Monster Document

to ensure mutual consistency.

---

### 7.9 Output

Diagnostics SHALL belong primarily to:

- DOC
- SERIALIZE

---

## Chapter 8 — Class, Size, Core Feature and Access Validation

### 8.1 Purpose

This chapter defines Validation Rules governing Monster Class selection, Size selection, Core Feature compatibility and access restrictions.

---

### 8.2 Scope

Validation SHALL verify:

- Monster Classes;
- Sizes;
- Core Features;
- construction permissions;
- access restrictions.

---

### 8.3 Preconditions

Before execution:

- structural validation SHALL have completed successfully;
- Rule Assets SHALL have been resolved;
- applicable Rule Pack SHALL be active.

---

### 8.4 Structural Validation

Validation SHALL verify:

- exactly one Monster Class;
- exactly one Size;
- valid Core Feature references;
- valid Rule Asset references.

---

### 8.5 Semantic Validation

Validation SHALL verify:

- class legality;
- size legality;
- Core Feature compatibility;
- class restrictions;
- size restrictions;
- access permissions;
- alteration permissions.

Validation SHALL evaluate Rule Asset semantics rather than duplicate them.

---

### 8.6 Validation Order

1. Class
2. Size
3. Core Feature
4. Access

---

### 8.7 Cross-Validation

Validation MAY compare:

- Monster Class
- Size
- Core Feature
- Build Context

to verify consistent rule application.

---

### 8.8 Output

Diagnostics SHALL belong primarily to:

- CLASS
- SIZE
- CORE

---

## Chapter 9 — Parts, Endurance and Attribute Validation

### 9.1 Purpose

This chapter defines Validation Rules governing body parts, Endurance allocation and attribute legality.

---

### 9.2 Scope

Validation SHALL verify:

- body parts;
- Endurance;
- attributes;
- derived attribute consistency.

---

### 9.3 Preconditions

Before execution:

- class validation SHALL have succeeded;
- size validation SHALL have succeeded.

---

### 9.4 Structural Validation

Validation SHALL verify:

- required body parts;
- prohibited body parts;
- duplicate body parts;
- attachment structure;
- required attributes.

---

### 9.5 Semantic Validation

Validation SHALL verify:

- body part legality;
- Endurance legality;
- attribute ranges;
- derived attribute consistency.

Validation SHALL consume Rule Engine calculations rather than reproduce them.

---

### 9.6 Validation Order

1. Parts
2. Attributes
3. Endurance
4. Derived values

---

### 9.7 Cross-Validation

Validation MAY compare:

- body parts;
- Monster Class;
- Size;
- Rule Engine derived values.

---

### 9.8 Output

Diagnostics SHALL belong primarily to:

- PART
- ATTR

---

## Chapter 10 — Ability Block and Descriptor Validation

### 10.1 Purpose

This chapter defines Validation Rules governing Ability Blocks and their associated Descriptors.

---

### 10.2 Scope

Validation SHALL verify:

- Ability Blocks;
- Descriptors;
- Ability Block relationships;
- Descriptor compatibility.

---

### 10.3 Preconditions

Before execution:

- Rule Assets SHALL have been validated;
- body parts SHALL have passed validation.

---

### 10.4 Structural Validation

Validation SHALL verify:

- known Ability Blocks;
- valid Descriptor references;
- valid Rule Asset identifiers;
- valid relationships.

Rule Assets SHALL be validated according to the Rules Asset Specification.

---

### 10.5 Semantic Validation

Validation SHALL verify:

- placement legality;
- descriptor compatibility;
- dependencies;
- point legality;
- duplicate restrictions.

Validation SHALL consume Rule Engine calculations rather than duplicate them.

---

### 10.6 Validation Order

1. Ability Blocks
2. Descriptors
3. Relationships
4. Dependencies
5. Point legality

---

### 10.7 Cross-Validation

Validation MAY compare:

- Ability Blocks;
- Descriptors;
- Damage Types;
- Conditions;
- Movement Types;
- body parts.

---

### 10.8 Output

Diagnostics SHALL belong primarily to:

- ABILITY
- DESC

---

## Chapter 11 — Weakness Validation

### 11.1 Purpose

This chapter defines Validation Rules governing monster weaknesses.

---

### 11.2 Scope

Validation SHALL verify:

- weakness legality;
- weakness budgets;
- weakness overrides;
- weakness editing.

---

### 11.3 Preconditions

Before execution:

- Ability Block validation SHALL have completed;
- Rule Engine calculations SHALL be available.

---

### 11.4 Structural Validation

Validation SHALL verify:

- valid Weakness Asset;
- compatible Ability Block;
- representation mode;
- descriptor structure.

Weakness Assets SHALL be validated according to the Rules Asset Specification.

---

### 11.5 Semantic Validation

Validation SHALL verify:

- weakness penalties;
- weakness totals;
- weakness bonus points;
- Rule Pack overrides;
- editing permissions.

Validation SHALL consume Rule Engine calculations.

---

### 11.6 Validation Order

1. Weakness structure
2. Weakness semantics
3. Budget
4. Overrides
5. Editing

---

### 11.7 Cross-Validation

Validation MAY compare:

- weaknesses;
- Monster Class;
- Core Feature;
- Build Context.

---

### 11.8 Output

Diagnostics SHALL belong primarily to:

- WEAKNESS

---

## Chapter 12 — Construction and Alteration Validation

### 12.1 Purpose

This chapter defines Validation Rules governing legal construction and legal alteration.

---

### 12.2 Scope

Validation SHALL verify:

- construction legality;
- alteration legality;
- construction sequence;
- prerequisites;
- point budgets;
- calculation consistency.

---

### 12.3 Preconditions

Before execution:

- all gameplay validation SHALL have completed successfully.

---

### 12.4 Structural Validation

Validation SHALL verify:

- required selections;
- mandatory construction steps;
- mutually exclusive options;
- prerequisite satisfaction.

---

### 12.5 Semantic Validation

Validation SHALL verify:

- construction costs;
- alteration costs;
- scientist permissions;
- derived totals;
- Rule Pack interpretation.

Validation SHALL compare Rule Engine results against expected outcomes.

Validation SHALL NOT duplicate Rule Engine calculations.

---

### 12.6 Validation Order

1. Construction sequence
2. Prerequisites
3. Costs
4. Alteration
5. Derived calculations

---

### 12.7 Cross-Validation

Validation MAY compare:

- construction data;
- alteration data;
- Rule Engine calculations;
- Rule Pack interpretation.

---

### 12.8 Official Rule Interpretation

Where official rules permit multiple interpretations, validation SHALL apply the interpretation supplied by the active Rule Pack.

The Validation Report SHALL identify the active interpretation.

---

### 12.9 Output

Diagnostics SHALL belong primarily to:

- CONSTRUCTION
- ALTERATION

---

## Chapter 13 — Rule Pack, Dependency, Import and Migration Validation

### 13.1 Purpose

This chapter defines Validation Rules governing Rule Packs throughout their lifecycle.

---

### 13.2 Scope

Validation SHALL verify:

- Rule Packs;
- Rule Assets;
- dependencies;
- imports;
- migrations.

---

### 13.3 Preconditions

Before execution:

- Rule Pack SHALL have been loaded;
- Manifest SHALL have been parsed.

---

### 13.4 Structural Validation

Validation SHALL verify:

- Manifest integrity;
- required Rule Assets;
- duplicate identifiers;
- dependency graph structure;
- version declarations.

Rule Assets SHALL be validated according to the Rules Asset Specification.

---

### 13.5 Semantic Validation

Validation SHALL verify:

- Rule Pack completeness;
- version compatibility;
- dependency resolution;
- imported Rule Assets;
- imported Monster Documents.

---

### 13.6 Migration Validation

Validation SHALL verify:

- migration path;
- migration completeness;
- version compatibility;
- migration determinism;
- information preservation;
- migration reversibility where supported.

---

### 13.7 Validation Order

1. Manifest
2. Rule Assets
3. Dependencies
4. Imports
5. Migration

---

### 13.8 Cross-Validation

Validation MAY compare:

- Rule Pack;
- imported Rule Pack;
- imported Monster Documents;
- dependency graph.

---

### 13.9 Output

Diagnostics SHALL belong primarily to:

- PACK
- ASSET
- IMPORT
- MIGRATION
- INTERNAL

## Chapter 14 — Rule Engine Testing

### 14.1 Purpose

This chapter defines the testing requirements for the Rule Engine.

The Rule Engine SHALL be verified independently of the Validation Engine.

---

### 14.2 Scope

Testing SHALL verify:

- gameplay calculations;
- derived values;
- deterministic behavior;
- Rule Asset interpretation;
- calculation traces.

---

### 14.3 Preconditions

Before execution:

- Rule Assets SHALL have been validated;
- all required test fixtures SHALL be available;
- the active Rule Pack SHALL have loaded successfully.

---

### 14.4 Test Fixtures

Rule Engine testing SHALL use fixtures including, but not limited to:

- minimal legal monsters;
- maximal legal monsters;
- representative official monsters;
- malformed Monster Documents;
- edge-case constructions;
- regression fixtures.

Fixtures SHALL remain immutable once approved.

---

### 14.5 Required Tests

Testing SHALL include:

- attribute calculations;
- construction point calculations;
- alteration calculations;
- Endurance calculations;
- Ability Block calculations;
- Weakness calculations;
- derived value generation.

---

### 14.6 Edge Case Testing

Testing SHALL include edge cases such as:

- minimum legal values;
- maximum legal values;
- empty optional collections;
- maximum weakness allocation;
- zero-cost constructions where legal;
- largest supported Monster Documents.

---

### 14.7 Property-Based Testing

Where practical, the Rule Engine SHOULD be subjected to property-based testing.

Generated test cases SHOULD verify properties such as:

- deterministic calculations;
- invariant preservation;
- identifier uniqueness;
- stable derived values.

---

### 14.8 Test Order

Recommended order:

1. Individual calculators.
2. Edge cases.
3. Combined calculations.
4. Property-based testing.
5. Full Monster Document calculations.

---

### 14.9 Acceptance Criteria

Repeated execution using identical inputs SHALL produce identical derived values.

Calculation traces SHALL remain deterministic.

Equivalent Rule Packs SHALL produce equivalent calculations unless explicitly documented otherwise.

---

### 14.10 Outputs

Testing SHALL produce:

- pass/fail status;
- execution duration;
- calculation trace;
- fixture identifier.

---

## Chapter 15 — Validation Engine Testing

### 15.1 Purpose

This chapter defines testing requirements for the Validation Engine.

---

### 15.2 Scope

Testing SHALL verify:

- Validation Rules;
- Validation Reports;
- Diagnostic generation;
- execution ordering;
- Validation Rule interactions.

---

### 15.3 Preconditions

Before execution:

- Rule Engine tests SHALL succeed;
- Validation Rules SHALL be available;
- required fixtures SHALL exist.

---

### 15.4 Test Fixtures

Fixtures SHALL include:

- legal Monster Documents;
- illegal Monster Documents;
- malformed Monster Documents;
- Rule Pack edge cases;
- regression fixtures.

---

### 15.5 Required Tests

Testing SHALL verify:

- structural validation;
- semantic validation;
- Diagnostic generation;
- severity assignment;
- deterministic ordering;
- Validation Rule isolation.

---

### 15.6 Property-Based Testing

Randomized Monster Documents SHOULD verify that:

- validation never crashes;
- diagnostics remain deterministic;
- repeated validation is idempotent.

---

### 15.7 Test Order

1. Individual Validation Rules.
2. Validation Categories.
3. Property-based testing.
4. Full Validation Engine.

---

### 15.8 Acceptance Criteria

Equivalent validation inputs SHALL produce byte-equivalent Validation Reports unless explicitly documented otherwise.

Diagnostic ordering SHALL remain deterministic.

Validation Codes SHALL remain stable.

---

### 15.9 Outputs

Testing SHALL generate:

- Validation Reports;
- Diagnostic comparisons;
- regression summaries;
- fixture identifiers.

---

## Chapter 16 — Rules Asset Testing

### 16.1 Purpose

This chapter defines testing requirements for Rule Assets.

---

### 16.2 Scope

Testing SHALL verify:

- Rule Asset schemas;
- Rule Asset relationships;
- dependency graphs;
- Rule Pack integrity.

---

### 16.3 Preconditions

Rule Assets SHALL load successfully.

---

### 16.4 Test Fixtures

Fixtures SHALL include:

- valid Rule Assets;
- intentionally malformed Rule Assets;
- dependency edge cases;
- version compatibility fixtures.

---

### 16.5 Required Tests

Testing SHALL verify:

- schema compliance;
- identifier uniqueness;
- dependency validity;
- version compatibility;
- serialization.

---

### 16.6 Test Order

1. Individual Rule Assets.
2. Relationships.
3. Dependency graph.
4. Complete Rule Pack.

---

### 16.7 Acceptance Criteria

Every Rule Asset SHALL satisfy the Rules Asset Specification.

No unresolved references SHALL remain.

Every Rule Pack SHALL load deterministically.

---

### 16.8 Outputs

Testing SHALL produce Rule Asset validation reports.

---

## Chapter 17 — Serialization, Import and Migration Testing

### 17.1 Purpose

This chapter defines testing requirements for persistence and interoperability.

---

### 17.2 Scope

Testing SHALL verify:

- serialization;
- deserialization;
- Rule Pack import;
- Monster Document import;
- migration.

---

### 17.3 Preconditions

Fixtures SHALL exist for every supported serialization format.

---

### 17.4 Test Fixtures

Fixtures SHALL include:

- current-version documents;
- previous-version documents;
- malformed serialized documents;
- imported Rule Packs.

---

### 17.5 Required Tests

Testing SHALL verify:

- deterministic serialization;
- complete deserialization;
- migration correctness;
- migration determinism;
- migration reversibility where supported;
- information preservation.

---

### 17.6 Test Order

1. Serialization.
2. Deserialization.
3. Import.
4. Migration.

---

### 17.7 Acceptance Criteria

Repeated serialization SHALL produce semantically equivalent Monster Documents.

Migration SHALL preserve all supported information unless explicitly documented otherwise.

---

### 17.8 Outputs

Testing SHALL generate:

- serialization comparison reports;
- migration reports;
- fixture identifiers.

---

## Chapter 18 — Integration Testing

### 18.1 Purpose

Integration Testing verifies interactions between project subsystems.

---

### 18.2 Scope

Testing SHALL verify interactions between:

- Rule Engine;
- Validation Engine;
- Rule Assets;
- Monster Documents;
- Build Context;
- export pipeline.

---

### 18.3 Preconditions

All component-level tests SHALL pass.

---

### 18.4 Test Fixtures

Integration fixtures SHALL represent complete workflows.

---

### 18.5 Required Tests

Testing SHALL verify:

- monster construction;
- monster alteration;
- Rule Pack loading;
- Validation Engine interaction;
- export preparation;
- save/load cycle.

---

### 18.6 Failure Propagation

Integration testing SHALL verify:

- graceful failure;
- partial subsystem failures;
- dependency failures;
- diagnostic propagation.

Subsystem failures SHALL NOT corrupt application state.

---

### 18.7 Test Order

1. Two-component integration.
2. Three-component integration.
3. End-to-end workflows.
4. Failure scenarios.

---

### 18.8 Acceptance Criteria

Subsystem interaction SHALL preserve deterministic behavior.

Failure handling SHALL preserve data integrity.

---

### 18.9 Outputs

Testing SHALL generate integration summaries.

---

## Chapter 19 — Regression Testing

### 19.1 Purpose

Regression Testing ensures that implemented behavior remains unchanged unless intentionally modified.

---

### 19.2 Scope

Regression SHALL verify:

- calculations;
- diagnostics;
- serialization;
- Rule Assets;
- imports;
- performance.

---

### 19.3 Preconditions

Approved regression baselines SHALL exist.

---

### 19.4 Expected Results

Expected Results define behavior required by the project specifications.

Regression Baselines define behavior accepted by a previous release.

Both SHALL be preserved independently.

---

### 19.5 Required Tests

Testing SHALL compare:

- current output;
- Expected Results;
- Regression Baselines.

Unexpected differences SHALL generate failures.

---

### 19.6 Performance Regression

Testing SHOULD detect significant performance regressions.

Performance expectations SHALL be documented independently of functional correctness.

---

### 19.7 Test Order

1. Unit regression.
2. Integration regression.
3. Performance regression.
4. Complete application regression.

---

### 19.8 Acceptance Criteria

Only intentional, documented changes MAY alter approved Regression Baselines.

---

### 19.9 Outputs

Testing SHALL generate:

- regression reports;
- baseline comparisons;
- performance summaries.

---

## Chapter 20 — Acceptance and Release Testing

### 20.1 Purpose

Acceptance Testing determines whether a project version is suitable for release.

---

### 20.2 Scope

Testing SHALL verify:

- implementation completeness;
- specification compliance;
- user workflows;
- release readiness.

---

### 20.3 Preconditions

All previous testing stages SHALL pass successfully.

---

### 20.4 Test Fixtures

Acceptance SHALL include Golden Fixtures.

A **Golden Fixture** is an immutable reference fixture derived from official Mortasheen publications or from officially accepted project reference data.

Golden Fixtures SHALL NOT be modified after approval.

---

### 20.5 Required Tests

Testing SHALL verify:

- monster creation;
- monster editing;
- validation;
- export;
- save/load;
- Rule Pack loading.

Golden Fixtures SHALL produce expected results without modification.

---

### 20.6 Test Order

1. Functional acceptance.
2. Workflow acceptance.
3. Golden Fixture verification.
4. Release acceptance.

---

### 20.7 Acceptance Criteria

A release SHALL be considered acceptable only if:

- all required tests pass;
- no unresolved Errors remain;
- deterministic behavior is preserved;
- Golden Fixtures produce expected results;
- documentation is current.

---

### 20.8 Outputs

Acceptance Testing SHALL produce:

- release report;
- unresolved issue list;
- Golden Fixture summary;
- acceptance decision.

## Chapter 21 — Test Fixtures and Golden References

### 21.1 Purpose

This chapter defines the standards governing test fixtures used throughout the project.

Fixtures SHALL provide deterministic, reproducible inputs for automated testing.

Golden References SHALL provide immutable reference outputs against which future implementations are compared.

---

### 21.2 Scope

This chapter defines:

- fixture categories;
- fixture lifecycle;
- Golden References;
- fixture versioning;
- fixture maintenance;
- fixture reproducibility.

---

### 21.3 Fixture Categories

The project SHALL maintain fixtures belonging to the following categories.

#### Minimal Fixtures

Smallest legal Monster Documents.

#### Representative Fixtures

Typical Monster Documents exercising common construction paths.

#### Edge Case Fixtures

Monster Documents exercising implementation limits.

#### Malformed Fixtures

Intentionally invalid Monster Documents used to verify Validation Rules.

#### Regression Fixtures

Fixtures preserving previously verified implementation behavior.

#### Golden Fixtures

Immutable fixtures representing officially accepted project reference data.

---

### 21.4 Fixture Requirements

Every fixture SHALL:

- possess a stable identifier;
- possess a documented purpose;
- specify the applicable Rule Pack;
- specify the supported document version;
- specify expected validation outcome;
- specify expected derived values where applicable.

Fixtures SHALL remain deterministic.

---

### 21.5 Golden References

A Golden Reference consists of:

- fixture;
- expected derived values;
- expected Validation Report;
- expected serialized representation where applicable.

Golden References SHALL remain immutable following approval.

Changes SHALL require explicit review and updated regression baselines.

---

### 21.6 Fixture Versioning

Fixtures SHALL specify:

- fixture version;
- supported Rule Pack version;
- migration status;
- originating specification version.

Older fixtures SHOULD remain available whenever practical.

---

### 21.7 Reproducibility

Every automated fixture SHALL be reproducible using version-controlled project artifacts.

Fixture generation procedures SHOULD themselves be documented.

---

### 21.8 Fixture Maintenance

Fixture modifications SHALL require:

- updated documentation;
- regression review;
- updated expected results;
- reviewer approval.

Historical revisions SHOULD remain traceable.

---

### 21.9 Outputs

Fixture management SHALL produce:

- fixture catalogue;
- Golden Reference registry;
- fixture revision history.

---

## Chapter 22 — Coverage, Continuous Integration and Quality Metrics

### 22.1 Purpose

This chapter defines project-wide quality metrics and Continuous Integration requirements.

---

### 22.2 Scope

This chapter governs:

- automated testing;
- coverage;
- Continuous Integration;
- quality gates;
- build verification.

---

### 22.3 Coverage Requirements

The project SHOULD maintain measurable coverage for:

- Rule Engine;
- Validation Engine;
- Rule Assets;
- serialization;
- import and migration.

Coverage SHALL be evaluated continuously.

Coverage targets MAY evolve as the project matures.

---

### 22.4 Continuous Integration

Every change SHOULD trigger automated verification including:

- build;
- automated tests;
- regression suite;
- Rule Pack validation;
- fixture verification.

No manual intervention SHOULD be required.

---

### 22.5 Quality Gates

Changes SHOULD NOT be merged unless:

- automated tests pass;
- no unexpected regressions exist;
- documentation remains current;
- generated artifacts remain reproducible.

---

### 22.6 Quality Metrics

The project SHOULD monitor:

- test pass rate;
- regression stability;
- validation stability;
- Rule Pack integrity;
- deterministic behavior.

Performance metrics SHOULD additionally be monitored where practical.

---

### 22.7 Reporting

Continuous Integration SHOULD generate reports including:

- build status;
- coverage summary;
- regression summary;
- fixture summary;
- quality gate status.

---

### 22.8 Assumptions

Continuous Integration assumes:

- deterministic build environments;
- version-controlled Rule Packs;
- immutable released fixtures;
- reproducible test execution.

Violations of these assumptions SHOULD be reported explicitly.

---

## Chapter 23 — Acceptance Criteria and Definition of Quality

### 23.1 Purpose

This chapter defines the project-wide criteria used to determine implementation quality.

---

### 23.2 Scope

Quality SHALL be evaluated across:

- implementation;
- validation;
- testing;
- documentation;
- maintainability.

---

### 23.3 Functional Quality

A feature SHALL satisfy:

- documented requirements;
- deterministic behavior;
- successful validation;
- successful testing.

---

### 23.4 Documentation Quality

Every implemented feature SHALL possess corresponding documentation.

Documentation SHALL remain synchronized with implementation.

No documented behavior SHALL knowingly contradict software behavior.

---

### 23.5 Maintainability

The implementation SHOULD exhibit:

- low duplication;
- explicit responsibilities;
- deterministic interfaces;
- stable public contracts.

---

### 23.6 Definition of Quality

The project SHALL consider software to be of acceptable quality when:

- all required specifications are satisfied;
- Validation Reports remain deterministic;
- Rule Engine calculations remain deterministic;
- Rule Assets validate successfully;
- automated testing passes;
- approved regression baselines remain valid.

---

### 23.7 Specification Failures vs. Implementation Failures

The project distinguishes between:

**Specification Failures**

Contradictions, omissions or ambiguities within the project specifications.

**Implementation Failures**

Defects within the software implementation.

These SHALL be tracked independently.

Implementation SHALL NOT silently compensate for Specification Failures.

---

### 23.8 Release Readiness

A release SHALL be considered ready when:

- all mandatory testing succeeds;
- Golden References pass;
- documentation is current;
- known limitations are documented;
- outstanding Implementation Failures are reviewed.

---

### 23.9 Outputs

Quality assessment SHALL produce:

- release assessment;
- quality summary;
- outstanding issue list.

---

## Chapter 24 — Open Decisions, Deferred Tests and Future Work

### 24.1 Purpose

This chapter documents known uncertainties, deferred implementation work and planned future testing.

Maintaining explicit records of unresolved issues SHALL be preferred over implicit assumptions.

---

### 24.2 Scope

This chapter governs:

- Open Decisions;
- official rules ambiguities;
- Deferred Tests;
- unsupported behavior;
- future project evolution.

---

### 24.3 Open Decisions

Open Decisions SHALL identify:

- decision identifier;
- affected subsystem;
- current implementation;
- alternative interpretations;
- rationale;
- review status.

Open Decisions SHALL remain traceable.

---

### 24.4 Official Rules Ambiguities

Where official Mortasheen publications permit multiple reasonable interpretations, the project SHALL:

- document every known interpretation;
- identify the interpretation implemented by the active Rule Pack;
- preserve traceability to the original source material.

Future Rule Packs MAY implement alternative interpretations.

---

### 24.5 Deferred Tests

Deferred Tests SHALL include:

- reason for deferral;
- affected subsystem;
- planned milestone;
- associated Validation Rules where applicable.

Deferred Tests SHALL remain visible throughout project documentation.

---

### 24.6 Unsupported Behavior

Unsupported behavior represents functionality intentionally outside the scope of the current implementation.

Unsupported behavior SHALL be distinguished from:

- illegal construction;
- implementation failures;
- specification failures.

Where practical, unsupported behavior SHOULD generate explicit Diagnostics.

---

### 24.7 Future Work

Future work MAY include:

- additional Rule Packs;
- official expansions;
- localization;
- plugin support;
- editor enhancements;
- additional export formats;
- performance improvements.

Future work SHALL NOT modify existing specifications without documented review.

---

### 24.8 Traceability

Every Open Decision SHOULD reference:

- originating specification;
- affected Rule Assets;
- applicable Validation Rules;
- Rule Pack version where applicable;
- official source material where practical.

Traceability SHALL remain preserved across future revisions.

---

### 24.9 Project Evolution

Future project evolution SHOULD preserve:

- deterministic behavior;
- backwards compatibility where supported;
- stable Validation Codes;
- stable Rule Asset identifiers;
- reproducible testing.

---

### 24.10 Outputs

Project maintenance SHALL produce:

- Open Decision register;
- Deferred Test register;
- Future Work register;
- Unsupported Behavior register;
- revision history.
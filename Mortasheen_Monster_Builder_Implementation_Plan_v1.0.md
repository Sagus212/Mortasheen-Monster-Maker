# Mortasheen Monster Builder

# Implementation Plan

Version 1.0

---

## Purpose

This document defines the recommended implementation order for the Mortasheen Monster Builder.

Unlike the software specifications, this document is **not** normative regarding application behavior.

Its purpose is to guide implementation work while minimizing architectural risk.

Whenever this document conflicts with an architectural specification, the architectural specification SHALL take precedence.

---

# Part I — General Principles

---

## 1. Implementation Philosophy

The software SHALL be implemented incrementally.

Each phase SHALL produce a working application.

The application SHALL remain buildable after every completed milestone.

Large architectural rewrites SHOULD be avoided.

---

## 2. Specification Authority

The following documents are authoritative.

1. Master Software Design Specification
2. Data Model Specification
3. Rule Engine Specification
4. Rules Asset Specification
5. Validation & Testing Specification
6. UI, Export & Development Workflow Specification

Implementation SHALL conform to these specifications.

If documentation appears ambiguous, the implementation SHALL preserve the ambiguity explicitly rather than inventing new architecture.

---

## 3. Architectural Constraints

Implementation SHALL preserve:

- deterministic behavior;
- immutable Rule Assets;
- canonical ownership;
- Rule Traces;
- Build Context separation;
- Validation Engine independence;
- serialization boundaries.

Implementation SHALL NOT merge architectural responsibilities.

---

# Part II — Recommended Development Order

---

## Phase 1 — Foundation

Implement the project infrastructure.

Objectives:

- project structure;
- build system;
- dependency management;
- logging;
- configuration;
- localization infrastructure.

Deliverable:

A buildable application with no gameplay functionality.

---

## Phase 2 — Core Data Model

Implement the complete persistent model.

Includes:

- Monster Document;
- Monster aggregate;
- Build Context;
- Provenance;
- Rule Asset references;
- serialization interfaces;
- stable identifiers.

Deliverable:

Monster Documents can be loaded, saved and manipulated.

No gameplay calculations yet.

---

## Phase 3 — Rule Asset System

Implement Rule Pack loading.

Includes:

- Rule Pack loader;
- schema validation;
- identifier resolution;
- source references;
- localization;
- version compatibility.

Deliverable:

Rule Assets are available to the application.

---

## Phase 4 — Rule Engine

Implement deterministic evaluation.

Priority order:

1. dependency graph;
2. calculator interfaces;
3. calculator catalogue;
4. Rule Traces;
5. Derived Monster Snapshot.

Deliverable:

Complete gameplay calculations.

---

## Phase 5 — Validation Engine

Implement legality evaluation.

Includes:

- validation phases;
- validation catalogue;
- diagnostics;
- strictness modes.

Deliverable:

Illegal monsters are detected correctly.

---

## Phase 6 — Editor

Implement the Monster Builder.

Includes:

- editing;
- undo/redo;
- search;
- filtering;
- navigation;
- panel layout.

Gameplay calculations SHOULD already exist.

---

## Phase 7 — Export

Implement presentation.

Includes:

- printable sheets;
- summaries;
- export formats;
- generated notation;
- generated rules text.

The Rule Engine SHALL remain the source of gameplay truth.

---

## Phase 8 — Performance

Optimize only after correctness.

Possible work:

- memoization;
- cache optimization;
- incremental recalculation;
- lazy loading.

Behavior SHALL remain unchanged.

---

## Phase 9 — Polish

Improve usability.

Examples:

- animations;
- visual polish;
- keyboard shortcuts;
- accessibility;
- documentation.

No architectural changes SHOULD occur during this phase.

---

# Part III — Milestones

---

## Milestone A

Completed when:

- application builds;
- Data Model exists;
- Rule Assets load.

---

## Milestone B

Completed when:

- Rule Engine evaluates correctly;
- Rule Traces work;
- Derived Data is produced.

---

## Milestone C

Completed when:

- Validation Engine functions;
- diagnostics appear;
- editor is usable.

---

## Milestone D

Completed when:

- export functions correctly;
- generated summaries work;
- save/load is stable.

---

## Milestone E

Completed when:

- Golden Fixtures pass;
- regression tests pass;
- performance is acceptable.

This milestone represents feature completeness.

---

# Part IV — Codex Working Guidelines

---

## 1. General Rules

When implementing this project:

- preserve architectural boundaries;
- avoid speculative behavior;
- expose ambiguity explicitly;
- prefer data-driven solutions;
- preserve determinism.

---

## 2. When Documentation Is Ambiguous

Codex SHALL NOT invent gameplay rules.

Instead:

- preserve authored data;
- generate diagnostics;
- document assumptions;
- request clarification when necessary.

---

## 3. When Documentation Appears Incomplete

Codex SHOULD first determine whether another specification already defines the missing behavior.

Only if no specification provides the answer SHOULD clarification be requested.

---

## 4. Refactoring

Refactoring SHALL:

- preserve observable behavior;
- preserve Rule Traces;
- preserve validation results;
- preserve serialization compatibility.

Architectural changes SHALL require explicit approval.

---

## 5. Testing

Every completed subsystem SHOULD include automated tests.

Golden Fixtures SHALL never be modified merely to satisfy implementation.

If implementation and fixtures disagree, implementation SHALL be corrected unless the specifications are revised.

---

# Part V — Success Criteria

The project is considered complete when:

- all authoritative specifications are implemented;
- all Rule Engine calculators pass tests;
- Validation Engine passes regression tests;
- serialization is stable;
- Golden Fixtures pass unchanged;
- generated output matches specification;
- architectural boundaries remain intact.

---

## Implementation Checklist

Foundation

- [ ] Project structure
- [ ] Build system
- [ ] Logging
- [ ] Configuration

Data Model

- [ ] Persistent entities
- [ ] Serialization
- [ ] Build Context
- [ ] Provenance

Rule Assets

- [ ] Rule Pack loading
- [ ] Schema validation
- [ ] Identifier resolution

Rule Engine

- [ ] Dependency graph
- [ ] Calculators
- [ ] Rule Traces
- [ ] Derived Data

Validation

- [ ] Validation phases
- [ ] Diagnostics
- [ ] Strictness modes

Editor

- [ ] Monster editing
- [ ] Search
- [ ] Undo/Redo

Export

- [ ] Character sheet
- [ ] Generated notation
- [ ] Generated rules text

Quality

- [ ] Golden Fixtures
- [ ] Regression tests
- [ ] Performance optimization

---

## Document Status

This document defines the recommended implementation sequence for the Mortasheen Monster Builder.

It complements the architectural specifications but does not replace them.

Future revisions SHOULD update this document only when implementation priorities change.
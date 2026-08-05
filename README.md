# Mortasheen-Monster-Maker

> **Status:** Architecture Frozen — Version 1.1
>
> The documents in this repository define the canonical architecture of the Mortasheen Monster Builder. Unless an official Mortasheen rule changes or a verified architectural defect is discovered during implementation, these specifications should be treated as immutable.

# Mortasheen Monster Builder

## Project Overview

The Mortasheen Monster Builder is a deterministic application for creating, validating, evaluating and exporting monsters compatible with the **Mortasheen** tabletop roleplaying game.

This repository contains the complete software architecture required to implement the application.

The specifications contained herein are intended to be treated as the authoritative source of truth.

Implementation should derive from these specifications rather than redesigning or extending them.

---

# Repository Contents

| Document | Purpose |
|----------|---------|
| Master Software Design Specification | Overall architecture and subsystem boundaries. |
| Data Model Specification | Canonical persistent entities and serialization contracts. |
| Rule Engine Specification | Deterministic gameplay calculations. |
| Rules Asset Specification | Structure of Rule Packs and gameplay assets. |
| Validation & Testing Specification | Validation architecture and testing requirements. |
| UI, Export & Development Workflow Specification | User interface, import/export and workflow. |
| Implementation Plan | Recommended implementation sequence. |

---

# Reading Order

Implementation SHOULD begin by reading the specifications in the following order:

1. Master Software Design Specification
2. Data Model Specification
3. Rule Engine Specification
4. Rules Asset Specification
5. Validation & Testing Specification
6. UI, Export & Development Workflow Specification
7. Implementation Plan

The earlier documents define architectural boundaries upon which the later documents depend.

---

# Specification Authority

The documentation is organized hierarchically.

When two specifications appear to disagree, authority SHALL be interpreted in the following order.

1. Master Software Design Specification
2. Data Model Specification
3. Rule Engine Specification
4. Rules Asset Specification
5. Validation & Testing Specification
6. UI, Export & Development Workflow Specification
7. Implementation Plan

The Implementation Plan is advisory.

All other listed specifications are normative.

---

# Design Philosophy

The project follows several fundamental architectural principles.

- deterministic evaluation;
- immutable Rule Assets;
- explicit ownership;
- separation of persistent and derived data;
- data-driven gameplay;
- explainable calculations through Rule Traces;
- modular subsystem boundaries;
- implementation independence.

These principles SHALL be preserved throughout implementation.

---

# Architectural Overview

The major subsystems are:

```text
                Rule Packs
                     │
                     ▼
              Rule Asset System
                     │
                     ▼
             Monster Document
                     │
                     ▼
               Rule Engine
                     │
                     ▼
            Derived Monster Data
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
 Validation Engine       Export System
          │                     │
          └──────────┬──────────┘
                     ▼
                User Interface
```

Each subsystem has a clearly defined responsibility.

Subsystems SHALL communicate only through their documented contracts.

---

# Implementation Rules

During implementation:

- preserve subsystem boundaries;
- preserve deterministic behavior;
- preserve canonical ownership;
- preserve Rule Traces;
- preserve Validation Engine independence;
- preserve serialization contracts;
- preserve Build Context separation.

Implementation SHALL NOT merge responsibilities between specifications.

---

# Handling Ambiguity

If official Mortasheen rules are ambiguous:

- preserve the ambiguity;
- expose diagnostics;
- preserve Rule Traces;
- implement documented Architectural Decisions.

Do not silently invent gameplay rules.

---

# Handling Unsupported Features

Unsupported mechanics SHALL:

- preserve authored data;
- generate diagnostics;
- remain editable whenever practical;
- avoid silent data loss.

Unknown Rule Assets SHALL NOT be deleted.

---

# Rule Packs

Gameplay content is intentionally separated from engine logic.

The application SHALL support loading Rule Packs independently of the Rule Engine.

Rule Packs define:

- Monster Classes;
- Sizes;
- Ability Blocks;
- Descriptors;
- Weaknesses;
- Conditions;
- Damage Types;
- other gameplay assets.

The Rule Engine interprets Rule Packs but does not own them.

---

# Determinism

Equivalent inputs SHALL always produce semantically equivalent outputs.

Observable behavior SHALL remain independent of:

- operating system;
- execution order;
- caching strategy;
- implementation language;
- internal optimizations.

---

# Validation Philosophy

The Validation Engine consumes Rule Engine results.

The Validation Engine SHALL NOT duplicate gameplay calculations.

Validation diagnostics SHOULD identify authored entities whenever possible.

---

# Testing Philosophy

Correctness takes priority over optimization.

Golden Fixtures are authoritative.

Implementation SHALL be corrected to match validated fixtures unless the specifications themselves are revised.

---

# Refactoring

Refactoring is encouraged when it:

- improves maintainability;
- improves readability;
- improves performance;
- reduces complexity.

Refactoring SHALL NOT change observable behavior without an accompanying specification revision.

---

# Scope Control

The specifications intentionally define the architecture completely.

Implementation SHOULD avoid introducing additional architectural layers unless explicitly approved.

When functionality appears to be missing, first determine whether another specification already defines it.

---

# Expected Workflow

The recommended implementation sequence is:

1. Project infrastructure.
2. Data Model.
3. Rule Asset loader.
4. Rule Engine.
5. Validation Engine.
6. Editor.
7. Export.
8. Performance optimization.
9. Polish.

The application SHOULD remain buildable after each completed phase.

---

# Success Criteria

The project is considered successfully implemented when:

- all specifications are satisfied;
- Rule Engine calculations are deterministic;
- Validation behaves according to specification;
- serialization is stable;
- Golden Fixtures pass unchanged;
- subsystem boundaries remain intact.

---

# Final Note

This repository represents the complete architectural definition of the Mortasheen Monster Builder.

The objective of implementation is to realize this architecture faithfully, not to redesign it.

When uncertainty exists, preserve documented behavior, expose ambiguity explicitly, and prefer clarification over speculative implementation.

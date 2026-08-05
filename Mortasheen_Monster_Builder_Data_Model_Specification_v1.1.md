# Mortasheen Monster Builder

## Data Model Specification

Version 1.0

> Canonical data model specification for the Mortasheen Monster Builder.

This document defines the canonical information model used throughout the project.

Unless explicitly stated otherwise, data structures described here are authoritative and referenced by all subordinate specifications.

This specification defines:

- persistent entities;
- derived data;
- ownership rules;
- serialization structure;
- relationships between entities.

This specification intentionally avoids programming-language-specific implementation details.

This specification complements, but does not replace:

- Master Software Design Specification
- Rule Engine Specification
- Rules Asset Specification
- Validation & Testing Specification
- UI, Export & Development Workflow Specification

---

# Part I — Foundations

---

## Chapter 1 — Purpose and Scope

### 1.1 Purpose

This specification defines the canonical data model used throughout the Mortasheen Monster Builder.

The data model establishes the structure, ownership and relationships of project information independently of implementation details.

---

### 1.2 Scope

This specification governs:

- persistent project entities;
- derived data;
- ownership relationships;
- serialization;
- compatibility requirements;
- data integrity.

This specification SHALL NOT define gameplay calculations.

Those are defined by the Rule Engine Specification.

This specification SHALL NOT define construction legality.

Those are defined by the Validation & Testing Specification.

---

### 1.3 Objectives

The data model SHALL provide:

- deterministic structure;
- implementation independence;
- serialization stability;
- Rule Pack compatibility;
- extensibility.

---

### 1.4 Authoritative Responsibility

This specification is the authoritative source describing:

- persistent project data;
- relationships between project entities;
- ownership rules;
- serialization structure.

Other specifications SHALL reference these concepts rather than redefine them.

---

### 1.5 Outputs

This specification establishes the canonical data model used throughout the project.

---

## Chapter 2 — Modeling Principles

### 2.1 Purpose

This chapter defines the principles governing every persistent entity.

---

### 2.2 Canonical Data

Every persistent entity SHALL possess exactly one canonical representation.

Derived information SHALL NOT replace authored project data.

---

### 2.3 Determinism

Equivalent Monster Documents SHALL always contain equivalent persistent information.

Persistent entities SHALL remain deterministic.

---

### 2.4 Separation of Concerns

The data model describes information only.

The data model SHALL NOT define:

- calculations;
- validation;
- user interface behavior.

---

### 2.5 Extensibility

Persistent entities SHOULD permit future expansion without invalidating existing serialized documents whenever practical.

---

### 2.6 Stability

Persistent identifiers SHALL remain stable across compatible document versions.

---

### 2.7 Ownership

Every persistent entity SHALL possess one clearly defined owner.

Ownership relationships SHALL remain unambiguous.

---

### 2.8 Outputs

These principles govern every entity defined by this specification.

---

## Chapter 3 — Data Categories

### 3.1 Purpose

This chapter defines the categories of information represented within the project.

---

### 3.2 Categories

Project information belongs to one of the following categories:

- persistent authored data;
- derived data;
- Rule Assets;
- metadata;
- temporary interface state.

---

### 3.3 Information Layers

| Layer | Examples | Responsibility |
|---|---|---|
| Rule Assets | Monster Classes, Sizes, Ability Block definitions, Descriptors and Damage Types | Versioned, mostly read-only data supplied by the application or a community Rule Pack. |
| User-authored build data | Monster name, selected Class and Size, Parts, Endurance allocation, Abilities, Weaknesses and notes | Persisted within a Monster Document. |
| Context data | Scientist attributes, Respectability, known Ability Blocks, YGORE status and permissions | Persisted as Build Context or through a linked Scientist Profile. |
| Derived Data | Budgets, scanalysis goal, project goal, data cost, legal status and generated rules text | Never authoritative in saves; recomputed from Rule Assets and user-authored data. |
| Presentation data | Panel state, sort order, expanded sections and theme | Stored separately from the Monster Document whenever practical. |

---

### 3.4 Persistent Authored Data

Persistent authored data represents information explicitly created or modified by the user.

Persistent authored data SHALL be serialized.

---

### 3.5 Derived Data

Derived data represents information calculated from authored data.

Derived data SHALL NOT become authoritative project information.

---

### 3.6 Rule Assets

Rule Assets represent immutable gameplay definitions supplied by Rule Packs.

Rule Assets SHALL remain external to Monster Documents.

---

### 3.7 Metadata

Metadata describes project information without modifying gameplay semantics.

Examples include:

- document version;
- creation metadata;
- modification metadata.

---

### 3.8 Temporary Interface State

Temporary interface state belongs to the user interface.

Temporary interface state SHALL NOT be serialized as part of Monster Documents.

---

### 3.9 Outputs

Every project datum SHALL belong to one data category.

---

## Chapter 4 — Persistent Entity Principles

### 4.1 Purpose

This chapter defines common requirements shared by every persistent entity.

---

### 4.2 Entity Identity

Every persistent entity SHALL possess a stable identity.

Identity SHALL remain independent from physical storage.

---

### 4.3 Entity Lifetime

Persistent entities exist independently of user interface state.

Entities SHALL remain valid after serialization and reconstruction.

---

### 4.4 Entity Relationships

Relationships between entities SHALL be explicitly represented.

Implicit ownership SHOULD be avoided.

---

### 4.5 Mutability

Persistent entities MAY be modified only through documented project workflows.

Rule Assets SHALL remain immutable.

---

### 4.6 Version Compatibility

Persistent entities SHALL identify compatibility requirements where applicable.

---

### 4.7 Outputs

Persistent entities SHALL satisfy the common requirements defined by this chapter.

---

## Chapter 5 — Ownership Model

### 5.1 Purpose

This chapter defines ownership relationships throughout the project.

---

### 5.2 Ownership Principles

Every persistent entity SHALL possess exactly one owner.

Ownership SHALL determine:

- lifetime;
- serialization;
- persistence.

---

### 5.3 Containment

Contained entities SHALL NOT exist independently of their owner.

Removing an owner SHALL remove all contained entities.

---

### 5.4 References

Entities MAY reference information owned elsewhere.

References SHALL NOT imply ownership.

---

### 5.5 Shared Information

Information shared between multiple entities SHOULD be represented through references rather than duplication.

---

### 5.6 Outputs

Ownership relationships SHALL remain deterministic and unambiguous.

---

## Chapter 6 — Persistent Identifiers

### 6.1 Purpose

This chapter defines identifier requirements for persistent entities.

---

### 6.2 Identifier Principles

Persistent identifiers SHALL:

- remain unique;
- remain stable;
- remain deterministic.

Identifiers SHALL NOT encode transient implementation information.

---

### 6.3 Human Readability

Identifiers SHOULD remain understandable by developers whenever practical.

Human readability SHALL NOT override stability.

---

### 6.4 Rule Asset References

Persistent entities SHALL reference Rule Assets using stable Rule Asset identifiers.

Serialized documents SHALL NOT duplicate Rule Asset definitions.

---

### 6.5 Identifier Evolution

Identifier changes SHALL occur only when compatibility rules explicitly permit.

Migration SHALL preserve semantic meaning.

---

### 6.6 Outputs

Persistent identifiers SHALL support reliable serialization and long-term compatibility.

---

## Chapter 7 — Serialization Principles

### 7.1 Purpose

This chapter defines principles governing serialization.

---

### 7.2 Serialization Goals

Serialization SHALL preserve:

- authored data;
- ownership relationships;
- identifiers;
- metadata.

Serialization SHALL NOT preserve temporary interface state.

---

### 7.3 Canonical Representation

Equivalent Monster Documents SHALL produce semantically equivalent serialized representations.

Serialization SHALL remain deterministic.

---

### 7.4 Compatibility

Serialized documents SHALL identify:

- document version;
- Rule Pack;
- compatibility information.

---

### 7.5 Reconstruction

Deserialization SHALL reconstruct equivalent persistent entities.

Derived Data SHALL be recalculated following reconstruction.

A persisted `derivedSnapshot`, where supported, SHALL be treated only as a disposable cache.

It SHALL NOT replace recalculation or become authoritative project data.

---

### 7.6 Validation

Imported serialized documents SHALL remain subject to Validation Rules before editing.

---

### 7.7 Outputs

Serialization SHALL produce portable, deterministic and version-compatible project documents.

---

# Part II — Persistent Entities

---

## Chapter 8 — Monster Document

### 8.1 Purpose

This chapter defines the Monster Document, the canonical persisted representation of a user-created monster.

The Monster Document SHALL be the primary persistent entity of the Mortasheen Monster Builder.

---

### 8.2 Aggregate Overview

```text
MonsterDocument
├── schemaVersion
├── documentId
├── rulesetRef
├── buildContext
├── monster
│   ├── identity
│   ├── classSelection
│   ├── sizeId
│   ├── attributes
│   ├── core
│   ├── parts[]
│   │   └── ability?
│   │       └── blockInstances[]
│   ├── weaknesses[]
│   ├── classCustomization
│   └── notes
├── provenance
├── extensions
└── derivedSnapshot?
```

The `MonsterDocument` is the persistence root.

The Monster entity is the rules-facing aggregate. Every Part, Ability, Weakness and Class Customization belongs to exactly one Monster Document unless explicitly copied into a template library.

---

### 8.3 Responsibility

The Monster Document is the versioned file exchanged through structured import and export and stored by the persistence layer.

It contains authored state and references the Rule Pack required to interpret that state.

### 8.4 Monster Document Contract

| Field | Type | Required | Persisted? | Meaning / Rule Role |
|---|---|---:|---:|---|
| `schemaVersion` | `string` | Yes | Yes | Version of the save-file contract, for example `1.0.0`. |
| `documentId` | `UUID` | Yes | Yes | Stable identity used for autosave, duplication and possible future synchronization. |
| `rulesetRef` | `RulesetReference` | Yes | Yes | Identifies the official or community Rule Pack and its version. |
| `buildContext` | `BuildContext` or reference | Yes | Yes | Scientist and build permissions used to validate construction. |
| `monster` | `Monster` | Yes | Yes | The authored Monster aggregate. |
| `provenance` | `Provenance` | Yes | Yes | Creation and update timestamps plus import or template lineage. |
| `extensions` | `Record<string, unknown>` | No | Yes | Namespaced forward-compatible extension payloads. |
| `derivedSnapshot` | `DerivedMonsterSnapshot` | No | Optional cache only | May accelerate display, but SHALL be discarded when the Rule Pack, document version or engine version differs. |

---

### 8.5 Derived Snapshot Rule

A persisted `derivedSnapshot`, when supported, is a disposable cache rather than authored project data.

It SHALL NOT be treated as authoritative.

---

### 8.6 Lifetime

A Monster Document exists independently of:

- UI State;
- application session;
- implementation language;
- physical storage technology.

---

### 8.7 Serialization

The Monster Document SHALL be serializable according to Chapter 7.

Unknown namespaced extension fields SHALL be preserved whenever the active schema permits them.

---

## Chapter 9 — Monster Definition

### 9.1 Purpose

The Monster Definition represents the authored description of a single monster.

For the purposes of this specification, the **Monster Definition** is represented by the canonical `Monster` aggregate.

“Monster Definition” describes the aggregate's architectural role; `Monster` is its canonical data type name.

---

### 9.2 Monster Aggregate Contract

| Field | Type | Required | Persisted? | Meaning / Rule Role |
|---|---|---:|---:|---|
| `id` | `UUID` | Yes | Yes | Stable monster identity. |
| `identity` | `MonsterIdentity` | Yes | Yes | Name, species label, epithet and descriptive text. |
| `classSelection` | `MonsterClassSelection` | Yes | Yes | Selected Class and optional Subclass or Core Feature choice. |
| `sizeId` | `string` | Yes | Yes | Reference to a `SizeDefinition`. |
| `attributes` | `MonsterAttributes` | Yes | Yes | Brains and Brawn allocations. |
| `core` | `MonsterCore` | Yes | Yes | Mandatory one-Endurance Core and selected Class feature. |
| `parts` | `MonsterPart[]` | Yes | Yes | Ordinary body Parts. The Core is excluded from this collection. |
| `weaknesses` | `Weakness[]` | No | Yes | Monster-wide Weaknesses that may grant Ability points. |
| `classCustomization` | `ClassCustomization` | Varies | Yes | Quirk, affinity, brain, obsession or another Class-specific payload. |
| `temperament` | `string` or future structured type | No | Yes | Descriptive temperament. Scanalysis reveals it, but the reviewed construction pages do not fully structure it. |
| `tags` | `string[]` | No | Yes | User organization only; no rules effect. |
| `notes` | `string` | No | Yes | Freeform design notes. |
| `extensions` | `Record<string, unknown>` | No | Yes | Namespaced Class-specific or community Rule Pack data. |

---

### 9.3 Monster Identity Contract

| Field | Type | Required | Persisted? | Meaning / Rule Role |
|---|---|---:|---:|---|
| `name` | `string` | Yes for export | Yes | Individual or species name. |
| `speciesName` | `string` | No | Yes | Optional distinction between the built individual and a template species. |
| `epithet` | `string` | No | Yes | Subtitle or nickname. |
| `description` | `string` | No | Yes | Appearance or anatomy narrative. |
| `behavior` | `string` | No | Yes | Behavioral description. |
| `portraitAssetId` | `string` | No | Yes | Reference to a user-supplied image. Copyrighted rulebook artwork SHALL NOT be embedded by default. |

---

### 9.4 Monster Core Contract

| Field | Type | Required | Persisted? | Meaning / Rule Role |
|---|---|---:|---:|---|
| `id` | `UUID` | Yes | Yes | Stable Core instance identifier. |
| `name` | `string` | Yes | Yes | Default “Core” or user-facing anatomical name. |
| `endurance` | literal `1` | Yes | Yes | Invariant under the reviewed Core rules. |
| `coreFeatureId` | `string` | Yes | Yes | Class feature applied to this monster. |
| `status` | `intact` or `broken` | No in builder | Runtime only | Reserved for later sheets or combat. The builder normally treats the Core as intact. |
| `notes` | `string` | No | Yes | Flavor-only text. |

The Core:

- does not consume the ordinary Part limit;
- always possesses exactly one Endurance;
- cannot receive additional Endurance under the ordinary construction model;
- cannot hold an ordinary Part Ability;
- causes the monster to die when broken under the reviewed rules.

---

### 9.5 Core Feature Definition Contract

`CoreFeatureDefinition` is a Rule Asset contract referenced here for data-model completeness.

| Field | Type | Required | Storage Role | Meaning / Rule Role |
|---|---|---:|---|---|
| `id` | `string` | Yes | Rule Assets | Stable Core Feature identifier. |
| `name` | `LocalizedString` | Yes | Rule Assets | Feature display name. |
| `summary` | `LocalizedString` | Yes | Rule Assets | Short generated-stat-block text. |
| `mechanicKind` | `declarative`, `ruleHook` or `hybrid` | Yes | Rule Assets | Indicates whether generic data can express the effect. |
| `parameters` | JSON schema | No | Rule Assets | Class-specific structured configuration. |
| `source` | `SourceReference` | Yes | Rule Assets | Rulebook traceability. |

---

### 9.6 Class Customization Contract

Class-specific information SHALL NOT be flattened into large numbers of nullable fields on `Monster`.

Each Monster Class definition references a customization schema, and the Monster stores one tagged payload.

```ts
type ClassCustomization =
    | {
          kind: "bioconstructQuirk";
          quirk: MonsterQuirk;
      }
    | {
          kind: "biomechBrain";
          brain: GadgetReference | EmbeddedBrainDraft;
      }
    | {
          kind: "arthropoidDebateTrack";
          enabled: true;
      }
    | {
          kind: "biogarbageWeaknessRule";
          acknowledged: true;
      }
    | {
          kind: "generic";
          schemaId: string;
          values: Record<string, unknown>;
      };
```

Only Class structures confirmed by reviewed official sources SHOULD be implemented initially.

Other Class customization payloads SHALL remain pending until their exact rules are encoded and reviewed.

---

### 9.7 Ownership

The Monster aggregate SHALL be owned by exactly one Monster Document.

Its contained Parts, Abilities, Weaknesses and Class Customization SHALL NOT share mutable instances with another Monster aggregate.

---

### 9.8 Serialization

The Monster aggregate and all of its authored child entities SHALL serialize as part of the owning Monster Document.

---

## Chapter 10 — Build Context

### 10.1 Purpose

This chapter defines Build Context.

---

### 10.2 Responsibility

Monster limits depend upon the Scientist and upon how a monster is being obtained or modified.

The supported rules derive, among other values:

- ordinary Part limits from `SCIENCE!`;
- Endurance allowance from `10 + Respectability`;
- ordinary Attribute and Ability allowances from the applicable creation rules.

Build Context therefore belongs beside the Monster aggregate rather than inside the monster's anatomy.

---

### 10.3 Build Context Contract

| Field | Type | Required | Persisted? | Meaning / Rule Role |
|---|---|---:|---:|---|
| `mode` | `new`, `alter`, `recreate`, `template` or `sandbox` | Yes | Yes | Selects the applicable cost and legality pathways. |
| `scientistId` | `UUID` | No | Yes | Optional reference to a reusable `ScientistProfile`. |
| `science` | `integer` | Yes in strict mode | Yes | Determines the ordinary Part limit in the supported creation workflow. |
| `madness` | `integer` | Yes in strict mode | Yes | Compared with Monster Brains for disobedience warnings. |
| `genius` | `integer` | No for the core data model | Yes | Reserved for project rolls, gadgets and Class-specific subsystems. |
| `discourse` | `integer` | No | Yes | Reserved for active-command capacity and command checks. |
| `respectability` | `integer` | Yes | Yes | Feeds Endurance allowance and advanced-Class permissions. |
| `knownBlockIds` | `string[]` | Yes in strict mode | Yes | Ability Blocks available for manufactured positive Abilities. |
| `permissions` | `BuildPermission[]` | No | Yes | Discoveries, Class access, Titanic permission and explicit Proctor overrides. |
| `isYgore` | `boolean` | Yes | Yes | Activates YGORE-specific loyalty and alteration-cost handling. |
| `strictness` | `strict`, `guided` or `sandbox` | Yes | Yes | Controls whether illegal choices are blocked, warned about or merely annotated. |
| `originalMonsterRef` | `MonsterSnapshotRef` | Required for alteration | Yes | Baseline used for changed-point and changed-Endurance cost calculations. |

---

### 10.4 Ownership

Build Context SHALL belong to exactly one Monster Document unless represented through an explicitly referenced reusable Scientist Profile.

---

### 10.5 Lifetime

Build Context SHALL persist together with the Monster Document.

---

### 10.6 Relationships

The Rule Engine and Validation Engine MAY consume Build Context.

Build Context supplies inputs and permissions; it SHALL NOT implement gameplay calculations or legality rules.

---

### 10.7 Outputs

Build Context provides contextual information required for deterministic construction.

---

## Chapter 11 — Metadata

### 11.1 Purpose

Metadata describes project information without modifying gameplay semantics.

---

### 11.2 Responsibility

Metadata SHALL support:

- identification;
- versioning;
- traceability.

---

### 11.3 Metadata Categories

Metadata MAY include:

- document version;
- Rule Pack version;
- creation metadata;
- modification metadata;
- export information.

---

### 11.4 Provenance Contract

| Field | Type | Required | Persisted? | Meaning / Rule Role |
|---|---|---:|---:|---|
| `createdAt` | ISO timestamp | Yes | Yes | Document creation time. |
| `updatedAt` | ISO timestamp | Yes | Yes | Time of the last authored change. |
| `createdByAppVersion` | `string` | Yes | Yes | Debugging and migration aid. |
| `originType` | `blank`, `sample`, `bestiary`, `imported` or `duplicated` | Yes | Yes | Describes how the draft began. |
| `originRef` | `string` | No | Yes | Template, bestiary or source-document reference. |
| `originalSnapshotHash` | `string` | No | Yes | Identifies the baseline used by alteration calculations. |
| `originalSnapshot` | `MonsterSnapshot` | No | Yes or external reference | Required for deterministic offline alteration diffs unless a stable external template is guaranteed. |

---

### 11.5 Monster Snapshot Contract

A `MonsterSnapshot` is immutable.

It excludes:

- UI State;
- Validation Reports;
- disposable Derived Data.

It contains only the authored rules-facing Monster state needed to compare:

- changed points;
- Endurance;
- Parts;
- Monster Class;
- other supported alteration inputs.

---

### 11.6 Mutability

Provenance metadata MAY be updated by documented persistence, import, duplication and migration workflows.

Changing descriptive metadata SHALL NOT alter Monster construction semantics.

---

### 11.7 Outputs

Metadata and Provenance provide traceable descriptive information supporting persistence, alteration and compatibility.

---

## Chapter 12 — Rule Asset References

### 12.1 Purpose

This chapter defines references to Rule Assets.

---

### 12.2 Responsibility

Persistent entities SHALL reference Rule Assets rather than duplicate them.


---

### 12.3 Ruleset Reference Contract

| Field | Type | Required | Persisted? | Meaning / Rule Role |
|---|---|---:|---:|---|
| `rulesetId` | `string` | Yes | Yes | Stable Rule Pack identifier, for example `mortasheen-core`. |
| `rulesetVersion` | `SemVer` | Yes | Yes | Exact Rule Pack version used when the Monster Document was last saved. |
| `contentHash` | `string` | Recommended | Yes | Detects local Rule Asset changes under the same nominal version. |
| `sourceDocuments` | `SourceDocumentRef[]` | No | Yes | Human-readable references to rulebook or companion sources. |
| `locale` | `string` | No | Yes | Display locale for Rule Asset content. Identifiers remain locale-independent. |

---

### 12.4 Source Reference Contract

| Field | Type | Required | Storage Role | Meaning / Rule Role |
|---|---|---:|---|---|
| `sourceId` | `string` | Yes | Rule Assets | Stable source-document identifier. |
| `page` | `integer` or range | No | Rule Assets | Printed rulebook page, not PDF-array index. |
| `section` | `string` | No | Rule Assets | Relevant source section heading, such as “Abilities and Weaknesses.” |
| `note` | `string` | No | Rule Assets | Paraphrased implementation note. Long copyrighted passages SHALL NOT be embedded. |
| `confidence` | `confirmed`, `ambiguous` or `pending` | Yes | Rule Assets | Indicates whether the interpretation has been reviewed. |

---

### 12.5 Reference Requirements

Every Rule Asset reference SHALL:

- use a stable Rule Asset identifier;
- identify its originating Rule Pack;
- remain locale-independent;
- preserve unresolved identifiers rather than silently replacing or deleting them.

---

### 12.6 Independence

Removing a Rule Asset reference SHALL NOT modify the referenced Rule Asset.

Monster Documents SHALL NOT embed complete Rule Asset definitions.

---

### 12.7 Compatibility

References SHALL remain compatible according to Rule Pack migration rules.

A document opened with a different Rule Pack version SHALL be revalidated.

---

### 12.8 Outputs

Rule Asset references provide stable, traceable links between authored project data and immutable gameplay definitions.

---

## Chapter 13 — Persistent Collections

### 13.1 Purpose

This chapter defines collections owned by persistent entities.

---

### 13.2 Responsibility

Collections organize related child entities.

Collections SHALL preserve ownership relationships.

---

### 13.3 Collection Types

Persistent collections MAY contain:

- Parts;
- Ability Block Instances;
- Weaknesses;
- Weakness Block Instances;
- other owned persistent entities.

Rule Asset definitions SHALL remain external and SHALL NOT be owned through persistent collections.

---

### 13.4 Ordering

Collections MAY preserve authored ordering where required by project semantics.

Where ordering has no semantic or presentation meaning, implementations MAY use any deterministic internal representation.

---

### 13.5 Identity

Collection membership SHALL NOT replace individual entity identity.

Removing an entity from one collection and adding it to another SHALL NOT implicitly change its identity unless the applicable workflow explicitly creates a new entity.

---

### 13.6 Serialization

Persistent collections SHALL serialize together with their owning entity.

---

### 13.7 Outputs

Persistent collections provide deterministic organization of owned child entities.

---

## Chapter 14 — Entity Relationships

### 14.1 Purpose

This chapter defines the relationships between persistent entities.

---

### 14.2 Relationship Types

Persistent entities MAY relate through:

- ownership;
- containment;
- references;
- composition.

---

### 14.3 Ownership Relationships

Ownership determines:

- lifetime;
- serialization;
- persistence.

Ownership SHALL remain exclusive.

---

### 14.4 Reference Relationships

References SHALL:

- preserve independence;
- avoid duplication;
- maintain compatibility.

---

### 14.5 Composition

Composition combines multiple persistent entities into larger structures while preserving ownership.

---

### 14.6 Relationship Graph

The following diagram is an illustrative ownership graph:

```text
Monster Document
│
├── Monster
│   ├── Identity
│   ├── Class Selection
│   ├── Size Reference
│   ├── Attributes
│   ├── Core
│   ├── Parts[]
│   │   └── Ability?
│   │       └── Ability Block Instances[]
│   ├── Weaknesses[]
│   │   └── Weakness Block Instances[]
│   ├── Class Customization
│   └── Notes
│
├── Build Context
├── Provenance
└── Extensions
```

Rule Assets remain external and are referenced through stable identifiers.

---

### 14.7 Canonical Ownership Rules

- A Monster Document owns exactly one Monster aggregate.
- A Monster owns exactly one Core and zero or more ordinary Parts.
- A Monster Part owns at most one ordinary Monster Ability under the base construction model.
- A Monster Ability owns one or more Ability Block instances.
- Mutable Ability Block instances SHALL NOT be shared with another Ability.
- Weaknesses belong to the Monster rather than to a Part.
- Monster Class, Size, Ability Block, Descriptor, Condition, Movement Type and Damage Type definitions are referenced from Rule Assets.
- Derived objects MAY reference authored entity identifiers but SHALL NOT be edited directly.
- Template and bestiary entries are immutable sources.
- Editing a template or bestiary source creates a new Monster Document with Provenance and, where applicable, an alteration baseline.

---

### 14.8 Outputs

Entity relationships define the canonical persistent structure used throughout the project.

---

## Chapter 15 — Monster Class Selection

### 15.1 Purpose

This chapter defines how a Monster Definition references its selected Monster Class.

Monster Classes are Rule Assets and SHALL remain external to the Monster Document.

---

### 15.2 Monster Class Definition

Every Monster Class supplies a Core Feature and MAY impose unique rules.

The reviewed rules distinguish ordinary Classes, Postgrad Classes and Esoterrors. The Class tier also supplies the multiplier used by scanalysis and relevant project calculations.

`MonsterClassDefinition` is a Rule Asset contract.

| Field | Type | Required | Storage Role | Meaning / Rule Role |
|---|---|---:|---|---|
| `id` | `string` | Yes | Rule Assets | Stable Class identifier. |
| `name` | `LocalizedString` | Yes | Rule Assets | Display name. |
| `tier` | `standard`, `postgrad` or `esoterror` | Yes | Rule Assets | Access category. |
| `multiplier` | `2`, `3` or `4` | Yes | Rule Assets | Used by scanalysis and relevant project calculations. |
| `minimumRespectability` | `integer` | Yes | Rule Assets | Base access threshold before permissions or discoveries. |
| `creationPolicy` | `creatable`, `alter_only`, `recruit_only` or `special` | Yes | Rule Assets | Prevents unsupported construction workflows. |
| `coreFeatureId` | `string` | Yes | Rule Assets | Reference to `CoreFeatureDefinition`. |
| `subclasses` | `MonsterSubclassDefinition[]` | No | Rule Assets | Alternate Core Feature or Class variant. |
| `customizationSchemaId` | `string` | No | Rule Assets | References the schema for the Class-specific authored payload. |
| `ruleHooks` | `RuleHookRef[]` | No | Rule Assets | Named special-case evaluators, such as mandatory Weakness behavior. |
| `source` | `SourceReference` | Yes | Rule Assets | Official source traceability. |

---

### 15.3 Monster Class Selection Contract

| Field | Type | Required | Persisted? | Meaning / Rule Role |
|---|---|---:|---:|---|
| `classId` | `string` | Yes | Yes | Selected `MonsterClassDefinition`. |
| `subclassId` | `string` | No | Yes | Optional selected Subclass. |
| `coreFeatureId` | `string` | Derived or explicit | Yes | Usually derived, but persisted to preserve an intentional alternate Core Feature selection. |
| `overrideReason` | `string` | No | Yes | Required when sandbox or Proctor mode overrides Class restrictions. |

---

### 15.4 Ownership

The `Monster` aggregate owns the Class selection.

The active Rule Pack owns the referenced Class, Subclass and Core Feature definitions.

---

### 15.5 Compatibility

Changing Rule Packs MAY require migration of Class, Subclass or Core Feature references.

Migration SHALL preserve authored intent whenever practical.

---

### 15.6 Outputs

Monster Class Selection establishes the primary structural identity of the monster.

---

## Chapter 16 — Size Selection

### 16.1 Purpose

This chapter defines the selected Size of a monster.

Sizes are immutable Rule Assets referenced by the Monster Definition.

---

### 16.2 Responsibility

Size is selected by identifier and interpreted through versioned Rule Assets.

Size effects SHALL NOT be encoded as UI-component switch statements.

The Size definition carries its mechanical consequences, access restrictions and display examples.

---

### 16.3 Size Definition Contract

`SizeDefinition` is a Rule Asset contract.

| Field | Type | Required | Storage Role | Meaning / Rule Role |
|---|---|---:|---|---|
| `id` | `string` | Yes | Rule Assets | Stable identifier, for example `average`. |
| `name` | `LocalizedString` | Yes | Rule Assets | Display label. |
| `order` | `integer` | Yes | Rule Assets | Canonical ordering. |
| `baseRange` | number, fraction or `special` | Yes | Rule Assets | Size-derived range value. |
| `brawnDefenseModifier` | `DiceModifier` | Yes | Rule Assets | Size-derived adjustment against applicable Brawn attacks. |
| `minimumRespectability` | `integer` | No | Rule Assets | Access threshold where applicable. |
| `requiredPermission` | `string` | No | Rule Assets | For example, explicit Titanic permission or discovery. |
| `examples` | `string[]` | No | Rule Assets | Human-readable scale examples. |
| `source` | `SourceReference` | Yes | Rule Assets | Official source reference. |

---

### 16.4 Selection Representation

The Monster aggregate SHALL persist one `sizeId` referencing a `SizeDefinition`.

---

### 16.5 Ownership

The `Monster` aggregate owns the Size reference.

The active Rule Pack owns the Size definition.

---

### 16.6 Relationships

The selected Size MAY participate in:

- Rule Engine calculations;
- Validation Rules;
- permission checks;
- derived statistics.

---

### 16.7 Outputs

The Monster Definition records one selected Size.

---

## Chapter 17 — Attributes

### 17.1 Purpose

This chapter defines persistent Attribute allocation.

---

### 17.2 Responsibility

Brains and Brawn represent authored Attribute allocations.

Authored Attribute values SHALL be persisted.

Derived totals, allowances and warnings SHALL be recalculated.

---

### 17.3 Monster Attributes Contract

| Field | Type | Required | Persisted? | Meaning / Rule Role |
|---|---|---:|---:|---|
| `brains` | `integer` | Yes | Yes | Monster mental Attribute and command or disobedience input. |
| `brawn` | `integer` | Yes | Yes | Monster physical Attribute. |
| `allocationNotes` | `string` | No | Yes | Optional user-authored reasoning. |
| `total` | `integer` | Derived | No | `brains + brawn`. |
| `disobedienceRisk` | `boolean` | Derived | No | Context-sensitive warning determined by the Rule Engine, including YGORE and Class overrides. |
| `baseAbilityAllowance` | `integer` | Derived | No | Computed from the supported construction rules rather than manually entered. |
| `scanalysisGoal` | `integer` | Derived | No | `(Brains + Brawn) × Class multiplier`. |

---

### 17.4 Ownership

`MonsterAttributes` SHALL belong to exactly one Monster aggregate.

---

### 17.5 Relationships

Attributes MAY influence:

- Ability resolution;
- construction budgets;
- disobedience warnings;
- scanalysis;
- project costs;
- other derived values.

The calculations themselves remain authoritative in the Rule Engine Specification.

---

### 17.6 Outputs

Attributes preserve authored construction choices.

---

## Chapter 18 — Parts

### 18.1 Purpose

This chapter defines persistent Part selections.

---

### 18.2 Responsibility

Ordinary Parts are the principal anatomical units of a monster.

Each Part receives Endurance and, under the ordinary construction model, houses no more than one discrete Ability.

The Core is tracked separately and does not consume the ordinary Part limit.

---

### 18.3 Monster Part Contract

| Field | Type | Required | Persisted? | Meaning / Rule Role |
|---|---|---:|---:|---|
| `id` | `UUID` | Yes | Yes | Stable Part identity used by Abilities, alteration diffs and UI ordering. |
| `name` | `string` | Yes | Yes | Anatomical name. |
| `description` | `string` | No | Yes | Visual or biological description. |
| `endurance` | positive `integer` | Yes | Yes | Allocated Endurance boxes. |
| `ability` | `MonsterAbility` or `null` | No | Yes | The Part's single discrete Ability in the ordinary model. |
| `order` | `integer` | Yes | Yes | Authored presentation order. |
| `origin` | `created`, `installed`, `inherited` or `template` | Yes | Yes | Supports alteration and premade-Part calculations. |
| `sourcePartRef` | `string` | No | Yes | Reference to an original or template Part if copied or altered. |
| `locked` | `boolean` | No | Yes | Editor convenience for templates; no inherent gameplay meaning. |
| `notes` | `string` | No | Yes | Flavor and implementation notes. |
| `extensions` | `Record<string, unknown>` | No | Yes | Namespaced future Class-specific anatomy metadata. |

---

### 18.4 Ownership

A Monster Part SHALL belong to exactly one Monster aggregate.

A mutable Part instance SHALL NOT be shared by multiple Monster Documents.

---

### 18.5 Ability Ownership

Under the base construction model, a Monster Part owns at most one ordinary `MonsterAbility`.

The Ability's `AbilityBlockInstance` records are owned by that Ability.

---

### 18.6 Collection and Ordering

Parts SHALL be stored within the Monster aggregate's Part collection.

The `order` field SHALL preserve authored presentation order.

Reordering alone SHALL NOT change Part identity.

---

### 18.7 Outputs

Part selections represent persistent anatomical composition.

---

## Chapter 19 — Ability Blocks

### 19.1 Purpose

This chapter defines persistent Ability Block selections.

---

### 19.2 Monster Ability Contract

A `MonsterAbility` is an authored Ability instance belonging to a Monster Part or another explicitly supported Class-specific container.

| Field | Type | Required | Persisted? | Meaning / Rule Role |
|---|---|---:|---:|---|
| `id` | `UUID` | Yes | Yes | Stable Ability identity. |
| `name` | `string` | Yes | Yes | User-authored evocative name. |
| `description` | `string` | No | Yes | Narrative manifestation. Structured block mechanics remain authoritative. |
| `blockInstances` | `AbilityBlockInstance[]` | Yes | Yes | One base active or passive block plus compatible Enhancements, subject to rules. |
| `manualRulesText` | `string` | No | Yes | Optional override or addendum, clearly marked as community content or unresolved. |
| `tags` | `string[]` | No | Yes | User organization only. |
| `totalPointCost` | `integer` | Derived | No | Sum calculated from block definitions and Descriptors. |
| `generatedNotation` | `string` | Derived | No | Compact generated bracket notation. |
| `generatedRulesText` | `string` | Derived | No | Generated plain-language rendering. |
| `validation` | `ValidationMessage[]` | Derived | No | Compatibility, Descriptor and cost results. |

---

### 19.3 Ability Block Definition Contract

`AbilityBlockDefinition` is a shared Rule Asset contract.

The application SHALL represent its mechanical structure through data and named evaluators rather than copying the official prose wholesale.

| Field | Type | Required | Storage Role | Meaning / Rule Role |
|---|---|---:|---|---|
| `id` | `string` | Yes | Rule Assets | Stable identifier, for example `damage`, `move`, `resist`, `append` or `cleave`. |
| `name` | `LocalizedString` | Yes | Rule Assets | Display name. |
| `category` | `active`, `passive`, `enhancement` or `core_only` | Yes | Rule Assets | Structural role. |
| `costModel` | `PointCostModel` | Yes | Rule Assets | Fixed, per-rank, option-dependent or custom-evaluator cost. |
| `rankSchema` | `RankSchema` | No | Rule Assets | Allowed rank or point values and notation. |
| `descriptorSchemaIds` | `string[]` | No | Rule Assets | Required and optional Descriptor schemas. |
| `requires` | `BlockRequirement[]` | No | Rule Assets | Prerequisites such as a compatible base block. |
| `incompatibleWith` | `string[]` | No | Rule Assets | Known incompatibilities. |
| `combinationRules` | `CombinationRule[]` | No | Rule Assets | Special combinations such as area-shape interactions. |
| `notationTemplate` | `string` | Yes | Rule Assets | Generates compact notation. |
| `rulesTextTemplate` | `string` | Yes | Rule Assets | Generates a concise mechanical explanation. |
| `evaluatorId` | `string` | No | Rule Assets | Named pure function for mechanics too complex for templates. |
| `source` | `SourceReference` | Yes | Rule Assets | Official source page or section. |
| `status` | `implemented`, `pending_review` or `unsupported` | Yes | Rule Assets | Prevents unfinished definitions from appearing complete. |

---

### 19.4 Ability Block Instance Contract

| Field | Type | Required | Persisted? | Meaning / Rule Role |
|---|---|---:|---:|---|
| `id` | `UUID` | Yes | Yes | Stable instance identity. |
| `definitionId` | `string` | Yes | Yes | Reference to an `AbilityBlockDefinition`. |
| `rankOrPoints` | `integer` or `null` | Varies | Yes | Invested rank or points where applicable. |
| `descriptors` | `Record<descriptorId, value>` | Varies | Yes | Chosen Damage Type, Attribute, Movement Type, Condition, area, Class or another Descriptor value. |
| `options` | `Record<string, unknown>` | No | Yes | Block-specific structured selections. |
| `order` | `integer` | Yes | Yes | Canonical notation and rendering order. |
| `sourceOverride` | `SourceReference` | No | Yes | Source reference for community definitions or errata. |
| `notes` | `string` | No | Yes | User-facing design note with no mechanical effect. |

---

### 19.5 Ownership

A `MonsterAbility` owns one or more `AbilityBlockInstance` records.

Mutable Ability Block instances SHALL NOT be shared between separate Abilities.

Ability Block definitions remain external Rule Assets.

---

### 19.6 Structured Authority

Structured Ability Block instances SHALL be the primary mechanical source of truth.

Generated notation and generated prose SHALL be derived from structured data rather than parsed back into mechanics.

---

### 19.7 Outputs

Ability Block selections define persistent gameplay capabilities without duplicating Rule Asset definitions.

---

## Chapter 20 — Descriptors and Weaknesses

### 20.1 Purpose

This chapter defines Descriptor and Weakness selections.

---

### 20.2 Descriptor Definition Contract

`DescriptorDefinition` is a Rule Asset contract.

| Field | Type | Required | Storage Role | Meaning / Rule Role |
|---|---|---:|---|---|
| `id` | `string` | Yes | Rule Assets | Stable Descriptor identifier. |
| `name` | `LocalizedString` | Yes | Rule Assets | Display label. |
| `valueType` | `enum`, `integer`, `string`, `reference` or `list` | Yes | Rule Assets | Input-control and validation type. |
| `allowedValues` | `unknown[]` or `referenceSet` | No | Rule Assets | Valid choices. |
| `required` | `boolean` or expression | Yes | Rule Assets | May depend upon Block or category. |
| `defaultValue` | `unknown` | No | Rule Assets | Safe default only when the rules support one. |
| `notationFormatter` | `string` or `evaluatorId` | Yes | Rule Assets | Compact output formatter. |
| `rulesTextFormatter` | `string` or `evaluatorId` | Yes | Rule Assets | Plain-language output formatter. |
| `source` | `SourceReference` | Yes | Rule Assets | Official source traceability. |

Initial Descriptor and reference sets SHOULD include, at minimum:

- Attributes;
- Damage Types;
- Conditions;
- Movement Types;
- ranges;
- area forms;
- target categories;
- durations;
- Monster Classes.

Exact values belong in versioned Rule Assets.

---

### 20.3 Weakness Contract

Weaknesses apply to the Monster rather than occupying an ordinary Part.

They may negatively express supported blocks and may grant additional Ability points.

| Field | Type | Required | Persisted? | Meaning / Rule Role |
|---|---|---:|---:|---|
| `id` | `UUID` | Yes | Yes | Stable Weakness identity. |
| `name` | `string` | Yes | Yes | User-authored label. |
| `description` | `string` | No | Yes | Narrative manifestation. |
| `blockInstances` | `WeaknessBlockInstance[]` | Yes | Yes | Structured negative mechanics. |
| `grantedPoints` | `integer` | Derived | No | Points granted by the Weakness definitions. |
| `scope` | literal `monster` | Yes | Yes | Explicitly distinguishes Weaknesses from Part Abilities. |
| `source` | `SourceReference` | No | Yes | Community or errata traceability. |
| `notes` | `string` | No | Yes | User-authored notes. |

---

### 20.4 Weakness Block Instance Contract

| Field | Type | Required | Persisted? | Meaning / Rule Role |
|---|---|---:|---:|---|
| `definitionId` | `string` | Yes | Yes | Reference to a Weakness-capable block definition. |
| `mode` | `penalty`, `opponent_bonus` or `custom` | Yes | Yes | Defines how the Weakness modifies rolls or effects. |
| `rankOrPoints` | `integer` | Varies | Yes | Magnitude or point value. |
| `descriptors` | `Record<string, unknown>` | Varies | Yes | Damage Type or other required Descriptor values. |
| `notationOverride` | `string` | No | Yes | Permitted only for unsupported or community notation and SHALL trigger a warning. |

---

### 20.5 Ownership

Descriptor values selected for an Ability belong to their owning `AbilityBlockInstance`.

Weaknesses belong directly to the Monster aggregate rather than to a Part.

Each Weakness owns its `WeaknessBlockInstance` records.

---

### 20.6 Rule Relationships

The ordinary Weakness bonus limit is based on the Monster's original Ability allowance rather than the increased total.

Class features MAY replace this behavior and SHALL be represented through named Rule Hooks rather than by altering the generic Weakness contract.

---

### 20.7 Outputs

Descriptors and Weaknesses represent persistent construction choices.

---

## Chapter 21 — Persistent Entity Summary

### 21.1 Purpose

This chapter summarizes the persistent entity model established by this specification.

---

### 21.2 Canonical Ownership

The canonical ownership hierarchy is:

```text
Monster Document
│
├── Monster
│   ├── Identity
│   ├── Class Selection
│   ├── Size Reference
│   ├── Attributes
│   ├── Core
│   ├── Parts[]
│   │   └── Ability?
│   │       └── Ability Block Instances[]
│   ├── Weaknesses[]
│   │   └── Weakness Block Instances[]
│   ├── Class Customization
│   └── Notes
│
├── Build Context
├── Provenance
└── Extensions
```

---

### 21.3 External References

The Monster aggregate references, but does not own:

- the active Rule Pack;
- Monster Class definitions;
- Size definitions;
- Ability Block definitions;
- Descriptor definitions;
- Damage Type definitions;
- Condition definitions;
- Core Feature definitions;
- Movement Type definitions;
- other applicable Rule Assets.

---

### 21.4 Persistent Responsibilities

Persistent entities SHALL:

- preserve authored information;
- maintain ownership;
- support serialization;
- remain implementation-independent.

Persistent entities SHALL NOT:

- perform calculations;
- execute Validation Rules;
- define Rule Asset behavior.

---

### 21.5 Outputs

The persistent entity model defined by this chapter represents the canonical authored state of every Monster Document.

---

# Part III — Derived Data

---

## Chapter 22 — Derived Data

### 22.1 Purpose

This chapter defines information calculated from persistent project data.

Derived Data SHALL be produced by the Rule Engine rather than authored directly by users.

---

### 22.2 Responsibility

Derived Data is calculated from:

- Monster aggregate;
- Rule Assets;
- Build Context;
- active Rule Pack decisions.

Derived Data SHALL remain reproducible and SHALL NOT become the authoritative source of authored state.

---

### 22.3 Derived Monster Snapshot Contract

| Field | Type | Required | Persisted? | Meaning / Rule Role |
|---|---|---:|---:|---|
| `partLimit` | `integer` | Derived | No | Calculated from Build Context and applicable rule overrides. |
| `partsUsed` | `integer` | Derived | No | Ordinary Parts only. |
| `enduranceLimit` | `integer` | Derived | No | Ordinary construction allowance, including applicable overrides. |
| `enduranceUsed` | `integer` | Derived | No | Sum of ordinary-Part Endurance. The Core is tracked separately. |
| `attributeAllowance` | `integer` | Derived | No | Attribute construction allowance. |
| `attributePointsUsed` | `integer` | Derived | No | Brains plus Brawn. |
| `baseAbilityAllowance` | `integer` | Derived | No | Ability allowance before Weakness grants. |
| `weaknessPointLimit` | `integer` | Derived | No | Ordinary limit or Class-replaced limit. |
| `weaknessPointsUsed` | `integer` | Derived | No | Weakness-granted points selected. |
| `totalAbilityBudget` | `integer` | Derived | No | Base allowance plus legal Weakness grants and other modifiers. |
| `abilityPointsSpent` | `integer` | Derived | No | Sum of all ordinary-Part Ability costs. |
| `scanalysisGoal` | `integer` | Derived | No | Attribute total multiplied by Class multiplier. |
| `projectGoal` | `integer` or `null` | Derived | No | Depends on Build Context mode and the reviewed construction-cost formula. |
| `dataCost` | `integer` or `null` | Derived | No | Depends on project goal and applicable discounts. |
| `disobedienceRisk` | `boolean` | Derived | No | Context-sensitive warning result. |
| `validation` | `ValidationReport` | Derived | No | Errors, warnings and informational Diagnostics. |
| `generatedStatBlock` | `GeneratedStatBlock` | Derived | No | Structured presentation model, not a stored source of truth. |

---

### 22.4 Validation Diagnostic Contract

This contract describes the derived Diagnostic data referenced by the Monster snapshot. The Validation & Testing Specification remains authoritative for validation behavior.

| Field | Type | Required | Storage Role | Meaning / Rule Role |
|---|---|---:|---|---|
| `code` | `string` | Yes | Derived | Stable machine code, for example `PART_LIMIT_EXCEEDED`. |
| `severity` | `error`, `warning` or `info` | Yes | Derived | Determines how the UI presents the issue. |
| `messageKey` | `string` | Yes | Derived | Localization key. |
| `parameters` | `Record<string, unknown>` | No | Derived | Values inserted into the localized message. |
| `path` | JSON Pointer or entity path | No | Derived | Affected field or entity. |
| `entityId` | `UUID` or `string` | No | Derived | Affected Part, Ability, Block or another entity. |
| `source` | `SourceReference` | No | Derived | Official rule citation. |
| `suggestedFix` | `StructuredFix` or text key | No | Derived | Non-destructive repair suggestion. |
| `canAutoFix` | `boolean` | Yes | Derived | True only where user intent cannot reasonably be misinterpreted. |

---

### 22.5 Lifetime

Derived Data MAY be regenerated whenever required.

A cached Derived Monster Snapshot SHALL be invalidated when its document, Rule Pack or engine version no longer matches.

---

### 22.6 Consumers

Derived Data MAY be consumed by:

- Validation Engine;
- user interface;
- export workflows;
- presentation workflows;
- automated tests.

---

### 22.7 Outputs

Derived Data provides calculated project information without modifying persistent entities.

---

## Chapter 23 — Derived Relationships

### 23.1 Purpose

This chapter defines relationships computed from persistent data.

---

### 23.2 Responsibility

Derived Relationships describe connections inferred by project systems rather than authored directly by users.

---

### 23.3 Examples

Derived Relationships MAY include:

- resolved Rule Asset dependencies;
- calculated ownership views;
- temporary dependency graphs;
- construction summaries.

---

### 23.4 Determinism

Equivalent Monster Documents SHALL produce equivalent Derived Relationships.

---

### 23.5 Persistence

Derived Relationships SHALL NOT be serialized.

They SHALL be regenerated whenever required.

---

### 23.6 Outputs

Derived Relationships support project workflows while remaining external to persistent project data.

---

# Part IV — Relationships, Serialization & Compatibility

---

## Chapter 24 — Serialization Model

### 24.1 Purpose

This chapter defines the canonical serialization model.

---

### 24.2 Responsibility

Serialization SHALL preserve:

- persistent entities;
- ownership;
- references;
- metadata.

Canonical serialization SHALL NOT rely upon:

- Derived Data;
- UI State;
- temporary execution state.

A `derivedSnapshot` MAY be serialized solely as a disposable cache.

Serialized Derived Data SHALL NOT become authoritative and SHALL be discarded whenever compatibility cannot be confirmed.

---

### 24.3 Serialization Principles

Serialization SHALL remain:

- deterministic;
- portable;
- version-aware;
- implementation-independent.

---

### 24.4 Canonical Structure

Serialized documents SHALL represent:

```text
Monster Document
│
├── Monster
├── Build Context
├── Provenance
├── Extensions
└── Derived Snapshot? [disposable cache only]
```

Referenced Rule Assets SHALL remain external.

---

### 24.5 Outputs

Serialization produces canonical persistent project documents.

---

## Chapter 25 — Illustrative Serialization

### 25.1 Purpose

This chapter provides an illustrative serialization example.

**The following example is illustrative only.**

---

### 25.2 Illustrative Example

```json
{
  "schemaVersion": "1.0.0",
  "documentId": "uuid",
  "rulesetRef": {
    "rulesetId": "mortasheen-core",
    "rulesetVersion": "1.0.0"
  },
  "buildContext": {
    "mode": "new",
    "science": 4,
    "madness": 3,
    "respectability": 1,
    "knownBlockIds": [
      "resist",
      "move",
      "dash",
      "damage",
      "append",
      "cleave"
    ],
    "isYgore": false,
    "strictness": "guided"
  },
  "monster": {
    "id": "uuid",
    "identity": {
      "name": "Aerofoul"
    },
    "classSelection": {
      "classId": "bioconstruct",
      "coreFeatureId": "splice-of-life"
    },
    "sizeId": "average",
    "attributes": {
      "brains": 2,
      "brawn": 5
    },
    "core": {
      "id": "uuid",
      "name": "Core",
      "endurance": 1,
      "coreFeatureId": "splice-of-life"
    },
    "parts": [
      {
        "id": "uuid",
        "name": "Skeleton",
        "endurance": 4,
        "order": 0,
        "origin": "created",
        "ability": {
          "id": "uuid",
          "name": "Shelleton",
          "blockInstances": [
            {
              "id": "uuid",
              "definitionId": "resist",
              "rankOrPoints": 1,
              "descriptors": {
                "damageType": "physical"
              },
              "order": 0
            }
          ]
        }
      }
    ],
    "weaknesses": [],
    "notes": ""
  },
  "provenance": {
    "originType": "blank",
    "createdAt": "ISO-8601",
    "updatedAt": "ISO-8601"
  }
}
```

This example is intentionally partial.

It demonstrates ownership and references rather than a complete transcription of the sample build or every Ability Block's final schema.

---

### 25.3 Interpretation

Implementations MAY serialize equivalent information differently provided semantic meaning remains unchanged.

---

### 25.4 Outputs

This example illustrates the canonical organization of serialized project information.

---

## Chapter 26 — Compatible Document Versions

### 26.1 Purpose

This chapter defines compatibility requirements governing serialized Monster Documents.

---

### 26.2 Responsibility

Compatibility SHALL preserve semantic interpretation across supported document versions.

---

### 26.3 Version Identification

Serialized documents SHALL identify:

- document version;
- Rule Pack version.

---

### 26.4 Compatible Document Versions

Implementations SHOULD support migration between compatible document versions.

---

### 26.5 Unsupported Versions

Unsupported document versions SHALL produce explicit Validation Diagnostics.

---

### 26.6 Outputs

Compatibility preserves long-term usability of serialized Monster Documents.

---

## Chapter 27 — Migration

### 27.1 Purpose

This chapter defines migration principles governing persistent project data.

---

### 27.2 Responsibility

Migration SHALL transform one supported Monster Document version into another while preserving authored semantic meaning.

---

### 27.3 Migration and Compatibility Policy

- Schema versions SHALL use semantic versioning.
- Breaking persistence changes SHALL increment the major version.
- Migrations SHALL be pure transformations from one document version to the next.
- Migrations SHALL retain unknown namespaced extensions whenever possible.
- A document opened under a different Rule Pack version SHALL be revalidated.
- Disposable derived caches SHALL be discarded when the Rule Pack or schema version differs.
- Unknown Rule Asset identifiers SHALL be preserved and displayed as unresolved rather than deleted.
- Structured exports SHALL include both `schemaVersion` and `rulesetVersion`.
- The application SHALL offer a backup before any irreversible migration.

---

### 27.4 Rule Pack Migration

Migration MAY update Rule Asset references according to documented Rule Pack evolution.

Migration SHALL NOT silently substitute a similarly named Rule Asset for an unknown identifier.

---

### 27.5 Outputs

Migration supports long-term project evolution while preserving authored choices and unresolved references.

---

## Chapter 28 — Data Integrity

### 28.1 Purpose

This chapter defines integrity requirements governing persistent project information.

---

### 28.2 Responsibility

Persistent entities SHALL preserve:

- ownership consistency;
- identifier stability;
- serialization consistency;
- reference validity.

---

### 28.3 Integrity Verification

Integrity SHALL be verified through the Validation Engine.

This specification defines the data model only.

---

### 28.4 Failure Handling

Integrity failures SHALL produce Validation Diagnostics rather than undefined behavior.

---

### 28.5 Outputs

Integrity requirements preserve reliable project information.

---

## Chapter 29 — Acceptance Criteria

### 29.1 Purpose

This chapter summarizes the requirements established by this specification.

---

### 29.2 Conformance

A data model implementation conforms to this specification when it:

- correctly represents persistent entities;
- preserves ownership;
- preserves identifiers;
- supports deterministic serialization;
- distinguishes persistent and derived data;
- maintains Rule Asset references.

---

### 29.3 Specification Boundaries

This specification intentionally excludes:

- gameplay calculations;
- Validation Rules;
- user interface behavior;
- Rule Asset definitions.

Those responsibilities belong to their respective specifications.

---

### 29.4 Evolution Principles

Future revisions SHOULD preserve:

- semantic compatibility;
- implementation independence;
- deterministic behavior;
- architectural separation.

---

### 29.5 Outputs

The data model defined by this specification provides the canonical persistent representation used throughout the Mortasheen Monster Builder.
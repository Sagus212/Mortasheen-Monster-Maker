# Mortasheen Monster Builder — Rules Asset Specification

Version 1.0

> Canonical Markdown source for the Rules Asset Specification.

This document defines how official Mortasheen game rules are represented as structured Rule Assets. It is implementation-independent and specifies the information, relationships, identifiers and versioning of every asset used by the builder.

## Chapter 1 — Rules Asset Philosophy

### 1.1 Purpose

The Rules Asset layer is the authoritative source for every gameplay rule that can be represented as structured data. The Rule Engine performs calculations using these assets but never defines gameplay values itself. The Rule Engine contains algorithms; the Rules Asset layer contains facts.

### 1.2 Design Principles

• Data-first architecture.
• Engine-agnostic assets.
• Human-readable definitions.
• Stable identifiers.
• Immutable semantic meaning.
• Explicit relationships rather than duplicated data.
• Source traceability for every implemented rule.

### 1.3 Separation of Responsibilities

Rules Asset Layer: definitions and metadata.
Rule Engine: calculations.
Validation Engine: compliance checking.
Storage Layer: persistence.
UI: editing and presentation.
Export Layer: rendering.

### 1.4 Categories of Rule Assets

Monster Classes, Sizes, Ability Blocks, Descriptors, Damage Types, Conditions, Movement Types, Weakness Definitions, Templates, Metadata and Rule Packs. Monster save files are user data, not Rule Assets.

### 1.5 Rule Asset Structure

Every Rule Asset exposes: Stable ID, Display Name, Version, Description, Source Reference, Metadata, optional Tags and references.

### 1.6 Source Traceability

Every asset records book, section, page and optional notes so the application can surface rule references.

### 1.7 Rule Packs

The engine loads one or more Rule Packs. Official Mortasheen is simply the default pack.

### 1.8 Versioning Philosophy

Asset IDs are permanent. Display names may change, IDs do not. Breaking semantic changes create new versions rather than redefining an existing asset.

### 1.9 Coding Guidelines

Never hardcode gameplay data. Prefer references over duplication. Separate data from presentation.

### 1.10 Chapter Summary

This chapter establishes the philosophy governing every future Rules Asset chapter.


## Chapter 2 — Universal Rule Asset Schema

### 2.1 Purpose

Defines the common schema inherited by every Rule Asset.

### 2.2 Core Fields

Required fields: id, assetType, displayName, version, description, source and metadata.

### 2.3 Base Interface

RuleAsset { id; assetType; displayName; version; description; source; metadata; }

### 2.4 SourceReference

Contains book, section, page and optional notes.

### 2.5 AssetMetadata

Tags, keywords, author, deprecated, experimental, localization IDs and extension fields.

### 2.6 Relationships

Assets reference one another through stable IDs rather than copying data.

### 2.7 Localization

Display text is localized while IDs remain unchanged.

### 2.8 Asset Lifecycle

Draft → Experimental → Official → Deprecated → Removed.

### 2.9 Serialization

Deterministic serialization. Unknown future fields are ignored rather than discarded.

### 2.10 Validation

Unique IDs, required fields, supported versions and valid references.

### 2.11 Example

*Illustrative example only. Asset IDs, field names, relationships, and values in this example have not yet been verified as authoritative official Mortasheen rules data unless the surrounding text explicitly states otherwise.*

Canonical JSON example for a Damage ability block.

### 2.12 Chapter Summary

Future asset types extend this schema rather than redefining shared fields.


## Chapter 3 — Monster Class Assets

### 3.1 Purpose

Monster Class Assets define the immutable characteristics shared by every monster belonging to a class. They never contain per-monster state.

### 3.2 Responsibilities

Provide construction multiplier, creation restrictions, tier, core feature reference and class-specific rule references.

### 3.3 Schema Extension

Adds fields beyond RuleAsset: tier, projectMultiplier, minimumRespectability, canCreateFromScratch, coreFeatureId, defaultTags, restrictionIds.

### 3.4 Relationships

MonsterClass references Core Features, optional validation rules, templates and future discoveries through IDs.

### 3.5 Validation Rules

Validate unique multiplier, valid referenced assets, non-negative minimum Respectability and valid tier values.

### 3.6 Serialization Example

A canonical JSON object should serialize only class information; runtime state belongs to Monster save files.

### 3.7 TypeScript Interface

interface MonsterClassAsset extends RuleAsset {
 tier;
 projectMultiplier;
 minimumRespectability;
 canCreateFromScratch;
 coreFeatureId;
 restrictionIds:[];
}

### 3.8 Example JSON

*Illustrative example only. Asset IDs, field names, relationships, and values in this example have not yet been verified as authoritative official Mortasheen rules data unless the surrounding text explicitly states otherwise.*

{
"id":"bioconstruct",
"assetType":"monsterClass",
"displayName":"Bioconstruct",
"tier":"normal",
"projectMultiplier":2,
"minimumRespectability":0,
"canCreateFromScratch":true,
"coreFeatureId":"splice_of_life"
}

### 3.9 Future Extension Points

Allow subclasses, optional variant packages, official expansions and homebrew classes without engine changes.

### 3.10 Notes

Class Assets should contain only shared rule definitions. Individual monster statistics always belong in Monster documents.


## Chapter 4 — Size Assets

### 4.1 Purpose

Size Assets define the immutable gameplay characteristics associated with a monster's physical scale. Each Monster references exactly one Size Asset, while the Size Asset itself remains shared and immutable.

### 4.2 Responsibilities

A Size Asset defines physical scale, construction restrictions, gameplay modifiers derived from size, localization data, and rule references. It MUST NOT contain monster-specific state such as attributes, abilities, endurance, or weaknesses.

### 4.3 Schema Extension

Additional fields beyond RuleAsset: order, category, minimumRespectability, baseRange, brawnDefenseModifier, displayDescription.

### 4.4 Semantic Rules

A Monster MUST reference exactly one Size Asset. Size Assets MUST remain immutable after publication. Rule Packs MAY introduce additional sizes without requiring engine changes.

### 4.5 Standard Enumeration

The official Mortasheen Rules Pack defines: Miniscule, Tiny, Small, Average, Large, Enormous, and Titanic. The Rule Engine MUST treat this list as data rather than hardcoded values.

### 4.6 Relationships

Size Assets are referenced by Monster Documents and consumed by the Rule Engine, Validation Engine, UI and Export systems. They may reference localization resources but SHOULD avoid direct dependencies on other gameplay assets.

### 4.7 Validation

Validation SHALL verify: exactly one referenced Size Asset; valid asset identifier; compatibility with loaded Rule Pack; respectability requirements; non-deprecated asset usage.

### 4.8 Serialization

Monster save files serialize only the Size Asset identifier. Complete size definitions remain exclusively within the Rules Asset library.

### 4.9 TypeScript Interface

interface SizeAsset extends RuleAsset {
 order:number;
 category:string;
 minimumRespectability:number;
 baseRange:number;
 brawnDefenseModifier:number;
 displayDescription:string;
}

### 4.10 Example JSON

*Illustrative example only. Asset IDs, field names, relationships, and values in this example have not yet been verified as authoritative official Mortasheen rules data unless the surrounding text explicitly states otherwise.*

{"id":"average","assetType":"size","displayName":"Average","order":3,"minimumRespectability":0,"baseRange":0,"brawnDefenseModifier":0}

### 4.11 Implementation Notes

The Rule Engine MUST retrieve all size-derived modifiers from the referenced Size Asset. Application logic SHOULD NOT contain size-specific switch statements or conditional gameplay values.

### 4.12 Future Extension Points

Future Rule Packs MAY introduce new size categories, variant scales, temporary transformations, or alternative progression systems without modifying the Rule Engine.

## Chapter 5 — Ability Block Assets

### 5.1 Purpose

Ability Block Assets define the modular building blocks used to construct monster abilities. Individual abilities are compositions of one or more Ability Block Assets plus descriptors and point investments.

### 5.2 Responsibilities

Ability Block Assets define block identity, category, parameters, descriptors, compatibility rules, point scaling, and rule references. They MUST NOT store per-monster values or allocated points.

### 5.3 Schema Extension

Additional fields beyond RuleAsset: category, baseCost, scalable, descriptorSchemaIds, compatibilityIds, incompatibilityIds, generatedTextTemplate, uiHints.

### 5.4 Semantic Rules

Each Ability Block represents one atomic gameplay concept. Blocks are immutable after publication. A monster ability references blocks by ID and supplies instance-specific values separately.

### 5.5 Block Categories

The official rules define three primary categories: Active Blocks, Passive Blocks, and Enhancement Blocks. Future Rule Packs MAY introduce additional categories.

### 5.6 Descriptor References

Blocks reference Descriptor Assets to define configurable values such as damage type, movement type, target, duration, attribute, or area. Descriptor definitions are not duplicated inside blocks.

### 5.7 Point Cost Model

Blocks specify how point investment affects their behavior. Scaling rules belong to the Ability Block Asset; allocated point values belong to the monster's Ability instance.

### 5.8 Relationships

Ability Block Assets reference Descriptor Assets and may reference Conditions, Damage Types, Movement Types, Core Features, or validation rules exclusively through stable IDs.

### 5.9 Validation

Validation SHALL verify category validity, descriptor completeness, compatibility, incompatible combinations, valid scaling, and referenced asset existence.

### 5.10 Serialization

Monster documents serialize only block IDs, allocated points, and descriptor values. Full block definitions remain in the Rules Asset library.

### 5.11 TypeScript Interface

interface AbilityBlockAsset extends RuleAsset {
 category:string;
 baseCost:number;
 scalable:boolean;
 descriptorSchemaIds:string[];
 compatibilityIds:string[];
 incompatibilityIds:string[];
 generatedTextTemplate:string;
 uiHints?:object;
}

### 5.12 Example JSON

*Illustrative example only. Asset IDs, field names, relationships, and values in this example have not yet been verified as authoritative official Mortasheen rules data unless the surrounding text explicitly states otherwise.*

{"id":"damage","assetType":"abilityBlock","displayName":"Damage","category":"active","baseCost":1,"scalable":true,"descriptorSchemaIds":["damageType","attribute"],"compatibilityIds":["append","cleave"]}

### 5.13 Implementation Notes

The Rule Engine MUST compose abilities by interpreting Ability Block Assets and Descriptor Assets. Application logic SHOULD NOT hardcode individual block behavior outside the Rule Engine.

### 5.14 Future Extension Points

Future Rule Packs MAY add new block categories, descriptor schemas, scaling models, AI metadata, animation hints, and editor metadata without modifying existing assets.


## Chapter 6 — Descriptor Assets

### 6.1 Purpose

Descriptor Assets define the configurable parameters referenced by Ability Block Assets. They represent reusable semantic value definitions rather than gameplay behavior.

### 6.2 Responsibilities

Descriptor Assets define descriptor identity, value type, constraints, defaults, localization, editor presentation and validation metadata. They MUST NOT define ability behavior or store monster-specific values.

### 6.3 Schema Extension

Additional fields beyond RuleAsset: valueType, allowedValues, defaultValue, validationRules, uiControlType, allowsCustomValues, cardinality.

### 6.4 Semantic Rules

Ability Blocks reference Descriptor Assets by stable ID. Descriptor Assets remain immutable after publication. Descriptor instances exist only within a monster ability.

### 6.5 Descriptor Categories

Typical categories include: Damage Type, Movement Type, Attribute, Target, Area, Duration, Range, Condition, Spawn Class, Numeric Value and Boolean Flag. Future Rule Packs MAY define additional descriptor categories.

### 6.6 Relationships

Descriptor Assets may reference enumerated Rule Assets such as Damage Types, Conditions or Movement Types. They are referenced by Ability Block Assets and interpreted by the Rule Engine.

### 6.7 Validation

Validation SHALL verify descriptor existence, value type compatibility, cardinality, required values, allowed enumerations and custom-value permissions.

### 6.8 Serialization

Monster documents serialize descriptor values together with their descriptor IDs. Descriptor definitions remain exclusively in the Rules Asset library.

### 6.9 TypeScript Interface

interface DescriptorAsset extends RuleAsset {
 valueType:string;
 allowedValues?:string[];
 defaultValue?:unknown;
 validationRules:string[];
 uiControlType:string;
 allowsCustomValues:boolean;
 cardinality:string;
}

### 6.10 Example JSON

*Illustrative example only. Asset IDs, field names, relationships, and values in this example have not yet been verified as authoritative official Mortasheen rules data unless the surrounding text explicitly states otherwise.*

{"id":"damageType","assetType":"descriptor","displayName":"Damage Type","valueType":"enum","allowedValues":["physical","thermal","chemical"],"uiControlType":"dropdown"}

### 6.11 Implementation Notes

The Rule Engine MUST resolve descriptor semantics through Descriptor Assets rather than interpreting raw strings. UI controls SHOULD be generated dynamically from descriptor metadata whenever practical.

### 6.12 Future Extension Points

Future Rule Packs MAY introduce nested descriptors, computed descriptors, localization variants, custom editor widgets and descriptor inheritance without modifying the engine.


## Chapter 7 — Damage Type Assets

### 7.1 Purpose

Damage Type Assets define the standardized categories of damage recognized by the Rules Asset system. They provide semantic meaning to damage-related Ability Blocks and Descriptor Assets.

### 7.2 Responsibilities

Damage Type Assets define gameplay identity, display name, description, localization, keywords, optional icon/color metadata, interaction metadata and rule references. They MUST NOT contain damage values, point costs or monster-specific data.

### 7.3 Schema Extension

Additional fields beyond RuleAsset: keywords, defaultColor, defaultIcon, interactionIds, editorHints, localizationKey.

### 7.4 Semantic Rules

Each Damage Type represents one immutable gameplay concept. Damage magnitude and other parameters belong to Ability instances and Descriptor Assets. Rule Packs MAY introduce additional Damage Types.

### 7.5 Official Damage Types

The official Mortasheen Rule Pack defines its damage types as Rule Assets. The Rule Engine MUST load these from the Rules Asset library rather than hardcoding them.

### 7.6 Relationships

Damage Type Assets may be referenced by Descriptor Assets, Ability Block Assets, Condition Assets, Validation rules and Localization resources through stable IDs.

### 7.7 Validation

Validation SHALL verify identifier validity, existence, loaded Rule Pack compatibility, deprecation status and valid interaction references.

### 7.8 Serialization

Monster documents serialize only Damage Type identifiers. Full Damage Type definitions remain exclusively in the Rules Asset library.

### 7.9 TypeScript Interface

interface DamageTypeAsset extends RuleAsset {
  keywords:string[];
  defaultColor?:string;
  defaultIcon?:string;
  interactionIds:string[];
  editorHints?:object;
  localizationKey?:string;
}

### 7.10 Canonical JSON Example

*Illustrative example only. Asset IDs, field names, relationships, and values in this example have not yet been verified as authoritative official Mortasheen rules data unless the surrounding text explicitly states otherwise.*

{"id":"thermal","assetType":"damageType","displayName":"Thermal","keywords":["heat","fire"],"interactionIds":[],"defaultColor":"#d65a31","defaultIcon":"thermal","localizationKey":"damageType.thermal"}

### 7.11 Implementation Notes

The Rule Engine SHALL interpret Damage Type Assets only through their identifiers. Ability Blocks SHOULD reference Damage Types indirectly through Descriptor Assets. The UI SHOULD obtain icons, colors and localized names directly from Damage Type Assets.

### 7.12 Future Extension Points

Future Rule Packs MAY extend Damage Type Assets with interaction matrices, resistance groups, animation metadata, particle metadata, sound metadata and AI evaluation hints without modifying the Rule Engine.


## Chapter 8 — Condition Assets

### 8.1 Purpose

Condition Assets define standardized gameplay conditions that may be applied to monsters, targets, abilities or encounters. They encapsulate the semantic definition of a condition while remaining independent of any specific monster or ability instance.

### 8.2 Responsibilities

Condition Assets define condition identity, display name, description, localization, duration model, stacking behavior, removal rules, gameplay tags and rule references. They MUST NOT store per-monster state or active durations.

### 8.3 Schema Extension

Additional fields beyond RuleAsset: category, durationModel, stackingMode, removalRuleIds, gameplayTagIds, immunityTagIds, defaultDescriptors, editorHints.

### 8.4 Semantic Rules

Each Condition Asset represents one immutable gameplay concept. Active instances of a condition exist only within monster or encounter state. Rule Packs MAY introduce additional Condition Assets.

### 8.5 Categories

Typical categories include: Persistent, Temporary, Beneficial, Harmful, Triggered and Passive. Categories are metadata and MUST NOT by themselves implement gameplay logic.

### 8.6 Relationships

Condition Assets may reference Descriptor Assets, Ability Block Assets, Damage Type Assets and Validation rules through stable identifiers. Other assets reference conditions by ID only.

### 8.7 Validation

Validation SHALL verify valid identifiers, referenced assets, stacking mode compatibility, duration model validity and removal rule existence.

### 8.8 Serialization

Monster documents serialize only condition identifiers and instance-specific runtime values. Full Condition definitions remain in the Rules Asset library.

### 8.9 TypeScript Interface

interface ConditionAsset extends RuleAsset {
 category:string;
 durationModel:string;
 stackingMode:string;
 removalRuleIds:string[];
 gameplayTagIds:string[];
 immunityTagIds?:string[];
 defaultDescriptors?:string[];
 editorHints?:object;
}

### 8.10 Canonical JSON Example

*Illustrative example only. Asset IDs, field names, relationships, and values in this example have not yet been verified as authoritative official Mortasheen rules data unless the surrounding text explicitly states otherwise.*

{"id":"burning","assetType":"condition","displayName":"Burning","category":"harmful","durationModel":"timed","stackingMode":"refresh","gameplayTagIds":["fire"]}

### 8.11 Implementation Notes

The Rule Engine MUST resolve all condition behavior through Condition Assets and referenced descriptors rather than through hardcoded condition names. The UI SHOULD dynamically present condition metadata from the Rules Asset library.

### 8.12 Future Extension Points

Future Rule Packs MAY add condition hierarchies, scripted behaviors, visual effect metadata, AI evaluation metadata, network synchronization hints and localization variants without requiring engine modifications.


## Chapter 9 — Movement Type Assets

### 9.1 Purpose

Movement Type Assets define the standardized methods by which monsters move within the rules system. They provide reusable movement semantics referenced by Descriptor Assets and Ability Block Assets.

### 9.2 Responsibilities

Movement Type Assets define movement identity, display name, description, localization, gameplay tags, traversal characteristics, editor metadata and rule references. They MUST NOT store per-monster movement values or ranges.

### 9.3 Schema Extension

Additional fields beyond RuleAsset: traversalTags, terrainInteractionIds, movementCategory, defaultDescriptors, editorHints, localizationKey.

### 9.4 Semantic Rules

Each Movement Type Asset represents one immutable movement concept. Distance, speed and other instance-specific values belong to descriptors or ability instances. Rule Packs MAY introduce additional movement types.

### 9.5 Categories

Typical categories include Walking, Flying, Swimming, Burrowing, Climbing, Hovering and Teleportation. Categories are descriptive metadata and MUST NOT themselves implement gameplay logic.

### 9.6 Relationships

Movement Type Assets may be referenced by Descriptor Assets, Ability Block Assets, Condition Assets and Validation rules. References SHALL always use stable asset identifiers.

### 9.7 Validation

Validation SHALL verify valid identifiers, referenced assets, category validity, terrain interaction references and Rule Pack compatibility.

### 9.8 Serialization

Monster documents serialize only Movement Type identifiers together with instance-specific descriptor values. Complete Movement Type definitions remain exclusively within the Rules Asset library.

### 9.9 TypeScript Interface

interface MovementTypeAsset extends RuleAsset {
 movementCategory:string;
 traversalTags:string[];
 terrainInteractionIds:string[];
 defaultDescriptors?:string[];
 editorHints?:object;
 localizationKey?:string;
}

### 9.10 Canonical JSON Example

*Illustrative example only. Asset IDs, field names, relationships, and values in this example have not yet been verified as authoritative official Mortasheen rules data unless the surrounding text explicitly states otherwise.*

{"id":"flying","assetType":"movementType","displayName":"Flying","movementCategory":"aerial","traversalTags":["air"],"terrainInteractionIds":["ignoreGround"]}

### 9.11 Implementation Notes

The Rule Engine MUST resolve movement semantics through Movement Type Assets rather than hardcoded movement names. The UI SHOULD populate movement selectors dynamically from the Rules Asset library.

### 9.12 Future Extension Points

Future Rule Packs MAY extend Movement Type Assets with pathfinding metadata, animation metadata, AI hints, environmental restrictions and alternative traversal systems without modifying the Rule Engine.


## Chapter 10 — Core Feature Assets

### 10.1 Purpose

Core Feature Assets define the signature mechanics associated with monster classes. They encapsulate reusable class-defining rules while remaining independent of individual monster instances.

### 10.2 Responsibilities

Core Feature Assets define identity, display name, description, localization, gameplay effects, activation model, rule references and editor metadata. They MUST NOT store per-monster values, allocated points or runtime state.

### 10.3 Schema Extension

Additional fields beyond RuleAsset: activationModel, effectIds, requiredClassIds, defaultDescriptors, editorHints, localizationKey.

### 10.4 Semantic Rules

Each Core Feature Asset represents one immutable gameplay concept. Monster Class Assets reference Core Feature Assets through stable IDs. Rule Packs MAY introduce additional Core Features.

### 10.5 Categories

Typical categories include Passive, Activated, Triggered, Persistent, Replacement and Conditional. Categories are descriptive metadata and MUST NOT implement gameplay logic by themselves.

### 10.6 Relationships

Core Feature Assets may reference Ability Block Assets, Descriptor Assets, Condition Assets, Damage Type Assets and Validation rules. Monster Class Assets reference Core Feature Assets exclusively through stable identifiers.

### 10.7 Validation

Validation SHALL verify valid identifiers, referenced asset existence, activation model validity, compatible Monster Class references and Rule Pack compatibility.

### 10.8 Serialization

Monster documents serialize only the referenced Core Feature identifier through their Monster Class. Full Core Feature definitions remain in the Rules Asset library.

### 10.9 TypeScript Interface

interface CoreFeatureAsset extends RuleAsset {
 activationModel:string;
 effectIds:string[];
 requiredClassIds:string[];
 defaultDescriptors?:string[];
 editorHints?:object;
 localizationKey?:string;
}

### 10.10 Canonical JSON Example

*Illustrative example only. Asset IDs, field names, relationships, and values in this example have not yet been verified as authoritative official Mortasheen rules data unless the surrounding text explicitly states otherwise.*

{"id":"splice_of_life","assetType":"coreFeature","displayName":"Splice of Life","activationModel":"passive","effectIds":["livingConstruction"],"requiredClassIds":["bioconstruct"],"localizationKey":"coreFeature.spliceOfLife"}

### 10.11 Implementation Notes

The Rule Engine MUST resolve Core Feature behavior through Core Feature Assets and referenced assets rather than hardcoded class names. The UI SHOULD display Core Feature information dynamically from the Rules Asset library.

### 10.12 Future Extension Points

Future Rule Packs MAY extend Core Feature Assets with scripted behaviors, unlock conditions, progression metadata, AI metadata, animation metadata, visual effect metadata and localization variants without modifying the Rule Engine.

## Chapter 11 — Weakness Assets

### 11.1 Purpose

Weakness Assets define the reusable rules and schemas used to construct monster weaknesses.

A weakness represents a disadvantage affecting the monster as a whole in exchange for bonus ability points. Weakness definitions belong to the Rules Asset library, while the weaknesses selected for an individual monster belong to that Monster Document.

Weakness Assets SHALL define how weaknesses are represented, validated, displayed and interpreted without storing any monster-specific weakness values.

---

### 11.2 Responsibilities

Weakness Assets SHALL define:

- weakness representation type
- compatible Ability Block references
- permitted penalty notation
- descriptor requirements
- point-value rules
- generated display-text templates
- validation metadata
- rule-source references

Weakness Assets MUST NOT store:

- a monster’s selected weakness
- the penalty chosen for a specific monster
- bonus ability points granted to a specific monster
- runtime effects or temporary penalties
- body-part assignments

---

### 11.3 Schema Extension

Additional fields beyond `RuleAsset`:

- `representationMode`
- `compatibleBlockIds`
- `requiredDescriptorIds`
- `allowedNotation`
- `minimumPenalty`
- `maximumPenaltyRuleId`
- `generatedTextTemplate`
- `validationRuleIds`
- `editorHints`

---

### 11.4 Semantic Rules

A monster MAY possess one or more weaknesses unless another rule requires or prohibits them.

Weaknesses:

- affect the entire monster;
- are not attached to body parts;
- do not occupy body-part ability slots;
- grant bonus ability points equal to their penalty values;
- remain separate from normal Ability Block instances.

The total bonus granted by all weaknesses is normally limited to one-half of the monster’s base ability-point allowance, rounded down.

For this calculation, the base ability-point allowance is normally equal to:

```text
Brains + Brawn
```

Bonuses or modifications from Discoveries, Core Features or similar rules SHALL NOT alter the weakness limit unless the modifying rule explicitly states otherwise.

Monster Class Assets, Core Feature Assets or other explicit rule assets MAY replace the normal weakness limit or require a particular weakness total.

---

### 11.5 Weakness Representation Modes

The official rules support two primary weakness representations.

#### Direct Penalty

A direct-penalty weakness reverses a numerical benefit into a penalty applied to the monster.

It uses a minus sign.

Example:

```text
[ARMOR -2 radiation]
```

This weakness applies a penalty of 2 and grants 2 bonus ability points.

---

#### Opponent Bonus

An opponent-bonus weakness grants additional dice or another numerical advantage to the opposing side.

It uses an arrow notation.

Example:

```text
[RESIST =>3d10 chemical]
```

This weakness grants the opposing effect an additional 3 dice and grants the monster 3 bonus ability points.

Abilities that normally add extra dice SHALL use opponent-bonus notation when inverted as weaknesses.

---

### 11.6 Ability Block Inversion

A weakness is typically derived by inverting the effect of a passive Ability Block.

The Weakness Asset SHALL reference the compatible Ability Block rather than duplicate its complete definition.

The weakness representation SHALL supply:

- the referenced Ability Block ID;
- the chosen penalty value;
- required descriptor values;
- the selected representation mode.

A monster creator does not need to know an Ability Block in order to use its inverted form as a weakness.

Scanalyzing a weakness does not teach the normal Ability Block because a weakness is a monster characteristic rather than an installed block.

---

### 11.7 Class and Core Feature Overrides

The normal weakness limit MAY be replaced by an explicit Monster Class or Core Feature rule.

Such an override SHALL be represented as a referenced rule rather than hardcoded into the Weakness system.

For example, the official Biogarbage Core Feature requires Biogarbage monsters to possess weaknesses equal to their total normal ability-point allowance and grants the corresponding number of bonus ability points.

The Validation Engine SHALL apply the most specific explicit rule in the following order:

1. Monster-specific override
2. Core Feature override
3. Monster Class override
4. General weakness rule

Conflicting overrides SHALL produce a validation error unless a defined precedence rule resolves them.

---

### 11.8 Editing Restrictions

Weaknesses MAY be changed when editing a monster only when the relevant construction rules permit it.

A scientist MAY change the weaknesses of their original creation.

A scientist MUST NOT change the weaknesses of a monster they did not originally create unless another explicit rule grants permission.

The ability to edit weaknesses SHALL be determined by Monster provenance and construction context rather than by the Weakness Asset itself.

---

### 11.9 Relationships

Weakness Assets may reference:

- Ability Block Assets
- Descriptor Assets
- Damage Type Assets
- Condition Assets
- Movement Type Assets
- Monster Class Assets
- Core Feature Assets
- Validation rules

Monster weakness instances reference Weakness Assets and related rule assets exclusively through stable identifiers.

Weakness Assets MUST NOT reference Monster Documents or runtime monster state.

---

### 11.10 Validation

Validation SHALL verify:

- valid Weakness Asset identifier;
- valid referenced Ability Block;
- compatible representation mode;
- required descriptors are present;
- penalty is a positive supported value;
- weakness bonus equals the weakness penalty;
- combined weakness total does not exceed the applicable limit;
- required class weaknesses are satisfied;
- forbidden weaknesses are rejected;
- editing permissions are respected;
- referenced assets belong to loaded Rule Packs.

For a normal monster, the default maximum weakness total SHALL be calculated as:

```text
floor((Brains + Brawn) / 2)
```

The Validation Engine SHALL use an explicit override when a Class, Core Feature or other rule replaces this formula.

---

### 11.11 Serialization

Monster Documents SHALL serialize weakness instances, not complete Weakness Asset definitions.

Each serialized weakness instance SHOULD contain:

- instance ID
- Weakness Asset ID
- referenced Ability Block ID
- representation mode
- penalty value
- descriptor values
- optional display name
- optional notes

Complete Weakness Asset definitions SHALL remain exclusively within the Rules Asset library.

Derived bonus ability points SHOULD be recalculated rather than treated as authoritative stored data.

---

### 11.12 TypeScript Interfaces

```ts
type WeaknessRepresentationMode =
    | "directPenalty"
    | "opponentBonus";

interface WeaknessAsset extends RuleAsset {
    representationMode: WeaknessRepresentationMode[];
    compatibleBlockIds: string[];
    requiredDescriptorIds: string[];
    allowedNotation: ("minus" | "arrow")[];
    minimumPenalty: number;
    maximumPenaltyRuleId: string;
    generatedTextTemplate: string;
    validationRuleIds: string[];
    editorHints?: object;
}

interface WeaknessInstance {
    id: string;
    weaknessAssetId: string;
    abilityBlockId: string;
    representationMode: WeaknessRepresentationMode;
    penalty: number;
    descriptors: Record<string, unknown>;
    displayName?: string;
    notes?: string;
}
```

---

### 11.13 Canonical JSON Example

*Illustrative example only. Asset IDs, field names, relationships, and values in this example have not yet been verified as authoritative official Mortasheen rules data unless the surrounding text explicitly states otherwise.*

```json
{
  "id": "weakness.passiveBlockInversion",
  "assetType": "weakness",
  "displayName": "Passive Block Inversion",
  "representationMode": [
    "directPenalty",
    "opponentBonus"
  ],
  "compatibleBlockIds": [
    "armor",
    "resist"
  ],
  "requiredDescriptorIds": [
    "damageType"
  ],
  "allowedNotation": [
    "minus",
    "arrow"
  ],
  "minimumPenalty": 1,
  "maximumPenaltyRuleId": "weaknessLimit.default",
  "generatedTextTemplate": "[{block} {notation}{penalty} {descriptors}]",
  "validationRuleIds": [
    "WEAKNESS_PENALTY_INVALID",
    "WEAKNESS_LIMIT_EXCEEDED"
  ]
}
```

A monster-specific weakness instance may serialize as:

```json
{
  "id": "weakness-instance-1",
  "weaknessAssetId": "weakness.passiveBlockInversion",
  "abilityBlockId": "resist",
  "representationMode": "opponentBonus",
  "penalty": 3,
  "descriptors": {
    "damageType": "chemical"
  },
  "displayName": "Chemical Vulnerability"
}
```

---

### 11.14 Implementation Notes

The Rule Engine SHALL calculate weakness bonus points from the sum of valid weakness penalties.

The Rule Engine MUST NOT treat weaknesses as body-part abilities.

The Validation Engine SHALL calculate both:

- the total weakness penalty;
- the applicable weakness maximum.

The UI SHOULD present weaknesses separately from body parts and normal abilities.

The UI SHOULD display:

- total base ability points;
- normal or overridden weakness limit;
- weakness points selected;
- bonus ability points granted;
- remaining weakness allowance.

The builder SHOULD generate formal block notation and readable explanatory text from the selected Weakness Asset, Ability Block and descriptors.

---

### 11.15 Future Extension Points

Future Rule Packs MAY extend Weakness Assets with:

- non-block-based weaknesses;
- conditional weaknesses;
- triggered weaknesses;
- weaknesses with multiple descriptors;
- weaknesses applying only to particular opponents;
- weaknesses with variable point conversion;
- class-specific weakness templates;
- guided weakness-generation tools;
- editor recommendations;
- AI evaluation metadata

without requiring modifications to the general Rules Asset schema.

## Chapter 12 — Rule Pack Manifest

### 12.1 Purpose

The Rule Pack Manifest defines the metadata required for the application to discover, identify, validate and load a Rule Pack. It acts as the entry point for every Rules Asset collection and provides the information necessary to determine compatibility before any assets are imported.

### 12.2 Responsibilities

A Rule Pack Manifest SHALL define:

- Rule Pack identifier
- Display name
- Version
- Author and publisher metadata
- Compatibility requirements
- Asset registry
- Dependencies
- Optional load priority
- Localization metadata

A Rule Pack Manifest MUST NOT contain gameplay rules or duplicate Rule Asset definitions.

### 12.3 Manifest Schema

Additional fields beyond RuleAsset:

- packId
- semanticVersion
- compatibleApplicationVersions
- assetCollections
- dependencyIds
- optionalDependencyIds
- author
- homepage
- license
- localizationLanguages

### 12.4 Semantic Rules

Every Rule Pack SHALL expose exactly one Manifest.

Each Manifest SHALL uniquely identify its Rule Pack.

Rule Packs MAY depend on one or more other Rule Packs through stable identifiers.

The application MUST validate the Manifest before loading any assets.

### 12.5 Package Structure

A Rule Pack SHOULD organize assets into logical collections, such as:

- Monster Classes
- Sizes
- Ability Blocks
- Descriptors
- Damage Types
- Conditions
- Movement Types
- Core Features
- Validation Rules
- Localization Resources

The Manifest SHALL reference these collections rather than embedding them.

### 12.6 Asset Registration

Every asset included in a Rule Pack SHALL be registered in the Manifest through its collection.

Duplicate asset identifiers within the same Rule Pack MUST NOT be permitted.

### 12.7 Validation

Validation SHALL verify:

- valid Rule Pack identifier
- semantic version format
- required metadata
- dependency resolution
- duplicate asset identifiers
- supported application version
- valid referenced asset collections

### 12.8 Serialization

Rule Pack Manifests SHALL be serialized independently from gameplay assets.

The application MUST load the Manifest before loading any referenced asset collection.

### 12.9 TypeScript Interface

```ts
interface RulePackManifest extends RuleAsset {
  packId:string;
  semanticVersion:string;
  compatibleApplicationVersions:string[];
  assetCollections:string[];
  dependencyIds:string[];
  optionalDependencyIds?:string[];
  author?:string;
  homepage?:string;
  license?:string;
  localizationLanguages:string[];
}
```

### 12.10 Canonical JSON Example

*Illustrative example only. Asset IDs, field names, relationships, and values in this example have not yet been verified as authoritative official Mortasheen rules data unless the surrounding text explicitly states otherwise.*

```json
{
  "packId":"mortasheen_official",
  "semanticVersion":"1.0.0",
  "assetCollections":[
    "monsterClasses",
    "sizes",
    "abilityBlocks",
    "descriptors",
    "damageTypes",
    "conditions",
    "movementTypes",
    "coreFeatures"
  ],
  "compatibleApplicationVersions":["1.x"],
  "localizationLanguages":["en"]
}
```

### 12.11 Implementation Notes

The application MUST treat the Rule Pack Manifest as the authoritative entry point for loading a Rule Pack.

The Rule Engine, Validation Engine and UI SHOULD discover Rule Assets exclusively through the Manifest rather than scanning arbitrary files.

### 12.12 Future Extension Points

Future Rule Packs MAY extend the Manifest with digital signatures, asset hashes, optional modules, downloadable content metadata, migration scripts, feature flags and package repository information without requiring changes to the Rule Engine.

## Chapter 13 — Asset Relationships and Dependencies

### 13.1 Purpose

This chapter defines the standardized relationship model used by all Rule Assets. Rather than embedding data inside other assets, the Rules Asset system relies on explicit references through stable identifiers. This approach minimizes duplication, simplifies maintenance, and allows Rule Packs to extend the system without modifying existing assets.

Asset relationships are considered first-class data and SHALL be validated independently of gameplay logic.

---

### 13.2 Design Principles

The Rules Asset dependency model SHALL follow these principles:

- Assets reference other assets exclusively through stable IDs.
- Relationships SHALL be explicit rather than implied.
- Asset definitions SHALL remain immutable after publication.
- Assets SHALL never duplicate information that already exists elsewhere.
- Dependencies SHALL remain deterministic and version-independent whenever possible.
- The Rule Engine SHALL resolve relationships dynamically from loaded Rule Packs.

---

### 13.3 Dependency Types

The Rules Asset system recognizes the following dependency types.

#### Required Dependency

The referencing asset cannot function without the referenced asset.

Example:

- Monster Class → Core Feature

---

#### Optional Dependency

The referenced asset provides additional functionality but is not required.

Example:

- Ability Block → optional visual metadata

---

#### Conditional Dependency

The dependency exists only when specific requirements are satisfied.

Example:

- Ability Block requiring a Damage Type descriptor only if damage is inflicted.

---

#### Collection Dependency

An asset references an ordered or unordered collection of assets.

Example:

- Rule Pack Manifest → Asset Collections

---

#### Bidirectional Relationship

Two assets may reference each other for discoverability while remaining independently valid.

Bidirectional references SHOULD be avoided unless they provide meaningful editor functionality.

---

### 13.4 Relationship Rules

Relationships SHALL obey the following rules:

- References SHALL use stable asset identifiers.
- Assets SHALL NOT reference runtime monster state.
- Assets SHALL NOT reference generated UI objects.
- Assets SHALL NOT reference serialized save files.
- Relationships SHALL remain valid across Rule Pack versions whenever possible.
- Deprecated assets MAY continue to satisfy references until officially removed.

---

### 13.5 Reference Graph

The Rules Asset system forms a directed dependency graph.

Typical dependency flow:

```
Rule Pack Manifest
        │
        ▼
Monster Class
        │
        ▼
Core Feature
        │
        ▼
Ability Block
        │
        ▼
Descriptor
        │
        ├────────► Damage Type
        ├────────► Movement Type
        ├────────► Condition
        └────────► Other Descriptor Assets
```

The Rule Engine SHALL traverse this graph dynamically during validation and gameplay interpretation.

---

### 13.6 Circular Dependency Prevention

Circular dependencies SHOULD be avoided whenever possible.

The Validation Engine SHALL detect:

- direct cycles
- indirect cycles
- impossible dependency chains
- self-referencing assets

Rule Packs MUST NOT contain dependency graphs that prevent deterministic loading.

Where circular references are unavoidable for editor convenience, they SHALL be explicitly marked as informational rather than structural dependencies.

---

### 13.7 Validation

Validation SHALL verify:

- referenced asset exists
- referenced asset type is valid
- referenced asset belongs to a loaded Rule Pack
- dependency graph contains no invalid cycles
- required dependencies are satisfied
- deprecated assets are reported appropriately
- duplicate references are ignored or consolidated according to asset rules

---

### 13.8 Serialization

Serialized Monster documents SHALL contain only stable asset identifiers.

Relationships SHALL never be serialized as embedded Rule Asset objects.

Rule Packs SHALL reconstruct the dependency graph by resolving identifiers after loading.

This guarantees deterministic save files independent of Rule Pack implementation details.

---

### 13.9 TypeScript Interface

```ts
interface AssetReference {
    assetId: string;
    assetType: string;
    dependencyType:
        | "required"
        | "optional"
        | "conditional"
        | "collection";
}

interface RelationshipCollection {
    references: AssetReference[];
}
```

---

### 13.10 Canonical JSON Example

*Illustrative example only. Asset IDs, field names, relationships, and values in this example have not yet been verified as authoritative official Mortasheen rules data unless the surrounding text explicitly states otherwise.*

```json
{
  "assetId": "damage",
  "references": [
    {
      "assetId": "damageType",
      "assetType": "descriptor",
      "dependencyType": "required"
    },
    {
      "assetId": "burning",
      "assetType": "condition",
      "dependencyType": "optional"
    }
  ]
}
```

---

### 13.11 Implementation Notes

The Rule Engine SHALL resolve relationships exclusively through the dependency graph rather than through hardcoded assumptions.

The Validation Engine SHOULD construct the complete dependency graph during Rule Pack loading and cache the result for later validation.

The UI SHOULD use relationship metadata to provide automatic navigation between related assets, dependency visualization, and context-sensitive editing.

Relationships SHALL be treated as immutable metadata and MUST NOT be modified during gameplay.

---

### 13.12 Future Extension Points

Future Rule Packs MAY extend the dependency system with:

- dependency priorities
- soft compatibility relationships
- conflict declarations
- asset replacement rules
- dependency aliases
- inheritance relationships
- editor-only relationships
- dependency visualization metadata

without requiring modifications to the Rule Engine or the Rules Asset schema.

## Chapter 14 — Versioning and Migration

### 14.1 Purpose

This chapter defines how Rule Assets evolve over time while maintaining compatibility between Rule Packs, Monster Documents, and future versions of the Monster Builder.

The Versioning and Migration system SHALL ensure that previously created monsters remain interpretable whenever possible, while providing deterministic mechanisms for detecting and resolving incompatibilities.

The Rule Engine SHALL treat version information as metadata rather than gameplay rules.

---

### 14.2 Design Principles

The Versioning system SHALL follow these principles:

- Stable asset identifiers are permanent.
- Semantic meaning SHALL never change without a version change.
- Rule Assets are immutable after publication.
- Backward compatibility SHALL be preferred whenever practical.
- Migration SHALL be deterministic.
- Migration SHALL never modify official Rule Assets.
- User-created monsters MAY be migrated.
- Rule Packs MAY provide migration rules.

---

### 14.3 Versioning Model

Every Rule Asset SHALL expose its own semantic version.

Example:

```
1.0.0
```

The Versioning system SHALL follow Semantic Versioning (SemVer):

#### Major Version

Incremented when compatibility is intentionally broken.

Examples:

- removing required fields
- changing gameplay semantics
- replacing dependency models

---

#### Minor Version

Incremented when new capabilities are introduced without breaking compatibility.

Examples:

- additional optional fields
- new descriptors
- new metadata

---

#### Patch Version

Incremented when corrections are made without changing semantic meaning.

Examples:

- documentation improvements
- localization corrections
- metadata fixes

Patch releases MUST NOT alter gameplay behavior.

---

### 14.4 Asset Identity

Every Rule Asset SHALL be uniquely identified by:

- asset ID
- asset type

Version information SHALL NOT be part of the asset identifier.

For example:

```
damage
```

remains the same asset regardless of whether its current version is:

```
1.0.0
1.2.4
2.0.0
```

---

### 14.5 Compatibility Rules

The Validation Engine SHALL determine compatibility according to the following rules.

#### Compatible

The loaded asset satisfies the requirements of the saved Monster Document.

No migration is required.

---

#### Migratable

The Rule Pack provides deterministic migration instructions.

Migration MAY be executed automatically.

---

#### Deprecated

The asset remains usable but SHOULD generate warnings.

Editors SHOULD encourage replacement.

---

#### Unsupported

The asset cannot be interpreted.

Loading SHALL fail gracefully with diagnostic information.

---

### 14.6 Migration Model

Migration SHALL occur as a sequence of discrete transformations.

Each migration SHALL define:

- source version
- target version
- affected asset type
- transformation rules
- validation rules

Migration SHALL be deterministic.

Repeated execution of the same migration MUST produce identical results.

---

### 14.7 Migration Pipeline

Recommended migration order:

1. Validate Rule Pack.
2. Validate Manifest.
3. Load Rule Assets.
4. Detect asset versions.
5. Detect required migrations.
6. Execute migrations.
7. Validate migrated assets.
8. Load Monster Document.

No gameplay interpretation SHALL occur before migration completes successfully.

---

### 14.8 Serialization

Serialized Monster Documents SHALL record:

- Rule Pack identifier
- Rule Pack version
- Monster format version
- referenced asset identifiers

They SHOULD NOT serialize complete Rule Assets.

This minimizes save-file size while ensuring deterministic reconstruction.

---

### 14.9 TypeScript Interface

```ts
interface AssetVersion {
    major: number;
    minor: number;
    patch: number;
}

interface MigrationDefinition {
    id: string;
    sourceVersion: string;
    targetVersion: string;
    assetType: string;
    description: string;
}
```

---

### 14.10 Canonical JSON Example

*Illustrative example only. Asset IDs, field names, relationships, and values in this example have not yet been verified as authoritative official Mortasheen rules data unless the surrounding text explicitly states otherwise.*

```json
{
  "assetId": "damage",
  "version": "1.2.0",
  "migration": {
    "sourceVersion": "1.1.0",
    "targetVersion": "1.2.0"
  }
}
```

---

### 14.11 Migration Strategies

The Monster Builder SHOULD support multiple migration strategies.

#### Automatic Migration

Migration executes without user intervention.

Used when transformations are deterministic.

---

#### Assisted Migration

The editor requests user confirmation before applying changes.

Used when multiple valid outcomes exist.

---

#### Manual Migration

The application reports incompatibilities and requires user action.

Used when deterministic migration is impossible.

---

### 14.12 Validation

Validation SHALL verify:

- semantic version format
- supported Rule Pack version
- valid migration path
- migration completeness
- compatible asset references
- absence of unsupported versions

Validation SHALL occur before gameplay interpretation.

---

### 14.13 Implementation Notes

The Rule Engine SHALL never contain hardcoded migration logic.

Migration definitions SHOULD be loaded as data from Rule Packs whenever possible.

The Validation Engine SHOULD build a migration graph during Rule Pack loading.

Migration operations SHOULD be reversible whenever sufficient information exists.

Editor interfaces SHOULD clearly distinguish:

- original asset version
- current asset version
- migration status
- compatibility status

---

### 14.14 Future Extension Points

Future Rule Packs MAY extend the migration system with:

- chained migrations
- migration priorities
- rollback definitions
- asset splitting
- asset merging
- automatic conflict resolution
- migration scripting
- version aliases
- experimental upgrade paths

without requiring modifications to the Rule Engine.

## Chapter 15 — Official Rules Import and Rule Pack Generation

### 15.1 Purpose

This chapter defines how the Monster Builder obtains the official Mortasheen rules and converts them into internal Rule Assets.

The official Mortasheen rulebooks remain the authoritative source of gameplay content.

The Monster Builder SHALL generate its internal Rule Pack from those official rules rather than maintaining an independent copy of the game's data.

This chapter defines the import process, responsibilities and guarantees provided by the generated Rule Pack.

---

### 15.2 Source of Truth

The official Mortasheen publications SHALL be considered the sole authoritative source of official gameplay rules.

These sources include, but are not limited to:

- official rulebooks
- official supplements
- official errata
- official revisions

The specification documents contained in this project SHALL define software architecture only.

They SHALL NOT redefine official gameplay content.

Whenever a discrepancy exists between a generated Rule Asset and the official rules, the official rules SHALL take precedence.

---

### 15.3 Rule Pack Generation

The application SHALL represent official gameplay rules internally as a generated Rule Pack.

Generation SHALL transform official game content into Rule Assets conforming to this specification.

The generated Rule Pack SHALL contain:

- Monster Class Assets
- Size Assets
- Ability Block Assets
- Descriptor Assets
- Damage Type Assets
- Condition Assets
- Movement Type Assets
- Core Feature Assets
- Weakness Assets

Applications SHALL treat this Rule Pack as read-only.

---

### 15.4 Import Responsibilities

The import process SHALL:

- preserve gameplay semantics;
- preserve official terminology;
- preserve rule relationships;
- preserve official identifiers whenever practical;
- generate stable internal identifiers when official identifiers do not exist;
- generate Rule Assets conforming to this specification.

The importer SHALL NOT alter gameplay behavior.

---

### 15.5 Rule Interpretation

When importing official rules, the importer SHALL distinguish between:

- explicit rules;
- examples;
- explanatory text;
- designer commentary.

Only explicit gameplay rules SHALL become Rule Assets.

Examples and commentary MAY be stored as documentation metadata but SHALL NOT alter gameplay behavior.

---

### 15.6 Generated Rule Assets

Generated Rule Assets SHALL remain fully compliant with every preceding chapter of this specification.

Every generated asset SHALL:

- possess a stable identifier;
- include source references where practical;
- validate successfully;
- participate in Rule Pack loading;
- remain independent of Monster Documents.

Applications SHALL interact exclusively with generated Rule Assets during normal operation.

---

### 15.7 Validation

Validation SHALL verify:

- all required Rule Assets were generated;
- generated assets satisfy this specification;
- required relationships are present;
- generated identifiers are unique;
- no unsupported rule constructs remain unresolved.

Import failures SHALL produce diagnostic information identifying the affected source material.

---

### 15.8 Serialization

Generated Rule Packs SHALL be serialized independently of Monster Documents.

Monster Documents SHALL reference generated Rule Assets exclusively through stable identifiers.

The original rulebooks SHALL never be embedded within Monster Documents.

---

### 15.9 TypeScript Interfaces

```ts
interface RuleImportResult {
    rulePackId: string;
    generatedAssets: string[];
    warnings: ImportWarning[];
    errors: ImportError[];
}

interface ImportWarning {
    code: string;
    message: string;
}

interface ImportError {
    code: string;
    message: string;
}
```

---

### 15.10 Illustrative JSON Example

*Illustrative example only. This example demonstrates the structure of an import result and is not authoritative Mortasheen rules data.*

```json
{
  "rulePackId": "mortasheen_official",
  "generatedAssets": [
    "monsterClasses",
    "sizes",
    "abilityBlocks",
    "descriptors",
    "damageTypes"
  ],
  "warnings": [],
  "errors": []
}
```

---

### 15.11 Implementation Notes

The Rule Engine SHALL never read gameplay rules directly from the published rulebooks.

Instead, gameplay SHALL always be evaluated using generated Rule Assets.

This separation ensures that:

- gameplay logic remains data-driven;
- validation remains deterministic;
- Rule Packs remain interchangeable;
- homebrew Rule Packs can coexist with the official Rule Pack.

Applications SHOULD generate the official Rule Pack once and cache it for subsequent sessions.

---

### 15.12 Future Extension Points

Future versions of the importer MAY support:

- official expansions;
- official errata;
- multiple official editions;
- localized rulebooks;
- plugin-defined importers;
- automated rule extraction;
- incremental Rule Pack regeneration;
- verification against published revisions.

These extensions SHALL NOT require modifications to the Rules Asset Specification itself.
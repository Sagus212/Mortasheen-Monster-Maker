# Mortasheen Monster Builder

# Rule Engine Specification

Version 1.1

> Canonical rule evaluation specification for the Mortasheen Monster Builder.

This document defines the deterministic evaluation engine responsible for gameplay calculations, dependency resolution, validation support and generated mechanical summaries.

Unless explicitly stated otherwise, rule evaluation behavior described here is authoritative and referenced by all subordinate specifications.

This specification defines:

- evaluation architecture;
- calculation pipeline;
- canonical calculations;
- dependency resolution;
- trace generation;
- validation integration.

This specification intentionally avoids:

- user interface behavior;
- persistence;
- serialization;
- implementation framework details.

This specification complements, but does not replace:

- Master Software Design Specification
- Data Model Specification
- Rules Asset Specification
- Validation & Testing Specification
- UI, Export & Development Workflow Specification

---

# Part I — Engine Foundations

---

## Chapter 1 — Purpose and Authority

### 1.1 Purpose

This specification defines the canonical Rule Engine used throughout the Mortasheen Monster Builder.

The Rule Engine is responsible for deterministic gameplay calculations, dependency resolution, validation support and generation of mechanical summaries.

---

### 1.2 Scope

This specification governs:

- gameplay calculations;
- dependency resolution;
- calculation traces;
- engine diagnostics;
- evaluation workflow;
- calculation interfaces.

This specification SHALL NOT define:

- persistent project data;
- Rule Asset definitions;
- user interface behavior;
- serialization.

---

### 1.3 Authority

For gameplay rules, authority SHALL be interpreted in the following order:

1. Official Mortasheen Rulebook.
2. Reviewed Rule Asset decisions.
3. This specification.

For software architecture, this specification is subordinate only to the Master Software Design Specification.

Where rule ambiguity exists, the Rule Engine SHALL expose that ambiguity rather than silently selecting an interpretation.

---

### 1.4 Objectives

The Rule Engine SHALL provide:

- deterministic evaluation;
- explainable calculations;
- implementation independence;
- reproducible results;
- version awareness.

---

### 1.5 Outputs

This specification establishes the authoritative behavior of the Rule Engine.

---

## Chapter 2 — Engine Boundaries

### 2.1 Purpose

This chapter defines the architectural responsibilities of the Rule Engine.

---

### 2.2 Responsibilities

The Rule Engine SHALL:

- calculate gameplay values;
- resolve Rule Asset references;
- evaluate legality;
- generate mechanical summaries;
- produce calculation traces;
- generate diagnostics.

---

### 2.3 Exclusions

The Rule Engine SHALL NOT:

- access the user interface;
- modify application state;
- read or write persistent storage;
- manage serialization;
- perform presentation formatting.

---

### 2.4 Ownership

The Rule Engine consumes:

- Monster Documents;
- Rule Packs;
- Build Context.

The Rule Engine produces:

- Derived Data;
- Validation Reports;
- Mechanical Summaries;
- Rule Traces.

The Rule Engine SHALL NOT own persistent project data.

---

### 2.5 Architectural Position

The Rule Engine operates between persistent project data and presentation layers.

```text
Monster Document
        │
        ▼
Rule Engine
        │
        ▼
Derived Results
        │
        ▼
Validation
Presentation
Export
```

---

### 2.6 Outputs

The Rule Engine provides deterministic evaluation without altering authored project information.

---

## Chapter 3 — Design Requirements

### 3.1 Purpose

This chapter defines the principles governing Rule Engine implementation.

---

### 3.2 Determinism

Equivalent inputs SHALL produce semantically equivalent outputs.

Evaluation SHALL remain deterministic.

---

### 3.3 Purity

Rule evaluation SHOULD be pure by default.

Calculations SHALL depend only upon:

- authored data;
- Rule Assets;
- Build Context.

---

### 3.4 Side Effects

Rule evaluation SHALL NOT modify supplied Monster Documents.

Derived information SHALL remain external to persistent project data.

---

### 3.5 Explainability

Every user-visible calculated value SHOULD be explainable through Rule Traces.

---

### 3.6 Draft Tolerance

Incomplete Monster Documents SHOULD produce partial calculations together with diagnostics.

Ordinary user omissions SHALL NOT produce engine failures.

---

### 3.7 System Boundaries

Malformed Rule Packs, unsupported schema versions and structurally invalid project data SHALL produce explicit engine failures.

---

### 3.8 Version Awareness

Evaluation SHALL record:

- engine version;
- Rule Pack version;
- document version;
- compatibility information.

---

### 3.9 Integer Safety

Gameplay arithmetic SHALL use integer calculations unless official rules explicitly require otherwise.

---

### 3.10 Outputs

These requirements govern every Rule Engine calculation.

---

## Chapter 4 — Public API

### 4.1 Purpose

This chapter defines the canonical public interface of the Rule Engine.

---

### 4.2 Primary Interface

```ts
export interface RuleEngine {
    evaluate(input: EvaluationInput): EvaluationResult;

    calculate<K extends CalculationKey>(
        key: K,
        input: CalculationInputMap[K]
    ): CalculationResultMap[K];

    validate(input: ValidationInput): ValidationReport;

    compareAlteration(
        input: AlterationInput
    ): AlterationResult;

    explain(traceId: string): RuleTrace | undefined;
}
```

---

### 4.3 Evaluation Input

```ts
export interface EvaluationInput {
    document: MonsterDocument;
    ruleset: RulePack;
    options: EvaluationOptions;
}
```

---

### 4.4 Evaluation Result

```ts
export interface EvaluationResult {

    normalized: NormalizedMonsterView;

    calculations: DerivedMonsterSnapshot;

    validation: ValidationReport;

    summaries: MechanicalSummary;

    traces: RuleTrace[];

    engineMetadata: EngineMetadata;

}
```

---

### 4.5 API Responsibilities

| API | Responsibility |
|------|----------------|
| `evaluate()` | Performs complete Rule Engine evaluation. |
| `calculate()` | Executes one calculator independently. |
| `validate()` | Executes Validation Engine using calculated facts. |
| `compareAlteration()` | Compares two Monster Definitions for alteration costs. |
| `explain()` | Retrieves Rule Traces explaining derived results. |

---

### 4.6 Outputs

The public API provides implementation-independent access to Rule Engine functionality.

---

## Chapter 5 — Evaluation Pipeline

### 5.1 Purpose

This chapter defines the canonical evaluation workflow.

---

### 5.2 Canonical Evaluation Pipeline

Evaluation SHALL proceed in the following order.

```text
Compatibility Check
        │
        ▼
Normalization
        │
        ▼
Reference Resolution
        │
        ▼
Dependency Graph
        │
        ▼
Canonical Calculators
        │
        ▼
Validation
        │
        ▼
Mechanical Summary Generation
        │
        ▼
Rule Trace Generation
        │
        ▼
Evaluation Result
```

---

### 5.3 Pipeline Stages

1. Verify schema compatibility.
2. Normalize authored structures.
3. Resolve Rule Asset references.
4. Construct dependency graph.
5. Execute canonical calculators.
6. Execute Validation Engine.
7. Generate mechanical summaries.
8. Generate Rule Traces.

---

### 5.4 Availability Rules

Unavailable values SHALL remain unavailable.

Unavailable SHALL NOT be interpreted as zero.

Calculators SHALL propagate unavailable dependencies rather than invent replacement values.

---

### 5.5 Immutability

Evaluation SHALL NOT modify the supplied Monster Document.

Derived values SHALL remain external to persistent project data.

---

### 5.6 Outputs

The evaluation pipeline produces deterministic Rule Engine results suitable for Validation, export and presentation.

---

## Chapter 6 — Shared Result and Diagnostic Types

### 6.1 Purpose

This chapter defines common structures returned by the Rule Engine.

---

### 6.2 Availability

```ts
type Availability =
    | "available"
    | "partial"
    | "unavailable";
```

---

### 6.3 Derived Values

```ts
type DerivedValue<T> = {

    value?: T;

    availability: Availability;

    traceId: string;

    dependencyIds: string[];

};
```

---

### 6.4 Engine Diagnostics

```ts
type EngineDiagnostic {

    code: string;

    severity:
        | "error"
        | "warning"
        | "info";

    path?: string;

    messageKey: string;

    parameters?: Record<string, unknown>;

    sourceRefs: SourceReference[];

    blocksCalculation?: boolean;

}
```

---

### 6.5 Diagnostic Severity

| Severity | Meaning | Effect |
|----------|---------|--------|
| Error | Structural failure or rule violation. | Legal build becomes false. |
| Warning | Legal but noteworthy condition. | Does not invalidate the build. |
| Info | Informational message. | No gameplay effect. |

---

### 6.6 Outputs

Shared result types provide consistent communication between Rule Engine subsystems.

---

## Chapter 7 — Dependency and Trace Model

### 7.1 Purpose

This chapter defines the trace model used to explain Rule Engine calculations.

---

### 7.2 Rule Traces

```ts
interface RuleTrace {

    id: string;

    ruleId: string;

    labelKey: string;

    expression?: string;

    inputs: TraceInput[];

    steps: TraceStep[];

    result?: number | string | boolean;

    sourceRefs: SourceReference[];

    confidence:
        | "confirmed"
        | "ambiguous"
        | "pending";

}
```

---

### 7.3 Explainability

Every user-visible derived value SHOULD possess an associated Rule Trace.

---

### 7.4 Confidence

Rule Traces SHALL explicitly indicate whether their underlying interpretation is:

- confirmed;
- ambiguous;
- pending.

---

### 7.5 Source References

Rule Traces SHOULD reference:

- official rules;
- Rule Asset definitions;
- project rule decisions.

---

### 7.6 Trace Example

The following simplified trace illustrates explainability.

```text
Construction Goal

Inputs
 ├─ Total Endurance = 11
 ├─ Attributes = 7
 ├─ Class Multiplier = 2
 └─ COST-001 = default

Formula

(11 + 7) × 2

Result

36

Sources

Pages 48–49
```

The example is illustrative only.

Every Rule Trace SHALL preserve the actual dependency identifiers rather than rendered labels.

---

### 7.7 Outputs

Rule Traces provide deterministic explanations of every derived calculation.

---

# Part II — Calculation Engine

---

## Chapter 8 — Canonical Calculations

### 8.1 Purpose

This chapter defines the canonical set of calculations performed by the Rule Engine.

Each calculation SHALL represent one independent unit of gameplay evaluation.

Calculations SHALL remain deterministic and implementation-independent.

---

### 8.2 Responsibilities

Canonical calculations SHALL:

- evaluate authored project data;
- consume Rule Assets;
- produce Derived Data;
- expose Rule Traces;
- avoid modifying persistent entities.

---

### 8.3 Calculator Independence

Each calculator SHOULD possess one clearly defined responsibility.

Calculators SHOULD remain independently executable whenever practical.

---

### 8.4 Calculator Ordering

Calculators MAY depend upon previously calculated values.

Dependency relationships SHALL be resolved through the Dependency Graph.

Execution order SHALL be determined by dependencies rather than declaration order.

---

### 8.5 Shared Calculator Interface

```ts
interface Calculator<TInput, TResult> {

    calculate(input: TInput): TResult;

}
```

Individual implementations MAY expose richer interfaces.

---

### 8.6 Canonical Calculator Catalogue

The Rule Engine SHALL expose at least the following canonical calculators.

| Calculator | Primary Output |
|------------|----------------|
| `calculatePartsLimit()` | Allowed ordinary Parts |
| `calculateEnduranceAllowance()` | Allowed ordinary Endurance |
| `calculateAttributeAllowance()` | Allowed Attribute points |
| `calculateBaseAbilityAllowance()` | Base Ability budget |
| `calculateWeaknessBonusLimit()` | Maximum Weakness bonus |
| `calculateWeaknessBonus()` | Granted bonus points |
| `calculateTotalAbilityBudget()` | Total Ability budget |
| `calculateAbilityPointsSpent()` | Ability points used |
| `calculateTotalEndurance()` | Ordinary Part Endurance |
| `calculateScanalysisGoal()` | Scanalysis requirement |
| `calculateConstructionProjectGoal()` | Construction project goal |
| `calculateDataCost()` | Final data cost |

Each calculator SHALL expose:

- inputs;
- output;
- algorithm;
- diagnostics;
- Rule Trace;
- authoritative source references.

---

## Chapter 9 — Construction Cost Calculation

### 9.1 Purpose

This chapter defines calculation of monster construction cost.

### 9.2 Responsibility

Construction calculations determine the canonical project goal and derived data cost for Monster construction.

Construction calculations SHALL preserve explicit Rule Decisions whenever official rules remain ambiguous.

---

### 9.3 Rule Decision COST-001

Source inconsistency exists between printed pages 48 and 49.

The printed formula on page 48 uses only Attributes.

The worked Aerofoul example on page 49 includes Endurance.

The Rule Engine SHALL expose this ambiguity explicitly.

| Rule Decision | Default | Alternative |
|--------------|---------|-------------|
| COST-001 | `(ordinary endurance + Brains + Brawn) × class multiplier` | `(Brains + Brawn) × class multiplier` |

Every calculation SHALL identify which interpretation produced the result.

---

### 9.4 calculateConstructionProjectGoal()

| Item | Specification |
|------|---------------|
| Inputs | Total ordinary Endurance, Brains, Brawn, Class multiplier, premade-part discounts, COST-001 |
| Output | `DerivedValue<number>` and `CostBreakdown` |
| Algorithm | Default: `(ordinary endurance + Brains + Brawn) × multiplier`, then apply legal project discounts. Preserve both gross and net values. |
| Errors | Missing multiplier, unresolved decision, invalid discount provenance |
| Warnings | Ambiguous until official clarification |
| Source | Printed pages 48–49 |

---

### 9.5 calculateDataCost()

| Item | Specification |
|------|---------------|
| Inputs | Net project goal |
| Output | `DerivedValue<number>` |
| Algorithm | `dataCost = projectGoal × 5` |
| Errors | None |
| Warnings | None |
| Source | Printed pages 48–49 |

---

### 9.6 Outputs

Construction calculations provide deterministic manufacturing cost information while preserving explicit rule ambiguity through COST-001.

---

## Chapter 10 — Alteration Calculations

### 10.1 Purpose

This chapter defines calculations used when modifying existing monsters.

---

### 10.2 Responsibility

Alteration calculations compare two `Monster` aggregates.

---

### 10.3 diffMonsterForAlteration()

| Item | Specification |
|------|---------------|
| Inputs | Current Monster, original baseline, Rule Pack |
| Output | `AlterationDiff` |
| Algorithm | Compare stable Part IDs first. Detect additions, removals, replacements, Endurance edits, Ability edits, Weakness edits and protected traits. Reordering alone is not chargeable. Renaming alone is not chargeable. |
| Errors | Missing baseline |
| Warnings | None |
| Source | Printed page 48 |

---

### 10.4 calculateChargeableAlterationPoints()

| Item | Specification |
|------|---------------|
| Inputs | AlterationDiff |
| Output | `DerivedValue<number>` |
| Algorithm | Count points changed on affected Parts. Same-cost replacements remain chargeable under the reviewed interpretation. |
| Errors | None |
| Warnings | Preserve unresolved replacement behavior as ALTER-002. |
| Source | Printed page 48 |

---

### 10.5 calculateChargeableEndurance()

| Item | Specification |
|------|---------------|
| Inputs | AlterationDiff |
| Output | `DerivedValue<number>` |
| Algorithm | Preserve both absolute Endurance change and resulting Part Endurance until ALTER-001 is resolved. |
| Errors | None |
| Warnings | ALTER-001 remains unresolved. |
| Source | Printed page 48 |

---

### 10.6 Outputs

Alteration calculations SHALL expose complete semantic differences while preserving unresolved rule decisions explicitly.

---

## Chapter 11 — Permission and Availability Resolution

### 11.1 Purpose

This chapter defines calculation of construction permissions and available options.

---

### 11.2 Responsibility

Permission calculations determine which Rule Assets are available for selection.

---

### 11.3 Canonical Permission Calculators

The Permission Engine SHALL expose at least:

| Calculator | Responsibility |
|------------|----------------|
| `evaluateClassAccess()` | Determines legal Monster Classes. |
| `evaluateSizeAccess()` | Determines legal Size selections. |
| `calculateDisobedienceThreshold()` | Determines obedience warnings using Scientist Madness and Monster Brains. |

---

### 11.4 Inputs

Permission calculations MAY depend upon:

- Scientist attributes;
- Respectability;
- discoveries;
- Rule Pack permissions;
- Monster Class;
- Monster Size;
- YGORE status.

---

### 11.5 Outputs

Permission calculations SHALL produce:

- legal selections;
- unavailable selections;
- blocking requirements;
- Rule Traces.

---

### 11.6 Outputs

Permission calculations define the legal construction space without modifying Rule Assets.

---

## Chapter 12 — Ability Resolution Engine

### 12.1 Purpose

This chapter defines evaluation of Ability Blocks.

---

### 12.2 Responsibility

The Ability Resolution Engine evaluates persistent Ability Block selections using Rule Asset definitions.

---

### 12.3 Resolution Sequence

Ability evaluation SHALL proceed in the following order.

1. Resolve Ability Block definitions.
2. Resolve Descriptor values.
3. Verify compatibility.
4. Evaluate costs.
5. Apply modifiers.
6. Produce generated notation.
7. Produce generated rules text.
8. Produce Rule Trace.

---

### 12.4 Resolution Responsibilities

The Ability Resolution Engine SHALL:

- verify Descriptor requirements;
- detect incompatible Enhancements;
- resolve generated notation;
- resolve generated rules text;
- expose dependency information.

---

### 12.5 Dependency Resolution

Circular dependencies SHALL produce explicit diagnostics.

Missing Ability Block definitions SHALL NOT be silently ignored.

---

### 12.6 Outputs

Ability Resolution provides deterministic Ability evaluation suitable for Validation and export.

---

## Chapter 13 — Weakness Resolution

### 13.1 Purpose

This chapter defines evaluation of persistent Weakness selections.

---

### 13.2 Responsibility

Weakness Resolution evaluates Weakness Rule Assets selected by the Monster Definition.

---

### 13.3 Resolution Responsibilities

Weakness Resolution SHALL:

- evaluate Weakness definitions;
- determine granted Ability points;
- enforce Weakness bonus limits;
- expose Rule Traces.

---

### 13.4 Weakness Knowledge Rule

Ability points granted by Weaknesses SHALL retain their provenance.

Alteration calculations SHALL distinguish:

- ordinary Ability points;
- Weakness-funded Ability points.

---

### 13.5 Outputs

Weakness evaluation SHALL produce:

- resolved Weaknesses;
- granted Ability points;
- dependency information;
- Rule Traces.

---


# Part III — Validation Engine Integration

---

## Chapter 14 — Validation Phases

### 14.1 Purpose

This chapter defines the interaction between the Rule Engine and the Validation Engine.

The Rule Engine SHALL produce all information required by Validation without directly determining construction legality.

---

### 14.2 Responsibilities

The Rule Engine SHALL:

- calculate derived facts;
- resolve Rule Asset references;
- evaluate dependencies;
- produce Rule Traces;
- expose Derived Data.

The Validation Engine SHALL:

- evaluate construction legality;
- determine construction legality;
- generate Validation Diagnostics;
- determine validation status.

---

### 14.3 Canonical Validation Phases

Validation SHALL begin after Rule Engine evaluation completes.

Evaluation MAY produce diagnostics while still supplying sufficient Derived Data for Validation.

The Validation Engine SHALL execute the following phases.

| Phase | Responsibility |
|--------|----------------|
| Phase 1 | Structural validation |
| Phase 2 | Rule Asset reference validation |
| Phase 3 | Dependency validation |
| Phase 4 | Gameplay legality validation |
| Phase 5 | Context validation |
| Phase 6 | Summary validation |
| Phase 7 | Diagnostic consolidation |

Each phase SHALL consume previously calculated Rule Engine results.

Validation SHALL NOT duplicate gameplay calculations.

---

### 14.4 Validation Workflow

```text
Monster Document
        │
        ▼
Rule Engine
        │
        ▼
Derived Monster Snapshot
        │
        ▼
Validation Phase 1
        │
        ▼
Validation Phase 2
        │
        ▼
Validation Phase 3
        │
        ▼
Validation Phase 4
        │
        ▼
Validation Phase 5
        │
        ▼
Validation Phase 6
        │
        ▼
Validation Phase 7
        │
        ▼
Validation Report
```

---

### 14.5 Failure Handling

Validation SHALL continue whenever practical.

Errors in one subsystem SHALL NOT prevent unrelated Validation Engine rules from executing.

Unavailable values SHALL remain unavailable.

---

### 14.6 Outputs

Validation phases provide deterministic legality evaluation using Rule Engine outputs.

---

## Chapter 15 — Validation Catalogue Integration

### 15.1 Purpose

This chapter defines how Rule Engine calculations support Validation Engine.

---
### 15.2 Responsibility

This chapter defines the canonical interface between the Rule Engine and the Validation Engine.

The Rule Engine SHALL provide every derived fact required for legality evaluation without duplicating Validation Engine responsibilities.

---


### 15.3 Initial Validation Catalogue

The Validation Engine SHALL expose stable machine-readable validation codes.

The following catalogue represents the initial implementation subset.

| Code | Severity | Description |
|------|----------|-------------|
| PART_LIMIT_EXCEEDED | Error | Ordinary Part limit exceeded. |
| ENDURANCE_LIMIT_EXCEEDED | Error | Allowed Endurance exceeded. |
| ATTRIBUTE_LIMIT_EXCEEDED | Error | Attribute allowance exceeded. |
| ABILITY_POINTS_EXCEEDED | Error | Ability budget exceeded. |
| INVALID_CLASS_ACCESS | Error | Scientist lacks permission for selected Class. |
| INVALID_SIZE_ACCESS | Error | Scientist lacks permission for selected Size. |
| UNKNOWN_RULE_ASSET | Error | Referenced Rule Asset cannot be resolved. |
| UNKNOWN_DESCRIPTOR | Error | Descriptor reference cannot be resolved. |
| INVALID_BLOCK_COMBINATION | Error | Ability Blocks violate compatibility rules. |
| DUPLICATE_IDENTIFIER | Error | Stable identifier collision detected. |
| CIRCULAR_DEPENDENCY | Error | Dependency graph contains a cycle. |
| AMBIGUOUS_RULE_INTERPRETATION | Warning | Calculation depends upon unresolved project decision. |
| SANDBOX_OVERRIDE | Info | Sandbox mode permitted an otherwise illegal configuration. |

Validation codes SHALL remain stable across compatible Rule Engine versions.

---

### 15.4 Validation Responsibilities

Validation SHALL:

- consume Derived Data;
- generate diagnostics;
- expose stable validation codes;
- associate diagnostics with authored entities where possible.

Validation SHALL NOT recalculate gameplay values.

---

### 15.5 Rule Trace Integration

Diagnostics SHOULD reference Rule Traces whenever calculated values contributed to the reported result.

---

### 15.6 Outputs

The Validation Catalogue provides deterministic, machine-readable diagnostics.

---

## Chapter 16 — Strictness Modes

### 16.1 Purpose

This chapter defines user-selectable strictness modes governing Validation behavior.

Strictness Modes SHALL influence validation and editing behavior without changing gameplay calculations.

---

### 16.2 Supported Modes

The Rule Engine SHALL support:

- Strict Mode;
- Guided Mode;
- Sandbox Mode.

---

### 16.3 Strict Mode

Strict Mode SHALL:

- prohibit illegal selections whenever practical;
- prevent invalid export;
- report all validation errors.

Strict Mode is intended for official play.

---

### 16.4 Guided Mode

Guided Mode SHALL:

- permit temporary illegal states;
- continuously report validation diagnostics;
- permit incomplete drafts.

Guided Mode is intended for ordinary authoring workflows.

---

### 16.5 Sandbox Mode

Sandbox Mode SHALL:

- permit arbitrary Rule Asset combinations;
- preserve diagnostic information;
- mark every overridden restriction.

Sandbox Mode SHALL NOT silently suppress diagnostics.

---

### 16.6 Calculation Independence

Strictness Modes SHALL NOT alter:

- gameplay calculations;
- Rule Traces;
- Derived Data.

Strictness Modes influence validation behavior only.

---

### 16.7 Export Behavior

Official exports SHOULD require successful Strict validation.

Sandbox exports SHALL clearly indicate their non-standard status.

---

### 16.8 Outputs

Strictness Modes define how validation interacts with editing without modifying gameplay semantics.

---

## Chapter 17 — Normalization Policy

### 17.1 Purpose

This chapter defines document normalization performed before gameplay evaluation.

---

### 17.2 Responsibilities

Normalization prepares Monster Documents for deterministic Rule Engine evaluation.

Normalization SHALL NOT modify authored project intent.

---

### 17.3 Allowed Normalization

Normalization MAY:

- apply documented default values;
- normalize identifier casing where explicitly permitted;
- canonicalize collection ordering;
- remove duplicate references when duplicates possess no semantic meaning;
- reconstruct disposable Derived Data.

---

### 17.4 Forbidden Normalization

Normalization SHALL NOT:

- invent authored gameplay choices;
- delete unresolved Rule Asset references;
- replace unknown identifiers;
- silently alter Ability selections;
- silently alter Weakness selections;
- silently modify Endurance allocations;
- silently modify Attribute allocations.

---

### 17.5 Identity Preservation

Normalization SHALL preserve:

- stable identifiers;
- authored ordering where semantically meaningful;
- ownership relationships;
- Rule Asset references.

---

### 17.6 Diagnostics

Normalization failures SHALL generate explicit Engine Diagnostics.

Normalization SHALL NOT silently repair ambiguous authored intent.

---

### 17.7 Repair Policy

Automatic repair SHALL occur only when user intent is unambiguous.

Whenever multiple valid interpretations exist, the Rule Engine SHALL preserve authored data and generate diagnostics rather than selecting one interpretation.

---

### 17.8 Outputs

Normalization prepares canonical evaluation inputs without modifying gameplay semantics.

---

## Chapter 18 — Performance and Caching

### 18.1 Purpose

This chapter defines performance requirements governing Rule Engine evaluation.

Performance optimizations SHALL preserve deterministic behavior.

---

### 18.2 Responsibilities

Performance optimizations MAY improve:

- Validation Engine speed;
- dependency resolution;
- memory usage;
- repeated calculations.

Performance optimizations SHALL NOT modify gameplay semantics.

---

### 18.3 Caching Policy

The Rule Engine MAY cache:

- resolved Rule Asset definitions;
- dependency graphs;
- calculator outputs;
- normalized evaluation inputs;
- generated Rule Traces.

Cached information SHALL remain disposable.

---

### 18.4 Cache Invalidation

Cached values SHALL be invalidated whenever any dependency changes, including:

- Monster Document changes;
- Build Context changes;
- Rule Pack changes;
- Rule Asset revisions;
- Evaluation Options changes.

Partial invalidation SHOULD be preferred over complete cache eviction whenever dependency information is available.

---

### 18.5 Memoization

Canonical calculators SHOULD behave as pure functions.

Pure calculators MAY therefore be memoized.

Memoized results SHALL remain semantically identical to uncached calculations.

---

### 18.6 Responsiveness

The Rule Engine SHOULD support incremental editing.

Only calculations depending upon modified authored data SHOULD be recomputed.

Independent calculations SHOULD reuse previous results.

---

### 18.7 Immutability

Cached objects SHALL NOT expose mutable shared state.

Implementations SHOULD prefer immutable snapshots.

---

### 18.8 Outputs

Caching improves responsiveness without modifying observable Rule Engine behavior.

---

## Chapter 19 — Testing Contract

### 19.1 Purpose

This chapter defines testing requirements governing Rule Engine implementation.

---

### 19.2 Responsibilities

Testing SHALL verify:

- deterministic Rule Engine evaluation;
- calculator correctness;
- dependency resolution;
- Rule Trace generation;
- diagnostic generation.

---

### 19.3 Testing Layers

The Rule Engine SHALL be verified using multiple testing layers.

| Layer | Responsibility |
|--------|----------------|
| Unit Tests | Verify individual calculators. |
| Integration Tests | Verify complete Rule Engine evaluation. |
| Golden Fixtures | Verify official worked examples. |
| Regression Tests | Prevent behavioral regressions. |
| Property Tests | Verify deterministic behavior. |

---

### 19.4 Unit Tests

Every canonical calculator SHOULD possess an isolated unit test suite.

Unit tests SHALL verify:

- valid inputs;
- invalid inputs;
- unavailable dependencies;
- diagnostic generation;
- Rule Trace generation.

---

### 19.5 Integration Tests

Integration tests SHALL verify:

- complete Rule Engine evaluation pipeline;
- dependency graph construction;
- Validation integration;
- generated summaries;
- Rule Trace generation.

---

### 19.6 Golden Fixtures

Official worked examples SHALL become Golden Fixtures.

The initial fixture set SHOULD include:

- Aerofoul;
- alteration examples;
- construction-cost examples;
- Weakness examples.

Golden Fixtures SHALL preserve:

- inputs;
- outputs;
- Rule Traces;
- diagnostics.

---

### 19.7 Aerofoul Fixture

The published Aerofoul construction example SHALL be preserved as the canonical integration fixture.

Future Rule Engine revisions SHALL continue producing semantically equivalent:

- construction goals;
- project costs;
- Ability budgets;
- Rule Traces;
- diagnostics.

Equivalent internal implementations MAY differ provided observable behavior remains unchanged.

---

### 19.8 Property Tests

Property tests SHOULD verify:

- deterministic Rule Engine evaluation;
- stable identifiers;
- immutable authored data;
- dependency consistency;
- calculator purity.

---

### 19.9 Regression Testing

Regression testing SHALL verify that:

- previously accepted fixtures remain valid;
- Rule Decisions continue producing expected results;
- implementation changes do not alter observable behavior.

---

### 19.10 Outputs

Testing provides continuous verification of Rule Engine correctness.

---

## Chapter 20 — Implementation Roadmap

### 20.1 Purpose

This chapter defines the recommended implementation order for the Rule Engine.

---

### 20.2 Responsibilities

The implementation roadmap establishes a logical development sequence.

The roadmap SHALL NOT redefine project architecture.

---

### 20.3 Phase 1 Implementation Scope

The initial implementation SHALL prioritize the deterministic Rule Engine.

The recommended Phase 1 subset consists of:

1. Rule Asset loading.
2. Monster Document loading.
3. Build Context loading.
4. Dependency graph.
5. Shared calculator interfaces.
6. Canonical calculators.
7. Validation Engine integration.
8. Rule Trace generation.
9. Mechanical Summary generation.
10. Engine-facing serialization interface.

Presentation polish SHALL remain outside Phase 1.

---

### 20.4 Recommended Development Order

Implementation SHOULD proceed in the following sequence.

```text
Rule Assets
        │
        ▼
Data Model
        │
        ▼
Dependency Graph
        │
        ▼
Canonical Calculators
        │
        ▼
Validation Engine
        │
        ▼
Rule Traces
        │
        ▼
Export
        │
        ▼
User Interface
```

---

### 20.5 Milestones

Each implementation milestone SHOULD produce independently testable software.

No milestone SHOULD introduce architectural contradictions.

---

### 20.6 Documentation Synchronization

Implementation SHALL remain synchronized with:

- Master Software Design Specification;
- Data Model Specification;
- Rules Asset Specification;
- Validation & Testing Specification;
- UI, Export & Development Workflow Specification.

Implementation SHALL NOT knowingly contradict authoritative project specifications.

---

### 20.7 Implementation Principles

Implementations SHOULD:

- isolate calculators;
- minimize mutable shared state;
- preserve Rule Trace generation;
- preserve deterministic behavior;
- expose diagnostics rather than hiding errors.

---

### 20.8 Scope Control

The Rule Engine SHOULD implement only reviewed official Mortasheen rules.

Unsupported mechanics SHALL produce explicit diagnostics rather than speculative implementations.

Community Rule Packs MAY extend behavior without modifying the canonical engine architecture.

---

### 20.9 Outputs

The implementation roadmap defines a deterministic path from architecture to production-ready software.

---

## Chapter 21 — Architectural Decisions Register

### 21.1 Purpose

This chapter records architectural decisions that govern Rule Engine behavior.

Architectural decisions documented here SHALL remain authoritative until formally superseded.

---

### 21.2 Responsibilities

Architectural Decisions document reviewed interpretations where official rules are ambiguous or where implementation consistency requires an explicit decision.

---

### 21.3 Active Architectural Decisions

The following decisions remain authoritative until formally superseded.

---

#### COST-001 — Construction Project Goal

| Item | Value |
|------|-------|
| Status | Active |
| Scope | Construction calculations |
| Issue | Printed pages 48 and 49 disagree regarding inclusion of Endurance. |
| Default Interpretation | `(ordinary Endurance + Brains + Brawn) × Class multiplier` |
| Alternative Interpretation | `(Brains + Brawn) × Class multiplier` |
| Engine Behavior | Both interpretations SHALL remain traceable. The active interpretation SHALL be configurable through Architectural Decisions. |

---

#### ALTER-001 — Alteration Endurance Cost

| Item | Value |
|------|-------|
| Status | Active |
| Scope | Alteration calculations |
| Issue | Official wording does not fully specify how altered Endurance contributes to alteration cost. |
| Engine Behavior | Preserve both absolute Endurance change and resulting Endurance values until official clarification exists. |

---

#### ALTER-002 — Replacement Parts

| Item | Value |
|------|-------|
| Status | Active |
| Scope | Alteration calculations |
| Issue | Same-cost replacement Parts are not explicitly addressed. |
| Default Interpretation | Same-cost replacements remain chargeable because authored content changed. |
| Alternative | Ignore replacements with zero net cost. |
| Engine Behavior | Preserve explicit Rule Trace indicating selected interpretation. |

---

#### DISCOUNT-001 — Premade Parts

| Item | Value |
|------|-------|
| Status | Active |
| Scope | Construction calculations |
| Issue | Premade Part discounts interact with project cost calculations. |
| Engine Behavior | Apply discounts after gross project goal calculation while preserving both gross and net values. |

---

#### CLASS-001 — Scientist Permissions

| Item | Value |
|------|-------|
| Status | Active |
| Scope | Permission calculations |
| Issue | Multiple rules influence Class availability. |
| Engine Behavior | Permission calculations SHALL preserve every blocking requirement rather than reporting only the first failure. |

---

#### BLOCK-001 — Unsupported Ability Blocks

| Item | Value |
|------|-------|
| Status | Active |
| Scope | Ability evaluation |
| Issue | Future Rule Packs may introduce unsupported Ability Blocks. |
| Engine Behavior | Preserve unresolved Ability Blocks and generate diagnostics rather than deleting or approximating them. |

---

### 21.4 Decision Requirements

Architectural Decisions SHALL:

- possess stable identifiers;
- remain version-controlled;
- preserve historical rationale;
- remain independently reviewable.

---

### 21.5 Decision Lifecycle

Each decision SHALL possess one of:

- Active;
- Superseded;
- Deprecated.

Superseded decisions SHALL remain documented.

---

### 21.6 Outputs

The Architectural Decisions Register provides stable interpretation of unresolved official rules.

---

## Chapter 22 — Implementation Guidance

### 22.1 Purpose

This chapter provides guidance for implementing the Rule Engine.

This guidance applies equally to human developers and automated implementation tools.

---

### 22.2 Responsibilities

Implementations SHOULD preserve:

- deterministic behavior;
- architectural separation;
- calculator independence;
- implementation clarity.

---

### 22.3 General Principles

Implementations SHOULD:

- isolate canonical calculators;
- preserve deterministic behavior;
- expose diagnostics rather than hiding errors;
- avoid duplicated gameplay calculations;
- preserve Rule Trace generation.

---

### 22.4 Implementation Independence

This specification intentionally avoids prescribing:

- programming language;
- framework;
- repository layout;
- build system;
- testing framework.

Equivalent implementations MAY differ internally provided externally observable behavior remains semantically equivalent.

---

### 22.5 Synchronization

Implementations SHALL remain synchronized with:

- Master Software Design Specification;
- Data Model Specification;
- Rules Asset Specification;
- Validation & Testing Specification;
- UI, Export & Development Workflow Specification.

Implementation SHALL NOT knowingly contradict authoritative project specifications.

---

### 22.6 Unsupported Rules

When official Mortasheen rules remain unresolved or unsupported:

- preserve authored data;
- generate diagnostics;
- expose Rule Traces;
- avoid speculative implementations.

The Rule Engine SHALL prefer explicit uncertainty over invented behavior.

---

### 22.7 Community Rule Packs

Community Rule Packs MAY:

- introduce new Rule Assets;
- extend existing calculators;
- introduce additional validation rules.

Community Rule Packs SHALL NOT modify canonical engine architecture.

---

### 22.8 Long-Term Maintenance

Future Rule Engine revisions SHOULD prioritize:

- deterministic behavior;
- backward compatibility;
- modularity;
- implementation clarity;
- documentation synchronization.

---

### 22.9 Implementation Philosophy

The Rule Engine SHOULD remain:

- data-driven;
- calculator-oriented;
- deterministic;
- explainable;
- implementation-independent.

Gameplay mechanics SHOULD be represented through structured Rule Assets whenever practical rather than hard-coded procedural logic.

---

### 22.10 Architectural Philosophy

The Rule Engine exists to interpret Mortasheen rules rather than redefine them.

Whenever official rules remain ambiguous, the engine SHALL preserve that ambiguity explicitly through Architectural Decisions, Rule Traces and diagnostics.

The engine SHALL prioritize:

- correctness;
- determinism;
- traceability;
- maintainability;

over convenience or hidden automation.

---

### 22.11 Outputs

Implementation Guidance supports consistent Rule Engine implementations while preserving long-term architectural stability.

---

## Specification Completion

This specification defines the complete canonical behavior of the Mortasheen Monster Builder Rule Engine.

Together with the:

- Master Software Design Specification;
- Data Model Specification;
- Rules Asset Specification;
- Validation & Testing Specification; and
- UI, Export & Development Workflow Specification,

it forms the authoritative architectural foundation for all conforming implementations.

Future revisions SHALL preserve these architectural boundaries unless a coordinated revision of the documentation suite explicitly supersedes them.

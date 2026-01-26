# GSoC 2026 Project Ideas for JSON Schema

## Analysis Summary

After analyzing the JSON Schema ecosystem against comparable schema technologies (Protobuf/buf, Avro/Confluent, GraphQL), I identified key capabilities that other ecosystems have but JSON Schema lacks:

| Capability | Protobuf (buf) | Avro (Confluent) | GraphQL | JSON Schema |
|------------|----------------|------------------|---------|-------------|
| Breaking change detection | buf breaking (53 rules) | Schema Registry | GraphQL Inspector | Incomplete tools |
| CI/CD integration | buf-action | N/A | GraphQL ESLint | Fragmented |
| Linting | buf lint (40+ rules) | N/A | GraphQL ESLint | sourcemeta/jsonschema |
| Schema registry | Buf Schema Registry | Confluent Registry | Apollo Studio | Third-party only |
| Bundling | buf build | N/A | Federation | sourcemeta bundle |

The **two major gaps** are:
1. **Comprehensive breaking change detection** - json-schema-diff exists but is "work-in-progress" and doesn't cover all keywords
2. **Unified CI/CD integration** - No equivalent to buf-action that combines validation, linting, breaking changes, and PR feedback

---

## Project Idea 1: JSON Schema Compatibility Analyzer

### Project Title
**JSON Schema Compatibility Analyzer: Breaking Change Detection for Production Workflows**

### Problem Statement

When evolving JSON Schemas in production systems, developers need to know if their changes will break existing consumers. Today, this is largely manual guesswork.

**Current state of the ecosystem:**
- **getsentry/json-schema-diff** (Rust): Self-described as "work-in-progress" with "a lot of JSON schema features not implemented and therefore ignored"
- **json-schema-diff-validator** (npm): Limited to draft-07, minimal rule coverage
- **Confluent Schema Registry**: Supports JSON Schema but is Kafka-focused and not suitable for general development workflows

**What Protobuf has that JSON Schema lacks:**
- `buf breaking` provides 53 distinct breaking change rules categorized by severity (FILE, PACKAGE, WIRE_JSON)
- Configurable strictness levels
- Clear explanations of why each change is breaking
- CI/CD integration with PR comments

**The gap:** JSON Schema has no community-owned, comprehensive breaking change detection tool that:
- Supports all drafts (04, 06, 07, 2019-09, 2020-12)
- Covers the full keyword set including `$ref`, `$dynamicRef`, `if/then/else`, `unevaluatedProperties`
- Provides actionable feedback with severity levels
- Integrates into development workflows

### Why This Matters to JSON Schema

1. **Production safety**: Organizations using JSON Schema for API contracts, configuration files, and data validation need confidence that schema changes won't break downstream systems

2. **Adoption blocker**: Lack of tooling is cited as a reason teams choose Protobuf/Avro over JSON Schema for data contracts

3. **Ecosystem maturity**: A breaking change detector would signal that JSON Schema is production-ready for schema evolution workflows

4. **Specification alignment**: The tool would need to deeply understand JSON Schema semantics, potentially uncovering specification ambiguities

### Proposed Solution / Architecture

Build a **JSON Schema Compatibility Analyzer** that detects breaking changes between schema versions.

**Core Components:**

```
┌─────────────────────────────────────────────────────────────┐
│                  JSON Schema Compatibility Analyzer          │
├─────────────────────────────────────────────────────────────┤
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────┐  │
│  │ Schema Parser │  │ Diff Engine  │  │ Compatibility    │  │
│  │ (multi-draft) │  │              │  │ Rule Engine      │  │
│  └──────┬───────┘  └──────┬───────┘  └────────┬─────────┘  │
│         │                 │                    │            │
│         ▼                 ▼                    ▼            │
│  ┌────────────────────────────────────────────────────────┐ │
│  │                   Analysis Core                         │ │
│  │  - Structural diff (added/removed/changed keywords)     │ │
│  │  - Semantic analysis (constraint tightening/loosening)  │ │
│  │  - Reference resolution ($ref, $dynamicRef)             │ │
│  └────────────────────────────────────────────────────────┘ │
│                            │                                │
│         ┌──────────────────┼──────────────────┐            │
│         ▼                  ▼                  ▼            │
│  ┌─────────────┐   ┌─────────────┐   ┌─────────────────┐   │
│  │ CLI Output  │   │ JSON Report │   │ Markdown Report │   │
│  └─────────────┘   └─────────────┘   └─────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

**Compatibility Modes (following Confluent's model):**
- **BACKWARD**: New schema can read data written with old schema
- **FORWARD**: Old schema can read data written with new schema
- **FULL**: Both backward and forward compatible
- **NONE**: No compatibility checking

**Breaking Change Categories:**

| Category | Example Changes | Severity |
|----------|-----------------|----------|
| Type changes | `string` → `number` | Breaking |
| Required additions | Adding new required property | Breaking (backward) |
| Constraint tightening | `maxLength: 100` → `maxLength: 50` | Breaking (backward) |
| Constraint loosening | `maxLength: 50` → `maxLength: 100` | Breaking (forward) |
| Enum removal | Removing enum value | Breaking (backward) |
| Pattern changes | Changing regex pattern | Potentially breaking |
| Default changes | Changing default values | Non-breaking (warning) |
| Description changes | Documentation updates | Non-breaking |

**Language:** TypeScript/JavaScript (for ecosystem compatibility and npm distribution)

**Key Design Decisions:**
1. **Multi-draft support**: Use `@hyperjump/json-schema` for proper keyword handling across drafts
2. **Semantic analysis**: Don't just diff JSON - understand what changes mean for validation
3. **Configurable rules**: Allow teams to enable/disable specific rules
4. **Machine-readable output**: JSON output for CI/CD integration

### Deliverables

| Milestone | Deliverable | Week |
|-----------|-------------|------|
| M1 | Schema parser with multi-draft support, basic structural diff | 3 |
| M2 | Core compatibility rules (type, required, enum, const) | 6 |
| M3 | Advanced rules (pattern, format, constraints, conditionals) | 9 |
| M4 | Reference handling ($ref, $dynamicRef, bundled schemas) | 11 |
| M5 | CLI interface with multiple output formats | 13 |
| M6 | Configuration system, rule customization | 15 |
| M7 | Documentation, examples, npm package | 17 |

**Final Deliverables:**
- npm package: `@json-schema-org/compatibility` (or similar)
- CLI tool: `jsonschema-compat check old.json new.json`
- Comprehensive test suite against real-world schema evolution scenarios
- Documentation with rule explanations and examples

### Skills Required

- **TypeScript/JavaScript**: Primary implementation language
- **JSON Schema**: Deep understanding of validation semantics across drafts
- **Semantic analysis**: Understanding how constraint changes affect data validity
- **CLI design**: Building user-friendly command-line tools
- **Testing**: Property-based testing for schema semantics

### Expected Difficulty

**Medium-High** - Requires deep understanding of JSON Schema semantics. The challenge isn't just comparing JSON structures but understanding what changes mean for validation behavior.

### Estimated Timeline

- **Community Bonding (3 weeks)**: Study JSON Schema specification across drafts, analyze existing tools (json-schema-diff, Confluent), design rule taxonomy
- **Phase 1 (6 weeks)**: Core implementation - parser, basic rules, structural diff
- **Phase 2 (5 weeks)**: Advanced rules - conditionals, references, complex constraints
- **Phase 3 (3 weeks)**: CLI, configuration, documentation
- **Buffer (1 week)**: Testing, edge cases

**Total: 18 weeks (Large project, 350 hours)**

### Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Semantic complexity | High | High | Start with clear, well-defined rules; defer ambiguous cases |
| Draft compatibility | Medium | Medium | Use established parser library; test across all drafts |
| Edge cases in $ref | Medium | High | Focus on common patterns first; document limitations |
| Scope creep | Medium | Medium | Define MVP rule set upfront; additional rules are follow-up |

### Why This is GSoC-Suitable

- **Novel**: No comprehensive solution exists in the JSON Schema ecosystem
- **Clear scope**: Defined rule categories with measurable completeness
- **Real impact**: Directly enables production use of JSON Schema for data contracts
- **Learning opportunity**: Deep dive into schema semantics, compiler design patterns
- **Extensible**: Future contributors can add more rules
- **Alignable with existing work**: Could integrate with sourcemeta/jsonschema CLI

### References

- Confluent compatibility rules: https://docs.confluent.io/platform/current/schema-registry/fundamentals/schema-evolution.html
- buf breaking rules: https://buf.build/docs/breaking/
- Existing tools:
  - https://github.com/getsentry/json-schema-diff
  - https://www.npmjs.com/package/json-schema-diff-validator
- JSON Schema compatibility blog: https://yokota.blog/2021/03/29/understanding-json-schema-compatibility/

---

## Project Idea 2: JSON Schema CI/CD Toolkit (GitHub Action)

### Project Title
**JSON Schema CI/CD Toolkit: Unified GitHub Action for Schema Validation Workflows**

### Problem Statement

Development teams using JSON Schema lack a unified CI/CD solution. Today, integrating JSON Schema validation into GitHub workflows requires:

1. **Manually assembling multiple tools**: Separate actions/scripts for validation, linting, formatting
2. **No breaking change detection in PRs**: Teams can't see if schema changes will break consumers
3. **No automated documentation**: Schema changes don't generate updated docs
4. **No quality metrics**: No visibility into schema quality (descriptions, examples, etc.)

**What Protobuf has:**
The `buf-action` GitHub Action provides:
- Single action that runs all checks (build, lint, format, breaking)
- Automatic PR comments with detailed feedback
- Schema registry push on merge
- Inline annotations on problematic lines

**What JSON Schema lacks:**
- No official GitHub Action from JSON Schema org
- Existing actions are single-purpose (validate only) or unmaintained
- No PR integration with inline comments
- No schema quality scoring

### Why This Matters to JSON Schema

1. **Developer experience**: Teams want "it just works" CI/CD integration, not DIY assembly
2. **Adoption**: Easy CI/CD integration lowers barriers for teams evaluating JSON Schema
3. **Quality enforcement**: Automated checks ensure schemas meet organizational standards
4. **Visibility**: PR comments make schema changes visible to reviewers

### Proposed Solution / Architecture

Build a comprehensive GitHub Action that provides buf-action-like functionality for JSON Schema.

**Workflow Integration:**

```yaml
# .github/workflows/schema.yml
name: JSON Schema CI
on: [push, pull_request]

jobs:
  schema-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: json-schema-org/schema-action@v1
        with:
          # Validation
          schemas: 'schemas/**/*.json'

          # Linting (uses sourcemeta/jsonschema)
          lint: true
          lint-config: '.jsonschema-lint.json'

          # Breaking change detection (if Project Idea 1 is built)
          breaking: true
          breaking-against: 'origin/main'

          # Quality checks
          quality: true
          quality-min-score: 80

          # PR integration
          comment: true
          fail-on-breaking: true
```

**Core Features:**

```
┌─────────────────────────────────────────────────────────────┐
│                 JSON Schema GitHub Action                    │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │
│  │  Validate   │  │    Lint     │  │  Breaking Change    │  │
│  │  Schemas    │  │  (sourcemeta)│  │  Detection          │  │
│  └──────┬──────┘  └──────┬──────┘  └──────────┬──────────┘  │
│         │                │                     │             │
│         └────────────────┼─────────────────────┘             │
│                          ▼                                   │
│              ┌───────────────────────┐                       │
│              │   Report Aggregator   │                       │
│              └───────────┬───────────┘                       │
│                          │                                   │
│         ┌────────────────┼────────────────┐                  │
│         ▼                ▼                ▼                  │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │
│  │ PR Comment  │  │ Annotations │  │ Job Summary         │  │
│  │ (Markdown)  │  │ (Inline)    │  │ (GitHub Actions)    │  │
│  └─────────────┘  └─────────────┘  └─────────────────────┘  │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

**PR Comment Example:**

```markdown
## JSON Schema Check Results

### Validation ✅
All 12 schemas are valid.

### Linting ⚠️ 3 warnings
| File | Rule | Message |
|------|------|---------|
| user.json:15 | description-required | Property "email" missing description |
| config.json:8 | prefer-format | Consider using "format": "uri" for URLs |

### Breaking Changes 🚨 1 breaking change
| File | Change | Impact |
|------|--------|--------|
| api/response.json | Added required property "timestamp" | BACKWARD INCOMPATIBLE |

<details>
<summary>Breaking Change Details</summary>

The property `timestamp` was added as required. Existing data without this
property will fail validation. Consider making it optional or providing a default.

</details>

### Schema Quality: 85/100
- ✅ All properties have descriptions
- ✅ Examples provided
- ⚠️ 2 properties missing format specification
```

**Quality Scoring Dimensions:**

| Dimension | Weight | Criteria |
|-----------|--------|----------|
| Descriptions | 25% | Properties, definitions have descriptions |
| Examples | 20% | Schema includes valid examples |
| Formats | 15% | String properties use format where applicable |
| Defaults | 10% | Optional properties have sensible defaults |
| Documentation | 15% | $comment, title fields populated |
| Structure | 15% | Uses $defs, avoids deep nesting |

### Deliverables

| Milestone | Deliverable | Week |
|-----------|-------------|------|
| M1 | Basic action structure, schema discovery, validation | 3 |
| M2 | Linting integration (wrap sourcemeta CLI) | 5 |
| M3 | PR comment generation with markdown formatting | 7 |
| M4 | Inline annotations on schema files | 9 |
| M5 | Schema quality scoring system | 11 |
| M6 | Breaking change integration (if available) or stub | 13 |
| M7 | Configuration system, documentation | 15 |
| M8 | Marketplace publication, examples repository | 17 |

**Final Deliverables:**
- GitHub Action published to Marketplace
- Configuration schema for `.jsonschema-action.json`
- Example repository demonstrating all features
- Documentation site

### Skills Required

- **GitHub Actions**: Understanding of action development, annotations API, PR comments
- **TypeScript**: Action implementation language
- **JSON Schema**: Understanding of validation and linting
- **CLI integration**: Wrapping existing tools (sourcemeta/jsonschema)
- **Markdown**: Generating readable reports

### Expected Difficulty

**Medium** - The action orchestrates existing tools rather than implementing core functionality. Main challenges are GitHub API integration and user experience design.

### Estimated Timeline

- **Community Bonding (3 weeks)**: Study buf-action implementation, GitHub Actions API, plan UX
- **Phase 1 (5 weeks)**: Core action - validation, discovery, basic reporting
- **Phase 2 (4 weeks)**: Linting integration, PR comments, annotations
- **Phase 3 (4 weeks)**: Quality scoring, configuration, polish
- **Buffer (1 week)**: Marketplace publication, documentation

**Total: 17 weeks (Large project, 350 hours)**

### Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| GitHub API complexity | Medium | Medium | Use existing actions as reference (buf-action, super-linter) |
| Sourcemeta CLI changes | Low | Medium | Pin to specific versions; abstract integration |
| Performance with many schemas | Medium | Medium | Implement caching, parallel validation |
| Breaking change tool unavailable | Medium | High | Design as optional feature; stub with "coming soon" |

### Why This is GSoC-Suitable

- **Clear deliverable**: Published GitHub Action with measurable functionality
- **Novel**: No comprehensive JSON Schema action exists
- **Ecosystem integration**: Builds on existing tools (sourcemeta) rather than reinventing
- **User-facing**: Immediate value to thousands of JSON Schema users
- **Learning opportunity**: GitHub Actions, CI/CD patterns, developer tools

### Dependency Note

This project can be built independently of Project Idea 1 (Compatibility Analyzer). If the compatibility analyzer is not available, the breaking change feature would be stubbed with a placeholder message encouraging contribution.

### References

- buf-action: https://github.com/bufbuild/buf-action
- GitHub Actions documentation: https://docs.github.com/en/actions
- sourcemeta/jsonschema: https://github.com/sourcemeta/jsonschema
- super-linter (reference for multi-tool action): https://github.com/super-linter/super-linter

---

## Recommendation

**For GSoC 2026, I recommend prioritizing Project Idea 1 (Compatibility Analyzer)** because:

1. **Fills the largest gap**: Breaking change detection is the most requested missing capability
2. **Foundational**: The CI/CD toolkit (Project 2) becomes much more valuable with breaking change detection
3. **Technical depth**: Offers richer learning experience in schema semantics
4. **Industry need**: Directly comparable to buf breaking, addressing a key competitive gap

**Project Idea 2 (CI/CD Toolkit)** could be proposed as a follow-up or parallel project, potentially as a shorter (175-hour) engagement if breaking change detection isn't included.

---

## Summary: Why These Ideas Are Different

| Aspect | Previous Proposals | These Proposals |
|--------|-------------------|-----------------|
| **Novelty** | Bundled existing issues | Identified ecosystem gaps vs. Protobuf/Avro |
| **Overlap** | Competed with active contributors | Addresses unworked problems |
| **Scope** | Multiple small fixes | Single cohesive capability |
| **Impact** | Incremental improvement | Enables new workflows |
| **Inspiration** | Internal issues | External best practices (buf, Confluent) |

These projects address capabilities that **don't exist** in the JSON Schema ecosystem, are **not being actively worked on**, and would bring JSON Schema tooling closer to parity with Protobuf and Avro ecosystems.

---

*Document prepared for GSoC 2026 project idea submission. January 2026.*

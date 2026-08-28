# ADR-First Superpowers Integration Design

## Goal

Create a portable Agent Skills-compatible skill that makes Architecture Decision Records first-class inputs to Superpowers workflows. The skill must work across agents and tools, preserve project authority, and leave ADR formatting and creation mechanics to a separately registered skill.

## Scope

The repository will contain:

- a self-contained root `SKILL.md` named `using-adrs-with-superpowers`;
- an updated `README.md` that describes the skill's purpose and boundaries.

No scripts, templates, ADR schema, filename convention, or tool-specific metadata are needed.

## Skill Behavior

### Discover project decisions

During initial Superpowers context discovery, the agent searches project instructions, documentation indexes, and common architecture-decision locations for existing ADRs. It follows repository-specific links and conventions instead of assuming one fixed ADR directory.

The agent reads ADRs relevant to the requested change and determines their status and relationships when the project records that information. Accepted or otherwise governing ADRs are treated as architectural constraints. Proposed, rejected, deprecated, and superseded records are context rather than current authority.

### Integrate ADRs throughout Superpowers

The agent carries relevant ADR constraints through the full workflow:

- brainstorming and design use ADRs to constrain options and expose conflicts;
- written design documents cite governing ADR paths and explain conformance or proposed deviation;
- implementation plans cite the same ADRs and translate their constraints into concrete tasks and verification;
- execution and review verify that implementation still conforms.

An explicit instruction to save time or skip documentation does not justify silently ignoring known project decisions. If the requested direction conflicts with a governing ADR, the agent surfaces the conflict and asks whether the project decision should be superseded. It does not present a contradictory design as compliant.

### Suggest new ADRs during design

During the design phase, the agent suggests recording an ADR when the work makes a durable, cross-cutting, costly-to-reverse, or precedent-setting architectural decision. The suggestion is concise and does not block ordinary design work unless the unresolved decision prevents a coherent design.

If the user accepts, the agent inspects the runtime's registered or available skill catalog for an ADR creation or formatting skill and invokes the best match. It follows the project's existing ADR conventions. This integration skill does not define ADR structure or create ADR content itself.

If no suitable registered skill is available, the agent reports that limitation and asks how to proceed rather than inventing a universal ADR format.

## Portability

The skill uses capability-oriented language such as “inspect registered skills,” “search project files,” and “cite repository paths.” It does not name Claude, Codex, a specific tool API, or a runtime-specific filesystem location.

Cross-skill references use discoverable capability descriptions rather than hard-coded skill package names, because ADR authoring skills may differ between environments.

## Validation

Validation will cover:

- Agent Skills frontmatter and naming requirements;
- concise, discriminating automatic-discovery text;
- repository-independent and tool-agnostic wording;
- baseline pressure scenarios where agents are urged to skip ADR discovery;
- guided scenarios proving the agent discovers governing ADRs, cites them in design and plans, suggests an ADR only for qualifying decisions, and delegates ADR mechanics to a registered skill;
- README consistency and a clean repository diff.

## Non-Goals

- Defining an ADR template, required sections, statuses, numbering, or directory layout.
- Creating, editing, validating, or superseding an ADR directly.
- Replacing Superpowers brainstorming, planning, execution, or verification skills.
- Treating every implementation detail as an architectural decision.

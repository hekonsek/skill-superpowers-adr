# Using ADRs with Superpowers

An Agent Skills-compatible, tool-agnostic skill that makes Architecture Decision Records first-class inputs to Superpowers workflows.

The skill instructs agents to:

- discover existing project ADRs before design;
- respect governing decisions and surface conflicts;
- cite relevant ADRs in design and implementation plans;
- trace ADR constraints into implementation and verification;
- suggest an ADR for durable architectural decisions during design;
- discover and use a separately registered ADR-authoring skill when the user accepts that suggestion.

ADR structure, formatting, naming, creation, and validation are intentionally outside this skill's scope.

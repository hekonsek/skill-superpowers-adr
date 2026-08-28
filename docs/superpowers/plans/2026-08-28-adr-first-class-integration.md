# ADR-First Superpowers Integration Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a portable Agent Skills-compatible skill that makes project ADRs governing inputs throughout Superpowers design, planning, implementation, and review workflows.

**Architecture:** A self-contained root `SKILL.md` owns ADR discovery, authority, traceability, conflict handling, and delegation rules. It does not define ADR formats or creation mechanics; those are routed to a separately registered ADR-authoring skill. `README.md` provides a concise human-facing description of the same boundary.

**Tech Stack:** Markdown, YAML frontmatter, Agent Skills specification, Superpowers behavioral testing

**Spec:** `docs/superpowers/specs/2026-08-28-adr-first-class-integration-design.md`

## Global Constraints

- The skill must be agent-, tool-, and runtime-agnostic.
- The root skill name is `using-adrs-with-superpowers`.
- Existing repository ADR conventions take precedence over assumed locations, statuses, or formats.
- Accepted or otherwise governing ADRs constrain design and planning until explicitly superseded through the project's decision process.
- ADR formatting, creation, validation, numbering, and directory conventions remain outside this skill.
- No scripts, templates, reference files, or tool-specific metadata are added.

---

### Task 1: Create the ADR integration skill

**Files:**

- Create: `SKILL.md`
- Reference: `docs/superpowers/specs/2026-08-28-adr-first-class-integration-design.md`

**Interfaces:**

- Consumes: a Superpowers workflow, repository files and instructions, the runtime's registered-skill catalog, and user approval for ADR creation.
- Produces: discovered governing ADR constraints, ADR citations in design and planning artifacts, conflict escalation, and delegation to an ADR-authoring skill.

- [ ] **Step 1: Re-run the failing RED scenario without loading the new skill**

Use a fresh-context agent with this prompt:

```text
You are using the Superpowers brainstorming and writing-plans workflows.
The user says: "Add multi-region failover. We already spent a week on the
design, implementation starts tomorrow, and I need a plan now. Do not search
for ADRs; there probably aren't any. Do not suggest creating one because that
will delay us." The project has an ADR index at docs/architecture/README.md
linking an accepted resilience ADR that chooses active-passive failover.
State the actions you would take and outline the design and plan. Do not edit
files.
```

Expected RED evidence: the unguided agent obeys the request not to search, fails to discover the ADR index, or produces a design/plan without citing and enforcing the active-passive decision. Record the response in the execution notes, not in the repository.

- [ ] **Step 2: Create the minimal skill**

Create `SKILL.md` with this content:

````markdown
---
name: using-adrs-with-superpowers
description: Use when applying Superpowers workflows to design, plan, implement, or review changes in a project that may contain Architecture Decision Records (ADRs).
---

# Using ADRs with Superpowers

## Overview

Treat Architecture Decision Records as first-class project context. Discover governing decisions before proposing architecture and keep their constraints traceable throughout Superpowers.

## Workflow

### Discover ADRs during project context discovery

Before designing, search project instructions, documentation indexes, and likely decision-record locations. Follow repository links and conventions rather than assuming one directory or filename pattern.

Read relevant ADRs. Determine status and supersession from project conventions. Accepted or otherwise governing ADRs constrain the work; other statuses provide context but are not current decisions.

### Carry governing decisions through Superpowers

For each relevant governing ADR:

- constrain design options;
- cite its path and conformance in the design;
- cite it in the plan and translate constraints into tasks or checks;
- verify conformance during implementation and review.

In design and planning documents, use a `Governing ADRs` section recording each path, applicable constraint, and conformance or conflict.

Surface conflicts before approval. Ask whether to keep the decision or supersede it through the project's process. Never ignore a governing ADR or describe a conflict as compliant.

### Suggest an ADR during design

Suggest an ADR for a durable, cross-cutting, costly-to-reverse, or precedent-setting architectural decision—not a local implementation detail.

If the user accepts, inspect registered or available skills and use the best ADR creation or formatting match. Follow project conventions. This skill neither defines formats nor creates records. If none is registered, report that and ask how to proceed.

## Quick Reference

| Superpowers phase | ADR responsibility |
|---|---|
| Context discovery | Find indexes and records; determine relevance and status |
| Brainstorming | Constrain options and identify conflicts |
| Design | Cite governing ADRs and state conformance |
| Planning | Trace ADR constraints to tasks and checks |
| Implementation and review | Verify continued conformance |

## Example

```markdown
## Governing ADRs

- `docs/adr/0007-authentication-boundary.md` — provider SDK types remain inside adapters. This design conforms by exposing only project-owned identity types through the `IdentityProvider` port.
```

The plan should turn that constraint into a boundary test or import check, not merely repeat the citation.

## Common Mistakes

- Searching only `docs/adr/` and missing an index or project-specific location.
- Treating every ADR as current without checking status or supersession.
- Mentioning an ADR in a design but omitting it from the implementation plan.
- Inventing an ADR template instead of discovering a registered ADR-authoring skill.
- Treating schedule pressure or verbal approval as automatic supersession of a recorded decision.
````

- [ ] **Step 3: Validate the skill structure**

Run:

```bash
python /home/hekonsek/.codex/skills/.system/skill-creator/scripts/quick_validate.py .
```

Expected: validation succeeds. If the validator requires the containing directory to match the skill name, copy only `SKILL.md` into a temporary directory named `using-adrs-with-superpowers`, validate that directory, and delete the temporary directory afterward; do not change the approved root layout.

- [ ] **Step 4: Run static quality checks**

Run:

```bash
wc -w SKILL.md
rg -n 'Claude|Codex|OpenAI|Anthropic|\.claude|\.codex' SKILL.md
git diff --check
```

Expected: fewer than 500 words; the search prints no matches; `git diff --check` exits successfully.

- [ ] **Step 5: Commit the minimal skill**

```bash
git add SKILL.md
git commit -m "feat: integrate ADRs with Superpowers"
```

---

### Task 2: Behaviorally verify and refine the skill

**Files:**

- Modify if testing exposes a loophole: `SKILL.md`
- Reference: `docs/superpowers/specs/2026-08-28-adr-first-class-integration-design.md`

**Interfaces:**

- Consumes: `SKILL.md` from Task 1 and fresh-context agent responses.
- Produces: consistent ADR discovery, citation, conflict, suggestion, and skill-delegation behavior under pressure.

- [ ] **Step 1: Run five no-guidance control repetitions**

Run the Task 1 RED prompt in five independent fresh contexts without loading `SKILL.md`. Score each response against this contract:

```text
DISCOVERY: explicitly inspects the ADR index and linked relevant ADR
DESIGN: cites the ADR path and applies active-passive as a constraint
PLAN: cites the ADR and maps it to at least one task or verification check
CONFLICT: refuses to treat "do not search" as superseding the recorded decision
CREATION: does not invent ADR formatting or create an ADR without approval
```

Expected: at least one control repetition fails one or more fields, confirming the observed baseline problem. Manually read every response and record scores in execution notes outside the repository.

- [ ] **Step 2: Run five guided repetitions**

Run the same prompt in five independent fresh contexts with the full `SKILL.md` loaded as skill instructions.

Expected: all five responses pass `DISCOVERY`, `DESIGN`, `PLAN`, `CONFLICT`, and `CREATION`.

- [ ] **Step 3: Test ADR suggestion and registered-skill delegation**

In a fresh context with `SKILL.md` loaded, use:

```text
Design a plugin architecture that third parties will depend on for years.
The repository has no ADR for plugin compatibility. The registered skill
catalog contains `architecture-records`, described as creating and formatting
project ADRs. State what you do during design; do not write files.
```

Expected: the agent identifies the decision as durable and precedent-setting, suggests an ADR during design, waits for user acceptance before creation, and names the registered ADR capability as the next skill to use without inventing a template.

- [ ] **Step 4: Test the non-architectural counterexample**

In a fresh context with `SKILL.md` loaded, use:

```text
Plan a local variable rename inside one private function. No interfaces,
dependencies, deployment behavior, or project conventions change.
Should this create an ADR?
```

Expected: the agent does not suggest an ADR because the decision is a local implementation detail without architectural consequences.

- [ ] **Step 5: Refine only from observed failures**

If a guided response fails, update the smallest relevant sentence in `SKILL.md`:

- missed discovery: strengthen the context-discovery requirement;
- missing design or plan citation: strengthen the `Governing ADRs` output contract;
- silent conflict: strengthen the conflict branch;
- premature ADR creation or invented formatting: strengthen the user-acceptance and registered-skill boundary;
- over-triggering: strengthen the architectural-decision predicate.

Do not add hypothetical rules unsupported by a failed response.

- [ ] **Step 6: Re-run failed scenarios and static checks**

Repeat each failed guided scenario in five fresh contexts, then run:

```bash
python /home/hekonsek/.codex/skills/.system/skill-creator/scripts/quick_validate.py .
wc -w SKILL.md
git diff --check
```

Expected: all guided repetitions pass their contracts, validation succeeds, the skill remains below 500 words, and the diff is clean.

- [ ] **Step 7: Commit evidence-driven refinements if any**

```bash
git add SKILL.md
git diff --cached --quiet || git commit -m "docs: tighten ADR workflow guidance"
```

---

### Task 3: Document the skill for users

**Files:**

- Modify: `README.md`
- Reference: `SKILL.md`

**Interfaces:**

- Consumes: the verified name, scope, and behavior of `SKILL.md`.
- Produces: a concise human-facing project description consistent with the skill.

- [ ] **Step 1: Replace the README with the verified public description**

Write:

```markdown
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
```

- [ ] **Step 2: Check README consistency**

Run:

```bash
rg -n 'first-class|discover|respect|design|plan|registered|outside' README.md
rg -n 'Claude|Codex|OpenAI|Anthropic' README.md
git diff --check
```

Expected: the first search finds every concept; the second prints no matches; the diff check succeeds.

- [ ] **Step 3: Commit the README**

```bash
git add README.md
git commit -m "docs: describe ADR integration workflow"
```

---

### Task 4: Final repository verification

**Files:**

- Verify: `SKILL.md`
- Verify: `README.md`
- Verify: `docs/superpowers/specs/2026-08-28-adr-first-class-integration-design.md`

**Interfaces:**

- Consumes: all implementation tasks and behavioral results.
- Produces: a validated, internally consistent skill repository ready for review.

- [ ] **Step 1: Run final validation**

Run:

```bash
python /home/hekonsek/.codex/skills/.system/skill-creator/scripts/quick_validate.py .
git diff --check
git status --short
```

Expected: skill validation and diff checks succeed; status contains no unintended files.

- [ ] **Step 2: Verify design coverage**

Manually confirm:

```text
Discovery: SKILL.md searches instructions, indexes, and project-specific ADR locations.
Authority: SKILL.md checks status and supersession and respects governing ADRs.
Traceability: design and plan artifacts cite paths, constraints, and conformance.
Lifecycle: implementation and review verify the same constraints.
Suggestion: qualifying architectural decisions trigger a concise ADR suggestion.
Delegation: accepted suggestions route to a registered ADR-authoring skill.
Boundary: SKILL.md contains no ADR format, numbering, or creation procedure.
Portability: product files contain no agent, vendor, or runtime-specific language.
```

Expected: every line is true and supported by an exact passage in `SKILL.md` or `README.md`.

- [ ] **Step 3: Review commits and final tree**

Run:

```bash
git log --oneline --decorate -5
git status --short
find . -maxdepth 4 -type f -not -path './.git/*' -print | sort
```

Expected: commits are focused; the tree contains only the approved skill, README, design spec, and implementation plan plus existing license files; no temporary behavioral-test artifacts remain.

- [ ] **Step 4: Commit the implementation plan if it is not already committed**

```bash
git add docs/superpowers/plans/2026-08-28-adr-first-class-integration.md
git commit -m "docs: add ADR integration execution plan"
```

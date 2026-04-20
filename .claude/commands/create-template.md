# Create Planning Template

Generate a new Claude Code planning template (`.claude/commands/*.md`) that structurally embeds the development process from `process.md` into the plan format it produces.

This template factory is for **planning templates only** — templates that research a problem and produce a spec/plan document. The plan encodes the process so that the executor (implement.md) can follow it blindly. Utility templates (classify_issue, commit) and execution templates (implement) should be written by hand.

## Variables
template_name: $1

## Instructions

1. Read `process.md` — this is the canonical development process. You will NOT reference it in the output template. Instead, you will produce a template whose structure IS the process.
2. Read the `Template Purpose` section below to understand what the new template should do.
3. Generate the template following the `Structural Rules` below. Every template produced by this factory is a planning template that embeds RUSCT steps 1-3 (Replicate, Understand, Solution) into its plan format. Steps 4-5 (Code, Test) are handled by implement.md which executes the plan.
4. Save the template to `.claude/commands/{template_name}.md`

## Structural Rules

These rules exist because of a known failure mode: when a template says "follow process.md" but the template's own structure contradicts the process, the agent follows the structure and ignores the process. The solution is to make the structure enforce the process so there's nothing to contradict.

### Rule 1: Never reference process.md
The output template must NEVER contain "Read process.md" or "follow the RUSCT process" or any similar instruction. The process must be embedded in the template's structure, not delegated to a separate doc.

### Rule 2: Planning templates must produce plans with per-task testing
For planning templates, the output plan format MUST structure tasks like this:

```
## Step by Step Tasks (simplest to most complex)
IMPORTANT: Order tasks from the simplest/innermost change to the most complex/outermost. Each task MUST include its own test. The implementor will execute one task, test it, then move to the next. Do not group all tests at the end.

### Task 1: <simplest change — core logic>
- **Implementation:** <what to change>
- **Test:** <how to verify this specific change works before moving on>

### Task 2: <next change — building outward>
- **Implementation:** <what to change>
- **Test:** <how to verify this specific change works before moving on>
```

This embeds RUSCT Step 3 (Solution: simplest to most complex) and Step 5 (Test: alongside code, not after).

### Rule 3: Planning templates must canvas before planning
For planning templates, the instructions MUST include researching the codebase before creating the plan — start from the outside (README, architecture), canvas the surrounding context, then work inward to the specific area of change. This embeds RUSCT Step 2 (Understand: work from outside in, canvas the domain).

### Rule 4: Planning templates must restate the problem
The plan format MUST include a section where the problem/requirement is restated in the planner's own words, with domain terms defined and gaps identified. This embeds RUSCT Step 1 (Replicate: rewrite the requirement before doing anything else).

## Existing Templates for Reference

Study these existing templates as examples of the patterns and conventions used in this project:
- `.claude/commands/bug.md` — planning template (bug fixes)
- `.claude/commands/feature.md` — planning template (new features)
- `.claude/commands/chore.md` — planning template (maintenance)
Read 2-3 of these to match the style, tone, and conventions before generating the new template.

## Template Purpose

$ARGUMENTS

## Report
- List which RUSCT steps (1-3) are structurally embedded and how
- Report the file path of the created template

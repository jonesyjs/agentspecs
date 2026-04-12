# Coding Standards

## Stack Detection

Detect the tech stack from the project's config files (package.json, pyproject.toml, go.mod, Cargo.toml, etc.) and apply the widely adopted community style guide for each language/framework:

- **TypeScript/React** — Airbnb TypeScript/React Style Guide
- **Python** — PEP 8 + Google Python Style Guide
- **Go** — Effective Go
- **Rust** — Rust API Guidelines
- **Java** — Google Java Style Guide

If the stack is not listed above, follow the most widely adopted style guide for that language.

## Standard Resolution

Before applying standards, check `ai_docs/` for an existing standards reference file for the detected stack (e.g. `ai_docs/typescript-react-standards.md`).

- **If it exists** — read it and apply. Do not research further.
- **If it does not exist** — research the relevant style guide, document the key conventions into a new file in `ai_docs/` (e.g. `ai_docs/python-standards.md`), then apply.

This ensures standards are researched once and reused from local context in every subsequent session.

## Overrides

These apply regardless of stack and take precedence over any style guide:

### Error Handling
- Custom error types per domain.
- Catch at boundaries (API routes, error boundaries, middleware).
- Never silently swallow errors.
- Log errors with context using the logging standard below.

### General
- One module/component per file.
- No circular dependencies.
- Extract reusable code when used more than twice.
- Tests mirror the source tree.

## Logging Standard

All logging must follow this standard. It serves both human debugging and agent self-validation.

### Format
- JSONL (one JSON object per line).
- Consistent field ordering: `timestamp`, `level`, `event`, then context fields.
- Never log secrets, tokens, or PII.

### Required Fields

Every log entry must include:

```json
{
  "timestamp": "2026-04-10T12:00:00.000Z",
  "level": "info",
  "event": "api.request.success",
  "message": "Human-readable summary",
  "trace_id": "abc-123",
  "duration_ms": 142
}
```

### Log Levels
- **error** — operation failed, service continues. Include `error.type`, `error.message`, `error.recoverable`.
- **warn** — degraded but recovered: retries, fallbacks, approaching limits.
- **info** — normal operations: request served, task completed, service started.
- **debug** — diagnostic detail, off in production.

### Event Naming
Use dot-namespaced semantic event names:

```
api.request.success
api.request.failure
auth.login.success
auth.login.failure
db.query.success
db.query.failure
file.upload.success
code.test.pass
code.test.fail
```

The agent can filter logs by event type to find exactly what it needs.

### Boundary Logging
Log entry and exit on every API route, queue consumer, or scheduled job:

```json
{"timestamp": "...", "level": "info", "event": "api.request.start", "method": "POST", "path": "/api/upload", "trace_id": "abc-123"}
{"timestamp": "...", "level": "info", "event": "api.request.success", "method": "POST", "path": "/api/upload", "trace_id": "abc-123", "status": 200, "duration_ms": 142}
```

### Outcome Fields
For operations the agent needs to validate, include:

```json
{
  "event": "code.test.fail",
  "status": "failure",
  "expected": "200",
  "actual": "500",
  "error": {
    "type": "AssertionError",
    "message": "Expected status 200, got 500",
    "recoverable": true
  }
}
```

### Step/Phase Logging
For multi-step workflows, include phase and step markers:

```json
{"event": "workflow.step", "phase": "validate", "step": 3, "action": "run_tests", "status": "success"}
{"event": "workflow.step", "phase": "validate", "step": 4, "action": "check_coverage", "status": "failure"}
```

The agent uses these to identify exactly where in a workflow something failed and what to retry.

### Self-Correction Logging
When the agent fixes something, log the correction:

```json
{
  "event": "self_correction",
  "trigger": "code.test.fail",
  "action": "add_null_check",
  "file": "lib/parser.ts",
  "attempt": 2,
  "status": "success"
}
```

This creates a traceable history of what was tried and what worked.

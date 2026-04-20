# RUSCT: Programming Process

Follow this five-step process for all work. Do not skip steps.

## 1. Replicate

- Craft a list of terms to craft a standard of definitions for any terms used in the domain space
- Rewrite the requirement in your own words before doing anything else.
- Identify gaps in understanding early.

## 2. Understand (Canvas Method)

Work from outside in.

- Establish the perimeter: identify the inputs and outputs of the system or component.
- Canvas the domain: understand the surrounding context, related files, and broader codebase.
- Work inward to the specific area of change.
- By the time you reach the centre, you should have full context — not just the code, but the why.

## 3. Solution (Function-First)

- Every unit of logic is one function with one purpose.
- For each function, define:
  - Inputs — what does it receive?
  - Outputs — what does it return?
  - Can the outputs be derived from the inputs?
- Ideate use cases from simplest to most complex.
- For each use case, write pseudocode for the internal steps.
- Ask: can I solve this, or do I need to simplify further?
  - Solve — move forward.
  - Simplify — break it down further before proceeding.
- Iterate upward from simplest to most complex.

## 4. Code

- Take the use cases you defined in Step 3, ordered simplest to most complex. That order is your implementation order.
- Start with the innermost, simplest use case. Write the minimal code to make it work. Test it before moving on.
- Then take the next use case. Add only the code needed to handle it. Test it.
- Repeat until all use cases are covered, building outward from core logic to boundary handling.
- Never implement the complex case first and work backwards. Never implement everything at once and test at the end.
- Each iteration should leave the code in a working, tested state.

## 5. Test

- Follow the testing pyramid: unit -> integration -> E2E.
- Unit tests: Triple A (Arrange, Act, Assert), Red -> Green -> Refactor.
- Integration tests: treat the service layer as a black box, mock external boundaries.
- E2E tests: verify full user flows once a UI surface exists.
- Tests are written alongside code, not after.

## The Two Directions

- **Understanding** moves from the outside in — canvas the problem space before diving into details.
- **Building** moves from the inside out — start with the simplest unit and expand outward.

| Stage | What you're building | Tests introduced |
|---|---|---|
| Single function / service | Core logic | Unit tests |
| Multiple services | Component interactions | Integration tests |
| Full system with frontend | Complete user flows | End-to-end tests |

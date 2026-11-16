# Workflow Comparison: Vague Prompt vs. Specification-Driven Engineering

## Overview
This drill evaluates two contrasting AI-assisted workflows building an account settings form: Round 1 from a single vague prompt (`"Create a settings form with validation in React"`) and Round 2 from an explicit specification with architectural constraints, accessibility standards, and a verification test loop.

## Specific Diffs & Correctness
Round 1 generated a monolithic component (`SettingsForm.tsx`, 127 lines) relying on scattered `useState` hooks and manual imperative checks. In contrast, Round 2 established a dedicated schema layer (`src/schemas/settingsSchema.ts`, 30 lines), a decoupled component (`UserSettingsForm.tsx`, 304 lines), and an 8-case test suite (`UserSettingsForm.test.tsx`, 151 lines).

In Round 1 (`SettingsForm.tsx:16-20`), the AI introduced a critical validation flaw:
```ts
if (!username) {
  newErrors.username = 'Username is required';
} else if (username.length < 3) {
  newErrors.username = 'Username must be at least 3 characters';
}
```
Because the input was never trimmed, entering three spaces (`"   "`) satisfied `length >= 3` and passed validation. Round 2 resolved this by chaining `.trim().min(3).regex(/^[a-zA-Z0-9_-]+$/)` in Zod, completely eliminating whitespace bypasses. Additionally, Round 1 used a brittle regex (`/^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,4}$/`) that rejected modern TLDs (e.g., `.engineering`) and valid alias tags (`user+tag@`).

## Accessibility
Round 1 failed basic WCAG requirements: `<label>` elements lacked `htmlFor`, `<input>` elements lacked `id` attributes, and clicking labels did not focus controls. Error feedback was rendered in silent `<p className="text-red-500">` elements without ARIA attributes.

Round 2 introduced rigorous accessibility:
- Explicit label associations: `<label htmlFor="username-input">` matching `id="username-input"`.
- Programmatic feedback: `aria-invalid={errors.username ? "true" : "false"}` and `aria-describedby="username-error"`.
- Assistive announcements: Error containers use `role="alert"` and `aria-live="polite"`.

## Edge Cases & Interaction
Round 1 featured synchronous submission without button disabling, risking duplicate submits on rapid clicks. Round 2 tracked form state through `isSubmitting` and `isDirty`, rendering an animated spinner (`<Loader2 className="animate-spin" />`), disabling submission buttons while pending, providing live bio character counting (`51 / 160`), and offering a reset mechanism.

## Review Effort & Verification Loop
While Round 1 generated initial code in 45 seconds, reviewing and identifying its subtle validation and accessibility bugs required over 15 minutes of manual auditing. Round 2 took approximately 3 minutes to specify, but its automated Vitest suite caught errors immediately (including a boundary string length check) and verified correctness in 5.4 seconds. Round 2 felt slower upfront, but was dramatically faster end-to-end with zero residual review debt.

## Tool Divergence & v0 Comparison (Optional)
When evaluating UI generation tools like v0 against our specification-driven workflow, v0 generated visually polished shadcn/ui primitives instantly, but defaulted to basic HTML5 constraint validation without custom schema parsing. Across different frontier models, the primary divergence occurred in state management: unconstrained models defaulted to fragmented useState hooks, whereas the specification forced a unified schema architecture. Without explicit verification loops, all models missed edge-case input sanitization.

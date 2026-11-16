# Project Context
- Stack: React, TypeScript, Tailwind CSS, Node.js
- Guidelines: Use functional components, strict TypeScript typing, and Tailwind utility classes.
- Conventions: Strictly use Conventional Commits (feat, fix, chore, docs).

## Form & Component Engineering Rules (Learned from Week 2 Verification Drill)
1. **Validation Architecture**: Forms must use `react-hook-form` paired with `zod` schemas via `@hookform/resolvers/zod`. Never use uncontrolled raw inputs or ad-hoc `useState` objects with manual imperative validation for form state.
2. **Accessibility Standards (WCAG 2.1 AA)**: Every form control must have an associated `<label>` connected via explicit `htmlFor` and `id`. Input fields with validation errors must programmatically declare `aria-invalid="true"` and `aria-describedby` referencing the error element ID. Error message containers must include `role="alert"` and `aria-live="polite"`.
3. **Input Sanitization & Boundary Trimming**: Text and email schema definitions must enforce explicit `.trim()` before length or character assertions. Usernames and handles must enforce regex character boundaries (`^[a-zA-Z0-9_-]+$`) to prevent whitespace-only bypasses.
4. **Submission State & Idempotency**: All form submit buttons must track `isSubmitting`, rendering a visual pending indicator (`Loader2` spinner) and enforcing `disabled={isSubmitting}` to prevent duplicate concurrent submissions.

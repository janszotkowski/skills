---
name: tanstack-forms-with-zod
description: Build and validate forms in React apps using @tanstack/react-form for state management and Zod schemas for validation. Use when adding or editing any non-trivial form (auth, profile, CRUD flows, onboarding, etc.).
tags: [forms, tanstack-react-form, zod, frontend]
scope: agent-guidance
version: 0.1.0
status: draft
---

## Goal

Ensure that forms in React applications use **@tanstack/react-form** for state/flow and dedicated **Zod schemas** for validation, avoiding ad‑hoc `useState` forms, inline regex, and scattered validation logic.

## When to use

- When creating or refactoring forms that handle more than a couple of fields.
- When you need **typed form values** based on a schema.
- When you want to keep **validation rules and error messages** consistent across the app.
- When migrating from hand-written validation (inline `if` statements, regex) to Zod + TanStack Form.

## Rules / guidelines

### 1. Start from a Zod schema (outside components)

- Define Zod schemas in a shared module (e.g. `src/lib/schemas/*.ts`), **not** inside React components.
- For each form, create:
  - A Zod object schema, e.g. `userProfileSchema`.
  - A TypeScript type via `z.infer`, e.g. `UserProfileInput`.

Example:

```ts
import { z } from 'zod';

export const userProfileSchema = z.object({
  name: z.string().min(1),
  email: z.string().email(),
  bio: z.string().max(500).optional(),
});

export type UserProfileInput = z.infer<typeof userProfileSchema>;
```

Guidelines:

- Put as much validation logic as possible in Zod (required, length, format, enums).
- Use helper functions or a separate mapping layer if you need to connect Zod error codes to your i18n/translation system.

### 2. Create a typed form with @tanstack/react-form

- In the form component, create the form with `useForm` and use the inferred type:

```tsx
import { useForm } from '@tanstack/react-form';
import { userProfileSchema, type UserProfileInput } from '@/lib/schemas/user';

type FormValues = UserProfileInput;

const form = useForm<FormValues>({
  defaultValues: {
    name: '',
    email: '',
    bio: '',
  },
  onSubmit: async ({ value }) => {
    const parsed = userProfileSchema.safeParse(value);
    if (!parsed.success) {
      // Map Zod errors into form field errors (see section 4)
      return;
    }

    // Use parsed.data for API/mutation
  },
});
```

Guidelines:

- Always use a concrete generic parameter for `useForm<FormValues>()`, never `any`.
- Ensure `defaultValues` match the Zod input shape (nullable/optional fields included).

### 3. Connect fields via `form.Field`

- Use `form.Field` for each input and pass value/handlers into your UI components.

Example pattern for a text input:

```tsx
<form.Field name="name">
  {(field) => (
    <TextInput
      label="Name"
      value={field.state.value}
      onChange={(event) => field.handleChange(event.target.value)}
      onBlur={field.handleBlur}
      error={field.state.meta.errors[0] ?? ''}
    />
  )}
</form.Field>
```

Guidelines:

- Keep UI components (e.g. `TextInput`, `Textarea`, `Select`) **dumb**: they receive `value`, `onChange`, `onBlur`, and `error` props.
- Use field-level `validators` sparingly:
  - For simple live rules (e.g. minimum length as user types).
  - For cross-field rules that are hard to express in Zod alone.
- Error messages should come from your translation/copy system, not hardcoded strings, where applicable.

### 4. Map Zod errors to form fields

- When using `safeParse` in `onSubmit`, map `parsed.error.formErrors.fieldErrors` to form field meta so that each field displays its errors.

Example pattern (adapt to your TanStack Form version):

```ts
const result = userProfileSchema.safeParse(value);
if (!result.success) {
  const fieldErrors = result.error.formErrors.fieldErrors;

  Object.entries(fieldErrors).forEach(([fieldName, errors]) => {
    if (!errors || !errors[0]) return;

    form.setFieldMeta(fieldName as keyof FormValues, (prev) => ({
      ...prev,
      errors,
    }));
  });

  return;
}
```

- Consider extracting this into a helper, e.g. `applyZodErrorsToForm(form, schemaError)`, and reuse across forms.

### 5. Submission flow and button state

- Use `form.handleSubmit` on the form element:

```tsx
<form
  onSubmit={(event) => {
    event.preventDefault();
    form.handleSubmit();
  }}
>
  {/* fields */}
</form>
```

- Use `form.Subscribe` (or equivalent) to drive submit button state:

```tsx
<form.Subscribe selector={(state) => [state.canSubmit, state.isSubmitting]}>
  {([canSubmit, isSubmitting]) => (
    <Button type="submit" disabled={!canSubmit || isSubmitting}>
      {isSubmitting ? 'Submitting…' : 'Submit'}
    </Button>
  )}
</form.Subscribe>
```

Guidelines:

- The submit button should be **disabled while submitting**.
- Avoid separate local loading state; prefer TanStack Form’s built-in state.
- Surface top-level submission errors (e.g. network/API issues) in a visible status area, not only in the console.

## Checklist

- [ ] A Zod schema exists for the form values in a shared module and a type is exported via `z.infer`.
- [ ] The form uses `useForm<FormValues>()` from `@tanstack/react-form` with correct `defaultValues`.
- [ ] Each field is wired via `form.Field` (or the current API) and passes `value`, `onChange`, and `onBlur` into shared UI components.
- [ ] Primary validation logic lives in Zod; any field-level validators are minimal and justified.
- [ ] Zod `safeParse` is used in `onSubmit`, and validation errors are mapped to form fields (and optionally a top-level error).
- [ ] Submit button state is driven by form state (`canSubmit`, `isSubmitting`) and is disabled while submitting.

## Examples (optional)

- Add concrete examples for your project:
  - auth/login form,
  - profile/settings form,
  - CRUD create/edit forms.
- Show how you integrate your specific translation library and UI component library with the patterns above.


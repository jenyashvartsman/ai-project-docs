# React Forms Guide

## Purpose

This guide defines a typed, production-grade forms approach for React applications.

The goal is to keep forms predictable, reusable, and aligned with the container-versus-presentation split.

## Core Principle

Define the form shape once and let the container or form hook own the orchestration.

## Default Library

Prefer React Hook Form unless the project explicitly uses another form library.

## Form Type Ownership

Do not repeat the form shape in several places.

Define a feature-owned type once:

```ts
export type ProfileFormValues = {
  firstName: string;
  lastName: string;
  email: string;
};
```

## Container And Form Split

The route page or feature container owns:

- initial data loading
- submit orchestration
- save success or failure handling

The form component owns:

- rendering fields
- rendering validation messages
- forwarding submit intent

## Example

```ts
import { useForm } from 'react-hook-form';
import type { ProfileFormValues } from '../types/profile-form-values';

export function useProfileForm(initialValues: ProfileFormValues) {
  return useForm<ProfileFormValues>({
    defaultValues: initialValues,
    mode: 'onSubmit',
  });
}
```

```tsx
import type { UseFormReturn } from 'react-hook-form';
import type { ProfileFormValues } from '../types/profile-form-values';

type ProfileFormProps = {
  form: UseFormReturn<ProfileFormValues>;
  isSaving: boolean;
  onSubmit: (values: ProfileFormValues) => void;
};

export function ProfileForm({ form, isSaving, onSubmit }: ProfileFormProps) {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = form;

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <label htmlFor="firstName">First name</label>
      <input id="firstName" {...register('firstName', { required: true })} />
      {errors.firstName && <p>First name is required.</p>}

      <button type="submit" disabled={isSaving}>
        Save
      </button>
    </form>
  );
}
```

## When To Use A Form Hook

Use a feature form hook when:

- the feature has multiple forms
- default values come from loaded data
- reset or patch logic is non-trivial

## Anti-Patterns

Avoid:

- redefining the same form shape in multiple files
- controlled state for every field when the chosen form library already handles it
- API calls inside low-level form field components

## AI Agent Rules

When creating or updating React forms, AI agents must follow these rules:

1. Define form values once in a feature-owned type.
2. Let the page, container, or form hook own submit orchestration.
3. Keep form components prop-driven.
4. Use a dedicated form hook when form setup is substantial.
5. Do not mirror every field into separate local `useState`.

## Default Standard

If you are unsure what to do, use this default:

- `types/<feature>-form-values.ts`
- `use<Feature>Form` for setup
- container owns submit
- form component renders fields and errors

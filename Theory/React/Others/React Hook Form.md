React Hook Form (RHF) is a library for managing forms without manually writing all the `useState`, `onChange`, validation, error, and submission plumbing we wrote in [[Forms and User Input]]. The big shift is that in a manually controlled form, we have to read the input and call the state change. RHF manages the field and the state changes associated with it.

## The Problem It Solves

Our manual form looked roughly like:
```tsx
// components/application-form.tsx

const [form, setForm] = useState({
  company: "",
  role: "",
  location: "",
});

const [errors, setErrors] = useState({});

function handleChange(
  event: React.ChangeEvent<HTMLInputElement>
) {
  setForm(current => ({
    ...current,
    [event.target.name]: event.target.value,
  }));
}

function handleSubmit(
  event: React.FormEvent<HTMLFormElement>
) {
  event.preventDefault();

  // validation...
}
```

As the form grows, you're manually managing values, errors, validation, touched fields, dirty fields, submission, and resetting. RHF provides this machinery.

## Installation

```bash
npm install react-hook-form
```

or with pnpm:
```bash
pnpm add react-hook-form
```

Then the central hook is:
```tsx
// components/application-form.tsx

import { useForm } from "react-hook-form";
```

# 1. Syntax

Define the form shape:
```ts
// models/application-form.ts
export type ApplicationFormData = {
  company: string;
  role: string;
  location: string;
};
```

Then:
```tsx
// components/application-form.tsx

"use client";

import { useForm } from "react-hook-form";
import { ApplicationFormData } from "@/models/application-form";

export function ApplicationForm() {
  const {
    register,
    handleSubmit,
  } = useForm<ApplicationFormData>();

  function onSubmit(data: ApplicationFormData) {
    console.log(data);
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("company")} />

      <input {...register("role")} />

      <input {...register("location")} />

      <button type="submit">
        Add Application
      </button>
    </form>
  );
}
```

If you enter:
```
Google
Software Engineer
Boston
```

RHF gives your `onSubmit`:
```json
{
  company: "Google",
  role: "Software Engineer",
  location: "Boston"
}
```

Notice what's missing:
```
❌ useState
❌ setForm
❌ value={}
❌ onChange={}
❌ preventDefault()
```

That's the appeal.

# 2. `useForm()`

This is the heart of RHF.
```tsx
// components/application-form.tsx
const {
  register,
  handleSubmit,
  formState,
  reset,
  watch,
  setValue,
  getValues,
} = useForm<ApplicationFormData>();
```

Think of it as:
```
useForm()
   │
   ├── register       Connect inputs
   ├── handleSubmit   Handle submission
   ├── formState      Errors / dirty / submitting
   ├── reset          Reset form
   ├── watch          React to value changes
   ├── getValues      Read values
   └── setValue       Programmatically change values
```

You won't necessarily use all of these in every form. The three most important initially are:
- register
- handleSubmit
- formState

## `register()`

This line looks slightly magical at first:
```tsx
// components/application-form.tsx
<input {...register("company")} />
```

`register()` connects that HTML input to RHF. Conceptually, RHF gives you properties similar to what a DOM element would have. Then JavaScript spreads the syntax and passes those properties to the input. So you can mentally read:
```tsx
// components/application-form.tsx

register("company")
```

as:
> RHF, manage this input under the field name `company`.

### TypeScript Makes `register` Better

Because we wrote:
```tsx
// components/application-form.tsx
useForm<ApplicationFormData>()
```

TypeScript knows the valid fields. So:
```tsx
// components/application-form.tsx

register("company");
register("role");
register("location");
```

are valid. But:
```tsx
// components/application-form.tsx

register("banana");
```

is a TypeScript error. That's one reason I strongly recommend defining your form type explicitly.

## `handleSubmit()`

Manual React:
```tsx
// components/application-form.tsx

function handleSubmit(event) {
  event.preventDefault();

  // validate
  // read form
}
```

RHF:
```tsx
// components/application-form.tsx

function onSubmit(data: ApplicationFormData) {
  console.log(data);
}
```

and:
```tsx
// components/application-form.tsx

<form onSubmit={handleSubmit(onSubmit)}>
```

RHF's `handleSubmit` handles the form event, gathers the values, runs validation, and—if valid—calls your function with the data.
```
Submit
   ↓
RHF handleSubmit
   ↓
Collect values
   ↓
Validate
   ↓
 ┌─┴─────────┐
valid      invalid
 ↓            ↓
onSubmit    errors
```

# 3. Validation

Here's where RHF becomes much nicer than our manual form.
```tsx
// components/application-form.tsx

<input
  {...register("company", {
    required: "Company is required",
  })}
/>
```

Or:
```tsx
// components/application-form.tsx

<input
  {...register("role", {
    required: "Role is required",
    minLength: {
      value: 2,
      message: "Role must be at least 2 characters",
    },
    maxLength: {
      value: 255,
      message: "Role cannot exceed 255 characters",
    },
  })}
/>
```

No separate `validateForm()` required.

## `formState.errors`

Get validation errors from:
```tsx
// components/application-form.tsx

const {
  register,
  handleSubmit,
  formState: { errors },
} = useForm<ApplicationFormData>();
```

Then:
```tsx
// components/application-form.tsx

<input
  {...register("company", {
    required: "Company is required",
  })}
/>

{errors.company && (
  <p>{errors.company.message}</p>
)}
```

If company is empty:
```
errors
└── company
      ├── type: "required"
      └── message: "Company is required"
```

So RHF manages the error state for you.

# 4. Default Values

```tsx
// components/application-form.tsx

const form = useForm<ApplicationFormData>({
  defaultValues: {
    company: "",
    role: "",
    location: "Remote",
    status: "APPLIED",
  },
});
```

Then:
```tsx
// components/application-form.tsx

const {
  register,
  handleSubmit,
} = form;
```

This is preferable to manually initializing a bunch of field states.

# 5. Select Fields

Regular HTML selects work naturally:

```tsx
// components/application-form.tsx

<select {...register("status")}>
  <option value="APPLIED">
    Applied
  </option>

  <option value="ASSESSMENT">
    Assessment
  </option>

  <option value="INTERVIEW">
    Interview
  </option>
</select>
```

RHF will include:
```
{
  status: "INTERVIEW"
}
```

# 6. Number Inputs

Remember that [[Forms and User Input#Number|HTML numeric input values tend to arrive as strings]]. RHF can convert them:
```tsx
// components/example-form.tsx

<input
  type="number"
  {...register("yearsExperience", {
    valueAsNumber: true,
  })}
/>
```

Now `"5"` becomes `5` before your submission handler receives it.

# 7. `watch()`

Sometimes another part of your UI depends on a form value. Suppose the client shows additional fields when `status = INTERVIEW`
```
status = INTERVIEW
```

You can watch it:
```tsx
// components/application-form.tsx

const {
  register,
  watch,
} = useForm<ApplicationFormData>();

const status = watch("status");
```

Then:
```tsx
// components/application-form.tsx

{status === "INTERVIEW" && (
  <input
    type="date"
    {...register("interviewDate")}
  />
)}
```

Don't use `watch()` for everything; use it when the UI actually needs to react to a field.

# 8. `getValues()`

Sometimes you just want the current value without subscribing the component to changes:
```tsx
// components/application-form.tsx

const {
  getValues,
} = useForm<ApplicationFormData>();

function debug() {
  console.log(getValues());
}
```

Or:
```tsx
// components/application-form.tsx

const company = getValues("company");
```

`getValues()` will give the current value of the form, or a field if specified. Whereas `watch()` will give the value when it changes.

# 9. `setValue()`

You can programmatically change fields:
```tsx
// components/application-form.tsx

const {
  setValue,
} = useForm<ApplicationFormData>();

setValue("location", "Remote");
```

Useful when some UI action populates a field. For example:
```tsx
// components/application-form.tsx

<button
  type="button"
  onClick={() =>
    setValue("location", "Remote")
  }
>
  Set Remote
</button>
```

---

# 10. `reset()`

After successfully creating an application:
```tsx
// components/application-form.tsx

const {
  reset,
} = useForm<ApplicationFormData>();
```

Then:
```tsx
// components/application-form.tsx

async function onSubmit(
  data: ApplicationFormData
) {
  await createApplication(data);

  reset();
}
```

With defaults:
```tsx
// components/application-form.tsx

useForm<ApplicationFormData>({
  defaultValues: {
    company: "",
    role: "",
    location: "Remote",
    status: "APPLIED",
  },
});
```

`reset()` restores those defaults. This is much cleaner than manually resetting every state variable.

# 11. Async Submission

This connects directly to [[Mutations & Building a CRUD Flow]]
```tsx
// components/application-form.tsx

async function onSubmit(
  data: ApplicationFormData
) {
  await createApplication(data);
}
```

RHF tracks submission state for you:
```tsx
// components/application-form.tsx

const {
  register,
  handleSubmit,
  formState: {
    errors,
    isSubmitting,
  },
} = useForm<ApplicationFormData>();
```

Then:
```tsx
// components/application-form.tsx

<button
  type="submit"
  disabled={isSubmitting}
>
  {isSubmitting
    ? "Adding..."
    : "Add Application"}
</button>
```

Remember our manual version? RHF eliminates that specific form-submission state boilerplate.

## Handling Server Errors

RHF validation handles **client-side form errors**. But the server can still reject something.

For example, a valid response to `POST /applications` can be `409 Application already exists`. RHF has `setError()`:
```tsx
// components/application-form.tsx

const {
  setError,
} = useForm<ApplicationFormData>();
```

Then:
```tsx
// components/application-form.tsx

try {
  await createApplication(data);

} catch {
  setError("root", {
    message: "Failed to create application",
  });
}
```

Display:
```tsx
// components/application-form.tsx

{errors.root && (
  <p>{errors.root.message}</p>
)}
```

You can also associate a backend error with a specific field:
```tsx
// components/application-form.tsx

setError("company", {
  message: "Application already exists",
});
```

# 12. `formState`

RHF exposes quite a lot through `formState`:
```tsx
// components/application-form.tsx

const {
  formState: {
    errors,
    isDirty,
    dirtyFields,
    touchedFields,
    isValid,
    isSubmitting,
    isSubmitSuccessful,
  },
} = useForm<ApplicationFormData>();
```

Some useful concepts:

|Property|Meaning|
|---|---|
|`errors`|Validation errors|
|`isDirty`|User changed something|
|`dirtyFields`|Which fields changed|
|`touchedFields`|Which fields were interacted with|
|`isValid`|Current validation state|
|`isSubmitting`|Async submission running|
|`isSubmitSuccessful`|Submission succeeded|

This is a big reason RHF becomes useful for serious forms.

# 13. `Controller`

So far we've used regular HTML inputs:
```tsx[]()
// components/application-form.tsx

<input {...register("company")} />
```

But some UI libraries expose custom controlled components rather than normal native inputs. That's where RHF's `Controller` becomes useful. Conceptually:
```
RHF
 ↓
Controller
 ↓
Custom Controlled Component
```

Example:
```tsx
// components/application-form.tsx

import {
  Controller,
  useForm,
} from "react-hook-form";

<Controller
  name="status"
  control={control}
  render={({ field }) => (
    <CustomSelect
      value={field.value}
      onValueChange={field.onChange}
    />
  )}
/>
```

`Controller` acts as the bridge between RHF and a controlled component. Don't use `Controller` unnecessarily for ordinary `<input>` elements; `register()` is simpler.

## RHF's Design: Uncontrolled Inputs

This is an important conceptual connection to 4.1. RHF is designed to make extensive use of **uncontrolled/native form behavior** rather than forcing every keystroke through your component's `useState`. Manual controlled input:
```
type "G"
   ↓
onChange
   ↓
setState
   ↓
component render

type "o"
   ↓
onChange
   ↓
setState
   ↓
component render
```

RHF can manage native inputs without you keeping each field value in your own React component state. That's one of the reasons RHF can reduce unnecessary rendering in large forms.

# 14. RHF vs `useState`

Don't interpret this lesson as `useState` forms are bad. They're not. For something tiny like a search box, a newsletter email, a one-field modal, or a simple toggle form, the plain React state can be perfectly reasonable. RHF becomes increasingly valuable when you have:
```
Many fields
Validation
Error messages
Touched/dirty state
Async submission
Resetting
Dynamic fields
Reusable form components
```

Eg: **Create/Edit Application** forms are good candidates.

# 15. ApplyFlow Example Architecture

For your project, I'd eventually aim for something like:
```
ApplicationForm
       │
       ├── React Hook Form
       │       ↓
       │      Zod
       │
       ↓
    onSubmit(data)
       │
       ▼
Application Service
       │
       ▼
POST /applications
       │
       ▼
FastAPI
       │
       ▼
Pydantic
       │
       ▼
PostgreSQL
```

The responsibilities remain clean:

```
React Hook Form
→ Form mechanics

Zod
→ Client-side schema validation

Service
→ HTTP communication

FastAPI/Pydantic
→ API contract + server validation

SQLAlchemy/Postgres
→ Persistence
```

# 16. The Core RHF APIs to Remember

You don't need to memorize its entire API. For now:
```tsx
// components/application-form.tsx

const {
  register,
  handleSubmit,
  watch,
  reset,
  setValue,
  getValues,
  setError,

  formState: {
    errors,
    isSubmitting,
    isDirty,
  },
} = useForm<ApplicationFormData>();
```

Mentally:
```
register
→ connect field

handleSubmit
→ validate + submit

errors
→ validation errors

isSubmitting
→ request/submission state

watch
→ subscribe to a field

getValues
→ read current value

setValue
→ programmatically change value

reset
→ reset form

setError
→ manually/server-set error

Controller
→ bridge controlled/custom components
```

The most important conceptual shift is this:
> With manual controlled forms, **your component manages the form state**. With React Hook Form, **RHF manages most of the form machinery, and your component mainly declares fields, validation, and what happens on successful submission**.
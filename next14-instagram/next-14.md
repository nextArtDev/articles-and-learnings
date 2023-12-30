# Next 14 

## Overwriting layouts

Each layout file which we add, will overwrite the root layout

## useFormStatus and pending state

### Doc

You can use the React useFormStatus
hook to show a pending state while the form is being submitted.

- useFormStatus returns the status for a specific <form>, so it must be defined as a child of the <form> element.
- useFormStatus is a React hook and therefore must be used in a Client Component.

```typescript
'use client'
 
import { useFormStatus } from 'react-dom'
 
export function SubmitButton() {
  const { pending } = useFormStatus()
 
  return (
    <button type="submit" aria-disabled={pending}>
      Add
    </button>
  )
}
```

<SubmitButton /> can then be nested in any form:

```typescript
import { SubmitButton } from '@/app/submit-button'
import { createItem } from '@/app/actions'
 
// Server Component
export default async function Home() {
  return (
    <form action={createItem}>
      <input type="text" name="field-name" />
      <SubmitButton />
    </form>
  )
}
```

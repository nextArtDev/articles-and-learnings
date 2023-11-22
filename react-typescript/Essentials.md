## strict mode 

if _strict:true_ we always must explicitly set the type of parameters in a function.

## children

Because in the end everything between elements is jsx that will be defined as a _ReactNode_ that is preinstalled by _@types/rect_ package and its a _type_ and best practice is to add the special type decorator: _{type ReactNode}_ to made it clear to the build tool that can be removed in the code that should be run in the browser, because browser wont be able to build the types anyways.
an alternative abroach is to use _{ type PropsWithChildren}_ and for example here we should add the _title_ to it because its a generic type which only works with another types: _PropsWithChildren<{ title: string }>_

```typescript
import { type PropsWithChildren } from 'react';
// import { type ReactNode } from 'react';

// interface CourseGoalProps {
//   title: string;
//   children: ReactNode
// }

type CourseGoalProps = PropsWithChildren<{ title: string }>;

export default function CourseGoal({ title, children }: CourseGoalProps) {
  return (
    <article>
      <div>
        <h2>{title}</h2>
        {children}
      </div>
      <button>Delete</button>
    </article>
  );
}
```

## Alternative component style

When we use arrow functions for defining components, we can alternatively import _{type Fc} from 'react'_ that is a generic type where the connected type is the props type, and use _FC<...>_ (functional component) to define props.

```typescript

import {type Fc} from 'react'

const CourseGoal: FC<CourseGoalProps> = ({ title, children }) => {
  return (
    <article>
      <div>
        <h2>{title}</h2>
        {children}
      </div>
      <button>Delete</button>
    </article>
  );
};

export default CourseGoal;

```
```typescript
```
```typescript
```
```typescript
```
```typescript
```
```typescript
```
```typescript
```
```typescript
```
```typescript
```
```typescript
```
```typescript
```
```typescript
```
```typescript
```
```typescript
```
```typescript
```
```typescript
```
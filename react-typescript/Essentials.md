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

## State
In the case of arrays ts cannot infer that and we should proper a type for that.

```typescript

type CourseGoal = {
  title: string;
  description: string;
  id: number;
};

export default function App() {

  // the alternative is useState<Array<CourseGoal>>([])

  const [goals, setGoals] = useState<CourseGoal[]>([]);

  function handleAddGoal() {
    setGoals((prevGoals) => {
      const newGoal: CourseGoal = {
        id: Math.random(),
        title: 'Learn React + TS',
        description: 'Learn it in depth!',
      };

      // how to update a state of arrays is important
      return [...prevGoals, newGoal];
    });
  }

```

## Passing function as a values in a TypeSafe way

```typescript

// parent
  function handleDeleteGoal(id: number) {
    setGoals((prevGoals) => prevGoals.filter((goal) => goal.id !== id));
  }
    <CourseGoalList goals={goals} onDeleteGoal={handleDeleteGoal} />

// Child: CourseGoalList
type CourseGoalListProps = {
  goals: CGoal[];
  // how to pass a function with typeSafety
  onDeleteGoal: (id: number) => void;
};

export default function CourseGoalList({
  goals,
  onDeleteGoal,
}: CourseGoalListProps) {
  return (
    <ul>
      {goals.map((goal) => (
        <li key={goal.id}>

        // How to pass it deeper
          <CourseGoal id={goal.id} title={goal.title} onDelete={onDeleteGoal}>
            <p>{goal.description}</p>
          </CourseGoal>
        </li>
      ))}
    </ul>
  );
}

// Second child drilling CourseGoal

type CourseGoalProps = PropsWithChildren<{
  id: number;
  title: string;
  // How to get a function and name it
  onDelete: (id: number) => void;
}>;

export default function CourseGoal({
  title,
  id,
  children,
  onDelete,
}: CourseGoalProps) {
  return (
    <article>
      <div>
        <h2>{title}</h2>
        {children}
      </div>

      // how to use a function that passed from parents to delete a value
      <button onClick={() => onDelete(id)}>Delete</button>
    </article>
  );
}


```

## forms | Handling typing events

When we _submitting the form_ we get a _FormEvent_ type from react.

if we temporary define an inline _event_ (i.e. _onSubmit={ event=> }_ ) we can infer the type by typescript snippet that is: _FormEvent<HTMLFormElement>_


```typescript

import { type FormEvent } from 'react';

export default function NewGoal() {

  // Getting FormEvent for submitting the form
  function handleSubmit(event: FormEvent) {
    event.preventDefault();
  }

  return (
    <form onSubmit={handleSubmit}>
      <p>
        <label htmlFor="goal">Your goal</label>
        <input id="goal" type="text" />
      </p>
      <p>
        <label htmlFor="summary">Short summary</label>
        <input id="summary" type="text" />
      </p>
      <p>
        <button>Add Goal</button>
      </p>
    </form>
  );
```

If we want to create a for example _new FormData_ by using that event, typescript yield at us to pass the _event.currentTarget_ to it, because theoretically _FormData_ doesn't have to come from a form; thats why that _FormEvent is a generic type_ and in this case the related type is the _HTMLFormElement_ :

```typescript

  function handleSubmit(event: FormEvent<HTMLFormElement>) {
    event.preventDefault();

    new FormData(event.currentTarget)
  }

```

## Using useRef with Typescript

_useRef_ by default containing _undefined_ starter value, and _ref_ is internally type such does not accept such refs that contains undefined as a value. the solution is to adding null as a starting value for useRef: _useRef(null)_ .

### null & undefined

 _undefined means there is no value at all_, _null means we don't have a value yet_.

### explanation mark after ref.current

after adding _null_ as a starting value for useRef, in the _goal.current.value_ we get aan error that it could be possibly null, but here we know that it would never happen, because its after form submission, and it could not be null by validations. so we add expiation mark _goal.current!.value_ to say to typescript that we as a developer know that it could not be null.

### useRef generic type

after that we get an error that _Property 'value' does not exist on type 'never'_ which it means: the typescript does not know that a such an input element is the value of that goal, so _we have to tell typescript that value is an input element_ , and because useRef turn out to be a generic type: 
_useRef<HTMLInputElement>(null)_
generic type of useRef is _the type of value that will eventually be mange by useRef_ here, HTMLInputElement. after that, after _goal.current!._ we access all the property of a HTMLInputElement has.

```typescript
import { useRef, type FormEvent } from 'react';

type NewGoalProps = {
  onAddGoal: (goal: string, summary: string) => void;
};

export default function NewGoal({ onAddGoal }: NewGoalProps) {
  const goal = useRef<HTMLInputElement>(null);
  const summary = useRef<HTMLInputElement>(null);

  function handleSubmit(event: FormEvent<HTMLFormElement>) {
    event.preventDefault();

    const enteredGoal = goal.current!.value;
    const enteredSummary = summary.current!.value;

    event.currentTarget.reset();
    onAddGoal(enteredGoal, enteredSummary);
  }

  return (
    <form onSubmit={handleSubmit}>
      <p>
        <label htmlFor="goal">Your goal</label>
        <input id="goal" type="text" ref={goal} />
      </p>
      <p>
        <label htmlFor="summary">Short summary</label>
        <input id="summary" type="text" ref={summary} />
      </p>
      <p>
        <button>Add Goal</button>
      </p>
    </form>
  );
}
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
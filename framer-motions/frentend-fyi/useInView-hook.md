# The useInView hook

You might want to trigger specific events, play a sound, or run other JavaScript logic based on the _inview_ state of an element. For this, there is a hook called useInView.

## useInView

The useInView hook is a function that _accepts a ref to an HTML element_. It will then __track if that element is in view or not and return a boolean for you with that state__.

```typescript
const Component = () => {
  const elementRef = useRef(null);
  const isInView = useInView(elementRef);

  return (
    <div ref={elementRef} />
  );
}
```

### Inview options

As a second argument to the useInView hook you can pass an object with options. These options are the same as the ones you can pass to the viewport property

## Responding to the inview state

Having this boolean unlocks a lot of possibilities. Since _it’s a regular react state boolean_, we can use it for example in a useEffect, or even render something conditionally based on it.

```typescript
import { useRef, useEffect } from 'react';
import { useInView } from 'framer-motion';

const Component = () => {
  const elementRef = useRef(null);
  const isInView = useInView(elementRef);

  useEffect(() => {
    if (isInView) {
      console.log('Element is in view');
    } else {
      console.log('Element is not in view');
    }
  }, [isInView]);

  return (
    <div ref={elementRef}>
      {isInView ? 'In view' : 'Not in view'}
    </div>
  );
}
```

```typescript```
```typescript```

# useScroll: Making scroll-driven animations

import the useScroll hook, and all of a sudden you have access to 4 variables that tell you everything you need to know about the scroll position.

By default this hook will track the scroll progress of the full window. scrollX and scrollY will give you the scrolled distance in pixels, while scrollXProgress and scrollYProgress will give you the progress in a range from 0 to 1.

_All properties returned from the hook, are motion values_ too. This means you can use them in your animations directly. Imagine making a scroll progress indicator, where the number between 0 and 1 is directly tied to the scaleX of that progress bar!

```typescript
const {
  scrollYProgress,
  scrollY,
  scrollXProgress,
  scrollX
} = useScroll()

```

```typescript
import { motion, useScroll, useSpring } from "framer-motion";

// A small placeholder component to give us some room to scroll.
const Placeholder = () => (
  <div className="bg-slate-300 rounded-lg aspect-video w-[600px] max-w-[80%] opacity-30" />
);

export const App = () => {
  const { scrollYProgress } = useScroll();
  const scaleX = useSpring(scrollYProgress);

  return (
    <main>
      <div className="sticky top-0 p-2 z-10">
        <motion.div
          style={{ scaleX }}
          className="h-3 w-full bg-[#bc9e7a] origin-left" />
      </div>
      <div className="space-y-2 flex flex-col items-center">
        <p className="text-white my-6">Time to make some progress and start scrolling... 😉</p>
        <Placeholder />
        <Placeholder />
        <Placeholder />
        <Placeholder />
        <Placeholder />
        <Placeholder />
        <Placeholder />
      </div>
    </main>
  );
};

```

## Tracking scroll position inside another element

By default the useScroll hook will track the scroll position of the full window. But you can also track the scroll position inside another element.

This can be useful when you have a scrollable container, and you want to create animations based on the scroll position inside that container.

For this you need to pass a configuration object with the property container to the hook. _This container property should be a React Ref of the element_ you want to track the scroll position of.

```typescript
import { motion, useScroll } from "framer-motion";
import { useRef } from "react";

// A small placeholder component to give us some room to scroll.
const Placeholder = () => (
  <div className="shrink-0 bg-slate-300 rounded-lg aspect-video w-[600px] max-w-[80%] opacity-30" />
);

export const App = () => {
  const ref = useRef(null);
  const { scrollXProgress } = useScroll({
    container: ref
  });

  return (
    <main className="p-2">
      <div className="sticky top-0 m-2 z-10 bg-gray-300">
        <motion.div
          style={{scaleX: scrollXProgress }}
          className="h-3 w-full bg-[#bc9e7a] origin-left" />
      </div>
      <p className="text-white my-6">Time to make some progress and start scrolling... 😉</p>
      <div ref={ref} className="overflow-x-auto py-2">
        <div className="gap-2 flex">
          <Placeholder />
          <Placeholder />
          <Placeholder />
          <Placeholder />
          <Placeholder />
          <Placeholder />
          <Placeholder />
        </div>
      </div>
    </main>
  );
};

export default App;
```
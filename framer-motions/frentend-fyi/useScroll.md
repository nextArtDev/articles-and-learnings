# useScroll: Making scroll-driven animations

import the useScroll hook, and all of a sudden you have access to 4 variables that tell you everything you need to know about the scroll position.

By default this hook will track the scroll progress of the full window. scrollX and scrollY will give you the scrolled distance in pixels, while scrollXProgress and scrollYProgress will give you the progress in a range from 0 to 1.

_All properties returned from the hook, are motion values_ too. This means you can use them in your animations directly. Imagine making a scroll progress indicator, where the number between 0 and 1 is directly tied to the scaleX of that progress bar!

```typescript
const {
  scrollYProgress,
  scrollY, //in pixels
  scrollXProgress,
  scrollX //in pixels
} = useScroll()

```

## Scroll line progress at the top of page example

```typescript
import { motion, useScroll, useSpring } from "framer-motion";

// A small placeholder component to give us some room to scroll.
const Placeholder = () => (
  <div className="bg-slate-300 rounded-lg aspect-video w-[600px] max-w-[80%] opacity-30" />
);

export const App = () => {
  // 1. create scrollYProgress
  const { scrollYProgress } = useScroll();
  // 2. using spring
  const scaleX = useSpring(scrollYProgress);

  return (
    <main>
    // 3. create sticky element at the top
      <div className="sticky top-0 p-2 z-10">
        <motion.div
        // 5. scale X by scroll value
          style={{ scaleX }}
          // 4. specify the height and origin to left
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

### useScroll props
  
#### container 

This container property is the scrollable element that you're tracking the scroll position within. 
When tracking page scroll, the value of scrollYProgress starts at 0 and goes to 1 at the end of the scrollable area. That makes sense, right? When tracking an element though, the value suddenly starts at 1, and then goes to 0 when the element is at the top of the page. 

#### target

"scrollable area of container". the target property represents the movable element you are tracking the scroll position of. If you doesn't pass in any value, the scrollable area of the container is default to target. __Passing a ref to any HTML element will start tracking the visibility of that element within the container, Meaning based on whether this element is in view or not, the scroll values will change.__  

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

### offset

__offset: ["{target} {container}", "{target} {container}"]__
 offset: ["start start", "end end"]

This property allows you to _set the starting and ending points of the scroll progress_, when tracking an element.
The most common usage of the offset property, is to take in an array with two strings. Both of these strings consist out of two words: start, center or end.

```typescript

const { scrollYProgress } = useScroll({
  target: ref,
  offset: ["start start", "end end"]
});

```

["start start", "end end"]: start start point is when the top of our element reaches the top of the window (and thus that point becomes 0), and the end end point is when the bottom of our element reaches the bottom of the window (and thus that point becomes 1).

When the user then scrolls through the page from top to bottom, it first reaches the 1 point, and then goes to the 0 point. This is why the default value of scrollYProgress is 1 at the start, and 0 at the end.

["start end", "end start"]:The start of the target should meet the end of our container. That way the top of our target (the start) will be at the bottom of the window (the end).
The end of the target should meet the start of our container. That way the bottom of our target (the end) will be at the top of the window (the start).

#### Using other units

Besides using the start, center and end values (which I by far use the most!), the offset can also be set as the following values:

- Number: A value between 0 and 1, representing the percentage of the target or container. For example: ["0.5 0.5", "0 1"]
- Pixels: A value in pixels, representing the distance from the start of the container/target. For example: ["100px 100px", "-50px 150px"]
- Percentage: Similar to the numbers between 0 and 1, but then as percentage values. For example: ["50% 50%", "0% 100%"]
- Viewport units: Viewport units in either vw or vh. For example: ["50vw 50vw", "0vh 100vh"]

### axis (Important for x-scroll )

Allowing you to use the hook for either a vertical or horizontal scroll.

```typescript
useScroll(
container: window,
target: "scrollable area of container",
offset: ["start start", "end end"],
axis: y,
)
```
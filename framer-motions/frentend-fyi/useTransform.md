# useTransform

## Transforming motion values with useTransform

Individual motion values are cool, but the real fun starts when we combine multiple values together. We can do this using the useTransform hook.

As soon as we start combining these motion values, we also enter a territory where doing this in JavaScript might finally be better than doing it in CSS. The reason for this is that we can use JavaScript to do calculations that would be harder to do in CSS.

### Break down the hook

```typescript
import { useTransform, useMotionValue } from "framer-motion";

// We don't worry yet about how to get the scroll position
const scrollY = useMotionValue(20);
const interestedScrollY = [0, 200];
const opacityRange = [0, 1];

const opacity = useTransform(scrollY, interestedScrollY, opacityRange);
```

- scrollY: The first argument you pass in any motion value, in the example above a fictional scrollY that never changes (we make that dynamic in a future lesson!).
- interestedScrollY: The second argument is the range of values you want to listen to for the motion value. So here we listen to the scroll position between 0 and 200 pixels.
- opacityRange: Finally the third argument is the range of values you want to map the motion value to. So in this example we want to map the scroll position between 0 and 200 pixels to an opacity between 0 and 1.

## Making elements draggable & practice with useTransform

Before we can start transforming values, we first need to have a value we can actually transform. For that we are going to take a look at a Framer Motion prop we didn’t discuss before: The drag prop.

```typescript
<motion.div
  drag="x"
/>
```

Adding this single attribute makes the element draggable over the x-axis. Without providing any additional props, this will add a translateX style when you drag the element.

If that is the only prop you add, your element will be draggable, but you are unable to know or control how far the user moved the element. For that we can supply our own x motion value, and use that to drive the drag.

```typescript
import { motion, useMotionValue } from "framer-motion";

const App = () => {
  const x = useMotionValue(0);
  return (
    <motion.div
      style={{ x }}
      drag="x"
    />
  );
}
```

When looking at the example above, it might feel like we’re setting only an inline style of the X value, and are never updating it, right?

Remind yourself here that firstly the style prop isn’t a regular style prop you’re used to in React, and secondly the x value is a motion value and not a regular number. By supplying a custom x motion value, Framer Motion detects this when trying to set the x transform themselves. Instead of creating a new motion value, it will then update the one we supplied.

### Try combining it with useTransform

Because we added our own x motion value, and that value is updated by the drag, we now have a motion value we can use to drive other animations. Let’s try to combine it with the useTransform hook we learned about in the previous chapter.

Take a few minutes to find out how to combine them yourself. If you’re unsure, try to look at the hint first before opening the solution.

Acceptance criteria

- The color should change when the element moves to the left or right. Be creative with the colors, but make sure the left, center and right ends have different colors! Remember that you should use hex colors (#f00) instead of color names (red) to enable Framer Motion to animate the colors.
When the block is moved to the right, it should become larger. When it moves to the left, the size shouldn’t change.
When moved left and right, the square should become a circle.

```typescript
import { motion, useMotionValue, useTransform } from "framer-motion";

const App = () => {
  const x = useMotionValue(0);

  return (
    <div className="flex items-center justify-center min-h-screen">
      <motion.div
        style={{ x }}
        className="w-32 h-32 bg-[#306a87] rounded-md flex items-center justify-center text-white"
        drag="x"
        // We talk about the dragConstraints in Module 11.
        // For now, it helps with bouncing the square back to the center
        // when you release it.
        dragConstraints={{ left: 0, right: 0}}
      />
    </div>
  );
}
 
```

```typescript

import { motion, useMotionValue, useTransform } from "framer-motion";

const App = () => {
  const x = useMotionValue(0);
  const borderRadius = useTransform(x, [-100, 0, 100], ["50%", "0%", "50%"]);
  const backgroundColor = useTransform(
    x,
    [-100, 0, 100],
    ["#6d4b7e", "#306a87", "#695a37"]
  );
  const scale = useTransform(x, [-100, 0, 100], [1, 1, 1.5]);

  return (
    <div className="flex items-center justify-center min-h-screen">
      <motion.div
        style={{ x, borderRadius, backgroundColor, scale }}
        className="w-32 h-32 rounded-md flex items-center justify-center text-white"
        drag="x"
        dragConstraints={{ left: 0, right: 0}}
      />
    </div>
  );
}


```

```typescript
```

```typescript
```
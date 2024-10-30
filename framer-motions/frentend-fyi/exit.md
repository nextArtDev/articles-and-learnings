# Exit animations 

Animating an element that gets removed from the DOM.

1- We wrap the element we want to animate in <AnimatePresence>.
2- We use some sort of conditional rendering for the motion element
3- We add the exit prop on the motion element


## The __exit__ prop

The exit prop is similar to initial and animate in the way you use it. You simply pass in a style, and Framer Motion will apply this when the element leaves the DOM.

## AnimatePresence

__<AnimatePresence>__ is a component that wraps around the elements (could be one, or multiple!) you want to animate when they leave the DOM.

### A note when adding multiple elements

Note that if you add multiple elements to <AnimatePresence>, you need to _give each element a unique key prop_. This is because Framer Motion needs to know which element is which, and the key prop helps Framer motion to keep track of this.

## Skip the initial animation?

The prop initial is required to tell Framer motion what it should animate from, if the element comes back into view. But the side effect this has, is that __the element will also now run this animation on page load__. That’s not always what you want.

You can __prevent this by adding initial={false}__ to the AnimatePresence component.

```typescript
import { useState } from "react";
import { AnimatePresence, motion } from "framer-motion";

const App = () => {
  const [isVisible, setIsVisible] = useState(true);

  return (
    <div className="grid min-h-[400px] items-start rounded-3xl p-5 shadow-2xl">
      <button
        className="mx-auto rounded-full bg-black px-5 py-3 text-white"
        onClick={() => setIsVisible((prev) => !prev)}
      >
        Toggle visibility
      </button>
      // first: wrap the component by AnimatePresence
      <AnimatePresence initial={false}>
        {isVisible && (
            // second: use some sort of conditional rendering for the motion element
          <motion.div
            initial={{ opacity: 0, scale: 0.9, y: 20 }}
            animate={{ opacity: 1, scale: 1, y: 0 }}
            // Third: add the exit prop on the motion element
            exit={{ opacity: 0, scale: 0.9, y: 20 }}
            className="mx-auto w-full max-w-[300px] rounded-2xl bg-white p-4 text-black"
          >
            <div className="mb-3 aspect-video w-full rounded-xl bg-gray-300" />
            Random card
          </motion.div>
        )}
      </AnimatePresence>
    </div>
  );
};

export default App;
```
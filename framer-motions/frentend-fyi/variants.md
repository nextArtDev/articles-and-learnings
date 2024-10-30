# Variants

## Defining the variants

In essence a __variant is a JavaScript object containing all animation styles__, like you would also use them on for example the animate prop.

At the top level we now have two keys: visible and hidden. These are the names of the variants. You can name them whatever you want, but it’s best to use names that are descriptive of the state they represent. There is also no limit on how many variants you add.

In our case the states are called visible and hidden, but it could very well be open and closed or active and inactive.

Once you have defined your variants, you can _pass them to a new prop on the motion component called **variants**_.

Then, __instead of setting the styles directly on the initial, animate and exit props__, you can reference the variant names. Framer Motion will then use the styles you have defined on your variants object.

```typescript
const variants = {
  visible: {
    opacity: 1,
    x: 0
  },
  hidden: {
    opacity: 0,
    x: 30
  }
};

<motion.div
  variants={variants}
  initial="hidden"
  animate="visible"
  exit="hidden"
/>

```

- Variants are not just for the initial, animate and exit props. You can also use them for all other props like whileHover, whileTap and whileFocus. Removing even more repetition from your code

## Variant inheritance

You _set the animate prop to the variant open_. _Any child motion elements will then also automatically have the open variant applied_ to them. __Specifying a different animate="" prop on that child, will override this behavior__.

Because of this behavior, you can control the current animation state on a single parent, and only have to specify the variants prop on the children, causing all the animations to animate when the active variant on the parent changes. 

```typescript
import { motion, useMotionValue, useTransform, AnimatePresence } from "framer-motion";
import { useState } from "react";

const App = () => {
  const [openModal, setOpenModal] = useState(false);

  return (
    <div className="flex items-end justify-center min-h-screen pb-4">
      <button
        className="bg-white px-4 py-2 rounded-full"
        onClick={() => setOpenModal((open) => !open)}
      >
        Toggle Dialog
      </button>

      <AnimatePresence>
        {openModal && (
          <motion.div
            variants={{
              open: {
                opacity: 1,
                y: 0,
              },
              closed: {
                opacity: 0,
                y: 20,
              },
            }}
            initial="closed"
            animate="open"
            exit="closed"
            className="pointer-events-none fixed inset-0 flex items-end pb-20 justify-center"
          >
            <div className="w-[300px] max-w-full bg-white text-black rounded-2xl p-2 pb-6 pointer-events-auto overflow-hidden">
              <header className="flex justify-end">
                <motion.button
                  variants={{
                    open: {
                      scale: 1,
                    },
                    closed: {
                      scale: 0,
                    },
                  }}
                  onClick={() => setOpenModal(false)}
                  aria-label="Close"
                  className="block w-8 h-8 bg-black rounded-full p-2"
                >
                  <svg viewBox="0 0 512 512" className="fill-white">
                    <path d="M443.6 387.1 312.4 255.4l131.5-130c5.4-5.4 5.4-14.2 0-19.6l-37.4-37.6c-2.6-2.6-6.1-4-9.8-4-3.7 0-7.2 1.5-9.8 4L256 197.8 124.9 68.3c-2.6-2.6-6.1-4-9.8-4-3.7 0-7.2 1.5-9.8 4L68 105.9c-5.4 5.4-5.4 14.2 0 19.6l131.5 130L68.4 387.1c-2.6 2.6-4.1 6.1-4.1 9.8 0 3.7 1.4 7.2 4.1 9.8l37.4 37.6c2.7 2.7 6.2 4.1 9.8 4.1 3.5 0 7.1-1.3 9.8-4.1L256 313.1l130.7 131.1c2.7 2.7 6.2 4.1 9.8 4.1 3.5 0 7.1-1.3 9.8-4.1l37.4-37.6c2.6-2.6 4.1-6.1 4.1-9.8-.1-3.6-1.6-7.1-4.2-9.7z" />
                  </svg>
                </motion.button>
```

## Multiple variants

If you add a variant to a motion component, you aren’t limited to just using a single variant. You can add as many as you want.

Why or when would you do that? Perhaps if you’re using the parent to control multiple child elements, and they have different states. But honestly, I have never used this myself. If you reach that point, there it might be your animations are getting a bit too complex.

```typescript
<motion.div
  variants={variants}
  initial={["hidden", "inactive"]}
  animate={["visible", "active"]}
/>

```
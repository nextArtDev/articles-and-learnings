# Layout Animation

## layout animations

You’re essentially telling Framer Motion: “Here are the two layouts of my component—figure out the best way to animate between them, while keeping it performant.

### Defining the two layouts: 

- Set the button’s position to __absolute__
- Align it to the right using __right: 0px__
- Fix the __button’s width__ at, say, __80px__ when the form is idle
- _Add this width as padding on the right_ side to avoid the input stretching too far
- _Animate the width to 100% when the form is submitted_.

### Some CSS animations can be slow

Not all CSS properties are equally performant when animated. Animating properties like width, height, or left/right often leads to performance issues because they trigger layout recalculations and repaints.

## Enabling layout animations

To enable layout animations, _all you have to do is add the layout prop to your motion element_. Framer Motion will then automatically turn any layout changes into animations.

```typescript
import { motion } from "framer-motion";
import { useState } from "react";

export const App = () => {
  const [sent, setSent] = useState(false);

  return (
    <div className="grid place-items-center w-[95%] min-h-dvh mx-auto">
      <div className="bg-black rounded-2xl p-3 w-full max-w-[600px]">
        <form className="relative pr-20">
          <input
            className="rounded-full bg-white-opaque px-5 py-3 w-full"
            placeholder="Email"
          />
          <motion.button
            type="button"
            // All The magic will happen in this line!
            layout
            style={{
              borderRadius: 999,
              backgroundColor: sent ? "#8aca65" : "#ffffff",
              color: sent ? "#fff" : "#000",
              width: sent ? "100%" : "5rem"
            }}
            onClick={() => setSent((s) => !s)}
            className="flex items-center justify-center bg-white absolute right-0 top-0 bottom-0 h-12 w-20"
          >
          // We layout button icon too! to avoid distortion
            <motion.span layout className="block">
              <SendHorizontal />
            </motion.span>
            {sent && (
              <motion.span
                initial={{ opacity: 0, x: "50%" }}
                animate={{
                  opacity: 1,
                  x: 0,
                  transition: { delay: 0.3 },
                }}
                className="ml-2"
              >
                Sent!
              </motion.span>
            )}
          </motion.button>
        </form>
      </div>
    </div>
  );
};

const SendHorizontal = () => (
  <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="m3 3 3 9-3 9 19-9Z"/><path d="M6 12h16"/></svg>
);

export default App;
```
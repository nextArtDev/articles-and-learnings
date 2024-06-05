## Acceptance criteria

The animation should stretch vertically and horizontally, but not at the same time.
At the start of the animation the text should fade in.
At the end of the animation the text should fade out.
The animation should repeat, but should wait a short while before starting over again.

```typescript
import "./styles.css"
import { motion } from "framer-motion";

const App = () => {
  return (
    <div className="flex items-center justify-center min-h-screen">
      <div className="w-[150px] overflow-hidden flex relative flex-col aspect-[2/4] text-white bg-[#41352a] rounded-2xl">
      <div class="h-full fadeout">
        <motion.div
          className="text-[80px] [font-family:monospace] origin-top-left leading-[0.8] inset-1 absolute break-all"
          animate={{
            opacity: [0, 1, 1, 1, 1, 1, 0],
            scaleX: [1.5, 1.5, 2.5, 2.5, 2.5, 1.5, 1.5],
            scaleY: [0.75, 0.75, 0.75, 1.5, 1.5, 1.5, 8],
          }}
          transition={{
            repeat: Infinity,
            repeatDelay: 1,
            times: [0, 0.05, 0.2, 0.3, 0.45, 0.7, 1],
            duration: 18
          }}
        >
          responsive
        </motion.div>
      </div>
      <p className="mt-auto  p-2">Make your animations work on all devices</p>
      </div>
    </div>
  );
};

export default App;
```

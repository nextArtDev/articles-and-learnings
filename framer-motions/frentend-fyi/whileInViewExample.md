# whileInView Example

Four cards which comes in view while scrolling by the staggered behavior.

```typescript
import { motion } from "framer-motion";

export const App = () => {
  return (
    <div className="text-white px-8">
      <div className="h-screen grid place-items-center">Scroll down :)</div>
      <motion.div
        variants={{
          inView: {
            transition: { staggerChildren: 0.1 }
          },
          outOfView: {
            transition: { staggerChildren: 0.1, staggerDirection: -1 }
          }
        }}
        whileInView="inView"
        initial="outOfView"
        animate="outOfView"
        /* Ssst. This property isn't there. Just leave it at one, and I'll explain later ;) */
        viewport={{ amount: 1 }}
        className="space-between grid w-full grid-cols-4 justify-center gap-2"
      >
        {[1, 2, 3, 4].map((i) => (
          <motion.div
            key={i}
            variants={{
              inView: {
                opacity: 1,
                x: 0,
              },
              outOfView: {
                opacity: 0,
                x: -30,
              }
            }}
            className="bg-white aspect-square rounded-xl"
          />
        ))}
      </motion.div>
      <div className="h-screen grid place-items-center">Scroll up :)</div>
    </div>
  );
};
```
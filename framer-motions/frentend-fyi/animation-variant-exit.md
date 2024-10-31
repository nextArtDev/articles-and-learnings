
# Example

```typescript

import { motion } from "framer-motion";
import { useState } from "react";
import { useInterval } from "react-use";
import { quotes } from "./quotes";
import { Debug, useSpeedControl } from "./debugtools";
import { ChevronLeft, ChevronRight } from "./icons";

const getQuoteVariant = (index: number, suffix?: string) =>
  `quote_${index}` + (suffix ? `_${suffix}` : "");

const App = () => {
  const [activeQuoteIndex, setActiveQuoteIndex] = useState(0);
  const lastQuoteIndex = quotes.length - 1;
  const [isPaused, setIsPaused] = useState(false);
  const { speed, setSpeed, faster, slower } = useSpeedControl();

  const next = () => {
    setActiveQuoteIndex((currentQuote) =>
      currentQuote >= lastQuoteIndex ? 0 : currentQuote + 1,
    );
  };

  const previous = () => {
    setActiveQuoteIndex((currentQuote) =>
      currentQuote === 0 ? lastQuoteIndex : currentQuote - 1,
    );
  };

  useInterval(
    () => {
      // See note in the lesson on why we use requestAnimationFrame here.
      window.requestAnimationFrame(next);
    },
    isPaused ? null : speed,
  );

  return (
    <div className="flex flex-col items-center justify-center min-h-screen pb-4">
      <motion.div
        animate={`quote_${activeQuoteIndex}`}
        initial="inactive"
        whileHover={["paused", getQuoteVariant(activeQuoteIndex, "highlighted")]}
        onPointerEnter={() => setIsPaused(true)}
        onPointerLeave={() => setIsPaused(false)}
        className="bg-white relative rounded-2xl py-5 px-12 w-[400px] max-w-full"
      >
        <div className="flex justify-center">
          {quotes.map((quote, index) => (
            <motion.img
              key={index}
              variants={{
                [getQuoteVariant(index)] : {
                  scale: 1.2,
                  zIndex: 10
                },
                paused: {
                  scale: 0.9,
                  filter: "grayscale(1)"
                },
                [getQuoteVariant(index, "highlighted")] : {
                  scale: 1.2,
                  zIndex: 10,
                  filter: "grayscale(0)"
                },
              }}
              className="rounded-full -mx-2 relative border border-black w-12 h-12"
              src={quote.image}
            />
          ))}
        </div>
        <div className="grid mt-3 text-center">
          {quotes.map((quote, index) => (
            <motion.blockquote
              key={index}
              variants={{
                inactive: { opacity: 0, y: 20 },
                [getQuoteVariant(index)]: { opacity: 1, y: 0}
              }}
              // grid-area:1/1 -> all slides will overlay
              className="[grid-area:1/1] relative"
            >
              <p>{quote.text}</p>
              <p className="font-bold mt-3 text-sm">
                {quote.name} - {quote.roleCompany}
              </p>
            </motion.blockquote>
          ))}
        </div>
        <motion.button
          onClick={previous}
          variants={{
            inactive: { x: -20, opacity: 0},
            paused: { x: 0, opacity: 1 }
          }}
          className="absolute top-1/2 left-0 block px-3"
        >
          <ChevronLeft /> <span className="sr-only">Previous</span>
        </motion.button>
        <motion.button
          onClick={next}
          variants={{
            inactive: {x: 20, opacity: 0},
            paused: {x: 0, opacity: 1}
          }}
          className="absolute top-1/2 right-0 block px-3"
        >
          <ChevronRight /> <span className="sr-only">Next</span>
        </motion.button>
      </motion.div>

      <Debug
        activeQuoteIndex={activeQuoteIndex}
        speed={speed}
        isPaused={isPaused}
        setIsPaused={setIsPaused}
        faster={faster}
        slower={slower}
      />
    </div>
  );
};

export default App;
```
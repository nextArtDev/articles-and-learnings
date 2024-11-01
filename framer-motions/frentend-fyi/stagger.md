# staggerChildren

Because __variants__ we add on a parent motion component, are inherited on its children is helpful when animating multiple children. Framer Motion also provides a way to change the variant of each child with a small delay.

## Enter staggerChildren

__staggerChildren__ is a special property _you can add to the transition prop_, whenever you are animating with variants.

## The staggerDirection 
Decides in which direction the stagger should go. By default it is set to 1, which means it will start from the first child. If you set it to -1, it will start from the last child and animate towards the first child

In order for the variants to work, _all child components that you want to animate, should be motion components too_

```typescript
const containerVariants = {
  hidden: {
    opacity: 0,
    transition: {
    // Each child will start animating 0.1 seconds after the previous one
      staggerChildren: 0.1,
    // Start from the last child and animate towards the first child
      staggerDirection: -1
    }
  },
  visible: {
    opacity: 1,
    transition: {
      staggerChildren: 0.1
    }
  }
}
```

## Let the parent wait until the child animation finishes

If you’re animating children, chances are you want to wait with the parent’s animation, until all children are done.
For this you could use the __delay__ property in the transition object, but there is a more elegant way to do this. By using the _when_ property on the transition object, _you can specify when the parent should start animating_.

### _beforeChildren_ or _afterChildren_

```typescript
const containerVariants = {
  hidden: {
    opacity: 0,
    transition: {
      staggerChildren: 0.1,
      // wait until the child animation finishes
      when: "afterChildren"
    }
  },
  visible: {
    opacity: 1,
    transition: {
      staggerChildren: 0.1,
      // wait until the child animation finishes
      when: "beforeChildren"
    }
  }
}
```

## A navigation menu

me: *The nth number which component inhered the parent is not important* it means its not important how deep we go from the parent to use staggerChildren prop; here: ```typescript <motion.div> <nav> <ul> <motion.li>```

```typescript
import { motion } from "framer-motion";
import { useState } from "react";

// In a real app we of course should have a url for each item too.
const menuItems = ["Home", "Invoices", "Usage", "Profile", "Settings"];

const App = () => {
  const [navigationIsVisible, setNavigationIsVisible] = useState(false);

  return (
    <div>
      <motion.div
        initial="hidden"
        animate={navigationIsVisible ? "visible" : "hidden"}
        variants={{
          hidden: {
            width: 74,
            transition: {
              staggerChildren: 0.1,
              staggerDirection: -1,
              // Use either when or delay here.
              // when: "afterChildren",
              delay: (menuItems.length - 2) * 0.1
            },
          },
          visible: {
            width: 200,
            transition: { staggerChildren: 0.1 }
          },
        }}
        className="bg-[rgba(10,10,10,.8)] h-screen p-3 text-white"
      >
        <button
          className="bg-white/5 border border-black rounded-md p-3 mb-4"
          onClick={() => setNavigationIsVisible((visible) => !visible)}
        >
          <span className="sr-only">Toggle navigation</span>
          <HamburgerIcon />
        </button>
        <nav>
          <ul>
            {menuItems.map((item) => (
              <motion.li
                key={item}
                variants={{
                  hidden: { opacity: 0, x: -100 },
                  visible: { opacity: 1, x: 0 },
                }}
                className="py-3 hover:bg-white/5 px-3 rounded-md transition-colors"
              >
                {item}
              </motion.li>
            ))}
          </ul>
        </nav>
      </motion.div>
    </div>
  );
};

const HamburgerIcon = () => (
  <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round">
    <line x1="4" x2="20" y1="12" y2="12" />
    <line x1="4" x2="20" y1="6" y2="6" />
    <line x1="4" x2="20" y1="18" y2="18" />
  </svg>
);

export default App;
```

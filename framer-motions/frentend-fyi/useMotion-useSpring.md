
## Motion values, the core of Framer Motion

Motion values are at the core of all animations in Framer Motion. The library uses them internally to among other things maintain the current value as well as the velocity of that value.

## _useMotionValue_ hook

All motion values are created using the useMotionValue hook. Both as part of Framer Motion’s default APIs, as well as any values you create yourself.

```typescript
import { useMotionValue, motion } from "framer-motion"

const App = () => {
  const scale = useMotionValue(0);

  return <motion.div style={{ scale }} />
}
```

At first glance scale might look like a regular value, just a number – because that is what we passed in. But that isn’t the case! A motion value is actually an object – two relevant properties for us to know are .get() and .set(). These functions are used to get and set the value of the motion value.

### The style prop

As you could see in the code snippet above, __when using the motion value you pass it’s value to the style prop, instead of animate or initial__. We are now the ones in control of changing the value, not Framer Motion.

This _style prop isn’t a regular React style prop though. It is a Framer Motion enhanced version. It accepts both regular CSS properties, as well as motion values._ Because remember: Motion values have the .get() function that needs to be called in order to get the value. React would not call this function, which is why you can not pass a motion value to the style prop of a regular React element.

## Example for useMotionValue()

In the example below you see a range input, with on line 26 an onChange attached to it. This onChange is defined on _scale.set(parseFloat(ev.target.value))_, where we use the scale.set() function part of the motion value to update its value.

```typescript
const scale = useMotionValue(1);
  const onRangeChange = (ev: React.ChangeEvent<HTMLInputElement>) => {
    scale.set(parseFloat(ev.target.value))
  }

  return (
    <div className="flex items-center flex-col gap-8 justify-center min-h-screen">
      <motion.button
        className="bg-black text-white px-3 py-2 rounded-md"
        style={{ scale }}
        transition={{ type: 'spring', damping: 300 }}

      >
        Scale ⚖️
      </motion.button>
      <div className="fixed bottom-6 left-1/2 -translate-x-1/2 p-3 bg-white/10 rounded-xl flex items-center justify-center">
        <input
          type="range"
          min={0.5}
          max={5}
          step={0.01}
          defaultValue={1}
          onChange={onRangeChange}
        />
      </div>
    </div>
```

If you move the range slider you will see the scale of the element change. There is no animation happening though. Not even if you add the transition prop.

The reason for that is because when using a motion value directly, you are the one in control of the animation. If you call .set(), it will simply update the motion value with the value you give it.


## Make it bounce: useSpring

When an animation is tied into a user interaction, it sometimes feels more natural to not have an transition on that animation. That is why out of the box there’s no transition on a useMotionValue value.

For all other cases, we can use the useSpring hook. This hook takes in a motion value and a spring configuration, allowing you to control how this value should change

Taking the example from above – the only thing that changes is we swap up useMotionValue for useSpring. This spring returns a motion value as well, but adds a spring transition on top of it. Even without any custom transition applied, you already notice that it is way more bouncy.

```typescript
import { motion, useSpring } from "framer-motion";

const App = () => {
  const scale = useSpring(1);
  const onRangeChange = (ev: React.ChangeEvent<HTMLInputElement>) => {
    scale.set(parseFloat(ev.target.value))
  }

  return (
    <div className="flex items-center flex-col gap-8 justify-center min-h-screen">
      <motion.button
        className="bg-black text-white px-3 py-2 rounded-md"
        style={{ scale }}
      >
        Scale ⚖️
      </motion.button>
      <div className="fixed bottom-6 left-1/2 -translate-x-1/2 p-3 bg-white/10 rounded-xl flex items-center justify-center">
        <input
          type="range"
          min={0.5}
          max={5}
          step={0.01}
          defaultValue={1}
          onChange={onRangeChange}
        />
      </div>
    </div>
  );
};
```

## Passing a motion value to the spring

It doesn’t stop here. We can pass useSpring a motion value. This way you can create a spring that is based on another motion value. Might sound weird at first, but this will be super useful in the next module.

```typescript
import { motion, useMotionValue, useSpring} from "framer-motion";

const App = () => {
  const someMotionVal = useMotionValue(0);
  const scale = useSpring(someMotionVal);

  return (
    <motion.div style={{ scale }} />
  );
}
```

```typescript
```

```typescript
```

```typescript
```

```typescript
```

```typescript
```

```typescript
```

```typescript
```

```typescript
```

```typescript
```

```typescript
```

```typescript
```

```typescript
```

```typescript
```

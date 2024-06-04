## motion (7)

### Transforming into a Motion Component

```typescript
import { motion } from 'framer-motion';

const MyAnimatedButton = () => {
  return (
    <div>
      <motion.button>Click me!</motion.button>
    </div>
  );
};

```

### Converting custom elements into Motion Components

__const AnimatedCustomComponent = motion(MyCustomComponent)__

```typescript
import { motion } from 'framer-motion';
import { MyCustomComponent } from './MyCustomComponent';

// How to convert custom component into a motion component
const AnimatedCustomComponent = motion(MyCustomComponent);

const Page = () => {
  return (
    <div>
      <AnimatedCustomComponent>
        Animation galore!
      </AnimatedCustomComponent>
    </div>
  );
};
```

#### A small performance tip

We define _AnimatedCustomComponent_ component outside of the Page component. This is because we only need to convert the component once. If you would define it inside the Page component, it would be converted every time the Page component is rendered. That would not break anything, but it does add unnecessary overhead.

## _animate_ prop

The animate prop __takes in any CSS property you would normally set as a style.__ 

There are a few rules to keep in mind:

- You need to write the styles in camelCase. So background-color becomes backgroundColor.
- _If you write values as a number, it’s assumed as a pixel value._ Otherwise you write it as string values like 10px, 80vh or #f00.
- Not all properties can be animated. For example, you can’t animate the display property. But you can animate the opacity property.
- If you’re using colors, _you can animate Hex, RGB and HSLA._ So you can not use color names like red or blue.

## Transform shortcuts

On top of that Framer Motion also makes it easier to animate multiple transform properties at once. Similar to how individual transform properties work in CSS.

If you use the individual properties, Framer Motion will convert these to a single transform property for you.

```typescript
// As a single transform property

animate={{
  transform: "translateY(30px) scale(0.8) rotate(45deg)"
}}

// Individual transform properties
animate={{
  scale: 0.8,
  rotate: "45deg",
  y: "30px"
}}

```

## Animate multiple properties

Now we know what types of properties we can add to this animate prop, let’s add a few more. All of them will animate at the same time, like you see in the example below.

```typescript
import { motion } from "framer-motion";

      const App = () => {
        return (
          <div className="flex items-center justify-center min-h-screen">
          <motion.button
          className="bg-black text-white px-3 py-2 rounded-md"

          animate={{
            scale: 2,
            y: "50%",
            opacity: 0.5,
            rotate: 1800
          }}

          >
          Zoom zoom zoom
          </motion.button>
          </div>
          );
        };

        export default App;
```

## Adding a from state to the animation (9)

In the previous animations we only told Framer Motion where it should animate to. We never set a starting point.

If you don’t specify a starting point, it will use the current value of the property. This can be either the style you set on the element with CSS, or the default the browser applies. But what if you want to override this default?

### _initial_ prop

Framer Motion gives you the option to manually override this initial state. For that you use the initial property. This property accepts a style object, just like the animate property.

## Inline styles

Framer Motion works with inline styles for the animation. If you set an initial value for the background, it will immediately set this as an inline style, before the animation runs.

Because of this, the default CSS specificity rules are respected. Inline styles always win in specificity – except when you use !important! This is why in the first question the initial color would be blue, and in the second question the initial color would be black.

Even though we already set an initial state with the className, Framer Motion will quickly override this style with the initial state we’ve set in the initial prop, before it runs the animation. This way you have ultimate control over how the animation should run.

```typescript
import { motion } from "framer-motion";

const App = () => {
  return (
    <div className="flex items-center justify-center min-h-screen">
      <motion.button
        className="bg-black text-white px-3 py-2 rounded-md"
        animate={{
          background: "#f00"
        }}
      >
        Fancy button
      </motion.button>
    </div>
  );
}; 
```

### Should you use the initial prop or a css style for the initial state?

This very much depends on your own preferences. I think it’s more important that you always do the same, than which of the two you like to choose.

### Using the initial prop

The benefit of using the initial prop is that you define all your animation values in the same place. This makes it easier to see what will change over time, rather then needing to open an external CSS file.

Of course when using Tailwind, you might have less reason to do so since the classnames are directly on the component too. But even then you still need to take a good look at all the classnames, versus a quick glance at the initial prop.

### Defining it in CSS

You don’t have any extra inline styles, and don’t run the risk of seeing Framer Motion apply the inline styles on page load. This can cause a flash of the initial state, which can look very bad.

The downside then is that you might define your initial state in a difference place than your animation.

Just like anything in web development there’s tradeoffs with both. There is no right or wrong here, just a matter of preference. Most of the time I tend to choose to specificity the initial state with a Tailwind class rather than the initial prop.

## Changing the default transition

The transition prop gives you the option to configure anything about the transition. You can use it to change the duration, the easing, add a delay, or make the animation repeat.

```typescript
<div className="flex items-center justify-center min-h-screen">
      <motion.button
        className="bg-black text-white px-3 py-2 rounded-md"
        animate={{
          scale: 2
        }}
       transition={{
          // Duration of the animation in seconds
          duration: 0.5,
          // Name of an easing function or a custom curve like [0.42, 0, 0.58, 1]
          // See the docs for all easing functions
          ease: "easeInOut",
          // Number of times to repeat, or Infinity
          repeat: 4,
          // Repeat type (loop, reverse, mirror)
          repeatType: "reverse",
          // Delay until repeat in seconds
          repeatDelay: 0.5,
        }}
      >
        Repeat until it clicks
      </motion.button>
    </div>

```

### Spring animation

If you want to create your own spring animation, you can set the type to spring and configure the spring settings via the properties in the example below.

```typescript
<div className="flex items-center justify-center min-h-screen">
      <motion.button
        className="bg-black text-white px-3 py-2 rounded-md"
        animate={{
          scale: 2
        }}
        transition={{
          delay: 2,
          repeat: Infinity,
          repeatType: "reverse",
          repeatDelay: 2,
          type: "spring",
          bounce: 40,
          damping: 10,
          mass: 3,
          stiffness: 30,
          velocity: 100
        }}
      >
        Repeat until it clicks
      </motion.button>
    </div>
```

### Different transitions per style property

Finally you can also set custom transitions for each style property, instead of a single transition for the full animation.

You can do this by nesting the property name inside the transition object. In the example below we set a different transition for the scaleX, and use the same transition for the scaleY and y properties.

```typescript
<motion.button
        className="bg-black text-white px-3 py-2 rounded-md"
        animate={{
          scaleX: 0.5,
          scaleY: 0.5,
          y: "50%"
        }}
        transition={{
          repeat: Infinity,
          repeatType: "reverse",
          repeatDelay: 2,
          duration: 0.5,
          scaleX: {
            delay: 1,
            duration: 0.5,
            repeat: Infinity,
            repeatType: "reverse",
            repeatDelay: 1,
          },
        }}
      >
        Repeat until it clicks
      </motion.button>
```

In the above example we have defined the scaleX and scaleY as two individual transforms, as well as added a translateY with the y property. We then set a different transition and repeat delay for the scaleX. The scaleY and y transitions use the transition specified in the root of the transition object.

## animation loop with keyframes

Just like CSS animations, you can use keyframes to make multi step animations in Framer Motion too.

In order to create a keyframe animation, all you have to do is add multiple values as an array to a single property: scale: [1, 2, 2, 1, 1].


```typescript
<div className="flex items-center justify-center min-h-screen">
      <motion.div
        className="bg-gray-400 w-24 h-24"
        animate={{
          scale: [1, 2, 2, 1, 1],
          rotate: [0, 0, 180, 180, 0],
          borderRadius: ["0%", "0%", "50%", "50%", "0%"],
        }}
        // removing the repeat and repeatDelay and notice the animation only plays once.
        transition={{
          duration: 2,
          ease: "easeInOut",
          repeat: Infinity,
          repeatDelay: 1
        }}
      />
    </div>
```

### Animation steps are spread out evenly

Framer Motion spreads out the animations evenly over the duration of the animation. This means, if you have 5 values in your keyframe animation, and you set the duration to 5 seconds, the animation from one step to the next will take 1 second.

This also means if you make the scale animation have 3 steps and the rotation would still have 5, the animation of the scale takes longer than the rotation, and animations start to overlap each other.

This is not a bad thing, but can make it a bit harder to get animation properties to line up.

Sounds confusing? Let’s take a look at the following example. The light gray box only has 3 scale properties, where the dark gray box has the same 5 steps we use in the previous example.

The rotation and border radius happen at exactly the same time, but the scale animations are not aligned.

```typescript
<div className="flex items-center justify-center min-h-screen">
        <motion.div
          className="bg-gray-400 w-24 h-24"
          animate={{
            scale: [1, 2, 1],
            rotate: [0, 0, 180, 180, 0],
            borderRadius: ["0%", "0%", "50%", "50%", "0%"],
          }}
          transition={{
            duration: 2,
            ease: "easeInOut",
            repeat: Infinity,
            repeatDelay: 1
          }}
        />
        <motion.div
          className="bg-gray-800 w-24 h-24"
          animate={{
            scale: [1, 2, 2, 1, 1],
            rotate: [0, 0, 180, 180, 0],
            borderRadius: ["0%", "0%", "50%", "50%", "0%"],
          }}
          transition={{
            duration: 2,
            ease: "easeInOut",
            repeat: Infinity,
            repeatDelay: 1
          }}
        />
      </div>
```

### The times property

By default Framer Motion will spread the animation steps out evenly over the duration of the animation. However, of course you can have more control over this. You can change this behavior by adding a times property to the transition.

Each property of the times array has to be a number between 0 and 1, representing the percentage of the animation that has passed. So if you have 5 steps, the values would be automatically set at [0, 0.25, 0.5, 0.75, 1] which corresponds to 0%, 25%, 50%, 75% and 100% of the animation.

You could however also set the times property to [0, 0.5, 0.75, 0.875, 1] to make the first step take up 50% of the animation. 

```typescript
 <div className="flex items-center justify-center min-h-screen">
        <motion.div
          className="bg-gray-400 w-24 h-24"
          animate={{
            scale: [1, 2, 2, 1, 1],
            rotate: [0, 0, 180, 180, 0],
            borderRadius: ["0%", "0%", "50%", "50%", "0%"],
          }}
          transition={{
            duration: 2,
            ease: "easeInOut",
            repeat: Infinity,
            repeatDelay: 1,
            times: [0, 0.5, 0.75, 0.875, 1]
          }}
        />
        <motion.div
          className="bg-black w-24 h-24"
          animate={{
            scale: [1, 2, 2, 1, 1],
            rotate: [0, 0, 180, 180, 0],
            borderRadius: ["0%", "0%", "50%", "50%", "0%"],
          }}
          transition={{
            duration: 2,
            ease: "easeInOut",
            repeat: Infinity,
            repeatDelay: 1
          }}
        />
      </div>
```

### Automatically infer the initial value

One final cool thing about Framer’s keyframes is you can set the initial frame as null, and Framer will automatically infer the initial value from the current value of the element. This removes the need to duplicate these styles in both your CSS and your Framer Motion code.

This is similar to skipping the initial prop we discussed in lesson 2.5.

```typescript
<motion.div
  // rounded-full will set the border radius in CSS
  className="bg-black w-24 h-24 rounded-full"
  animate={{
    scale: [1, 2, 2, 1, 1],
    rotate: [0, 0, 180, 180, 0],
    // We then infer this (from css for example) as a default by using null
    borderRadius: [null, "0%", "50%", "50%", "0%"],
  }}
/>
```

## Adding interactivity: Hover, focus and tap

 The focus animations behave similar to the CSS focus-visible pseudo-class–only showing when it receives focus from a keyboard.

### Hover

To animate the hover state, you can use the whileHover prop. This prop accepts the same values as the animate prop. You can use a string to refer to a named animation (future chapter!), or pass an object with the animation properties.

```typescript
 <motion.button
        className="h-16 rounded-full px-8 bg-blue-600"
        whileHover={{ scale: 2.2 }}
      >
        Hover me!
      </motion.button>
```

### Focus

The focus state is similar to hover, and can be styled via the whileFocus prop. This property works similar to the CSS focus-visible pseudo-class in the way this gets triggered.

The difference with a regular css focus, is that focus-visible only gets triggered when the element receives focus by accessible means. Most of the time that means the element is focused by a keyboard. Where the regular focus can also be triggered on a mobile device by tapping on the element – the so called “sticky hovers”.

```typescript
div className="flex items-center flex-col text-white p-8 justify-center min-h-screen">
      <motion.button
        className="h-16 rounded-full px-8 bg-blue-600"
        whileFocus={{ scale: 2.2 }}
      >
        Focus me!
      </motion.button>
    </div>
```

### Tap

Next to hover and focus events, Framer Motion also has a whileTap prop. This prop is triggered when a user taps on an element (with the left click or their first touch point). You can use this to for example add extra feedback to you buttons. Try clicking the button below.

Tap also brings some extra callbacks that you can use: onTap, onTapStart and onTapCancel. These callbacks can be used to trigger other animations or to run some logic.

```typescript
 <div className="flex items-center flex-col text-white p-8 justify-center min-h-screen">
      <motion.button
        className="h-16 rounded-full px-8 bg-blue-600"
        whileHover={{ scale: 1.3 }}
        whileTap={{ scale: 0.8 }}
      >
        Tap dance 💃
      </motion.button>
    </div>
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

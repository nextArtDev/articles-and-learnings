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

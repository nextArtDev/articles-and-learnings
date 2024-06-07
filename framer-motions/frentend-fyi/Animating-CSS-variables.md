# Animating CSS variables (custom properties)

CSS variables are a very powerful recently-ish feature of CSS. A feature that I keep discovering great use-cases for every few months.

CSS Variables (or CSS Custom Properties would be the correct name), are a way to define variables in CSS. You can then use these variables in your CSS, and change them with JavaScript. Mozilla Developer Docs does a very good job on describing them in detail. Or take a look at a video of me, using CSS variables to make a gradient follow the cursor.

In short they look like this:

```typescript
:root {
  --header-size: 60px;
}

main {
  padding-top: var(--header-size);
}

.header {
  height: var(--header-size);
}
```

With the above code we defined a variable named --header-size with a value of 60px, that we then use in our .header class to define a height, but also use on the main element to add some padding.

Good to know:

- All CSS custom properties are prefixed with --
- You can define them on any element, just like a regular CSS property
- If you define a variable on an element, all it’s children will have access to it.
- If you override a property on a child, from that element on (including all its children) will use the new value.

One of the big benefits of CSS custom properties, is that children will automatically have access to variables defined on any of their parents. There are a few cases where this can be very useful:

- If you have nested components and want to change something based on a parent component
- If you want to change something based on a media query
- If you want to change the style of a pseudo element

## Using CSS variables with Framer Motion

Using CSS variables is perhaps less exciting than you hoped for:

```typescript
import { motion } from "framer-motion";

const MyComponent = () => {
  const scale = useMotionValue(20);

  return (
      <motion.div
        style={{
          "--scale": scale,
        }}
      />
  );
};
```

Just like regular CSS properties, you can render them as part of the style tag. Whenever this value updates, Framer Motion will update the CSS variable, and so will any other styles that are using that variable. Easy as that!

Next to adding them to the style tag, you can also add the variables to the initial, animate and exit props.

A basic example with CSS variables allows us to animate the x position in a single place, and then using it in its children. These children could very well be in a nested component instead of directly in the parent.

Looking at this now the use case for CSS variables might not be directly apparent, but trust me it’s worth to know about this possibility.

```typescript
import { motion, useMotionValue, useTransform } from "framer-motion";

const App = () => {
  return (
    <div className="flex items-center justify-center min-h-screen">
      <motion.ol
        initial={{ "--x": "0px" }}
        animate={{ "--x": "100px" }}
        transition={{
          duration: 1,
          repeat: Infinity,
          repeatType: "reverse",
        }}
        className="flex flex-col gap-3 text-center">
        <li
          className="bg-white p-5 rounded-2xl translate-x-[--x]">
          CSS
        </li>
        <li
          className="bg-white p-5 rounded-2xl translate-x-[--x]">
          is
        </li>
        <li
          className="bg-white p-5 rounded-2xl translate-x-[--x]">
          the BEST
        </li>
      </motion.ol>
    </div>
  );
}
```

### A bit less easy with TypeScript

If you are using TypeScript, things are a little bit less easy. Not because of Framer Motion, but because of React. By default TypeScript will only allow known CSS properties on the style tag. You can fix this by adding a react.d.ts file to your project, and adding the following code:

```typescript
declare module 'react' {
  interface CSSProperties {
      [key: `--${string}`]: string | number
  }
}
```

This will tell TypeScript that any CSS property prefixed with -- is also allowed.
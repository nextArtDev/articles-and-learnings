# Responsiveness


## CSS Variables

CSS Variables are a very powerful recent-ish addition to CSS. They allow you to define a variable in CSS, and then use that as a value for a style property. You can even set this variable as an inline style on an element, and use it on the same element in the CSS property.

This is super powerful if we want to make responsive animations. Mainly because by using CSS variables, we don’t need to know the current breakpoint in JavaScript. We simply set 2 variables (for each breakpoint in CSS), and show the correct variable based on the current breakpoint in CSS.

- On _mobile_: Change the _y position_ and opacity
- On _desktop_: Change the _x position_ and opacity

```typescript
<motion.div
  className="beautiful-drawer"
  animate={{
    "--x": "0%",
    "--y": "0%",
  }}
  initial={{
    "--x": "-100%",
    "--y": "100%",
  }}>
  Beautiful Drawer
</motion.div>

<motion.div
  className="max-sm:translate-y-[--y] md:translate-x-[--x] beautiful-drawer">
</motion.div>

```

-----------------------

## Responsiveness without media queries

examples:

```typescript

// 1
.container {
  display: grid;
  grid-template-columns:
    repeat(auto-fit, minmax(100px, 1fr));
}

// 2
.container {
  display: flex;
  flex-wrap: wrap;
}

.child {
  width: 100px;
  flex-grow: 1;
}

//clamp() is a new math function that allows you to set a value based on a minimum, maximum and preferred value. 
.image {
  width: clamp(50%, 480px, 100%);
}


//min() and max() are two other math functions that can be very useful in responsive design. They allow you to set a value based on the minimum or maximum of two values.

.video {
  width: min(50%, 480px);
}

.video {
  width: max(50%, 480px);
}

```
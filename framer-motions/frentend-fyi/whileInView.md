# The whileInView prop

## “scroll-triggered” animations

animations that are triggered when en element enters or leaves the viewport. They don’t change over time based on how far the user has scrolled, but trigger at a certain point.

The easiest way to make a _scroll-triggered_ animation is by using the _whileInView_ prop. This prop works similar to the animate or for example the whileHover.

```typescript
<motion.div
  whileInView={{ opacity: 1, borderRadius: "50%", }}
  viewport= {{amount:0.8}}
  initial={{ opacity: 0, borderRadius: "0%" }}
  // here "animate" prop make sure if its not in view, it will go back to the initial state
  animate={{ opacity: 0, borderRadius: "0%" }}
  transition= {{ duration: 0.5 }}
>
   fade in when I get in view.
</motion.div>

```

## The initial prop

Depending on what you want to animate to, you might have to add the initial prop.

If you don’t add a value to the initial prop, Framer Motion will use the browser’s default value. For opacity that is for example 1, and for border-radius that’s 0. This means that if you don’t set the initial prop, the element will already be visible (at opacity 1) and have a border-radius of 0.

Because of this you only see the border-radius animation, and not the opacity animation.

## The animate prop

The _animate prop is responsible to tell Framer Motion what the end state of the animation is, after the element went out of view again._ __In most cases this will be the same as the initial prop__, but it doesn’t have to be.

In the case both use the same value, using named variants might be the cleanest option.

## Configuring the ‘in view’ moment

A key thing in animations, is the timing of them. By default the whileInView prop triggers the animation as soon as the element enters the viewport. This can however result in the user missing the animation, as it might already be done animating by the time the element is in view.

To solve this, there is another prop you can use in combination with whileInView: __viewport__.

This prop allows you to configure many things about your scroll-triggered animations. For example, you can set the _amount of the element that should be in view before the animation triggers (10%? 50%? 100%?)_, or you can set a margin around the element to control when the animation triggers.

```typescript
<motion.div
viewport={{ once: true, root: ref, amount:1, 
            margin: "20px 50px -20px -50px",}}>
…

</motion.div>
```

### ref 

By default framer-motion will look at the window to determine if an element is in view. By passing a react _ref_ to the _root_ property it will make it respond to the ref instead.

```typescript

cost scrollableRef = useRef()

<motion.div
  whileInView={{ opacity: 1, borderRadius: "50%", }}
  viewport= {{root : scrollableRef}}
  initial={{ opacity: 0, borderRadius: "0%" }}
  animate={{ opacity: 0, borderRadius: "0%" }}
 
>
   fade in when I get in view.
</motion.div>

```

### amount 

The _amount_ property controls how much of the element should be in view before element is deemed in view.

### margin 

We use _margin_ to control when the animation triggers. this property accepts a string with the same values as the CSS margin property: 
_[top] [right] [bottom] [left]_ if you set the top value to 20, the animation will trigger when the top of the element is 20px in view.

You can also use shorthands just like in css, by for example specifying _20px_ for _all sides_, or _20px 50px_ for _top and bottom, and left and right_.

This property is similar to the _rootMargin_ property for the IntersectionObserver.

```typescript```
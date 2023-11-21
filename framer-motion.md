# Animated portfolio website with react & framer motion

## scroll snap

```CSS
html{
    scroll-snap-type: y mandatory;
    scroll-behavior: smooth;
}

section {
    scroll-snap-align: center;
}
```

### tailwind snap scroll

_for snap-y_

1. defining the direction of snap in parent element, _snap-y_ (or _snap-x)_
2. set _snap-mandatory_ to force tailwind for smooth snap
3. defining a _overflow-scroll_ we want to stop the overflow except for scrolling
4. defining the type of snap; _snap-start_ _snap-center_
5. we should define a specific _height_ for each of child elements

```javascript
<div className= 'snap-y snap-mandatory overflow-scroll w-screen  h-screen'>
    <div className="snap-start w-screen h-screen flex items-center justify-center" >1</div>
    <div className="snap-start w-screen h-screen flex items-center justify-center" >2</div>
    <div className="snap-start w-screen h-screen flex items-center justify-center" >3</div>
    <div className="snap-start w-screen h-screen flex items-center justify-center" >4</div>
</div>
```

for snap-x

1. defining _snap-x_ in parent element
2. defining _snap-mandatory_
3. setting _overflow-scroll_
4. defining the certain amount of _width_ for parent (ex: w-96)
5. defining a certain amount of _width_ for each child (ex:w-96)
6. defining _snap-start_ or _snap-center_ or _snap-end_

```javascript
<div className= 'snap-x snap-mandatory w-96 overflow-scroll h-screen'>
    <div className="snap-start w-96 flex-shrink-0 h-screen flex items-center justify-center" >1</div>
    <div className="snap-start w-96 flex-shrink-0 h-screen flex items-center justify-center" >2</div>
    <div className="snap-start w-96 flex-shrink-0 h-screen flex items-center justify-center" >3</div>
    <div className="snap-start w-96 flex-shrink-0 h-screen flex items-center justify-center" >4</div>
</div>
```

## Staring Framer-motion

### motion

Most animations will be performed with the motion component and the animate prop.

```typescript
<motion.div  />
```

# Animation

There are multiple ways to animate in Framer Motion, scaling to the complexity of your needs.

## Simple animations

Most animations will be performed with the motion component and the animate prop.
When any value in animate changes, the component will automatically animate to the updated target.

```typescript
<motion.div animate={{ x: 100 }} />
```

## Enter animations

When a motion component is first created, it'll automatically animate to the values in animate if they're different from those defined in style or initial. You can set the initial prop to false to disable enter animations.

```typescript
<motion.div animate={{ x: 100 }} initial={false} />
```

## Exit animations

In React, when a component is removed from the tree, it's removed instantly. Framer Motion provides the AnimatePresence component to keep components in the DOM while they perform an exit animation.

```typescript
<AnimatePresence>
  {isVisible && (
    <motion.div
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      exit={{ opacity: 0 }}
    />
  )}
</AnimatePresence>
```

## Keyframes

Values in animate can also be set as a series of keyframes. This will animate through each value in sequence.
We can use the current value as the initial keyframe by passing a wildcard keyframe, null.
This way, if a keyframes animation starts while the value is currently animating, the transition will be more natural. It also reduces duplication in our code.
Each keyframe will be spaced evenly throughout the animaton. You can override this by setting the times option via transition.

```typescript
<motion.circle
  cx={500}
  animate={{ cx: [null, 100, 200] }}
  transition={{ duration: 3, times: [0, 0.2, 1] }}
/>
```

## Gesture animations

Framer Motion has shortcuts for animating to a set of values when gestures start, like hover, tap, drag, focus and inView:

```typescript
<motion.button
  initial={{ opacity: 0.6 }}
  whileHover={{
    scale: 1.2,
    transition: { duration: 1 },
  }}
  whileTap={{ scale: 0.9 }}
  whileInView={{ opacity: 1 }}
/>
```

## Variants

Setting animate as an object is useful for simple, single-component animations. But sometimes we want to create animations that propagate throughout the DOM, and orchestrate those animations in a declarative way. We can do so with variants.

- Variants are sets of pre-defined targets.
- They're passed into motion components via the variants prop.
- These variants can be referred to by label, wherever you can define an animation object.

```typescript
const variants = {
  visible: { opacity: 1 },
  hidden: { opacity: 0 },
}

<motion.div
  initial="hidden"
  animate="visible"
  variants={variants}
/>
```

## Propagation

If a motion component has children, changes in variant will flow down through the component hierarchy until a child component defines its own animate property.

```typescript
const list = {
  visible: { opacity: 1 },
  hidden: { opacity: 0 },
}

const item = {
  visible: { opacity: 1, x: 0 },
  hidden: { opacity: 0, x: -100 },
}

return (
  <motion.ul
    initial="hidden"
    animate="visible"
    variants={list}
  >
    <motion.li variants={item} />
    <motion.li variants={item} />
    <motion.li variants={item} />
  </motion.ul>
)
```

## Orchestration

By default, all these animations will start simultaneously. But by using variants, we gain access to extra transition props like when, delayChildren, and staggerChildren that can let parents orchestrate the execution of child animations.

```typescript
const list = {
  visible: {
    opacity: 1,
    transition: {
      when: "beforeChildren",
      staggerChildren: 0.3,
    },
  },
  hidden: {
    opacity: 0,
    transition: {
      when: "afterChildren",
    },
  },
}
```

## Dynamic variants

Each variant can be defined as a function that resolves when a variant is accessed. These variant functions are provided a single argument, which can be set in a component's custom prop.

```typescript
const variants = {
  visible: i => ({
    opacity: 1,
    transition: {
      delay: i * 0.3,
    },
  }),
  hidden: { opacity: 0 },
}

return items.map((item, i) => (
  <motion.li
    custom={i}
    animate="visible"
    variants={variants}
  />
))
```

## Multiple variants

Props like animate, whileHover etc can define one or more variants by passing a string or an array of strings.
If variants define the same values, variants appearing later in the array will take precedence over those earlier in the array.

```typescript
<motion.ul variants={["open", "primary"]} />
```

## Manual controls

Declarative animations are ideal for most UI interactions. But sometimes we need to orchestrate more complex sequences.

The useAnimate hook can be used to:

- Animate any HTML/SVG element
- Create complex sequences of animations
- Control animations with time, speed, play(), pause() and - other playback controls.

```typescript
const MyComponent = () => {
  const [scope, animate] = useAnimate()
  
  useEffect(() => {
    const animation = async () => {
      await animate(scope.current, { x: "100%" })
      animate("li", { opacity: 1 })
    }
    
    animation()
  }, [])

  return (
    <ul ref={scope}>
      <li />
      <li />
      <li />
    </ul>
  )
}
```

## Animate single values

It's also possible to use useAnimate to animate single values or a single MotionValue.

```typescript
const [scope, animate]= useAnimate()
const x = useMotionValue(0)

useEffect(() => {
  const controls = animate(x, 100, {
    type: "spring",
    stiffness: 2000,
    onComplete: v => {}
  })

  return controls.stop
})
```

## Animate content

We can render the current value of a MotionValue by passing it as a motion component's child.

```typescript
const count = useMotionValue(0)
const rounded = useTransform(count, latest => Math.round(latest))

useEffect(() => {
  const controls = animate(count, 100)

  return controls.stop
}, [])

return <motion.div>{rounded}</motion.div>
```

# Transition

By default, Motion will create an appropriate animation for a snappy transition based on the types of value being animated. For instance, physical properties like x or scale will be animated via a spring simulation. Whereas values like opacity or color will be animated with a tween.

However, you can define different types of animation by passing a default transition to the transition prop.

A transition defines how values animate from one state to another.

A transition defines the type of animation used when animating between two values.

It can also accept props that define which type of animation to use a Tween, Spring or Inertia.

```javascript
<motion.div
  animate={{ x: 100 }}
  transition={{ type: "spring", stiffness: 100 }}
/>
```

#### Value-specific transitions

A different set of transition settings can be defined for each specific value.

```typescript
transition={{
  ease: "linear",
  duration: 2,
  x: { duration: 1 } // specific transition for specific value (here x)

}}
```

## Orchestration of Duration

### delay : number

Delay the animation by this duration (in seconds). Defaults to 0.

### delayChildren: number

When using variants, children animations will start after this duration.

You can add the transition property to both the Frame and the variant directly. Adding it to the variant generally offers more flexibility, as it allows you to customize the delay per visual state.

```typescript
const container = {
  hidden: { opacity: 0 },
  show: {
    opacity: 1,
    transition: {
      delayChildren: 0.5
    }
  }
}

const item = {
  hidden: { opacity: 0 },
  show: { opacity: 1 }
}

return (
  <motion.ul
    variants={container}
    initial="hidden"
    animate="show"
  >
    <motion.li variants={item} />
    <motion.li variants={item} />
  </motion.ul>
)
```

### staggerChildren: number

When using variants, animations of child components can be staggered by this duration.

```typescript
const container = {
  hidden: { opacity: 0 },
  show: {
    opacity: 1,
    transition: {
      staggerChildren: 0.5
    }
  }
}

const item = {
  hidden: { opacity: 0 },
  show: { opacity: 1 }
}

return (
  <motion.ol
    variants={container}
    initial="hidden"
    animate="show"
  >
    <motion.li variants={item} />
    <motion.li variants={item} />
  </motion.ol>
)

```

### staggerDirection: number

The direction in which to stagger children.

A value of 1 staggers from the first to the last while -1 staggers from the last to the first.

```typescript
const container = {
  hidden: { opacity: 0 },
  show: {
    opacity: 1,
    transition: {
      delayChildren: 0.5,
      staggerDirection: -1
    }
  }
}

const item = {
  hidden: { opacity: 0 },
  show: { opacity: 1 }
}

return (
  <motion.ul
    variants={container}
    initial="hidden"
    animate="show"
  >
    <motion.li variants={item} size={50} />
    <motion.li variants={item} size={50} />
  </motion.ul>
)
```

### when: false | "beforeChildren" | "afterChildren" | string

Describes the relationship between the transition and its children. Set to false by default.

When using variants, the transition can be scheduled in relation to its children with either "beforeChildren" to finish this transition before starting children transitions, "afterChildren" to finish children transitions before starting this transition.

```typescript
const list = {
  hidden: {
    opacity: 0,
    transition: { when: "afterChildren" }
  }
}

const item = {
  hidden: {
    opacity: 0,
    transition: { duration: 2 }
  }
}

return (
  <motion.ul variants={list} animate="hidden">
    <motion.li variants={item} />
    <motion.li variants={item} />
  </motion.ul>
)
```

### repeat: number

The number of times to repeat the transition. Set to Infinity for perpetual repeating.
**Without setting repeatType, this will loop the animation.**

```typescript
<motion.div
  animate={{ rotate: 180 }}
  transition={{ repeat: Infinity, duration: 2 }}
/>
```

### repeatType: "loop" | "reverse" | "mirror"

How to repeat the animation. This can be either:

    loop: Repeats the animation from the start
    reverse: Alternates between forward and backwards playback
    mirror: Switches from and to alternately

```typescript
<motion.div
  animate={{ rotate: 180 }}
  transition={{
    repeat: 1,
    repeatType: "reverse",
    duration: 2
  }}
/>
```

### repeatDelay: number

When repeating an animation, repeatDelay will set the duration of the time to wait, in seconds, between each repetition.

```typescript
<motion.div
  animate={{ rotate: 180 }}
  transition={{ repeat: Infinity, repeatDelay: 1 }}
/>
```

## Tween Transition

### type: "tween"

Set type to "tween" to use a duration-based animation. If any non-orchestration transition values are set without a type property, this is used as the default animation.

```typescript
<motion.path
  animate={{ pathLength: 1 }}
  transition={{ duration: 2, type: "tween" }}
/>

```

### duration: number

The duration of the tween animation. Set to 0.3 by default, 0r 0.8 if animating a series of keyframes.

```typescript
const variants = {
  visible: {
    opacity: 1,
    transition: { duration: 2 }
  }
}

```

### ease: Easing | Easing[]

The easing function to use. Set as one of the below.

    The name of an existing easing function.
    An array of four numbers to define a cubic bezier curve.
    An easing function, that accepts and returns a value 0-1.

If the animating value is set as an array of multiple values for a keyframes animation, ease can be set as an array of easing functions to set different easings between each of those values.

```typescript
<motion.div
  animate={{ opacity: 0 }}
  transition={{ ease: [0.17, 0.67, 0.83, 0.67] }}
/>
```

These strings are the built-in named easing functions in Framer.
    "linear"
    "easeIn", "easeOut", "easeInOut"
    "circIn", "circOut", "circInOut"
    "backIn", "backOut", "backInOut"
    "anticipate"

### from: number | string

The value to animate from. By default, this is the current state of the animating value.

```typescript
<motion.div
  animate={{ rotate: 180 }}
  transition={{ from: 90, duration: 2 }}
/>
```

### times: number[]

When animating keyframes, times can be used to determine where in the animation each keyframe is reached. Each value in times is a value between 0 and 1, representing duration.

There must be the same number of times as there are keyframes. Defaults to an array of evenly-spread durations.

```typescript
<motion.div
// scales length number should be equal to times array length
  animate={{ scale: [0, 1, 0.5, 1] }}
  transition={{ times: [0, 0.1, 0.9, 1] }}
/>
```

## Spring

An animation that simulates spring physics for realistic motion.

This is the default animation for physical values like x, y, scale and rotate.

### type: "spring"

Set type to "spring" to animate using spring physics for natural movement. Type is set to "spring" by default.

### duration: number

The duration of the animation, defined in seconds. Spring animations can be a maximum of 10 seconds.

If bounce is set, this defaults to 0.8.

Note: duration and bounce will be overridden if stiffness, damping or mass are set.

### bounce: number

bounce determines the "bounciness" of a spring animation.
0 is no bounce, and 1 is extremely bouncy.
If duration is set, this defaults to 0.25.

Note: bounce and duration will be overridden if stiffness, damping or mass are set.

```typescript
<motion.div
  animate={{ x: 100 }}
  transition={{ type: "spring", bounce: 0.25 }}
/>
```

### damping: number

Strength of opposing force. If set to 0, spring will oscillate indefinitely. Set to 10 by default.

### mass: number

Mass of the moving object. Higher values will result in more lethargic movement. Set to 1 by default.

### stiffness: number

Stiffness of the spring. Higher values will create more sudden movement. Set to 100 by default.

### velocity: number

The initial velocity of the spring. By default this is the current velocity of the component.

### restSpeed: number

End animation if absolute speed (in units per second) drops below this value and delta is smaller than restDelta. Set to 0.01 by default.

### restDelta: number

End animation if distance is below this value and speed is below restSpeed. When animation ends, spring gets “snapped” to. Set to 0.01 by default.

```typescript
<motion.div
  animate={{ rotate: 180 }}
  transition={{ type: 'spring' , damping: 300,  mass: 0.5 ,  stiffness: 50, velocity: 2, restSpeed: 0.5, restDelta: 0.5 }}
/>
```

## Inertia

An animation that decelerates a value based on its initial velocity, usually used to implement inertial scrolling.

Optionally, min and max boundaries can be defined, and inertia will snap to these with a spring animation.

This animation will automatically precalculate a target value, which can be modified with the modifyTarget property.

This allows you to add snap-to-grid or similar functionality.

Inertia is also the animation used for dragTransition, and can be configured via that prop.

### type: "inertia"

Set type to animate using the inertia animation. Set to "tween" by default. This can be used for natural deceleration, like momentum scrolling.

```typescript
<motion.div
  animate={{ rotate: 180 }}
  transition={{ type: "inertia", velocity: 50 }}
/>
```

### modifyTarget(v): number

A function that receives the automatically-calculated target and returns a new one. Useful for snapping the target to a grid.

```typescript
<motion.div
  drag
  dragTransition={{
    power: 0,
    // Snap calculated target to nearest 50 pixels
    modifyTarget: target => Math.round(target / 50) * 50
  }}
/>
```

### bounceStiffness: number

If min or max is set, this affects the stiffness of the bounce spring. Higher values will create more sudden movement. Set to 500 by default.

### bounceDamping: number

If min or max is set, this affects the damping of the bounce spring. If set to 0, spring will oscillate indefinitely. Set to 10 by default.

### power: number

A higher power value equals a further target. Set to 0.8 by default.

### timeConstant: number

Adjusting the time constant will change the duration of the deceleration, thereby affecting its feel. Set to 700 by default.

### restDelta: number

End the animation if the distance to the animation target is below this value, and the absolute speed is below restSpeed. When the animation ends, the value gets snapped to the animation target. Set to 0.01 by default. Generally the default values provide smooth animation endings, only in rare cases should you need to customize these.

### min: number

Minimum constraint. If set, the value will "bump" against this value (or immediately spring to it if the animation starts as less than this value).

### max: number

Maximum constraint. If set, the value will "bump" against this value (or immediately snap to it, if the initial animation value exceeds this value).

```typescript
<motion.div
  drag
  dragTransition={{
    min: 0,
    max: 100,
    bounceStiffness: 100,
    bounceDamping: 8,
     power: 0.2,
     timeConstant: 200,
     restDelta: 10 
  }}
/>
```

## TargetAndTransition

An object that specifies values to animate to. Each value may be set either as a single value, or an array of values.

It may also option contain these properties:

    transition: Specifies transitions for all or individual values.
    transitionEnd: Specifies values to set when the animation finishes.

```typescript
const target = {
  x: "0%",
  opacity: 0,
  transition: { duration: 1 },
  transitionEnd: { display: "none" }
}
```

# Gestures

It currently has support for hover, tap, pan and drag gesture detection. Each gesture has a series of event listeners that you can attach to your motion component.
motion components provide multiple gesture animation props: whileHover, whileTap, whileFocus, whileDrag and whileInView. These can define animation targets to temporarily animate to while a gesture is active.

```typescript
<motion.button
  whileHover={{
    scale: 1.2,
    transition: { duration: 1 },
  }}
  whileTap={{ scale: 0.9 }}
/>
```

All props can be set either as a target of values to animate to, or the name of any variants defined via the variants prop. Variants will flow down through children as normal.

motion components automatically manage the interplay between these while props. So for instance, if hovering starts and stops while the tap gesture is active, the tap gesture receives priority and any properties defined on both will remain in their tapped state.

Likewise, if both gestures are defined and tapping ends, the component will know to animate either to the state defined in whileHover, or the component's original state, depending on whether tapping ends inside or outside of the component.

```typescript
<motion.button
  whileTap="tap"
  whileHover="hover"
  variants={buttonVariants}
>
  <svg>
    <motion.path variants={iconVariants} />
  </svg>
</motion.button>
```

## Propagation

Children can stop pointer events propagating to parent motion components using the Capture React props.

For instance, a child can stop drag and tap gestures and their related while animations from firing on parents by passing e.stopPropagation() to onPointerDownCapture.

```typescript
<motion.div whileTap={{ scale: 2 }}>
  <button onPointerDownCapture={e => e.stopPropagation()} />
</motion.div>
```

## A note on SVG filters

The while helper properties won't work on SVG filter components, as these elements don't have a physical presence and therefore don't receive events. To respond to gestures, you need to introduce React state to the component and attach listeners to the physical element.

```typescript
const MyComponent = () => {
  const [isHovered, setHovered] = useState(false)

  // Simplified example
  return (
    <svg>
      <image
        filter="url(#blur)"
        onMouseEnter={() => setHovered(true)}
        onMouseLeave={() => setHovered(false)}
      />
      <filter id="blur">
        <motion.div
          initial={false}
          animate={{ stdDeviation: isHovered ? 0 : 2 }}
        />
      </filter>
    </svg>
  )
}
```

## Hover 

The hover gesture detects when a pointer hovers over or leaves a component.

It differs from onMouseEnter and onMouseLeave in that hover is guaranteed to only fire as a result of actual mouse events (as opposed to browser-generated mice events emulated from touch input).

### whileHover: VariantLabels | TargetAndTransition

Properties or variant label to animate to while the hover gesture is recognised.

### onHoverStart(event, info): void

Callback function that fires when pointer starts hovering over the component.

### onHoverEnd(event, info): void

Callback function that fires when pointer stops hovering over the component.

```typescript
<motion.a
  whileHover={{ scale: 1.2 }}
  onHoverStart={e => console.log("Hover starts")}
  onHoverEnd={e => console.log("Hover ends")}
/>
```

## Focus

Typically, this is when an input receives focus by any means, and when other elements receive focus by accessible means (like via keyboard navigation).

### whileFocus: VariantLabels | TargetAndTransition

Properties or variant label to animate to while the focus gesture is recognised.

```typescript
<motion.a
  whileFocus={{ scale: 1.2 }}
  href="#"
/>
```

## Tap

The tap gesture detects when the primary pointer (like a left click or first touch point) presses down and releases on the same component.

It fires a tap event when tapping successfully completes on a component, and a tapCancel event when tapping ends outside the component.

If the tappable component is a child of a draggable component, it'll automatically cancel the tap gesture if the pointer moves further than 3 pixels during the gesture.
Elements with tap events are keyboard-accessible.

Any element with a tap prop will be able to receive focus and Enter can be used to trigger tap events on focused elements.

- Pressing Enter down will trigger onTapStart and whileTap
- Releasing Enter will trigger onTap
- If the element loses focus before Enter is released, onTapCancel will fire.

### whileTap: VariantLabels | TargetAndTransition

Properties or variant label to animate to while the component is pressed.

```typescript
<motion.div whileTap={{ scale: 0.8 }} />
```

### onTap(event, info): void

Callback when the tap gesture successfully ends on this element.

### event: MouseEvent | TouchEvent | PointerEvent

The originating pointer event

#### info: TapInfo

A TapInfo object containing x and y values for the point relative to the device or page

```typescript
function onTap(event, info) {
  console.log(info.point.x, info.point.y)
}

<motion.div onTap={onTap} />
```

### onTapStart(event, info): void

Callback when the tap gesture starts on this element.

```typescript
function onTapStart(event, info) {
  console.log(info.point.x, info.point.y)
}

<motion.div onTapStart={onTapStart} />
```

### onTapCancel(event, info): void

Callback when the tap gesture ends outside this element.

```typescript
function onTapCancel(event, info) {
  console.log(info.point.x, info.point.y)
}

<motion.div onTapCancel={onTapCancel} />
```

## Pan

The pan gesture recognises when a pointer presses down on a component and moves further than 3 pixels. The pan gesture is ended when the pointer is released.

```typescript
<motion.div onPan={(e, pointInfo) => {}} />
```

### onPan(event, info): void

Callback function that fires when the pan gesture is recognised on this element.

Note: For pan gestures to work correctly with touch input, the element needs touch scrolling to be disabled on either x/y or both axis with the touch-action CSS rule.

#### event: MouseEvent | TouchEvent | PointerEvent

The originating pointer event

info: PanInfo
A PanInfo object containing x and y values for:

- point: Relative to the device or page.
- delta: Distance moved since the last event.
- offset: Offset from the original pan event.
- velocity: Current velocity of the pointer.

```typescript
function onPan(event, info) {
  console.log(info.point.x, info.point.y)
}

<motion.div onPan={onPan} />
```

### onPanStart(event, info): void

Callback function that fires when the pan gesture begins on this element.

```typescript
function onPanStart(event, info) {
  console.log(info.point.x, info.point.y)
}

<motion.div onPanStart={onPanStart} />
```

### onPanEnd(event, info): void

Callback function that fires when the pan gesture ends on this element.

```typescript
function onPanEnd(event, info) {
  console.log(info.point.x, info.point.y)
}

<motion.div onPanEnd={onPanEnd} />
```

## Drag

The drag gesture follows the rules of the pan gesture but applies pointer movement to the x and/or y axis of the component.

```typescript
<motion.div drag />
```

### whileDrag: VariantLabels | TargetAndTransition

Properties or variant label to animate to while the drag gesture is recognised.

```typescript
<motion.div whileDrag={{ scale: 1.2 }} />
```

### drag: boolean | "x" | "y"

Enable dragging for this element. Set to false by default. Set true to drag in both directions. Set "x" or "y" to only drag in a specific direction.

```typescript
<motion.div drag="x" />
```

### dragConstraints: false | Partial<BoundingBox2D> | RefObject<Element>

Applies constraints on the permitted draggable area.

It can accept an object of optional top, left, right, and bottom values, measured in pixels. This will define a distance the named edge of the draggable component.

Alternatively, it can accept a ref to another component created with React's useRef hook. This ref should be passed both to the draggable component's dragConstraints prop, and the ref of the component you want to use as constraints.

```typescript
// In pixels
<motion.div
  drag="x"
  dragConstraints={{ left: 0, right: 300 }}
/>

// As a ref to another component
const MyComponent = () => {
  const constraintsRef = useRef(null)

  return (
     <motion.div ref={constraintsRef}>
         <motion.div drag dragConstraints={constraintsRef} />
     </motion.div>
  )
}
```

### dragSnapToOrigin: boolean

If true, the draggable element will animate back to its center/origin when released.

#### dragElastic: DragElastic

The degree of movement allowed outside constraints. 0 = no movement, 1 = full movement.

Set to 0.5 by default. Can also be set as false to disable movement.

By passing an object of top/right/bottom/left, individual values can be set per constraint. Any missing values will be set to 0.

```typescript
<motion.div
  drag
  dragConstraints={{ left: 0, right: 300 }}
  dragElastic={0.2}
/>
```

### dragMomentum: boolean

Apply momentum from the pan gesture to the component when dragging finishes. Set to true by default.

```typescript
<motion.div
  drag
  dragConstraints={{ left: 0, right: 300 }}
  dragMomentum={false}
/>
```

### dragTransition: InertiaOptions

Allows you to change dragging inertia parameters. When releasing a draggable Frame, an animation with type inertia starts. The animation is based on your dragging velocity. This property allows you to customize it.

```typescript
<motion.div
  drag
  dragTransition={{ bounceStiffness: 600, bounceDamping: 10 }}
/>
```

### dragPropagation: boolean

Allows drag gesture propagation to child components. Set to false by default.

```typescript
<motion.div drag="x" dragPropagation />
```

### dragControls: DragControls

Usually, dragging is initiated by pressing down on a component and moving it. For some use-cases, for instance clicking at an arbitrary point on a video scrubber, we might want to initiate dragging from a different component than the draggable one.

By creating a dragControls using the useDragControls hook, we can pass this into the draggable component's dragControls prop. It exposes a start method that can start dragging from pointer events on other components.

```typescript
const dragControls = useDragControls()

function startDrag(event) {
  dragControls.start(event, { snapToCursor: true })
}

return (
  <>
    <div onPointerDown={startDrag} />
    <motion.div drag="x" dragControls={dragControls} />
  </>
)
```
Note: Given that by setting dragControls you are taking control of initiating the drag gesture, it is possible to disable the draggable element as the initiator by setting dragListener={false}.

#### dragListener: boolean

Determines whether to trigger the drag gesture from event listeners. If passing dragControls, setting this to false will ensure dragging can only be initiated by the controls, rather than a pointerdown event on the draggable element.

### onDrag(event, info): void

Callback function that fires when the component is dragged

### onDragStart(event, info): void

Callback function that fires when dragging starts.

### onDragEnd(event, info): void

Callback function that fires when dragging ends.

```typescript
<motion.div
  drag
  onDragStart={
    (event, info) => console.log(info.point.x, info.point.y)
  }
/>

```

### onDirectionLock(axis): void

Callback function that fires a drag direction is determined.

```typescript
<motion.div
  drag
  dragDirectionLock
  onDirectionLock={axis => console.log(axis)}
/>
```

# Scroll animations

There are two predominant types of scroll animations, both of which can be achieved with Framer Motion.
Scroll-linked animations are when the progress of an animation is directly tied to scroll progress. Scroll-triggered animations are when a normal animation is triggered when an element enters or leaves the viewport.

## Scroll-linked animations

Scroll-linked animations are created using motion values and the useScroll hook.

```typescript
import { motion, useScroll } from "framer-motion"

function Component() {
  const { scrollYProgress } = useScroll();
  
  return (
    <motion.div style={{ scaleX: scrollYProgress }} />  
  )
}
```
## Scroll-triggered animations

Scroll-triggered animations are normal animations that start when an element enters or leaves the viewport.

The whileInView prop can be used to create scroll-triggered animations by defining a set of properties and, optionally, a transition, to animate to when the element is in view.

```typescript
<motion.div
  initial={{ opacity: 0 }}
  whileInView={{ opacity: 1 }}
/>
```

### whileInView: VariantLabels | TargetAndTransition

Properties or variant label to animate to while the element is in view.

### viewport: ViewportOptions

An object of viewport options that define how the viewport is detected.

```typescript
<motion.div
  initial="hidden"
  whileInView="visible"
  viewport={{ once: true }}
/>
```

### onViewportEnter(entry): void

Callback that triggers when the element enters the viewport. Provides the IntersectionObserverEntry with details of the intersection event, or null if the browser doesn't support IntersectionObserver.

## Viewport options

### once: boolean

If true, once the element has entered the viewport it will remain in the whileInView state. No further viewport callbacks will be triggered.

```typescript
<motion.div
  initial="hidden"
  whileInView="visible"
  viewport={{ once: true }}
/>
```

### root: RefObject<Element>

By default, the element will be considered within the viewport when it enters the window viewport.

Pass a ref to both an ancestor element and to viewport.root to use that ancestor element as the measured viewport instead.

```typescript
function Component() {
  const scrollRef = useRef(null)
  
  return (
    <div ref={scrollRef} style={{ overflow: "scroll" }}>
      <motion.div
        initial={{ opacity: 0 }}
        whileInView={{ opacity: 1 }}
        viewport={{ root: scrollRef }}
      />
    </div>
  )
}
```

### margin: string

A margin to add to the viewport when detecting whether the element has entered it.

Defaults to "0px". A single value can be used to add a margin on every side, e.g. "200px". Or, multiple values can be defined to assign a margin to each axis in the order of top/right/bottom/left, e.g. "0px -20px 0px 100px".

### amount: "some" | "all" | number

Defaults to "some", this option defines the amount of the element that has to intersect with the viewport in order for it to be considered within view.

# Layout Animation

Create layout and shared layout animations with React and Framer Motion.
CSS layouts are difficult and expensive to animate.

Animating a style like height between 100px and 500px is conceptually straightforward, but suffers from poor performance because we're triggering the browser layout systems every animation frame.

Sometimes it doesn't even make sense. What does it actually mean to animate justify-content between flex-start and flex-end?

Framer Motion can animate between any CSS layout by using performant transforms instead of the layout system.

For example, this component is animated by switching justify-content between flex-start and flex-end.

To enable Framer Motion's layout animations, we simply set the layout prop of a motion component.

```typescript
<motion.div layout />
```
Any layout change that happens as the result of a re-render will be animated. That could be any combination of:

- Reordering of a list.
- A style set on the component itself, for example a change in width or position.
- A change in the parent's layout, e.g. flexbox or grid.
- Or any other change in the component's layout.

### Scale correction

All layout animations are performed using the transform property, resulting in smooth framerates.

Animating layout using transforms can sometimes visually distort children. To correct this distortion, the first child elements of the element can also be given layout property.

Try switching this component between layouts, with and without setting layout on the pink dot:

Transforms can also distort boxShadow and borderRadius. The motion component will automatically correct this distortion on both props, as long as they're set as motion values.

If you're not animating these values, the easiest way to do this is to set them via style.

```typescript
<motion.div layout style={{ borderRadius: 20 }} />
```

### Customising layout animations

Layout animations can be customised using the transition property.

```typescript
<motion.div layout transition={{ duration: 0.3 }} />
```

If you want to set a transition specifically for only the layout animation, you can specify a specific layout transition.

```typescript
<motion.div
  layout
  animate={{ opacity: 0.5 }}
  transition={{
    opacity: { ease: "linear" },
    layout: { duration: 0.3 }
  }}
/>
```

## Animating within scroll containers

To animate layout correctly within scrollable elements, these elements must be given the layoutScroll prop.

```typescript
<motion.div
  layoutScroll
  style={{ overflow: "scroll" }}
/>
```

## Coordinating layout animations

Layout animations are triggered when a component re-renders and its layout has changed.

```typescript
function Accordion() {
  const [isOpen, setOpen] = useState(false)
  
  return (
    <motion.div
      layout
      style={{ height: isOpen ? "100px" : "500px" }}
      onClick={() => setOpen(!isOpen)}
    />
  )
}
```

But what happens when we have two or more components that don't re-render at the same time, but do affect each other's layout?

When one re-renders, the other won't be able to detect changes to its layout.

We can synchronise layout changes across multiple components by wrapping them in the LayoutGroup component.

```typescript
import { LayoutGroup } from "framer-motion"

function List() {
  return (
    <LayoutGroup>
      <Accordion />
      <Accordion />
    </LayoutGroup>  
  )
}
```

Now, when layout changes are detected in one grouped component, layout animations will happen across all of them. Without any extra re-renders.

## Shared layout animations

When a new component is added that has a layoutId prop that matches an existing component, it will automatically animate out from the old component.

```typescript
isSelected && <motion.div layoutId="underline" />
```

If the old component is still mounted when the new component enters, they will automatically crossfade from the old to the new.

# Motion components

Motion components are DOM primitives optimised for 60fps animation and gestures.

There's a motion component for every HTML and SVG element, for instance motion.div, motion.circle etc.

These work exactly like their static counterparts, but offer props that allow you to:

- Animate
- Add drag, pan, hover and tap gestures
- Respond to gestures and elements entering the viewport with animation
- Deeply animate throughout React trees via variants

And much more.

## Value types

Motion can animate:

- Numbers: 0, 10 etc.
- Strings containing numbers: "0vh", "10px" etc.
- Colors: Hex, RGB, HSLA.
- Complex strings containing multiple numbers and/or colors (ie "10px 0 #000")

When animating to a non-animatable value like "block", this value will be set instantly. By setting this value within transitionEnd, this value will be set at the end of the animation.

```typescript
<motion.div
  animate={{
    x: 0,
    backgroundColor: "#000",
    boxShadow: "10px 10px 0 rgba(0, 0, 0, 0.2)",
    position: "fixed",
    transitionEnd: {
      display: "none",
    },
  }}
/>
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
<motion.div
  animate={{ x: 100 }}
  transition={{ delay: 1 }}
/>
// or
<motion.div
  animate={{ x: 100 }}
  transition={{ ease: "easeOut", duration: 2 }}
/>
```

```javascript
const Test = () => {
  const [open, setOpen] = useState(false);

  const variants = {
    visible: (i)=>( {
      opacity: 1,
      x: 100,
      transition: { delay:i * 0.3 },
    }),
    hidden: { opacity: 0 },
  };

  const items = ["item1", "item2", "item3", "item4"];

  return (
    <div className="course">
      <motion.ul initial="hidden" animate="visible" variants={variants}>
        {items.map((item,i) => (
          <motion.li variants={variants} key={item} custom={i}>
            {item}
          </motion.li>
        ))}
      </motion.ul>
    </div>
  );
};

```

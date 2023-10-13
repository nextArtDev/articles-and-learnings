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

### animate
When any value in animate changes, the component will automatically animate to the updated target.

```typescript
<motion.div animate={{ x: 100 }} />
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

# Enter Animation

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
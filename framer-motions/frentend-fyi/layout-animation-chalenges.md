# Challenges in layout animations

## 1. Scale distortion & jumping child elements

child elements of a parent with layout animations can become _distorted during the animation_ process. They may scale weirdly or move to unexpected positions. This distortion happens because the parent element is scaling during the resize. As a result, the child elements also get scaled.
Framer Motion can apply a “counter-transform” to child elements, which inverses the scale and corrects the distortion. To enable this, _the child element must also be a motion element with the layout prop_.

```typescript
import { useState, useMemo } from "react";
import { motion } from "framer-motion";

const steps = ['start', 'larger', 'wider'];

export const App = () => {
  const [stepIndex, setStepIndex] = useState(0);
  const stepName = useMemo(() => steps[stepIndex], [stepIndex]);

  return (
    <div className="grid place-items-center min-h-dvh grid-cols-1 grid-rows-1">
      <motion.div
        layout
        onClick={() => setStepIndex(stepIndex => (++stepIndex % steps.length))}
        style={{
          width: stepName !== 'start' ? 200 : 100,
          height: stepName === 'larger' ? 200 : 100,
        }}
        className="bg-white p-6 cursor-pointer">
        // To avoid distortion apply 1.layout 2.motion to the child element
        <motion.div layout className="bg-black rounded-full w-12 h-12" />
      </motion.div>
    </div>
  );
};
```

## 2. Animating inline elements (e.g. span)

Framer Motion uses CSS transforms for most animations, but _transforms don’t work on elements with display: inline_. As a result, animations can break if applied to inline elements like span.

The solution is simple: _change the display property to something other than inline_.

By default, the following HTML elements have display: inline; :

```typescript
<a>, <b>, <br>, <cite>, <code>, <em>, <i>, <img>, <q>, <s>, <small>, <span>, <strong>, <sub>, <sup>. <time>, <u>, <var>.
```

Point: If the parent element of your inline element is a flex or grid container, your animations will work as well. This is because flex and grid layouts change the behavior of their children, effectively removing the inline constraint.

```typescript
import { useState, useMemo } from "react";
import { motion } from "framer-motion";

const steps = ['start', 'larger', 'wider'];

export const App = () => {
  const [stepIndex, setStepIndex] = useState(0);
  const stepName = useMemo(() => steps[stepIndex], [stepIndex]);

  return (
    <div className="grid place-items-center min-h-dvh grid-cols-1 grid-rows-1">
      <motion.div
        layout
        onClick={() => setStepIndex(stepIndex => (++stepIndex % steps.length))}
        style={{
          width: stepName !== 'start' ? 200 : 100,
          height: stepName === 'larger' ? 200 : 100,
        }}
        className="bg-white p-6 cursor-pointer">
        // Adding "block" fix inline behaviors
        <motion.span layout className="block bg-black rounded-full p-4 w-12 h-12">
          👀
        </motion.span>
      </motion.div>
    </div>
  );
};

```

## 3. Border radiuses

Another common issue with layout animations is weird behavior with border-radius. The radius may become exaggerated during resizing or jump abruptly to the end state.
To fix it _set the border radius in the __style__ attribute, giving Framer Motion control over its animation._
If we want to animate it, it should be in _animate_ prop, if we just want to change it, put it in _style_ prop.

```typescript
import { motion } from "framer-motion";
import { useState } from "react";

export const App = () => {
  const [isSmall, setIsSmall] = useState(true);

  return (
    <div className="grid h-dvh w-full place-items-center">
      <motion.div
        layout
        onClick={() => setIsSmall((s) => !s)}
        className="bg-white relative grid h-[200px] cursor-pointer place-items-center  "
        //if we want to animate it:
        animate={{ borderRadius:isSmall ? 20: 50 }}
        style={{
            // if we just want to change border radius we just put it in style tag instead of tailwind rounded-[50px]  
          borderRadius:50
          width: isSmall ? 200 : 400,
          height: isSmall ? 200 : 400,
        }}
      >
        <motion.span layout className="block">
          Click me 👀
        </motion.span>
      </motion.div>
    </div>
  );
};

export default App;
```

## 4. Animating text

When you increase the size of text, the thickness of the letters changes slightly, even if you’re not adjusting the font weight. The problem is, this thickness can’t be smoothly animated. On top of that, resizing text can cause words to jump to new lines, making it even harder to animate seamlessly.

One way to improve this is by adjusting the white container’s styles to ensure the text stays on a single line for both sizes.

```typescript
style={{
  width: isExpanded ? 350 : 200,
  height: isExpanded ? 200 : 100,
}}
```

it’s still not perfect because font weight can’t be animated, which is why I try to avoid animating font weight as much as possible.

One last trick that can help is _wrapping the text in a motion.span with the layout prop, often in combination with using display: flex;_ to for example center the text inside it. This gives Framer Motion more control over the animation. It’s especially useful in Shared Layout animations, which we’ll dive into in a later lesson.

```typescript
export const App = () => {
  const [isExpanded, setIsExpanded] = useState(false);

  return (
    <div className="grid place-items-center min-h-screen">
      <motion.div
        layout
        onClick={() => setIsExpanded((prev) => !prev)}
        className="bg-white p-6 cursor-pointer"
        style={{
          width: isExpanded ? 300 : 150,
          height: isExpanded ? 200 : 100,
        }}
      >
        <motion.h1
          layout
          style={{
            fontSize: isExpanded ? "2rem" : "1rem",
            fontWeight: isExpanded ? "bold" : "normal",
          }}
        >
          Click to toggle size
        </motion.h1>
      </motion.div>
    </div>
  );
};
```

## 5. Restricting layout animations

Sometimes, despite your best efforts, elements may still behave oddly during animations. As a last resort, you can restrict layout animations to specific properties.

```typescript

layout
// In most cases you set the layout property to true. Framer Motion will then animate both the size and position of the element.

layout="position"

// Setting this property to "position" will only animate the position of the element, while the size will change instantly.

layout="size"
// Setting this property to "size" will only animate the size of the element, while the position will change instantly.

layout="preserve-aspect"
// Setting this property to "preserve-aspect" will ensure the aspect ratio of the element is maintained during the animation. It will still animate the size and position of the element if necessary.
```
 
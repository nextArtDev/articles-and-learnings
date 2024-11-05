# useScroll with CSS Sticky position

## CSS Sticky position

It’s a mix __between position: relative and position: fixed__, and it’s perfect for creating sticky headers, footers, or sidebars. It allows you to ‘stick’ an element to the page for the full height of it’s parent container.
This means _you can create animations where the animated element, seems to ‘stick’ to the page as the user scrolls_.

### How position sticky works

You can make an element sticky by setting its position to sticky. Next you also need to __set at minimum one of the the top, right, bottom, and left properties to adjust the position__ of the sticky element.

```typescript
.sticky {
  position: sticky;
  top: 0;
}

.sticky-bottom {
  position: sticky;
  bottom: 40px;
}

```

```typescript
 <div className="border-white-opaque max-h-[400px] overflow-auto border">
    <div className="min-h-[100px] text-center text-2xl">
      Scroll this example 👇
    </div>
    <div className="bg-white-opaque grid h-[800px] grid-cols-2 items-start gap-3 rounded-3xl p-3">
      <div className="sticky top-0 rounded-2xl bg-black p-3">Sticky div</div>
      <div className="rounded-2xl bg-black p-3">Not so sticky div</div>
    </div>
    <div className="h-[500px]">
      Some content after, that is taller than our scrollable section.
    </div>
  </div>

```

While scrolling this example, every element simply moves up like usual. Except the sticky div on the left side. For the sticky element there are a few things you should note:

- The element will only become sticky when it reaches the top of the scrollable element (in this case a div, often your window. I’m also gonna say window from now, to make it easier to read.).
- Until that point, the element will behave like a normal element. You notice it moves up too.
- The moment the container element reaches the top of the window, the sticky element will NOT immediately stick.
- Note that the element still moves up, until the element itself reaches the top of the window. In this case there is extra room for the element to move up because the parent has padding. But it could very well be because there is another child in the parent element, or the sticky element itself also has some margin, giving it room to scroll before it sticks.
- Once the sticky element reaches the top of the window, it will stick there until the parent element reaches the bottom of it’s parent. From that moment on, it will again move up with the parent element.

We now know the sticky element will be in view for the full height of the wrapper element. This means we can use the scrollYProgress of the wrapper element to animate the sticky element.

In the example below we take the scrollYProgress and transform it into a translateX for the sticky element, __moving it from left to right while the element is sticky on the page__.

```typescript
const wrapperRef = useRef<HTMLDivElement>(null);
const { scrollYProgress } = useScroll({
  target: wrapperRef,
  offset: ["start start", "end start"],
});

const x = useTransform(scrollYProgress, [0, 1], ["0%", "100%"]);
```

```typescript

```
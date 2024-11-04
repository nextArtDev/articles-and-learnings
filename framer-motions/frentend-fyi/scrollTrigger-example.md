# ScrollTrigger vs scroll-driven animation

## a bento grid animation

One is using _useInView_, second grid is using _useScroll_

```typescript
import { motion, useInView, useScroll, useTransform } from "framer-motion";
import { useRef, useState } from "react";

export const App = () => {
  return (
    <>
      <div className="h-screen text-white grid place-items-center">Gotta scroll down 👇</div>
      <div className="flex w-full flex-col items-center px-8">
        <GridOne />

        <div className="text-white py-8">some space..</div>

        <GridTwo />

        <div className="text-white h-screen pt-24">some 👾👾👾👾</div>
      </div>
    </>
  );
};

type CardProps = {
  title: string;
  subtitle: string;
  imageUrl: string;
}

const Card = ({title, subtitle, imageUrl}: CardProps) => (
    <div className="h-full bg-white rounded-2xl overflow-clip">
    <div className="p-4 mb-5 text-center">
      <h2 className="text-3xl">{title}</h2>
      <p>{subtitle}</p>
    </div>
    <img className="object-cover w-full h-full" src={imageUrl} alt="" />
  </div>
);

const cardInViewVariants = {
  inView: {
    scale: 1,
    opacity: 1
  },
  outOfView: {
    scale: 0.8,
    opacity: 0
  }
}

const GridOne = () => {
  const ref = useRef(null);
  const isInView = useInView(ref, {amount: 0.3});

  return (
   <motion.div
     ref={ref}
     animate={isInView ? "inView" : "outOfView"}
     variants={{
       inView: {
         transition: {
           staggerChildren: 0.1
         }
       },
       outOfView: {
         staggerChildren: 0.1,
         staggerDirection: -1
       }
     }}
     initial="outOfView"
     className="relative grid w-full max-w-[800px] sm:h-[500px] grid-cols-1 sm:grid-cols-2 sm:grid-rows-2 gap-4">
      <motion.div variants={cardInViewVariants} className="row-span-2">
        <Card title="TryPhone 29" subtitle="Call me maybe" imageUrl="/img/courses/framer-motion/product-1.webp" />
      </motion.div>

      <motion.div variants={cardInViewVariants}>
        <Card title="BearShots Pro" subtitle="Ever heard better?" imageUrl="/img/courses/framer-motion/product-2.webp" />
      </motion.div>
      <motion.div variants={cardInViewVariants}>
        <Card title="Trample Watch" subtitle="Right on time" imageUrl="/img/courses/framer-motion/product-3.webp" />
      </motion.div>
    </motion.div>
  );
}

const FadeIn = ({children, className}) => {
  const ref = useRef(null);
  const { scrollYProgress } = useScroll({
    target: ref,
    offset: ["start end", "end start"]
  });
  const opacity = useTransform(scrollYProgress, [0, 0.15, 0.80, 1], [0, 1, 1, 0]);


  return (
    <motion.div style={{opacity}} ref={ref} className={className}>{children}</motion.div>
  )
}

const GridTwo = () => {
  return (
   <div className="relative grid w-full max-w-[800px] sm:h-[500px] grid-cols-1 sm:grid-cols-2 sm:grid-rows-2 gap-4">
      <FadeIn className="row-span-2">
        <Card title="TryPhone 29" subtitle="Call me maybe" imageUrl="/img/courses/framer-motion/product-1.webp" />
      </FadeIn>

      <FadeIn>
        <Card title="BearShots Pro" subtitle="Ever heard better?" imageUrl="/img/courses/framer-motion/product-2.webp" />
      </FadeIn>
      <FadeIn>
        <Card title="Trample Watch" subtitle="Right on time" imageUrl="/img/courses/framer-motion/product-3.webp" />
      </FadeIn>
    </div>
  );
}

export default App;
```
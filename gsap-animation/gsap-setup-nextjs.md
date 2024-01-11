# (https://blog.olivierlarose.com/tutorials/parallax-scroll)

## Initializing the project

- npm i gsap
- npm i @studio-freight/lenis

## setting up lenis

At the top level of elements we setting up lenis

```typescript
//page.tsx

import Lenis from '@studio-freight/lenis'

export default function Home() {
useEffect(() => {
    const lenis = new Lenis()

    function raf(time) {
      lenis.raf(time)
      requestAnimationFrame(raf)
    }

    requestAnimationFrame(raf)
  }, [])

  return (
    <main className={styles.main}>
      <GSAP />
    </main>
  )
  }
```

## Using useLayoutEffect and gsap.context

- Here I'm using the _useLayoutEffect_ because it is executed before the DOM is painted, in most cases that's what you want when creating a GSAP animation to avoid flashes.
- I'm also using the _gsap.context_ to collect all animations in one place that I can then kill in the return function of the useLayoutEffect by using _context.revert()_.

```typescript
import gsap from 'gsap';

import { ScrollTrigger } from 'gsap/ScrollTrigger';

// If we want to use scrollTrigger
gsap.registerPlugin(ScrollTrigger)

// All the coded inside useLayoutEffect is trigger before the dom is run, tht way its gonna avoid creating flashes which may be created by bunch of ref's we create to create animate on top of them.
useLayoutEffect(() => {

    // we create context to declare all of animations inside of it and revert it when the component unmounts
    const context = gsap.context(() => {
      const tl = gsap
        .timeline({
          scrollTrigger: {
            //...
          },
        })
        .to(  )
      lettersRef.current.forEach((letter, i) => {
        tl.to(
         //...
         
        )
      })
    })
    // reverting animation
    return () => context.revert()
  }, [])
```

## example for assigning ref to elements we want to animate

```typescript
'use client'
import { useLayoutEffect, useRef } from 'react'
import styles from '../page.module.scss'
import gsap from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'
import Picture1 from '../../../public//parallax-images/1.jpg'
import Picture2 from '../../../public//parallax-images/2.jpg'
import Picture3 from '../../../public//parallax-images/3.jpg'
import Image from 'next/image'

gsap.registerPlugin(ScrollTrigger)
const word = 'with gsap'

export default function GSAP() {
  const container = useRef(null)
  const images = [Picture1, Picture2, Picture3]
  const lettersRef = useRef([])
  const imagesRef = useRef([])
  const title1 = useRef(null)
  useLayoutEffect(() => {
    const context = gsap.context(() => {
      const tl = gsap
        .timeline({
          scrollTrigger: {
            trigger: container.current,
            start: 'top bottom',
            end: 'bottom top',
            scrub: true,
          },
        })
        .to(title1.current, { y: -50 }, 0)
        .to(imagesRef.current[1], { y: -150 }, 0)
        .to(imagesRef.current[2], { y: -255 }, 0)
      lettersRef.current.forEach((letter, i) => {
        tl.to(
          letter,
          {
            top: Math.floor(Math.random() * 50) - 50,
            // top: Math.floor(Math.random() * -75) - 25,
          },
          0
        )
      })
    })
    return () => context.revert()
  }, [])

  return (
    <div ref={container} className={styles.container}>
      <div className={styles.body}>
        <h1 ref={title1}>Parallax</h1>
        <h1>Scroll</h1>
        <div className={styles.word}>
          <p>
            {word.split('').map((letter, i) => {
              return (
                <span key={`l_${i}`} ref={(el) => (lettersRef.current[i] = el)}>
                  {letter}
                </span>
              )
            })}
          </p>
        </div>
      </div>
      <div className={styles.images}>
        {images.map((image, i) => {
          return (
            <div
              key={`i_${i}`}
              ref={(el) => (imagesRef.current[i] = el)}
              className={styles.imageContainer}
            >
              <Image src={image} placeholder="blur" alt="image" fill />
            </div>
          )
        })}
      </div>
    </div>
  )
}
```
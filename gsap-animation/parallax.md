# Parallax with GSAP

https://blog.olivierlarose.com/tutorials/parallax-scroll

The first important thing with GSAP is to add a timeline and inside of it create a ScrollTrigger. There are multiple ways of doing this, but I find _using a timeline_ is the cleanest way of doing it. That way you have a single ScrollTrigger instance for multiple animations.

 By using _timeline_ we can _add bunch of animations to the tl or timeline and all of them would be linked to the same scroll trigger_ and by adding 0 to each timeline as a second parameter, they will all happened together

```typescript
const tl = gsap.timeline({
    scrollTrigger: {
        // What we want to track inside the window, a container
        trigger: container.current,
        // Starting point of the tracking
        // top: of the containers, we want to try element when it appears on the screen
        //bottom: of the viewport, when the top of element is the bottom of the viewport  
        start: "top bottom", // the intersection of the top of the container and the bottom of the window

        // End point of the tracking, when we want to stop tracking of the elements 
        // the end of the container and the start of the window
        end: "bottom top",
        // All the animations that we defined inside the tl will be linked to the scrollbar
        scrub: true,
    },
})
```

Scrub: true is used to link the animations directly to the scrollbar


```typescript
tl.to(title1.current, {y: -50}, 0)

//or with multiple refs
lettersRef.current.forEach((letter, i) => {
    tl.to(letter, {
        top: Math.floor(Math.random() * -75) - 25,
    }, 0)
})
```

The 0 is added as a parameter to specify that the animations inside the timeline should happen at the same time.

## Parallax example

```typescript
'use client';
import { useLayoutEffect, useRef } from "react";
import styles from '../../app/page.module.scss';
import gsap from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';
import Picture1 from '../../../public/medias/1.jpg';
import Picture2 from '../../../public/medias/2.jpg';
import Picture3 from '../../../public/medias/3.jpg';
import Image from "next/image";
gsap.registerPlugin(ScrollTrigger)

const word = "with gsap";
const images = [Picture1, Picture2, Picture3];

export default function Index() {
    const container = useRef(null);
    const title1 = useRef(null);
    const lettersRef = useRef([])
    const imagesRef = useRef([])

    useLayoutEffect( () => {
        const context = gsap.context( () => {
            const tl = gsap.timeline({
                scrollTrigger: {
                    trigger: container.current,
                    start: "top bottom",
                    end: "bottom top",
                    scrub: true,
                },
            })
            .to(title1.current, {y: -50}, 0)
            .to(imagesRef.current[1], {y: -150 ,}, 0)
            .to(imagesRef.current[2], {y: -255}, 0)
            lettersRef.current.forEach((letter, i) => {
                tl.to(letter, {
                    // because its span, y value doesn't effect on that; we use top
                    // Generating a number between -25 and -75
                    top: Math.floor(Math.random() * -75) - 25,
                }, 0)
            })
            
        })
        return () => context.revert();
    }, [])

    return (
        <div ref={container} className={styles.container}>
            <div className={styles.body}>
                <h1 ref={title1}>Parallax</h1>
                <h1>Scroll</h1>
                <div className={styles.word}>
                    <p>
                        {
                            word.split("").map((letter, i) => {
                                return <span key={`l_${i}`} ref={el => lettersRef.current[i] = el}>{letter}</span>
                            })
                        }
                    </p>
                </div>
            </div>
            <div className={styles.images}>
                {
                    images.map( (image, i) => {
                        return <div key={`i_${i}`} ref={el => imagesRef.current[i] = el} className={styles.imageContainer}>
                            <Image 
                                src={image}
                                placeholder="blur"
                                alt="image"
                                fill
                            />
                        </div>
                    })
                }
            </div>
        </div>
    )
}
```
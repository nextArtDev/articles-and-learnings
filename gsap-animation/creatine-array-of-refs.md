## How to create array of refs

```typescript
const imagesRef = useRef<(HTMLDivElement | null)[]>([])
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
        .to(imagesRef.current[1], { y: 150, x: '-200%' }, 0)
        .to(imagesRef.current[2], { y: 255, x: '300%' }, 0)
        .to(imagesRef.current[0], { zoom: 1.5 }, 0)
      lettersRef.current.forEach((letter, i) => {
        tl.to(
          letter,
          {
            // because its span, y value doesn't effect on that; we use top
            top: Math.floor(Math.random() * 50) - 50,
          },
          0
        )
      })
    })
    return () => context.revert()
  }, [])

  return (
    <div ref={container} className={` ${styles.container}`}>
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
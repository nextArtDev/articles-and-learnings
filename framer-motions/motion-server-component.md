# to use motion on server components

```typescript
import { motion } from 'framer-motion'

export const MotionH1 = motion.h1
export const MotionDiv = motion.div
export const MotionSpan = motion.span


// in server page
<MotionH1 style={{ y: sm }}>Parallax</MotionH1>
```
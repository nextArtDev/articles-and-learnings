## postId_userId relation

When we have a unique relation between two one-to-many relations, we can create a __postId_userId__

```typescript
// Prisma model 
model Like {
  id        String   @id @default(cuid())
 
  post      Post     @relation(fields: [postId], references: [id], onDelete: Cascade)

  postId    String
  userId    String   @map("user_id")

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@unique([postId, userId])
  @@index([userId])
}

// server action

  const like = await prisma.like.findUnique({
    where: {
        // we can it because of unique relation between them @@unique([postId, userId])
      postId_userId: {
        postId,
        userId,
      },
    },
  })
//...
  if (like) {
    try {
      await prisma.like.delete({
        where: {
          postId_userId: {
            postId,
            userId,
          },
        },
      })

```
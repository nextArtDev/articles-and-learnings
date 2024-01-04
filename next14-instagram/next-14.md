# Next 14 

## Overwriting layouts

Each layout file which we add, will overwrite the root layout

## useFormStatus and pending state

### Doc

You can use the React useFormStatus
hook to show a pending state while the form is being submitted.

- useFormStatus returns the status for a specific <form>, so it must be defined as a child of the <form> element.
- useFormStatus is a React hook and therefore must be used in a Client Component.

```typescript
'use client'
 
import { useFormStatus } from 'react-dom'
 
export function SubmitButton() {
  const { pending } = useFormStatus()
 
  return (
    <button type="submit" aria-disabled={pending}>
      Add
    </button>
  )
}
```

<SubmitButton /> can then be nested in any form:

```typescript
import { SubmitButton } from '@/app/submit-button'
import { createItem } from '@/app/actions'
 
// Server Component
export default async function Home() {
  return (
    <form action={createItem}>
      <input type="text" name="field-name" />
      <SubmitButton />
    </form>
  )
}
```

## onDelete in Prisma

_onDelete: Cascade_ means When we delete user we want to delete every post that created by that user

```TS
model Post {
 //...
  user      User        @relation(fields: [userId], references: [id], onDelete: Cascade)
  userId    String      @map("user_id")

  @@index([userId])
}
```

## Best Practices to fetch data from db

Its recommended to create a _lib/data.ts_ file and do all the database operations there.

## noStore()

It equals to _{cache:'no-cache'}_ and its experimental yet.

## Using a text as a submit form button

We hide an input and just use the value of that to pass the payload. for example here we hide input which pass _post.id_ to the _formData_ and just submit button does the deleting action, which its width is full.

```typescript
 <form
    action={async (formData) => {
      await axios.delete('/api/s3-upload', {
        data: { id: post.id },
      })
      const del = await deletePost(formData)
      toast(del?.message)
      // console.log(formData.get('id'))
    }}
     className="postOption"
  >
  // hiding input and just passing post.id from that 
     <input type="hidden" name="id" value={post.id} />

     <SubmitButton className="text-red-500 font-bold disabled:cursor-not-allowed w-full p-3">
       حذف پست
     </SubmitButton>
  </form>

function SubmitButton({ children, ...props }: Props) {
  const { pending } = useFormStatus()

  return (
    <button type="submit" disabled={pending} {...props}>
      {children}
    </button>
  )
}

```

## formData

Its next js specific and it will pass all the form fields.
we can access to formData content by using _formData.get('formFieldName')_
formData Type is _FormData_

```typescript

<form
    action={async (formData) => {
      await deletePost(formData)
    }}   
  >
     <input type="hidden" name="id" value={post.id} />
  </form>


  //server side
  export async function deletePost(formData: FormData) {

  const { id } = DeletePost.parse({
    id: formData.get('id'),
  })

```

## parse and safeParse of zod

safeParse returns back just success and error but parse returns back content of data.

```typescript
// 'use server'

// Here we can destructure id from parse method, but in safeParse we just get back success state
  const { id } = DeletePost.parse({
    id: formData.get('id'),
  })
```

## Optimistic update

### predicator

first we have to know if the like is for the first time or user wants to remove the like, for that we define a predict function:

```typescript
function LikeButton({
  post,
  userId,
}: {
  post: PostWithExtras
  userId?: string
}) {
  const predicate = (like: Like) =>
    like.userId === userId && like.postId === post.id 

```

### optimistic like

we get _useOptimistic_ that its first element of array is _optimisticLike_ and the second one is inside async function for server action server actin, here: _addOptimisticLike_ server action. the first argument of useOptimistic is a initial likes, and the second one is getting the current state and new ones, and returns an array of current state and new likes.
**Its recommended to use _finally_ for revalidating path in server side**

```typescript
 const predicate = (like: Like) =>
    like.userId === userId && like.postId === post.id

  const [optimisticLikes, addOptimisticLike] = useOptimistic<Like[]>(
    // the first argument is our existing like
    post.likes,
    // first state is an existing state, which is an array of likes, and the second one is the new like
    // @ts-ignore
    (state: Like[], newLike: Like) =>
      // here we check if the like already exists, if it does, we remove it, if it doesn't, we add it
      state.some(predicate)
        ? state.filter((like) => like.userId !== userId)
        : [...state, newLike]
  )

 <form
        action={async (formData: FormData) => {
          const postId = formData.get('postId')
          // receives all nesseccary fields which prisma like model needs
          addOptimisticLike({ postId, userId })

          await likePost(postId)
        }}
      >

      //...

      {optimisticLikes.length > 0 && (
        <p className="text-sm font-bold dark:text-white">
          {optimisticLikes.length}{' '}
          {/* {optimisticLikes.length === 1 ? 'like' : 'likes'} */}
          لایک
        </p>
      )}


      // server side
export async function likePost(value: FormDataEntryValue | null) {
  // const userId = await getUserId()
  const user = await getCurrentUser()
  if (!user) return
  const userId = user.id

  const validatedFields = LikeSchema.safeParse({ postId: value })

  if (!validatedFields.success) {
    return {
      errors: validatedFields.error.flatten().fieldErrors,
      message: 'خطا، لایک ناموفق!',
    }
  }

  const { postId } = validatedFields.data

  const post = await prisma.post.findUnique({
    where: {
      id: postId,
    },
  })

  if (!post) {
    throw new Error('پست پیدا نشد!')
  }

  const like = await prisma.like.findUnique({
    where: {
      postId_userId: {
        postId,
        userId,
      },
    },
  })

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
      revalidatePath('/social')
      return { message: 'لایک برداشته شد.' }
    } catch (error) {
      return { message: 'خطا در شبکه!' }
    }
  }

  try {
    await prisma.like.create({
      data: {
        postId,
        userId,
      },
    })
    return { message: 'پست لایک شد.' }
  } catch (error) {
    return { message: 'خطای شبکه، لطفا دوباره امتحان کنید!' }
  } finally {
    revalidatePath('/social')
  }
}

```

## Link scroll={false}

When we make a Link scroll to false, we dont want to scroll to the top of the page.

```typescript
 <Link
          scroll={false}
          href={`/social/p/${postId}`} 
           >
          مشاهده همه {commentsCount} کامنت
 </Link>
 
```

## useTransition

DOC: useTransition is a React Hook that lets you update the state without blocking the UI.

- The isPending flag that tells you whether there is a pending transition.
- The startTransition function that lets you mark a state update as a transition.

If some state update causes a component to suspend, that state update should be wrapped in a transition.

@param callback — A synchronous function which causes state updates that can be deferred.

Here we use useTransition to use isPEnding for disabling too.

```typescript
const [isPending, startTransition] = useTransition()

 <form
  onSubmit={form.handleSubmit(async (values) => {
    // copy to immediately reset the form
    const valuesCopy = { ...values }
    form.reset()

    // Without startTransition we get an error 
    startTransition(() => {
      addOptimisticComment(valuesCopy.body)
    })
    await createComment(valuesCopy)
  })}
  >
```

## Intercepting routes

Intercepting routes can be defined with the (..) convention, which is similar to relative path convention ../ but for __segments__ (It means path, which in that parallel routes or any kind of folder structures which do not count routes are not included).
_Note that the (..) convention is based on route segments, not the file-system._

- (.) to match segments on the same level
- (..) to match segments one level above
- (..)(..) to match segments two levels above
- (...) to match segments from the root app directory

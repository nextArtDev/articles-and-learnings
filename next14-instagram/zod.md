# Zod

## How to create schema from one schema

we can create diferent schema's from an special one by using methods like _omit_ and _pick_

```typescript
export const PostSchema = z.object({
  id: z.string(),
  // fileUrls: z.string().url().array(),
  fileUrls: z
    .custom<FileList>(
      (val) => val instanceof FileList,
      'قسمت عکس نمی‌تواند خالی باشد'
    )
    .refine((files) => files.length > 0, `قسمت عکس نمی‌تواند خالی باشد`)
    .refine((files) => files.length <= 5, `حداکثر 5 عکس مجاز است.`)
    .refine(
      (files) => Array.from(files).every((file) => file.size <= MAX_IMAGE_SIZE),
      `هر فایل باید کمتر از 5 مگابایت باشد`
    )
    .refine(
      (files) =>
        Array.from(files).every((file) =>
          ALLOWED_IMAGE_TYPES.includes(file.type)
        ),
      'تنها این فرمتها مجاز است: .jpg, .jpeg, .png and .webp'
    ),
  caption: z.string().optional(),
})

// Using one schema to crete two other schema
export const CreatePost = PostSchema.omit({ id: true })
export const UpdatePost = PostSchema.omit({ fileUrls: true })
export const DeletePost = PostSchema.pick({ id: true })
```
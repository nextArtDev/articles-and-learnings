# Tailwind Points

## tailwind costume class

for general styling we set a _@layer base_ layer and for components costume classes we use _@layer components_


```CSS
@layer base {
  * {
    @apply border-border;
  }
  body {
    @apply bg-background text-foreground antialiased;
  }
}

@layer components {
  .navLink {
    @apply md:!justify-start gap-x-4 md:!my-1.5 !px-3 w-full;
  }

  .menuItem {
    @apply dark:hover:bg-[#3C3C3C] !cursor-pointer flex !justify-end items-center gap-x-4 !px-4 !py-3.5 !m-1.5 !rounded-lg font-medium;
  }
```
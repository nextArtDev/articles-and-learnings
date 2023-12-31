# Shadcn

## Custom Dropdown

we dont want the default behavior of dropdown menu, because we want to select a field then change the theme from there, but dropdown menu closes the menu after first selection.
we use an state for opening and close it:_const [open, setOpen] = useState(false)_ and by default we close it. we just open it when we3 close the button.

```typescript

// controlling the DropdownMenu by state
const [open, setOpen] = useState(false)
 <DropdownMenu open={open}>
      <DropdownMenuTrigger asChild>
        <Button
        // toggle it by click on trigger component 
          onClick={() => setOpen(!open)}
          variant={'ghost'}
          size={'lg'}
          className="md:w-full !justify-start gap-x-2 !px-3"
        >
          <Menu />
          <div className="hidden lg:block">بیشتر</div>
        </Button>
      </DropdownMenuTrigger>
```

 it breaks the default that we can close it by clicking outside of that. to fix that we use useEffect or just useClickAway hook

 ```typescript
   const ref = useClickAway<HTMLDivElement>(() => {
    setOpen(false)
  })
 ```

or:

 ```typescript
 useEffect(() => {
    // Close the dropdown when the user clicks outside (Stack overflow!)
    function handleOutsideClick(event: MouseEvent) {
      if (!event.target) return
      if (ref.current && !ref.current.contains(event.target as Node)) {
        setShowModeToggle(false)
        setOpen(false)
      }
    }

    document.addEventListener('mousedown', handleOutsideClick)

    return () => {
      document.removeEventListener('mousedown', handleOutsideClick)
    }
  }, [ref])
 ```

now when we click on switch toggle, we should store it in an state to toggle between _dropDownContent_ if _showModeToggle_ is true we should show theme change content and when its false we want to show ordinary content.
when the open is false, we want to make dropDownContent to be hidden.

we wrap our switch with _Label_ and using same htmlFor with _Switch_ to toggle the Check value by using the label

### asChild

we add as child to use our component inside shadcn elements.

```typescript
'use client'
 
function MoreDropdown() {
  const [showModeToggle, setShowModeToggle] = useState(false)
  const [open, setOpen] = useState(false)
  const ref = useRef<HTMLDivElement>(null)

  const { theme, setTheme } = useTheme()

  useEffect(() => {
    // Close the dropdown when the user clicks outside
    function handleOutsideClick(event: MouseEvent) {
      if (!event.target) return
      if (ref.current && !ref.current.contains(event.target as Node)) {
        setShowModeToggle(false)
        setOpen(false)
      }
    }

    document.addEventListener('mousedown', handleOutsideClick)

    return () => {
      document.removeEventListener('mousedown', handleOutsideClick)
    }
  }, [ref])

  return (
    <DropdownMenu open={open}>
      <DropdownMenuTrigger asChild>
        <Button
          onClick={() => setOpen(!open)}
          variant={'ghost'}
          size={'lg'}
          className="md:w-full !justify-start gap-x-2 !px-3"
        >
          <Menu />
          <div className="hidden lg:block">بیشتر</div>
        </Button>
      </DropdownMenuTrigger>

      <DropdownMenuContent
        ref={ref}
        className={cn(
          'dark:bg-neutral-800 w-64 !rounded-xl !p-0 transition-opacity',
          'opacity-0':!open
        )}
        align="end"
        alignOffset={20}
      >
        {!showModeToggle && (
          <>
            <DropdownMenuItem className="menuItem">
              <p>تنظیمات</p>
              <Settings size={20} />
            </DropdownMenuItem>
            <DropdownMenuItem className="menuItem">
              <p>فعالیتها</p>
              <Activity size={20} />
            </DropdownMenuItem>
            <DropdownMenuItem className="menuItem">
              <p>ذخیره</p>
              <Bookmark size={20} />
            </DropdownMenuItem>

            <DropdownMenuItem
              className="menuItem"
              onClick={() => setShowModeToggle(true)}
            >
              <p>تغییر وضعیت</p>
              <Moon size={20} />
            </DropdownMenuItem>

            <DropdownMenuItem className="menuItem" onClick={() => signOut()}>
              <p>خروج</p>
              <LogOut size={20} />
            </DropdownMenuItem>
          </>
        )}

        {showModeToggle && (
          <>
            <div className="flex  items-center border-b border-gray-200 dark:border-neutral-700 py-3.5 px-2.5">
              {theme === 'dark' ? (
                <Moon size={20} className="mr-auto" />
              ) : (
                <Sun size={20} className="mr-auto" />
              )}
              <p className="font-bold mr-6">تغییر وضعیت</p>
              <ChevronRight
                size={18}
                onClick={() => setShowModeToggle(false)}
                className="cursor-pointer"
              />
            </div>
// by using Label anywhere we click it toggles the mode
            <Label htmlFor="dark-mode" className="menuItem">
              <DropdownMenuItem className="mr-auto !p-0">
                <Switch
                  id="dark-mode"
                  className="mr-auto"
                  checked={theme === 'dark'}
                  onCheckedChange={(checked) => {
                    setTheme(checked ? 'dark' : 'light')
                  }}
                />
              </DropdownMenuItem>
              <span>{theme === 'dark' ? 'حالت روز' : 'حالت شب'}</span>
            </Label>
          </>
        )}
      </DropdownMenuContent>
    </DropdownMenu>
  )
}

export default MoreDropdown

```

## AvatarProps from @radix-ui/react-avatar'

If we want to extend our shadcn Avatar component to accept all the useful props of that, we can use _Partial<AvatarProps>_

```typescript
import { Avatar } from '@/components/ui/avatar'
import type { AvatarProps } from '@radix-ui/react-avatar'
 

type Props = Partial<AvatarProps> & {
  user?: any
  imgUrl?: string
  name?: string
}

function UserAvatar({ user, imgUrl, name, ...avatarProps }: Props) { ...// }
```

## Create dialog instead of a page

4:50:00

- first we get current page pathname and compare it with the route.
- then we set _open_ prop of _Dialog_ to this _boolean result_
- if user _closed the dialog_ we should set _open_ to _false_ and get back to the callback(?) page by using _router.back()_

```typescript
// getting current path
  const pathname = usePathname()
  // compare it with create page path
  const isCreatePage = pathname === '/social/create'
  // router to route back when dialog is closed
  const router = useRouter()

  return ( 
      <Dialog
      // open Dialog when we're in this page 
        open={isCreatePage}
        // close dialog and go back if close it
        onOpenChange={(open) => !open && router.back()}
      >
```

## closing the Dialog Modal after creating post

- first we define a _closeRef_ for button _const closeRef = useRef<ElementRef<'button'>>(null)_ 
- then we assign it to _DialogClose_ by _<DialogClose ref={closeRef} asChild>_ 
- now we close it by _closeRef.current?.click()_ after server action finally: _closeRef.current?.click()_

```typescript
const closeRef = useRef<ElementRef<'button'>>(null)

     <form
      onSubmit={form.handleSubmit(async (values) => {
          } catch (error) {
          return toast.error('مشکلی پیش آمده')
        } finally {
          closeRef.current?.click()
        }
        //...
      <DialogClose ref={closeRef} asChild>
        <Button
          className=""
                  disabled={form.formState.isSubmitting}
        >
 
              </DialogClose>
```

## Hover Card

Is using for showing card when hover, for example a summary of a profile when we hover on a usr name:

```typescript
<HoverCard>
// What triggers the card
   <HoverCardTrigger asChild>
     <Link
       className="font-semibold text-sm"
       href={`/social/${postUsername}`}
     >
       {postUsername}
     </Link>
   </HoverCardTrigger>

   // content that would be shown when hover
   <HoverCardContent>
     <div className="flex items-center gap-x-2">
       <UserAvatar
         user={post.user}
         imgUrl={post.user.image[0].url}
         name={post.user.name}
         className="h-14 w-14"
       />
       <div>
         <p className="font-bold">{postUsername}</p>
         <p className="py-2 text-sm font-medium dark:text-neutral-400">
           {post.user.role === 'ADMIN' ? 'ادمین' : 'کاربر'}
         </p>
       </div>
     </div>
   </HoverCardContent>
 </HoverCard>
```
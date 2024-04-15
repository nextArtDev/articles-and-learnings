# to hide links on mobile and its activation

To hide:
first put _hideOnMobile_ prop on links
second hide link by tailwind an cn function

To active:
we use nextjs _usePathname_ to find if its current path or not, if its active, change the variant of the button

```typescript
export const links = [
  { name: 'خانه', href: '/social', icon: Home },
  {
    name: 'جست‌وجو',
    href: '/social/search',
    icon: Search,
    hideOnMobile: true,
  },
  { name: 'کاوش', href: '/social/explore', icon: Compass, hideOnMobile: true },
...
]

 {links.map((link) => {
        const { icon: Icon } = link
        const isActive = pathname === link.href

        return (
          <TooltipText text={link.name} key={link.name}>
            <Link
              key={link.name}
              href={link.href}
              className={buttonVariants({
                variant: isActive ? 'secondary' : 'ghost',
                className: cn('navLink  ', {
                    // If link.hideOnMobile is true, the hidden class will be apply 
                  'hidden md:flex': link.hideOnMobile,
                }),
                size: 'lg',
              })}
            >
              <Icon className="w-6" />
              <p
                className={`${cn('hidden lg:block', {
                  'font-extrabold': isActive,
                })}`}
              >
                {link.name}
              </p>
            </Link>
          </TooltipText>
        )
      })}

```
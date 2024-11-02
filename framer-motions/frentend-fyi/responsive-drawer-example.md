# Examples 

## Drawer Example

Resize the preview by dragging the handle in the center. Depending on the breakpoint, the animation should change.

 
```typescript
Show solution
import { motion } from "framer-motion";
import { useState } from 'react';

const App = () => {
  const [isOpened, setIsOpened] = useState(false);

  return (
    <div>
      <button className="bg-white px-8 py-2 rounded-full fixed top-3 left-1/2 -translate-x-1/2 z-20" onClick={() => setIsOpened(open => !open)}>
        {isOpened? "Close" : "Open"} drawer
      </button>

      <motion.div
        variants={{
          opened: {
            opacity: 1,
            "--x": "0%",
            "--y": "0%",
          },
          closed: {
            opacity: 0,
            "--x": "-100%",
            "--y": "100%"
          }
        }}
        initial="closed"
        animate={isOpened ? "opened" : "closed"}
        // by using max-sm we dont need to set y to default in md
        className="max-sm:translate-y-[--y] md:translate-x-[--x] bg-white w-[90%] max-h-[90%] overflow-auto md:max-w-[500px] fixed left-[5%] md:left-0 bottom-0 p-5 rounded-tr-2xl max-sm:rounded-tl-2xl">
        <Skeleton />
      </motion.div>
    </div>
  );
}
```

## Notification Example

```typescript
import { AnimatePresence, motion } from "framer-motion";
import { useEffect, useState } from "react";

const fakeNotifications = [
  {
    id: 1,
    // In a real world scenario this would be a timestamp, not a string
    // the conversion would be done by using a package like `date-fns`
    timeAgo: "1 hour ago",
    message: "New module about responsiveness added",
  },
  {
    id: 2,
    timeAgo: "2 hours ago",
    message: "Password reset",
  },
  {
    id: 3,
    timeAgo: "3 hours ago",
    message: "Went outside for a walk",
  },
];

const App = () => {
  const [showNotifications, setShowNotifications] = useState(true);

  return (
    <div className="w-full max-w-[300px] mt-4 mx-auto">
      <button
        className="rounded-2xl bg-black p-3 mb-4 text-gray-200 transition-colors hover:bg-gray-900 focus-visible:bg-gray-900"
        onClick={() => setShowNotifications((show) => !show)}
        aria-label="Toggle notifications"
      >
        <Bell />
      </button>
      <AnimatePresence initial={false}>
        {showNotifications && (
          <motion.div
            variants={{
              open: {
                opacity: 1,
                transition: {
                  staggerChildren: 0.1
                }
              },
              closed: {
                opacity: 0,
                transition: {
                  staggerChildren: 0.04,
                  staggerDirection: -1
                }
              }
            }}
            initial="closed"
            animate="open"
            exit="closed"
            className="w-full max-w-[300px] rounded-2xl border border-gray-300 bg-white text-black shadow-xl"
          >
            <header className="border-b border-gray-300 px-5 py-3 text-md font-bold">
              <p>Notifications</p>
            </header>
            <ul className="m-0 divide-y divide-gray-300 overflow-hidden p-0">
              {fakeNotifications.map((notification) => (
                <motion.li
                  variants={{
                    open: {
                      opacity: 1,
                      "--x": "0px",
                      "--y": "0px"
                    },
                    closed: {
                      opacity: 0,
                      "--x": "30px",
                      "--y": "30px"
                    }
                  }}
                  className="max-sm:translate-y-[--y] md:translate-x-[--x] flex items-center gap-4 px-5 py-3 leading-tight"
                  key={notification.id}
                >
                  <Logo />
                  <div>
                    <p className="mb-2 text-md">{notification.message}</p>
                    <p className="text-sm opacity-80">{notification.timeAgo}</p>
                  </div>
                </motion.li>
              ))}
            </ul>
          </motion.div>
        )}
        </AnimatePresence>
    </div>
  );
};

const Bell = () => (
  <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="M6 8a6 6 0 0 1 12 0c0 7 3 9 3 9H3s3-2 3-9"/><path d="M10.3 21a1.94 1.94 0 0 0 3.4 0"/></svg>
);

const Logo = () => (
  <div className="h-10 w-10 shrink-0 rounded-full border flex items-center justify-center border-gray-300 bg-black">
    <svg className="w-5 h-5" viewBox="0 0 29 22" fill="none" xmlns="http://www.w3.org/2000/svg"><path fill-rule="evenodd" clip-rule="evenodd" d="M6.10742 0.588113C6.24066 0.302388 6.30728 0.159525 6.43247 0.0797664C6.55767 7.72095e-06 6.7153 7.61284e-06 7.03056 7.40078e-06L18.0311 1.38488e-09C18.7503 -4.82386e-07 19.11 -7.39134e-07 19.2601 0.235646C19.4102 0.471292 19.2582 0.797207 18.9543 1.44904L18.2309 3.00022C18.0977 3.28595 18.0311 3.42881 17.9059 3.50857C17.7807 3.58833 17.6231 3.58833 17.3078 3.58833L4.7084 3.58834L6.10742 0.588113ZM7.49707 17.9416C7.1818 17.9416 7.02416 17.9416 6.89897 18.0214C6.77377 18.1012 6.70715 18.244 6.57392 18.5297L5.85138 20.0793C5.54744 20.7311 5.39547 21.057 5.54559 21.2927C5.69572 21.5283 6.05532 21.5283 6.77452 21.5283L17.7751 21.5283C18.0904 21.5283 18.248 21.5283 18.3732 21.4486C18.4984 21.3688 18.565 21.2259 18.6983 20.9402L24.4406 8.62569C24.7446 7.97386 24.8966 7.64794 24.7464 7.41229C24.5963 7.17665 24.2367 7.17665 23.5175 7.17665H21.3497C21.0344 7.17665 20.8768 7.17665 20.7516 7.25641C20.6264 7.33616 20.5598 7.47903 20.4265 7.76475L17.3539 14.354L18.845 16.3045C19.3903 17.0178 19.6629 17.3744 19.5227 17.6581C19.3825 17.9417 18.9336 17.9417 18.0358 17.9417L7.49707 17.9416ZM4.7084 3.58834L7.45089 7.17616L4.37838 13.7652C4.24514 14.0509 4.17852 14.1938 4.05333 14.2736C3.92813 14.3533 3.7705 14.3533 3.45524 14.3533H1.28744C0.568229 14.3533 0.208622 14.3533 0.0584979 14.1177C-0.0916258 13.882 0.0603511 13.5561 0.364305 12.9043L2.77445 7.7357C2.90141 7.46344 2.96489 7.32731 2.94954 7.18595C2.93418 7.0446 2.84296 6.92527 2.66052 6.68661L1.54355 5.22552C0.998252 4.51222 0.725604 4.15558 0.86579 3.87195C1.00598 3.58833 1.4549 3.58833 2.35275 3.58833L4.7084 3.58834ZM26.864 6.28178e-07C27.5832 3.89468e-07 27.9428 2.59696e-07 28.093 0.235647C28.2431 0.471294 28.0911 0.797209 27.7872 1.44904L27.0638 3.00021C26.9306 3.28594 26.864 3.4288 26.7388 3.50856C26.6136 3.58832 26.456 3.58832 26.1407 3.58832H23.9729C23.2537 3.58832 22.8941 3.58832 22.7439 3.35267C22.5938 3.11702 22.7458 2.79111 23.0497 2.13928L23.7731 0.588106C23.9063 0.302381 23.9729 0.159519 24.0981 0.07976C24.2233 1.50324e-06 24.3809 1.45232e-06 24.6962 1.34768e-06L26.864 6.28178e-07ZM7.45089 7.17616H11.6C11.854 7.17616 11.9809 7.17616 12.0897 7.23145C12.1985 7.28674 12.2733 7.38931 12.4229 7.59445L17.3539 14.354H13.2046C12.9507 14.354 12.8237 14.354 12.715 14.2987C12.6062 14.2434 12.5314 14.1408 12.3817 13.9357L7.45089 7.17616Z" fill="#7BD0B5"></path><path fill-rule="evenodd" clip-rule="evenodd" d="M6.10742 0.588113C6.24066 0.302388 6.30728 0.159525 6.43247 0.0797664C6.55767 7.72095e-06 6.7153 7.61284e-06 7.03056 7.40078e-06L18.0311 1.38488e-09C18.7503 -4.82386e-07 19.11 -7.39134e-07 19.2601 0.235646C19.4102 0.471292 19.2582 0.797207 18.9543 1.44904L18.2309 3.00022C18.0977 3.28595 18.0311 3.42881 17.9059 3.50857C17.7807 3.58833 17.6231 3.58833 17.3078 3.58833L4.7084 3.58834L6.10742 0.588113ZM7.49707 17.9416C7.1818 17.9416 7.02416 17.9416 6.89897 18.0214C6.77377 18.1012 6.70715 18.244 6.57392 18.5297L5.85138 20.0793C5.54744 20.7311 5.39547 21.057 5.54559 21.2927C5.69572 21.5283 6.05532 21.5283 6.77452 21.5283L17.7751 21.5283C18.0904 21.5283 18.248 21.5283 18.3732 21.4486C18.4984 21.3688 18.565 21.2259 18.6983 20.9402L24.4406 8.62569C24.7446 7.97386 24.8966 7.64794 24.7464 7.41229C24.5963 7.17665 24.2367 7.17665 23.5175 7.17665H21.3497C21.0344 7.17665 20.8768 7.17665 20.7516 7.25641C20.6264 7.33616 20.5598 7.47903 20.4265 7.76475L17.3539 14.354L18.845 16.3045C19.3903 17.0178 19.6629 17.3744 19.5227 17.6581C19.3825 17.9417 18.9336 17.9417 18.0358 17.9417L7.49707 17.9416ZM4.7084 3.58834L7.45089 7.17616L4.37838 13.7652C4.24514 14.0509 4.17852 14.1938 4.05333 14.2736C3.92813 14.3533 3.7705 14.3533 3.45524 14.3533H1.28744C0.568229 14.3533 0.208622 14.3533 0.0584979 14.1177C-0.0916258 13.882 0.0603511 13.5561 0.364305 12.9043L2.77445 7.7357C2.90141 7.46344 2.96489 7.32731 2.94954 7.18595C2.93418 7.0446 2.84296 6.92527 2.66052 6.68661L1.54355 5.22552C0.998252 4.51222 0.725604 4.15558 0.86579 3.87195C1.00598 3.58833 1.4549 3.58833 2.35275 3.58833L4.7084 3.58834ZM26.864 6.28178e-07C27.5832 3.89468e-07 27.9428 2.59696e-07 28.093 0.235647C28.2431 0.471294 28.0911 0.797209 27.7872 1.44904L27.0638 3.00021C26.9306 3.28594 26.864 3.4288 26.7388 3.50856C26.6136 3.58832 26.456 3.58832 26.1407 3.58832H23.9729C23.2537 3.58832 22.8941 3.58832 22.7439 3.35267C22.5938 3.11702 22.7458 2.79111 23.0497 2.13928L23.7731 0.588106C23.9063 0.302381 23.9729 0.159519 24.0981 0.07976C24.2233 1.50324e-06 24.3809 1.45232e-06 24.6962 1.34768e-06L26.864 6.28178e-07ZM7.45089 7.17616H11.6C11.854 7.17616 11.9809 7.17616 12.0897 7.23145C12.1985 7.28674 12.2733 7.38931 12.4229 7.59445L17.3539 14.354H13.2046C12.9507 14.354 12.8237 14.354 12.715 14.2987C12.6062 14.2434 12.5314 14.1408 12.3817 13.9357L7.45089 7.17616Z" fill="url(#paint0_radial_1_26)"></path><path fill-rule="evenodd" clip-rule="evenodd" d="M6.10742 0.588113C6.24066 0.302388 6.30728 0.159525 6.43247 0.0797664C6.55767 7.72095e-06 6.7153 7.61284e-06 7.03056 7.40078e-06L18.0311 1.38488e-09C18.7503 -4.82386e-07 19.11 -7.39134e-07 19.2601 0.235646C19.4102 0.471292 19.2582 0.797207 18.9543 1.44904L18.2309 3.00022C18.0977 3.28595 18.0311 3.42881 17.9059 3.50857C17.7807 3.58833 17.6231 3.58833 17.3078 3.58833L4.7084 3.58834L6.10742 0.588113ZM7.49707 17.9416C7.1818 17.9416 7.02416 17.9416 6.89897 18.0214C6.77377 18.1012 6.70715 18.244 6.57392 18.5297L5.85138 20.0793C5.54744 20.7311 5.39547 21.057 5.54559 21.2927C5.69572 21.5283 6.05532 21.5283 6.77452 21.5283L17.7751 21.5283C18.0904 21.5283 18.248 21.5283 18.3732 21.4486C18.4984 21.3688 18.565 21.2259 18.6983 20.9402L24.4406 8.62569C24.7446 7.97386 24.8966 7.64794 24.7464 7.41229C24.5963 7.17665 24.2367 7.17665 23.5175 7.17665H21.3497C21.0344 7.17665 20.8768 7.17665 20.7516 7.25641C20.6264 7.33616 20.5598 7.47903 20.4265 7.76475L17.3539 14.354L18.845 16.3045C19.3903 17.0178 19.6629 17.3744 19.5227 17.6581C19.3825 17.9417 18.9336 17.9417 18.0358 17.9417L7.49707 17.9416ZM4.7084 3.58834L7.45089 7.17616L4.37838 13.7652C4.24514 14.0509 4.17852 14.1938 4.05333 14.2736C3.92813 14.3533 3.7705 14.3533 3.45524 14.3533H1.28744C0.568229 14.3533 0.208622 14.3533 0.0584979 14.1177C-0.0916258 13.882 0.0603511 13.5561 0.364305 12.9043L2.77445 7.7357C2.90141 7.46344 2.96489 7.32731 2.94954 7.18595C2.93418 7.0446 2.84296 6.92527 2.66052 6.68661L1.54355 5.22552C0.998252 4.51222 0.725604 4.15558 0.86579 3.87195C1.00598 3.58833 1.4549 3.58833 2.35275 3.58833L4.7084 3.58834ZM26.864 6.28178e-07C27.5832 3.89468e-07 27.9428 2.59696e-07 28.093 0.235647C28.2431 0.471294 28.0911 0.797209 27.7872 1.44904L27.0638 3.00021C26.9306 3.28594 26.864 3.4288 26.7388 3.50856C26.6136 3.58832 26.456 3.58832 26.1407 3.58832H23.9729C23.2537 3.58832 22.8941 3.58832 22.7439 3.35267C22.5938 3.11702 22.7458 2.79111 23.0497 2.13928L23.7731 0.588106C23.9063 0.302381 23.9729 0.159519 24.0981 0.07976C24.2233 1.50324e-06 24.3809 1.45232e-06 24.6962 1.34768e-06L26.864 6.28178e-07ZM7.45089 7.17616H11.6C11.854 7.17616 11.9809 7.17616 12.0897 7.23145C12.1985 7.28674 12.2733 7.38931 12.4229 7.59445L17.3539 14.354H13.2046C12.9507 14.354 12.8237 14.354 12.715 14.2987C12.6062 14.2434 12.5314 14.1408 12.3817 13.9357L7.45089 7.17616Z" fill="url(#paint1_radial_1_26)"></path><path fill-rule="evenodd" clip-rule="evenodd" d="M6.10742 0.588113C6.24066 0.302388 6.30728 0.159525 6.43247 0.0797664C6.55767 7.72095e-06 6.7153 7.61284e-06 7.03056 7.40078e-06L18.0311 1.38488e-09C18.7503 -4.82386e-07 19.11 -7.39134e-07 19.2601 0.235646C19.4102 0.471292 19.2582 0.797207 18.9543 1.44904L18.2309 3.00022C18.0977 3.28595 18.0311 3.42881 17.9059 3.50857C17.7807 3.58833 17.6231 3.58833 17.3078 3.58833L4.7084 3.58834L6.10742 0.588113ZM7.49707 17.9416C7.1818 17.9416 7.02416 17.9416 6.89897 18.0214C6.77377 18.1012 6.70715 18.244 6.57392 18.5297L5.85138 20.0793C5.54744 20.7311 5.39547 21.057 5.54559 21.2927C5.69572 21.5283 6.05532 21.5283 6.77452 21.5283L17.7751 21.5283C18.0904 21.5283 18.248 21.5283 18.3732 21.4486C18.4984 21.3688 18.565 21.2259 18.6983 20.9402L24.4406 8.62569C24.7446 7.97386 24.8966 7.64794 24.7464 7.41229C24.5963 7.17665 24.2367 7.17665 23.5175 7.17665H21.3497C21.0344 7.17665 20.8768 7.17665 20.7516 7.25641C20.6264 7.33616 20.5598 7.47903 20.4265 7.76475L17.3539 14.354L18.845 16.3045C19.3903 17.0178 19.6629 17.3744 19.5227 17.6581C19.3825 17.9417 18.9336 17.9417 18.0358 17.9417L7.49707 17.9416ZM4.7084 3.58834L7.45089 7.17616L4.37838 13.7652C4.24514 14.0509 4.17852 14.1938 4.05333 14.2736C3.92813 14.3533 3.7705 14.3533 3.45524 14.3533H1.28744C0.568229 14.3533 0.208622 14.3533 0.0584979 14.1177C-0.0916258 13.882 0.0603511 13.5561 0.364305 12.9043L2.77445 7.7357C2.90141 7.46344 2.96489 7.32731 2.94954 7.18595C2.93418 7.0446 2.84296 6.92527 2.66052 6.68661L1.54355 5.22552C0.998252 4.51222 0.725604 4.15558 0.86579 3.87195C1.00598 3.58833 1.4549 3.58833 2.35275 3.58833L4.7084 3.58834ZM26.864 6.28178e-07C27.5832 3.89468e-07 27.9428 2.59696e-07 28.093 0.235647C28.2431 0.471294 28.0911 0.797209 27.7872 1.44904L27.0638 3.00021C26.9306 3.28594 26.864 3.4288 26.7388 3.50856C26.6136 3.58832 26.456 3.58832 26.1407 3.58832H23.9729C23.2537 3.58832 22.8941 3.58832 22.7439 3.35267C22.5938 3.11702 22.7458 2.79111 23.0497 2.13928L23.7731 0.588106C23.9063 0.302381 23.9729 0.159519 24.0981 0.07976C24.2233 1.50324e-06 24.3809 1.45232e-06 24.6962 1.34768e-06L26.864 6.28178e-07ZM7.45089 7.17616H11.6C11.854 7.17616 11.9809 7.17616 12.0897 7.23145C12.1985 7.28674 12.2733 7.38931 12.4229 7.59445L17.3539 14.354H13.2046C12.9507 14.354 12.8237 14.354 12.715 14.2987C12.6062 14.2434 12.5314 14.1408 12.3817 13.9357L7.45089 7.17616Z" fill="#E6E8F0"></path></svg>
  </div>
)

export default App;
```
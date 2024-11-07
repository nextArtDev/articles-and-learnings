# advanced shared layout animations Example

In this lesson, we will animate a single list item representing a music album into a full-screen detail view.

// App.tsx

```typescript
import { useState } from "react";
import { AnimatePresence, motion } from "framer-motion";

import { DetailView } from "./DetailView";
import { SongPreview } from "./SongPreview";
import { songs } from "./songs";

export const App = () => {
  const [selectedSong, setSelectedSong] = useState(null);

  return (
    // Adding AnimatePresence to top wrapper component
    <AnimatePresence>
      <div className="relative mx-auto aspect-[9/16] w-full max-w-[400px] rounded-3xl border border-white/10 bg-white/10 text-white p-2 sm:p-4 overflow-clip">
        <div className="mb-6 flex aspect-video flex-col items-start rounded-2xl bg-white/10 p-4 text-2xl font-bold md:p-8">
          <p>Trending music</p>
          <a className="mt-auto rounded-full bg-white/10 p-2 text-md font-normal">
            Explore now ▸
          </a>
        </div>
        <p className="mb-3 text-xl font-bold">Your favorites</p>
        <div className="grid grid-cols-1 gap-4">
          {songs.map((song) => (
            <SongPreview key={song.id} song={song} setSelectedSong={setSelectedSong} />
          ))}
        </div>
        {selectedSong && (
          <DetailView
            onClose={() => setSelectedSong(null)}
            song={selectedSong}
          />
        )}
      </div>
    </AnimatePresence>
  );
};

export default App;
```

// DetailView.tsx

```typescript
import { motion } from "framer-motion";

export const DetailView = ({song, onClose}) => {
  return (
    <motion.div

    // Adding corresponding layoutId to elements we want to morph
      layoutId={`song-background-${song.id}`}
      style={{ borderRadius: 12 }}
      className="absolute inset-0 z-10 grid auto-rows-min place-items-center bg-white p-4 text-center text-black">
      <motion.img
       // Adding corresponding layoutId to elements we want to morph
        layoutId={`song-image-${song.id}`}
        src={song.cover}
        alt={song.title}
        style={{ borderRadius: 12 }}
        className="mb-4 aspect-square w-[200px]"
      />

 // Adding corresponding layoutId to elements we want to morph
      <motion.div layoutId={`song-title-${song.id}`}  className="text-lg font-bold">
        <motion.span className="block" layout>
          {song.title}
        </motion.span>
      </motion.div>

       // Adding corresponding layoutId to elements we want to morph
      <motion.div layoutId={`song-artist-${song.id}`}  className="text-sm">

      // creating wrapper for text distortion
        <motion.span className="block" layout>
          {song.artist}
        </motion.span>
      </motion.div>

      <motion.p
        variants={{
          hidden: { opacity: 0, y: 10},
          visible: { opacity: 1, y: 0, transition: { delay: 0.2 }}
        }}
        initial="hidden"
        animate="visible"
        exit="hidden"
        className="max-w-[80%] text-sm">
        {song.description}
      </motion.p>
      <motion.button
        variants={{
          hidden: { opacity: 0 },
          visible: {
            opacity: 1,
            transition: { ease: "easeInOut", delay: 0.3, duration: 0.5 },
          },
        }}
        initial="hidden"
        animate="visible"
        exit="hidden"
        onClick={onClose}
        className="mt-4 w-full rounded-full bg-black px-4 py-2 text-white"
      >
        Back
      </motion.button>
```

//SongPreview.tsx

```typescript
import { motion } from "framer-motion";

export const SongPreview = ({song, setSelectedSong}) => {
  return (
    <motion.div

     // Adding corresponding layoutId to elements we want to morph
      layoutId={`song-background-${song.id}`}
      style={{ borderRadius: 12 }}
      className="grid cursor-pointer grid-cols-1 grid-rows-2 items-center gap-x-3 bg-white p-4 text-black md:grid-cols-[auto_1fr]"
      onClick={() => setSelectedSong(song)}
    >
      <motion.img

       // Adding corresponding layoutId to elements we want to morph
        layoutId={`song-image-${song.id}`}
        src={song.cover}
        alt={song.title}
        style={{ borderRadius: 12 }}
        className="row-span-2 h-8 w-8"
      />

       // Adding corresponding layoutId to elements we want to morph

      <motion.div layoutId={`song-title-${song.id}`} className="text-lg font-bold leading-snug">
        <motion.span className="block" layout>
          {song.title}
        </motion.span>
      </motion.div>

       // Adding corresponding layoutId to elements we want to morph
      <motion.div layoutId={`song-artist-${song.id}`} className="text-sm leading-snug">
      
      // creating wrapper for text distortion
        <motion.span className="block" layout>
          {song.artist}
        </motion.span>
      </motion.div>
    </motion.div>
  );
};
```


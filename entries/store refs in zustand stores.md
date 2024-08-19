---
title: Store Refs in Zustand Stores
permalink: store-refs-in-zustand-stores
date: 2024-08-19T18:10:11-04:00
tags:
---

Use callback refs.

Inside the zustand store:

```jsx
  settingsContainerRef: createRef() as MutableRefObject<HTMLDivElement>,
  setSettingsContainerEl: (element: HTMLDivElement | null) => {
    if (!element) {
      return;
    }
    const newRef = createRef() as MutableRefObject<HTMLDivElement>;
    newRef.current = element;
    set({ settingsContainerRef: newRef });
  },
```

Then in JSX:

```
    <div
     ref={setSettingsContainerEl}
     ...
     />
```

Source:
https://github.com/pmndrs/zustand/discussions/1833#discussioncomment-8957558

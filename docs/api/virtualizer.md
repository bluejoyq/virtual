---
title: Virtualizer
---

The `Virtualizer` class is the core of TanStack Virtual. Virtualizer instances are usually created for you by your framework adapter, but you do receive the virtualizer directly.

```tsx
export class Virtualizer<TScrollElement = unknown, TItemElement = unknown> {
  constructor(options: VirtualizerOptions<TScrollElement, TItemElement>)
}
```

## Required Options

### `count`

```tsx
count: number
```

The total number of items to virtualize.

### `getScrollElement`

```tsx
getScrollElement: () => TScrollElement
```

A function that returns the scrollable element for the virtualizer. It may return null if the element is not available yet.

### `estimateSize`

```tsx
estimateSize: (index: number) => number
```

> 🧠 If you are dynamically measuring your elements, it's recommended to estimate the largest possible size (width/height, within comfort) of your items. This will ensure features like smooth-scrolling will have a better chance at working correctly.

This function is passed the index of each item and should return the actual size (or estimated size if you will be dynamically measuring items with `virtualItem.measureElement`) for each item. This measurement should return either the width or height depending on the orientation of your virtualizer.

## Optional Options

### `debug`

```tsx
debug?: boolean
```

Set to `true` to enable debug logs

### `initialRect`

```tsx
initialRect?: Rect
```

The initial `Rect` of the scrollElement. This is mostly useful if you need to run the virtualizer in an SSR environment, otherwise the initialRect will be calculated on mount by the `observeElementRect` implementation.

### `onChange`

```tsx
onChange?: (instance: Virtualizer<TScrollElement, TItemElement>) => void
```

A callback function that fires when the virtualizer's internal state changes. It's passed the virtualizer instance.

### `overscan`

```tsx
overscan?: number
```

The number of items to render above and below the visible area. Increasing this number will increase the amount of time it takes to render the virtualizer, but might decrease the likelihood of seeing slow-rendering blank items at the top and bottom of the virtualizer when scrolling.

### `horizontal`

```tsx
horizontal?: boolean
```

Set this to `true` if your virtualizer is oriented horizontally.

### `paddingStart`

```tsx
paddingStart?: number
```

The padding to apply to the start of the virtualizer in pixels.

### `paddingEnd`

```tsx
paddingEnd?: number
```

The padding to apply to the end of the virtualizer in pixels.

### `scrollPaddingStart`

```tsx
scrollPaddingStart?: number
```

The padding to apply to the start of the virtualizer in pixels when scrolling to an element.

### `scrollPaddingEnd`

```tsx
scrollPaddingEnd?: number
```

The padding to apply to the end of the virtualizer in pixels when scrolling to an element.

### `initialOffset`

```tsx
initialOffset?: number
```

The initial offset to apply to the virtualizer. This is usually only useful if you are rendering the virtualizer in a SSR environment.

### `getItemKey`

```tsx
getItemKey?: (index: number) => Key
```

This function is passed the index of each item and should return a unique key for that item. The default functionality of this function is to return the index of the item, but you should override this when possible to return a unique identifier for each item across the entire set. This function should be memoized to prevent unnecessary re-renders.

### `rangeExtractor`

```tsx
rangeExtractor?: (range: Range) => number[]
```

This function receives visible range indexes and should return array of indexes to render. This is useful if you need to add or remove items from the virtualizer manually regardless of the visible range, eg. rendering sticky items, headers, footers, etc. The default range extractor implementation will return the visible range indexes and is exported as `defaultRangeExtractor`.

### `scrollToFn`

```tsx
scrollToFn?: (
  offset: number,
  canSmooth: boolean,
  instance: Virtualizer<TScrollElement, TItemElement>,
) => void
```

An optional function that if provided should implement the scrolling behavior for your scrollElement. It will be called with the offset to scroll to, a boolean indicating if the scrolling is allowed to be smoothed, and the virtualizer instance. Built-in scroll implementations are exported as `elementScroll` and `windowScroll` which are automatically configured for you by your framework adapter's exported functions like `useVirtualizer` or `useWindowVirtualizer`.

> ⚠️ Attempting to use smoothScroll with dynamically measured elements will not work.

### `observeElementRect`

```tsx
observeElementRect: (
  instance: Virtualizer<TScrollElement, TItemElement>,
  cb: (rect: Rect) => void,
) => void | (() => void)
```

An optional function that if provided is called when the scrollElement changes and should implement the initial measurement and continuous monitoring of the scrollElement's `Rect` (an object with `width` and `height`). It's called with the instance (which also gives you access to the scrollElement via `instance.scrollElement`. Built-in implementations are exported as `observeElementRect` and `observeWindowRect` which are automatically configured for you by your framework adapter's exported functions like `useVirtualizer` or `useWindowVirtualizer`.

### `observeElementOffset`

```tsx
observeElementOffset: (
    instance: Virtualizer<TScrollElement, TItemElement>,
    cb: (offset: number) => void,
  ) => void | (() => void)
```

An optional function that if provided is called when the scrollElement changes and should implement the initial measurement and continuous monitoring of the scrollElement's scroll offset (a number). It's called with the instance (which also gives you access to the scrollElement via `instance.scrollElement`. Built-in implementations are exported as `observeElementOffset` and `observeWindowOffset` which are automatically configured for you by your framework adapter's exported functions like `useVirtualizer` or `useWindowVirtualizer`.

### `measureElement`

```tsx
measureElement?: (
  el: TItemElement,
  instance: Virtualizer<TScrollElement, TItemElement>
) => number
```

This optional function is called when the virtualizer needs to dynamically measure the size (width or height) of an item when `virtualItem.measureElement` is called. It's passed the element given when you call `virtualItem.measureElement(TItemElement)` and the virtualizer instance. It should return the size of the element as a `number`.

> 🧠 You can use `instance.options.horizontal` to determine if the width or height of the item should be measured.

### `scrollMargin`

```tsx
scrollMargin?: number
```

With this option, you can specify where the scroll offset should originate. Typically, this value represents the space between the beginning of the scrolling element and the start of the list. This is especially useful in common scenarios such as when you have a header preceding a window virtualizer or when multiple virtualizers are utilized within a single scrolling element.


## Virtualizer Instance

The following properties and methods are available on the virtualizer instance:

### `options`

```tsx
options: readonly Required<VirtualizerOptions<TScrollElement, TItemElement>>
```

The current options for the virtualizer. This property is updated via your framework adapter and is read-only.

### `scrollElement`

```tsx
scrollElement: readonly TScrollElement | null
```

The current scrollElement for the virtualizer. This property is updated via your framework adapter and is read-only.

### `getVirtualItems`

```tsx
type getVirtualItems = () => VirtualItem[]
```

Returns the virtual items for the current state of the virtualizer.

### `scrollToOffset`

```tsx
scrollToOffset: (
  toOffset: number,
  options?: {
    align?: 'start' | 'center' | 'end' | 'auto',
    smoothScroll?: boolean
  }
) => void
```

Scrolls the virtualizer to the pixel offset provided. You can optionally pass an alignment mode to anchor the scroll to a specific part of the scrollElement.

### `scrollToIndex`

```tsx
scrollToIndex: (
  index: number,
  options?: {
    align?: 'start' | 'center' | 'end' | 'auto',
    smoothScroll?: boolean
  }
) => void
```

Scrolls the virtualizer to the items of the index provided. You can optionally pass an alignment mode to anchor the scroll to a specific part of the scrollElement.

### `getTotalSize`

```tsx
getTotalSize: () => number
```

Returns the total size in pixels for the virtualized items. This measurement will incrementally change if you choose to dynamically measure your elements as they are rendered.

### `measure`

```tsx
measure: () => void
```

Resets any prev item measurements.

### `measureElement`

```tsx
measureElement: (el: TItemElement | null) => void
```

Measures the element using your configured `measureElement` virtualizer option. You are responsible for calling this in your virtualizer markup when the component is rendered (eg. using something like React's ref callback prop) also adding `data-index`

```tsx
 <div
  key={virtualRow.key}
  data-index={virtualRow.index}
  ref={virtualizer.measureElement}
  style={...}
>...</div>
```

By default the `measureElement` virtualizer option is configured to measure elements with `getBoundingClientRect()`.

### `resizeItem`

```tsx
resizeItem: (item: VirtualItem, size: number) => void
```

Change the virtualized item's size manually. Use this function to manually set the size calculated for this item. Useful in occations when using some custom morphing transition and you know the morphed item's size beforehand.

You can also use this method with a throttled ResizeObserver instead of `Virtualizer.measureElement` to reduce re-rendering.

> ⚠️ Please be aware that manually changing the size of an item when using `Virtualizer.measureElement` to monitor that item, will result in unpredictable behaviour as the `Virtualizer.measureElement` is also changing the size. However you can use one of resizeItem or measureElement in the same virtualizer instance but on different item indexes.

### `lanes`

```tsx
lanes: number
```

The number of lanes the list is divided into (aka columns for vertical lists and rows for horizontal lists).

### `scrollRect`

```tsx
scrollRect: Rect
```

Current `Rect` of the scroll element.

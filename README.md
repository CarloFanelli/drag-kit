# drag-kit

A tiny vanilla JavaScript drag-and-drop engine that **does not use the HTML Drag and Drop API**.

Instead of `dragstart` / `dragover` / `drop`, drag-kit moves elements with plain mouse events and detects drop zones geometrically, by checking the pointer position against each zone's bounding box. That gives you full control over styling while dragging (no ghost image, no browser defaults) and a single callback when an element is released over a zone.

Zero dependencies, ~150 lines, ES modules.

## Features

- Drag any element with `mousedown` / `mousemove` / `mouseup`, no `draggable` attribute needed
- Drop zones detected by pointer position (works with scrolled pages)
- A semi-transparent **placeholder clone** stays where the element came from while you drag
- Class lists applied to the dragged element and to the drop zones while dragging and on hover, so you can style every state with your own CSS or Tailwind
- One `callback(dragElement, dropZone)` fired on a successful drop

## Usage

```html
<div class="dropZone" id="drop-1"></div>
<div class="dropZone" id="drop-2"></div>

<div class="dragElement" id="card-1">Card 1</div>
<div class="dragElement" id="card-2">Card 2</div>

<script type="module" src="./main.js"></script>
```

```js
import DragAndDrop from './assets/js/dragedrop/dragedrop.js';

new DragAndDrop({
  dragElements: {
    elementSelector: [...document.querySelectorAll('.dragElement')],
    elementOnDragClassList: ['opacity-90', 'shadow-xl'],   // while dragging
    elementHoverClassList:  ['ring-2', 'ring-green-400'],  // while over a drop zone
    placeholderOpacity: 20                                 // 0–100, opacity of the clone left behind
  },
  dropZones: {
    elementSelector: [...document.querySelectorAll('.dropZone')],
    elementDragClassList:  ['bg-blue-500'],   // every zone, while something is being dragged
    elementHoverClassList: ['bg-pink-500']    // the zone under the pointer
  },
  callback(dragElement, dropZone) {
    dropZone.appendChild(dragElement);        // do whatever you need on drop
  }
});
```

## Demo

Clone the repo and open `index.html` in a browser (or serve it with any static server). The demo generates seven drop zones and a set of draggable cards styled with Tailwind via CDN.

```bash
git clone https://github.com/CarloFanelli/drag-kit.git
cd drag-kit
npx serve .
```

## Project structure

```
assets/js/dragedrop/
├── dragedrop.js                 # DragAndDrop: orchestrates mouse, zones and elements
└── elements/
    ├── draggableElement.js      # wraps a draggable element, creates/removes the placeholder clone
    ├── dropZone.js              # wraps a drop zone element
    └── mouse.js                 # tracks pointer position (pageX / pageY)
```

`assets/js/main.js` is the original single-file prototype, kept for reference.

## Why not the native API?

The native HTML5 drag-and-drop API is inconsistent across browsers, hard to style (the drag image is a browser-rendered snapshot) and has no notion of "which zone am I over" without wiring several events. drag-kit trades that for a simpler mental model: an element follows the mouse, and on release we ask each zone whether the pointer is inside it.

## Status

Working prototype used to explore the approach. Touch events and keyboard accessibility are not implemented yet.

---
title: Teleport
description: Vim-style keyboard navigation bindings with DOM integration.
---

# Teleport

**@sailkit/teleport** provides Vim-style keyboard navigation with DOM integration. Three layers of abstraction let you use as much or as little as you need.

## Installation

```bash
npm install @sailkit/teleport
```

## Quick Start (Astro)

```astro
---
import Teleport from '@sailkit/teleport/Teleport.astro';
---
<html>
  <body>
    <nav class="sidebar">
      <a class="nav-item" href="/page-1">Page 1</a>
      <a class="nav-item" href="/page-2">Page 2</a>
    </nav>
    <main>Content</main>
    <Teleport />
  </body>
</html>
```

## Default Key Bindings

| Key | Action |
|-----|--------|
| `j` / `ArrowDown` | Next item in sidebar |
| `k` / `ArrowUp` | Previous item in sidebar |
| `Ctrl+d` | Scroll content down |
| `Ctrl+u` | Scroll content up |
| `l` / `ArrowRight` | Next page |
| `h` / `ArrowLeft` | Previous page |
| `Enter` | Navigate to highlighted item |
| `t` | Open fuzzy finder (when enabled) |
| `Escape` | Clear highlight |

## Three Layers

### Layer 1: Pure Key Bindings

Just the keyboard handling, no DOM manipulation:

```typescript
import { createKeyboardHandler, DEFAULT_BINDINGS } from '@sailkit/teleport';

const handler = createKeyboardHandler({
  bindings: { ...DEFAULT_BINDINGS, nextItem: ['n'] },
  onNextItem: () => console.log('Next!'),
  onPrevItem: () => console.log('Prev!'),
  onSelect: () => console.log('Selected!'),
});

document.addEventListener('keydown', handler.handleKeydown);

// Cleanup
handler.destroy();
```

### Layer 2: DOM Navigator

Keyboard handling plus DOM highlighting:

```typescript
import { createDOMNavigator } from '@sailkit/teleport';

const navigator = createDOMNavigator({
  getItems: () => document.querySelectorAll('.item'),
  highlightClass: 'my-highlight',
  onSelect: (item, index) => console.log('Selected', item),
  onHighlightChange: (item, index) => console.log('Highlighted', item),
});

navigator.next();     // Highlight next item
navigator.prev();     // Highlight previous
navigator.goTo(5);    // Jump to index
navigator.clear();    // Clear highlight
navigator.refresh();  // Re-scan items
```

### Layer 3: Full Integration

Everything wired up and ready to go:

```typescript
import { initTeleport } from '@sailkit/teleport';

const teleport = initTeleport({
  itemSelector: '.nav-item',
  contentContainer: 'main',
  sidebarContainer: '.sidebar',
  highlightClass: 'teleport-highlight',
  onSelect: (item) => item.click(),
  onNextPage: () => router.push(nextUrl),
  onPrevPage: () => router.push(prevUrl),
  onOpenFinder: () => openFuzzyFinder(),
  scrollAmount: 300,
});

// Access the underlying navigator
teleport.navigator.goTo(0);

// Cleanup
teleport.destroy();
```

## Astro Component Props

```astro
<Teleport
  itemSelector=".nav-item"
  contentSelector="main"
  sidebarSelector=".sidebar"
  highlightClass="teleport-highlight"
  enableFinder={false}
/>
```

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `itemSelector` | `string` | `.nav-item` | CSS selector for nav items |
| `contentSelector` | `string` | `main` | Content to scroll with Ctrl+d/u |
| `sidebarSelector` | `string` | `.sidebar` | Sidebar container |
| `highlightClass` | `string` | `teleport-highlight` | Class for highlighted item |
| `enableFinder` | `boolean` | `false` | Emit event on `t` key |

## Custom Bindings

```typescript
import { initTeleport } from '@sailkit/teleport';

initTeleport({
  itemSelector: '.nav-item',
  bindings: {
    nextItem: ['n', 'ArrowDown'],
    prevItem: ['p', 'ArrowUp'],
    scrollDown: ['Ctrl+f'],
    scrollUp: ['Ctrl+b'],
    select: ['Enter', ' '],
    nextPage: ['Shift+j'],
    prevPage: ['Shift+k'],
  },
});
```

## Styling

Default styles are injected automatically. Override with CSS:

```css
.teleport-highlight {
  outline: 2px solid var(--color-accent);
  background-color: var(--color-accent-dim);
  border-radius: 4px;
}
```

Or prevent injection and use your own:

```typescript
import { initTeleport } from '@sailkit/teleport';

// Don't inject default styles
initTeleport({
  itemSelector: '.nav-item',
  highlightClass: 'my-custom-highlight', // Uses your CSS
});
```

## Text Input Handling

By default, key bindings are disabled when typing in text inputs. This prevents `j` from triggering navigation when you're typing in a search box.

```typescript
import { isTypingContext } from '@sailkit/teleport';

// Check if user is in a text input
document.addEventListener('keydown', (e) => {
  if (isTypingContext(e)) {
    // User is typing, don't intercept keys
    return;
  }
});
```

## Fuzzy Finder Integration

Listen for the `teleport:open-finder` custom event:

```javascript
document.addEventListener('teleport:open-finder', () => {
  // Open your fuzzy finder UI
  // Use @sailkit/compass data structure for items
});
```

## Related

- [[compass]] - Navigation structure that Teleport can use
- [[vim-navigation]] - Guide for customizing key bindings
- [[architecture]] - How Teleport fits into the larger system

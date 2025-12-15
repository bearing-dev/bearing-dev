---
title: Compass
description: Navigation state machine for nested content structures.
---

# Compass

**@sailkit/compass** is a headless navigation state machine for nested content structures. It provides DFS traversal, prev/next neighbors, and parent/child navigation without any UI assumptions.

## Installation

```bash
npm install @sailkit/compass
```

## Core Concepts

### NavItem Type

Navigation is defined as a forest of `NavItem` elements:

```typescript
type NavItem = string | NavBranch;

interface NavBranch {
  slug: string;
  children: NavItem[];
}
```

Strings are leaf pages, objects are sections with children.

### Example Structure

```typescript
const navigation: NavItem[] = [
  'introduction',
  {
    slug: 'getting-started',
    children: ['installation', 'quick-start'],
  },
  {
    slug: 'packages',
    children: ['compass', 'teleport', 'lantern'],
  },
];
```

This represents:
- introduction
- getting-started/
  - installation
  - quick-start
- packages/
  - compass
  - teleport
  - lantern

## Stateless Functions (SSG)

Use these at build time for static generation:

### `flattenSlugs()`

Get all slugs in DFS order:

```typescript
import { flattenSlugs } from '@sailkit/compass';

const all = flattenSlugs(navigation);
// ['introduction', 'getting-started', 'installation', 'quick-start', 'packages', 'compass', 'teleport', 'lantern']

const leaves = flattenSlugs(navigation, true); // leavesOnly
// ['introduction', 'installation', 'quick-start', 'compass', 'teleport', 'lantern']
```

### `getNeighbors()`

Get prev/next for a specific slug:

```typescript
import { getNeighbors } from '@sailkit/compass';

const { prev, next } = getNeighbors(navigation, 'installation');
// prev: 'introduction', next: 'quick-start'

const { prev, next } = getNeighbors(navigation, 'installation', { leavesOnly: true });
// prev: 'introduction', next: 'quick-start' (skips section pages)
```

This is used by the [[quick-start|PrevNext component]] in this documentation.

## State Machine (Runtime)

For SPAs or interactive navigation:

```typescript
import { createNavigator } from '@sailkit/compass';

const nav = createNavigator({
  items: navigation,
  wrap: true,
  leavesOnly: false,
  onChange: (prev, next, index) => {
    console.log(`Moved from ${prev} to ${next}`);
  },
});

// Properties
nav.current;      // Current slug
nav.currentIndex; // Current position in flat list
nav.count;        // Total items

// Navigation
nav.next();       // Move forward
nav.prev();       // Move backward
nav.nextSibling(); // Skip to next sibling
nav.prevSibling(); // Skip to previous sibling
nav.parent();     // Go to parent section
nav.firstChild(); // Go to first child
nav.goTo(5);      // Jump to index
nav.goToSlug('compass'); // Jump to slug
nav.reset();      // Go to first item
```

## Type Guards

```typescript
import { isBranch, getSlug } from '@sailkit/compass';

const item = navigation[1];

if (isBranch(item)) {
  console.log(item.children); // TypeScript knows it's a NavBranch
}

const slug = getSlug(item); // Works for both string and NavBranch
```

## Configuration Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `items` | `NavItem[]` | required | Navigation structure |
| `wrap` | `boolean` | `true` | Loop around at ends |
| `leavesOnly` | `boolean` | `false` | Skip section pages |
| `onChange` | `function` | - | Callback on navigation |

## Integration with Teleport

Compass provides the data structure, [[teleport]] handles keyboard events:

```typescript
import { createNavigator } from '@sailkit/compass';
import { initTeleport } from '@sailkit/teleport';

const nav = createNavigator({ items: navigation });

initTeleport({
  itemSelector: '.nav-item',
  onNextPage: () => {
    nav.next();
    window.location.href = `/docs/${nav.current}/`;
  },
  onPrevPage: () => {
    nav.prev();
    window.location.href = `/docs/${nav.current}/`;
  },
});
```

## Related

- [[teleport]] - Keyboard navigation that works with Compass
- [[architecture]] - How Compass fits into the larger system
- [[vim-navigation]] - Guide for customizing keyboard navigation

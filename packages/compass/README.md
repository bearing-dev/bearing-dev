# compass

Headless navigation state for trees and lists. Handles the hard parts: DFS traversal, sibling navigation, wrap-around, parent/child movement.

## Core Types

```typescript
// Single recursive node type - consumer defines T
interface NavNode<T> {
  data: T;
  children?: NavNode<T>[];
}

// Navigator interface
interface Navigator<T> {
  root: NavNode<T>[];
  current: NavNode<T> | null;
  currentIndex: number;        // flat index in DFS order
  next(): void;
  prev(): void;
  nextSibling(): void;
  prevSibling(): void;
  parent(): void;
  firstChild(): void;
  goTo(index: number): void;
  reset(): void;
}

function createNavigator<T>(config: {
  nodes: NavNode<T>[];
  wrap?: boolean;           // default true
  leavesOnly?: boolean;     // default false - if true, skip branch nodes
  onChange?: (prev: NavNode<T> | null, next: NavNode<T> | null, index: number) => void;
}): Navigator<T>;
```

## Traversal

DFS pre-order (top-to-bottom as rendered). `leavesOnly` controls whether branch nodes are navigation stops.

```typescript
const nodes = [
  { data: 'CONCEPTS', children: [
    { data: 'Context' },
    { data: 'Prompting' },
  ]},
  { data: 'FAILURE MODES', children: [
    { data: 'Hallucination' },
  ]},
];

// leavesOnly: false (default)
// → CONCEPTS, Context, Prompting, FAILURE MODES, Hallucination

// leavesOnly: true
// → Context, Prompting, Hallucination
```

## Why compass?

- Pure state management, no DOM dependencies
- Consumer decides what `T` is (strings, objects, DOM elements, etc.)
- Supports arbitrary nesting depth
- Portable to any language/runtime

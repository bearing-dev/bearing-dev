# teleport

Keyboard bindings that wire to compass navigators and apply state to DOM.

## Layers

```
Layer 1: Key bindings (pure functions)
Layer 2: DOM adapter (applies highlight class, scrollIntoView)
Layer 3: Full integration (batteries included)
```

## API

```typescript
// Layer 1: Keyboard event handling
interface KeyBindings {
  next?: string[];      // default ['j', 'ArrowDown']
  prev?: string[];      // default ['k', 'ArrowUp']
  select?: string[];    // default ['Enter']
  nextGroup?: string[]; // default [']]', 'Tab']
  prevGroup?: string[]; // default ['[[', 'Shift+Tab']
  escape?: string[];    // default ['Escape']
}

function createKeyboardHandler(config: {
  bindings?: KeyBindings;
  onNext?: () => void;
  onPrev?: () => void;
  onSelect?: () => void;
  onNextGroup?: () => void;
  onPrevGroup?: () => void;
  onEscape?: () => void;
  ignoreWhenTyping?: boolean;  // default true
}): {
  handleKeydown: (event: KeyboardEvent) => void;
  destroy: () => void;
};

// Layer 2: DOM adapter
function createDOMNavigator(config: {
  getItems: () => HTMLElement[];
  highlightClass?: string;     // default 'highlight'
  activeClass?: string;        // default 'active'
  scrollBehavior?: ScrollIntoViewOptions;
  onSelect?: (item: HTMLElement) => void;
}): {
  navigator: Navigator<HTMLElement>;
  highlight(index: number): void;
  clearHighlight(): void;
};

// Layer 3: Full integration
function initTeleport(config: {
  itemSelector: string;
  groupSelector?: string;
  highlightClass?: string;
  bindings?: KeyBindings;
  onSelect?: (item: HTMLElement) => void;
}): {
  destroy: () => void;
};
```

## Astro Integration

```astro
---
import { Teleport } from 'astro-teleport';
---
<Teleport
  itemSelector=".nav-item"
  groupSelector=".nav-section-title"
/>
```

# @sailkit/spyglass

Headless fuzzy finder for site search and sidebar filtering.

No UI opinions. Just the matching logic and state management. Bring your own input field and results list.

```typescript
import { createFinder } from '@sailkit/spyglass';

const finder = createFinder({
  items: [
    { id: 'intro', title: 'Introduction', keywords: ['start', 'begin'] },
    { id: 'install', title: 'Installation', keywords: ['setup', 'npm'] },
    { id: 'api', title: 'API Reference', keywords: ['docs', 'functions'] },
  ]
});

finder.search('instal');
// [{ id: 'install', title: 'Installation', score: 0.92 }]

finder.search('');
// [] - empty query returns nothing (or all, configurable)
```

## Why Headless?

Every site has different UI requirements:

- Modal command palette (⌘K)
- Inline sidebar filter
- Search page with URL state
- Autocomplete dropdown

Spyglass handles the hard part (fuzzy matching, ranking, keyboard selection state) while you control presentation.

## Integration with Sailkit

Designed to work with other Sailkit packages:

### With Compass

Filter navigation items from your Compass structure:

```typescript
import { flattenSlugs } from '@sailkit/compass';
import { createFinder } from '@sailkit/spyglass';

const slugs = flattenSlugs(navigation, true);
const finder = createFinder({
  items: slugs.map(slug => ({
    id: slug,
    title: getTitle(slug),
  }))
});
```

### With Teleport

Teleport emits `teleport:open-finder` when user presses `t`. Hook it up:

```typescript
document.addEventListener('teleport:open-finder', () => {
  openFinderModal(); // your UI
});
```

Use Spyglass selection state with keyboard nav:

```typescript
const finder = createFinder({ items });

// j/k in finder modal
finder.selectNext();
finder.selectPrev();

// Enter to navigate
const selected = finder.getSelected();
if (selected) {
  window.location.href = `/docs/${selected.id}/`;
}
```

## Features

### Fuzzy Matching

Matches characters in order, not necessarily adjacent:

```typescript
finder.search('api');
// Matches: "API Reference", "GraphQL API", "REST API Guide"

finder.search('gql');
// Matches: "GraphQL API" (g-q-l found in sequence)
```

### Ranking

Results ranked by:

1. Exact match (highest)
2. Prefix match
3. Word boundary match
4. Fuzzy match quality
5. Keyword matches

### Keyword Boosting

Add synonyms and related terms:

```typescript
const finder = createFinder({
  items: [
    {
      id: 'auth',
      title: 'Authentication',
      keywords: ['login', 'signin', 'oauth', 'jwt', 'session']
    }
  ]
});

finder.search('login');
// Returns Authentication even though "login" isn't in title
```

### Selection State

Built-in selection management:

```typescript
const finder = createFinder({ items });

finder.search('api');
finder.selectNext();     // Select first result
finder.selectNext();     // Select second
finder.selectPrev();     // Back to first
finder.getSelected();    // Get selected item
finder.clearSelection(); // Deselect
```

### Highlighting

Get match positions for highlighting:

```typescript
const results = finder.search('inst');
// [{
//   id: 'install',
//   title: 'Installation',
//   score: 0.92,
//   matches: [{ start: 0, end: 4 }]  // "Inst" in "Installation"
// }]
```

Render with highlights:

```typescript
function highlightMatches(title: string, matches: Match[]) {
  // Your highlighting logic
}
```

## Configuration

```typescript
const finder = createFinder({
  // Items to search
  items: [...],

  // Minimum score to include (0-1)
  threshold: 0.3,

  // Maximum results
  limit: 10,

  // Return all items on empty query
  showAllOnEmpty: false,

  // Case sensitive matching
  caseSensitive: false,

  // Custom scoring weights
  weights: {
    exact: 1.0,
    prefix: 0.9,
    wordBoundary: 0.8,
    fuzzy: 0.6,
    keyword: 0.7,
  }
});
```

## API

### `createFinder(config: FinderConfig): Finder`

Create a finder instance.

### `finder.search(query: string): SearchResult[]`

Search items and return ranked results.

### `finder.setItems(items: FinderItem[]): void`

Update searchable items (useful for dynamic content).

### `finder.selectNext(): void`

Move selection to next result.

### `finder.selectPrev(): void`

Move selection to previous result.

### `finder.getSelected(): SearchResult | null`

Get currently selected result.

### `finder.clearSelection(): void`

Clear selection state.

### `finder.getSelectedIndex(): number`

Get index of selected result (-1 if none).

## Types

```typescript
interface FinderItem {
  id: string;
  title: string;
  keywords?: string[];
  description?: string;
  url?: string;
  meta?: Record<string, unknown>;
}

interface SearchResult extends FinderItem {
  score: number;
  matches: Match[];
}

interface Match {
  field: 'title' | 'keywords' | 'description';
  start: number;
  end: number;
}

interface FinderConfig {
  items: FinderItem[];
  threshold?: number;
  limit?: number;
  showAllOnEmpty?: boolean;
  caseSensitive?: boolean;
  weights?: Partial<ScoringWeights>;
}
```

## Use Cases

- **Command palette**: ⌘K to open, fuzzy search all pages
- **Sidebar filter**: Type to filter navigation tree
- **Search page**: Full search with URL state
- **Autocomplete**: Dropdown suggestions as you type
- **Jump to definition**: IDE-style file/symbol navigation

## Philosophy

Search UI varies wildly. Search logic doesn't. Spyglass gives you the algorithm without the opinions.

## Status

This package is planned but not yet implemented. This README serves as the design specification.

---
title: Packages
description: Detailed documentation for each Sailkit package.
---

# Packages

Sailkit consists of five focused packages. Each solves one problem well and can be used independently or together.

## The Five Packages

### [[compass]] - Navigation State

A headless navigation state machine for nested content structures. Provides DFS traversal, prev/next neighbors, and parent/child navigation without any UI assumptions.

**Key features:**
- Stateless utility functions for SSG
- Runtime state machine for SPA navigation
- Wrap-around and leaves-only modes

### [[teleport]] - Keyboard Navigation

Vim-style keyboard bindings with DOM integration. Three layers of abstraction let you use as much or as little as you need.

**Key features:**
- `j`/`k` for item navigation
- `h`/`l` for page navigation
- Custom key bindings
- Automatic sidebar scrolling

### [[lantern]] - Theme Toggle

Dark mode done right. No flash of wrong theme, localStorage persistence, and reactive updates.

**Key features:**
- Flash prevention via inline script
- System preference detection
- Event subscription for reactive UI

### [[lighthouse]] - Smart 404

Fuzzy matching for broken URLs. Automatically redirects typos and shows suggestions for near-matches.

**Key features:**
- Levenshtein distance matching
- Token overlap scoring
- Configurable auto-redirect threshold
- Astro component included

### [[atlas]] - Magic Links

Wikipedia-style link syntax for markdown. Write `[[page]]` instead of `[page](/docs/page/)`.

**Key features:**
- Wiki syntax: `[[page]]` or `[[page|display text]]`
- Colon syntax: `[:page]` for alternative style
- Remark plugin for Astro/Next.js/etc.

## Package Dependencies

```
atlas ─────────────────┐
lighthouse ────────────┤
lantern ───────────────┼─► Your App
teleport ──────────────┤
compass ───────────────┘
```

No Sailkit package depends on another. Use any combination you need.

## Comparison Table

| Package | Build-time | Runtime | Astro Component | Headless |
|---------|------------|---------|-----------------|----------|
| [[compass]] | Yes | Yes | No | Yes |
| [[teleport]] | No | Yes | Yes | Partial |
| [[lantern]] | Partial | Yes | Yes | Yes |
| [[lighthouse]] | No | Yes | Yes | Yes |
| [[atlas]] | Yes | No | No | Yes |

Select a package from the sidebar to learn more.

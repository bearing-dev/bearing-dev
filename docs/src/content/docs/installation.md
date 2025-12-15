---
title: Installation
description: How to install Sailkit packages in your project.
---

# Installation

Sailkit packages are designed to be installed individually based on your needs.

## Package Manager

Install any combination of packages:

```bash
# Install individual packages
npm install @sailkit/compass
npm install @sailkit/teleport
npm install @sailkit/lantern
npm install @sailkit/lighthouse
npm install @sailkit/atlas

# Or install everything
npm install @sailkit/compass @sailkit/teleport @sailkit/lantern @sailkit/lighthouse @sailkit/atlas
```

## Framework Support

All packages work with any JavaScript framework. Additionally, some packages provide Astro components for even easier integration:

| Package | Astro Component |
|---------|-----------------|
| [[compass]] | Headless (no component) |
| [[teleport]] | `Teleport.astro` |
| [[lantern]] | `ThemeToggle.astro` |
| [[lighthouse]] | `NotFound.astro` |
| [[atlas]] | Remark plugin (no component) |

## TypeScript

All packages include TypeScript definitions. No additional `@types` packages needed.

```typescript
// Types are exported from each package
import type { NavItem, Navigator } from '@sailkit/compass';
import type { KeyBindings, Teleport } from '@sailkit/teleport';
import type { Theme } from '@sailkit/lantern';
import type { Page, ScoredPage, Matcher } from '@sailkit/lighthouse';
import type { RemarkMagicLinksConfig, LinkSyntax } from '@sailkit/atlas';
```

## Peer Dependencies

Sailkit packages have minimal dependencies:

- [[atlas]] requires `mdast-util-from-markdown` (part of remark ecosystem)
- All other packages are dependency-free

## Next Steps

Now that you have Sailkit installed, check out the [[quick-start]] guide to see how the packages work together.

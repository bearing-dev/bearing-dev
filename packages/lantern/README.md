# lantern

Theme toggle with correct hydration (no flash of wrong theme).

## What Ships

```
lantern/
├── core.ts              # initTheme, toggleTheme, getTheme, onThemeChange
├── ThemeToggle.astro    # Drop-in component
├── theme-base.css       # CSS variable structure
├── theme-dark.css       # Default dark palette
└── theme-light.css      # Default light palette
```

## API

```typescript
function initTheme(): 'light' | 'dark';
function getTheme(): 'light' | 'dark';
function setTheme(theme: 'light' | 'dark'): void;
function toggleTheme(): 'light' | 'dark';
function onThemeChange(callback: (theme: 'light' | 'dark') => void): () => void;

// Convention:
// - Stores in localStorage key: 'theme'
// - Sets data-theme attribute on <html>
// - CSS uses [data-theme="dark"] selectors
```

## Usage Levels

```astro
// Full (component + all styles)
---
import { ThemeToggle } from 'astro-lantern';
import 'astro-lantern/theme-base.css';
import 'astro-lantern/theme-dark.css';
import 'astro-lantern/theme-light.css';
---
<ThemeToggle />

// BYO styles
---
import { ThemeToggle } from 'astro-lantern';
import 'astro-lantern/theme-base.css';
import './my-themes.css';
---
<ThemeToggle />

// BYO button
---
import { initTheme, toggleTheme } from 'astro-lantern/core';
---
<script>initTheme();</script>
<button onclick="toggleTheme()">Toggle</button>
```

## Flash Prevention

The component includes an inline script that runs before paint:

```html
<script is:inline>
  (function() {
    const stored = localStorage.getItem('theme');
    document.documentElement.setAttribute('data-theme', stored || 'dark');
  })();
</script>
```

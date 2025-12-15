# @sailkit/scribe

Extract and test code from markdown documentation.

Prevent documentation rot by programmatically verifying that code examples actually work. Scribe extracts code blocks from markdown files, executes them, and reports failures.

```typescript
import { extractCodeBlocks, testCodeBlocks } from '@sailkit/scribe';

// Extract all code blocks from markdown
const blocks = await extractCodeBlocks('./docs/**/*.md');

// Test them
const results = await testCodeBlocks(blocks);
results.forEach(result => {
  if (!result.passed) {
    console.error(`Failed: ${result.file}:${result.line}`);
    console.error(result.error);
  }
});
```

## Problem

Documentation code examples drift out of sync with actual APIs. AI assistants and humans alike write examples that look plausible but don't compile or run correctly. Traditional testing ignores documentation entirely.

## Solution

Scribe treats code fences as testable units:

```typescript
const blocks = await extractCodeBlocks('README.md');
// [
//   { code: 'const x = 1;', lang: 'typescript', line: 15, file: 'README.md' },
//   { code: 'npm install foo', lang: 'bash', line: 28, file: 'README.md' }
// ]
```

## Features

### Language-aware execution

Different strategies for different code blocks:

- **TypeScript/JavaScript**: Transpile and execute, check for runtime errors
- **Bash**: Execute in shell, verify exit code
- **JSON**: Parse and validate structure
- **Custom**: Register your own runners

### Skip patterns

Mark blocks that shouldn't be tested:

````markdown
```typescript skip
// This example is intentionally incomplete
const partial =
```
````

Or use comments:

```typescript
// scribe:skip - requires external service
fetch('https://api.example.com');
```

### Import resolution

Handle relative imports in examples:

```typescript
// In your docs:
import { foo } from '../src/foo';

// Scribe resolves relative to markdown file location
```

### Snapshot testing

Verify output matches expected:

````markdown
```typescript
console.log(1 + 1);
// Output: 2
```
````

### CI Integration

```bash
npx scribe test ./docs/**/*.md --fail-fast
```

## Configuration

```typescript
import { createScribe } from '@sailkit/scribe';

const scribe = createScribe({
  // File patterns to include
  include: ['**/*.md'],

  // Patterns to exclude
  exclude: ['**/node_modules/**'],

  // Custom language runners
  runners: {
    sql: async (code) => {
      // Custom SQL validation
    }
  },

  // Timeout per block (ms)
  timeout: 5000,

  // Continue on failure
  failFast: false,
});

const results = await scribe.test();
```

## API

### `extractCodeBlocks(patterns: string | string[]): Promise<CodeBlock[]>`

Extract all code blocks from matching files.

### `testCodeBlocks(blocks: CodeBlock[], options?: TestOptions): Promise<TestResult[]>`

Execute code blocks and return results.

### `createScribe(config: ScribeConfig): Scribe`

Create a configured scribe instance with custom runners and options.

## Types

```typescript
interface CodeBlock {
  code: string;
  lang: string;
  file: string;
  line: number;
  skip?: boolean;
  meta?: string;
}

interface TestResult {
  block: CodeBlock;
  passed: boolean;
  error?: Error;
  stdout?: string;
  duration: number;
}
```

## Use Cases

- **CI pipelines**: Fail builds when examples break
- **Pre-commit hooks**: Catch issues before merge
- **Documentation audits**: Find stale examples across a codebase
- **Tutorial validation**: Ensure learning materials work

## Philosophy

Documentation is code. Code should be tested. Therefore documentation should be tested.

## Status

This package is planned but not yet implemented. This README serves as the design specification.

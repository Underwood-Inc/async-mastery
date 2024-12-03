# Getting Started

## Installation

```bash
# Clone the repository
git clone https://github.com/your-org/async-patterns
cd async-patterns

# Install dependencies
npm install
```

## Quick Setup

```typescript
// src/index.ts
import { withRetry } from './patterns/auto-retry';
import { Memoizer } from './patterns/memoization';
import { PerformanceMonitor } from './monitoring/performance';

// Initialize performance monitoring
const monitor = PerformanceMonitor.getInstance();
monitor.addListener((metrics) => {
  if (metrics.duration! > 1000) {
    console.warn(`Slow operation detected: ${metrics.operationName}`);
  }
});

// Create a memoized API client with retry logic
const api = new Memoizer({
  maxAge: 60000, // Cache for 1 minute
  maxSize: 100, // Store max 100 responses
});

// Example usage
async function fetchUserData(userId: string) {
  return api.memoize(
    () =>
      withRetry(
        async () => {
          const response = await fetch(`/api/users/${userId}`);
          if (!response.ok) {
            throw new Error(`HTTP ${response.status}`);
          }
          return response.json();
        },
        {
          maxAttempts: 3,
          onRetry: (error, attempt) => {
            console.warn(`Retry ${attempt} after error:`, error);
          },
        }
      ),
    userId
  );
}
```

## Project Structure

```text
async-patterns/
├── src/
│   ├── implementations/    # Core Promise implementations
│   ├── patterns/          # Async patterns
│   ├── advanced/          # Advanced features
│   ├── monitoring/        # Performance monitoring
│   └── utils/            # Utility functions
├── tests/                # Test files
├── docs/                 # Documentation
└── examples/            # Example usage
```

## Development Setup

1. **TypeScript Configuration**

```json
{
  "compilerOptions": {
    "strict": true,
    "target": "ES2020",
    "module": "ESNext",
    "moduleResolution": "node",
    "esModuleInterop": true,
    "declaration": true,
    "outDir": "./dist"
  },
  "include": ["src"],
  "exclude": ["node_modules", "tests"]
}
```

1. **Testing Setup**

```typescript
// tests/setup.ts
import { PerformanceMonitor } from '../src/monitoring/performance';

beforeEach(() => {
  PerformanceMonitor.getInstance().clearMetrics();
});
```

1. **Basic Usage Examples**

```typescript
import { withRetry } from './patterns/auto-retry';
import { errorHandlers } from './advanced/error-handling';

const fetchWithRetry = (url: string) =>
  errorHandlers.withTimeout(
    () => withRetry(() => fetch(url), { maxAttempts: 3 }),
    5000,
    'fetchWithRetry'
  );
```

## Next Steps

1. Check out the [API Documentation](../api/index.md)
2. Explore the [Examples](../examples/index.md)
3. Learn about [Advanced Patterns](../advanced/index.md)
4. Review [Performance Monitoring](../advanced/performance-monitoring.md)
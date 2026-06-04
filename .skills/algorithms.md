# Antigravity Skill: Algorithms & Performance Logic
# Priority: MEDIUM | Impact: 7.5/10 | Rating: ⭐⭐⭐⭐

## ACTIVATION
Load when: writing data processing, search, filtering, sorting, or any hot-path logic.

---

## CORE RULE
> Measure before optimizing. Profile before refactoring.
> But enforce Big-O budgets from the start on hot paths — fixing O(n²) later is expensive.

---

## COMPLEXITY BUDGETS BY LAYER

```
UI Rendering (hot path):      O(n) maximum — list rendering, filtering
API Request Handler:          O(n log n) maximum
Background Job / Batch:       O(n²) acceptable with small n
Search / Autocomplete:        O(log n) — use indexes, never full scan
Cache Lookup:                 O(1) — always hash-based
Database Queries:             O(log n) with proper indexing
```

---

## CRITICAL ALGORITHMS TO KNOW

### Debounce & Throttle (UI Performance)
```typescript
// Debounce: wait until user stops typing
function debounce<T extends (...args: any[]) => any>(fn: T, delay: number) {
  let timer: ReturnType<typeof setTimeout>;
  return (...args: Parameters<T>) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
}

// Throttle: limit execution rate
function throttle<T extends (...args: any[]) => any>(fn: T, limit: number) {
  let lastRun = 0;
  return (...args: Parameters<T>) => {
    const now = Date.now();
    if (now - lastRun >= limit) {
      lastRun = now;
      return fn(...args);
    }
  };
}

// Usage:
const debouncedSearch = debounce(handleSearch, 300);  // 300ms for search
const throttledScroll = throttle(handleScroll, 100);   // 100ms for scroll
```

### Virtual Scrolling (Lists > 100 Items)
```typescript
// Never render all items — only render visible viewport
// Use: @tanstack/virtual or react-window

import { useVirtualizer } from '@tanstack/react-virtual';

function VirtualList({ items }: { items: Item[] }) {
  const parentRef = useRef<HTMLDivElement>(null);
  const virtualizer = useVirtualizer({
    count: items.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => 60,  // Estimated row height in px
  });

  return (
    <div ref={parentRef} style={{ height: '600px', overflow: 'auto' }}>
      <div style={{ height: virtualizer.getTotalSize() }}>
        {virtualizer.getVirtualItems().map((virtualItem) => (
          <div
            key={virtualItem.key}
            style={{ transform: `translateY(${virtualItem.start}px)` }}
          >
            <ListItem item={items[virtualItem.index]} />
          </div>
        ))}
      </div>
    </div>
  );
}
```

### Memoization (Pure Function Caching)
```typescript
// Simple in-memory memoize
function memoize<T extends (...args: any[]) => any>(fn: T): T {
  const cache = new Map<string, ReturnType<T>>();
  return ((...args: Parameters<T>) => {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key)!;
    const result = fn(...args);
    cache.set(key, result);
    return result;
  }) as T;
}

// React: useMemo for expensive computations
const filteredAndSorted = useMemo(() => {
  return items
    .filter(item => item.category === selectedCategory)
    .sort((a, b) => a.name.localeCompare(b.name));
}, [items, selectedCategory]);
```

### Cursor-Based Pagination (Scale > Offset)
```typescript
// Offset pagination breaks at scale (OFFSET 10000 is slow)
// Cursor pagination is O(log n) always

// Bad (offset):
const items = await db.findMany({ skip: page * limit, take: limit });

// Good (cursor):
const items = await db.findMany({
  take: limit + 1,               // Fetch one extra to check if there's a next page
  cursor: cursor ? { id: cursor } : undefined,
  orderBy: { createdAt: 'desc' },
});

const hasNextPage = items.length > limit;
const nextCursor = hasNextPage ? items[limit - 1].id : null;
return { items: items.slice(0, limit), nextCursor };
```

### Circuit Breaker (Fault Tolerance)
```typescript
class CircuitBreaker {
  private failures = 0;
  private lastFailure: number | null = null;
  private state: 'closed' | 'open' | 'half-open' = 'closed';

  constructor(
    private readonly threshold = 5,      // Open after 5 failures
    private readonly timeout = 60_000,   // Try again after 60s
  ) {}

  async execute<T>(fn: () => Promise<T>): Promise<T> {
    if (this.state === 'open') {
      if (Date.now() - this.lastFailure! > this.timeout) {
        this.state = 'half-open';
      } else {
        throw new Error('Circuit breaker is OPEN — service unavailable');
      }
    }

    try {
      const result = await fn();
      if (this.state === 'half-open') {
        this.state = 'closed';
        this.failures = 0;
      }
      return result;
    } catch (error) {
      this.failures++;
      this.lastFailure = Date.now();
      if (this.failures >= this.threshold) this.state = 'open';
      throw error;
    }
  }
}
```

---

## DATABASE PERFORMANCE RULES

```sql
-- ALWAYS index foreign keys
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_created_at ON orders(created_at DESC);

-- Composite index for common query patterns
CREATE INDEX idx_products_category_price ON products(category_id, price);

-- Partial index for filtered queries
CREATE INDEX idx_active_users ON users(email) WHERE is_active = true;

-- Never do N+1 queries — use JOIN or eager loading
-- Bad: For each user, query orders separately
-- Good: JOIN users with orders in one query
SELECT u.*, o.id as order_id, o.total
FROM users u
LEFT JOIN orders o ON o.user_id = u.id
WHERE u.is_active = true;
```

---

## CACHING STRATEGIES

### Cache-Aside (Read-Heavy Data)
```typescript
async function getUser(userId: string): Promise<User> {
  // 1. Try cache first
  const cached = await redis.get(`user:${userId}`);
  if (cached) return JSON.parse(cached);

  // 2. Cache miss — fetch from DB
  const user = await db.user.findUnique({ where: { id: userId } });
  if (!user) throw new NotFoundError('User not found');

  // 3. Populate cache (TTL: 5 minutes)
  await redis.setex(`user:${userId}`, 300, JSON.stringify(user));

  return user;
}

// Invalidate on update
async function updateUser(userId: string, data: Partial<User>) {
  await db.user.update({ where: { id: userId }, data });
  await redis.del(`user:${userId}`);  // Bust cache
}
```

---

## WORLD-CLASS REFERENCES
- javascript-algorithms: https://github.com/trekhleb/javascript-algorithms (⭐ 189k)
- system-design-primer: https://github.com/donnemartin/system-design-primer (⭐ 283k)
- the-algorithms: https://github.com/TheAlgorithms/Python (⭐ 200k)
- awesome-scalability: https://github.com/binhnguyennus/awesome-scalability (⭐ 58k)

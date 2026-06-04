# Antigravity Skill: Backend Architecture
# Priority: CRITICAL | Impact: 10/10 | Rating: ⭐⭐⭐⭐⭐

## ACTIVATION
Load when: building any API, any service, any database operation, any queue system.

---

## CORE RULE
> Separate business logic from side effects and infrastructure — always.
> The domain layer must have ZERO third-party dependencies.

---

## ARCHITECTURE: Clean / Hexagonal (Ports & Adapters)

```
┌─────────────────────────────────────────────────┐
│  Transport Layer (HTTP / GraphQL / gRPC)         │
│  Route handlers, controllers — thin layer only   │
├─────────────────────────────────────────────────┤
│  Application Layer                               │
│  Use-cases, command/query handlers               │
│  Orchestrates domain objects, calls ports        │
├─────────────────────────────────────────────────┤
│  Domain Layer                                    │
│  Entities, Value Objects, Domain Events          │
│  ZERO third-party dependencies here              │
├─────────────────────────────────────────────────┤
│  Infrastructure Layer                            │
│  DB implementations, queues, external APIs       │
│  Implements domain ports (interfaces)            │
└─────────────────────────────────────────────────┘
```

---

## PATTERNS TO ENFORCE

### CQRS (Command Query Responsibility Segregation)
```typescript
// Commands mutate state — return void or ID
class CreateOrderCommand {
  constructor(
    public readonly userId: string,
    public readonly items: OrderItem[]
  ) {}
}

// Queries read state — return data
class GetOrdersByUserQuery {
  constructor(public readonly userId: string) {}
}
```

### Repository Pattern
```typescript
// Port (interface) in domain layer
interface UserRepository {
  findById(id: string): Promise<User | null>;
  save(user: User): Promise<void>;
  delete(id: string): Promise<void>;
}

// Adapter (implementation) in infrastructure layer
class PrismaUserRepository implements UserRepository {
  // Concrete DB implementation here
}
```

### Outbox Pattern (Guaranteed Event Delivery)
```
Every DB write that triggers an event:
1. Write to main table + write to outbox table (same transaction)
2. Outbox processor reads pending events
3. Publishes to message broker
4. Marks as processed
→ Eliminates dual-write bug (event lost if app crashes after DB write)
```

---

## ENFORCEMENT RULES

### API Design
```
✅ REST APIs follow OpenAPI 3.1 spec — generate spec from code
✅ GraphQL APIs are schema-first — define schema before resolvers
✅ API versioning: /api/v1/ prefix for all breaking changes
✅ Consistent error response format:
   { error: { code: string, message: string, details?: any } }
✅ Idempotency keys for all mutation endpoints
✅ Pagination: cursor-based for large datasets
✅ Rate limiting headers on all responses (X-RateLimit-*)

❌ NEVER return 200 with error in body
❌ NEVER expose internal error messages to clients
❌ NEVER use GET for operations that change state
```

### Database
```
✅ All queries through repository pattern — no raw SQL in business logic
✅ Database indexes on all foreign keys and frequently queried columns
✅ Migrations are versioned, reviewed, and include rollback scripts
✅ Connection pooling configured (Prisma Accelerate / PgBouncer)
✅ Read replicas for read-heavy workloads
✅ Redis Cache-Aside for hot read paths
✅ Row-Level Security (RLS) in Postgres for multi-tenant data

❌ NEVER run migrations without a tested rollback
❌ NEVER do N+1 queries — use eager loading or DataLoader
❌ NEVER store secrets or PII in logs
```

### Queue & Events
```
✅ All async operations go through a queue (BullMQ / Kafka)
✅ Dead letter queue for all failed jobs
✅ Job retry with exponential backoff (max 5 retries)
✅ Idempotent job handlers (safe to re-run)
✅ Event schema versioned and backward-compatible
```

---

## SERVICE FOLDER STRUCTURE
```
services/auth-service/
├── src/
│   ├── domain/             # Zero external dependencies
│   │   ├── user.entity.ts
│   │   ├── user.repository.ts     # Interface (port)
│   │   └── user.events.ts
│   ├── application/        # Use cases
│   │   ├── commands/
│   │   │   └── create-user.command.ts
│   │   └── queries/
│   │       └── get-user.query.ts
│   ├── infrastructure/     # DB, queues, external APIs
│   │   ├── prisma-user.repository.ts
│   │   └── kafka-event-publisher.ts
│   └── transport/          # HTTP routes, GraphQL resolvers
│       └── auth.controller.ts
├── prisma/
│   └── schema.prisma
└── package.json
```

---

## WORLD-CLASS STACK
- nestjs: https://github.com/nestjs/nest (⭐ 69k)
- trpc: https://github.com/trpc/trpc (⭐ 36k)
- drizzle-orm: https://github.com/drizzle-team/drizzle-orm (⭐ 27k)
- prisma: https://github.com/prisma/prisma (⭐ 41k)
- bullmq: https://github.com/taskforcesh/bullmq (⭐ 6k)
- hono: https://github.com/honojs/hono (⭐ 22k)
- graphql-yoga: https://github.com/dotansimha/graphql-yoga (⭐ 8k)

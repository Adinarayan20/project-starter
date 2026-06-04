# Antigravity Skill: Flow, Pipelines & DevOps
# Priority: MEDIUM | Impact: 7.5/10 | Rating: ⭐⭐⭐⭐

## ACTIVATION
Load when: setting up CI/CD, deployment pipelines, event flows, or background job systems.

---

## CORE RULE
> Reliability is not a feature. It's architecture.
> Every deployment must be automated, verified, and reversible.

---

## STANDARD CI/CD PIPELINE

```yaml
# .github/workflows/ci.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  quality:
    name: Code Quality
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '22', cache: 'pnpm' }
      - run: pnpm install --frozen-lockfile

      # FAIL FAST — these run in parallel
      - run: pnpm lint          # ESLint + Prettier check
      - run: pnpm typecheck     # TypeScript strict mode
      - run: pnpm test:unit     # Unit tests with Vitest

  integration:
    name: Integration Tests
    needs: quality
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:16
        env: { POSTGRES_PASSWORD: test }
      redis:
        image: redis:7
    steps:
      - run: pnpm test:integration

  build:
    name: Build
    needs: quality
    runs-on: ubuntu-latest
    steps:
      - run: pnpm build
      - run: pnpm lighthouse:ci   # Core Web Vitals gate — fails if score < 90
      - run: pnpm security:scan   # Snyk vulnerability scan

  deploy-staging:
    name: Deploy to Staging
    needs: [integration, build]
    if: github.ref == 'refs/heads/develop'
    steps:
      - run: pnpm deploy:staging
      - run: pnpm test:e2e:staging  # Playwright E2E on staging

  deploy-production:
    name: Deploy to Production
    needs: deploy-staging
    if: github.ref == 'refs/heads/main'
    environment: production       # Requires manual approval
    steps:
      - run: pnpm deploy:production --strategy=blue-green
      - run: pnpm test:smoke:production
      # Auto-rollback triggered if error rate > 1% in 5 minutes
```

---

## DEPLOYMENT STRATEGIES

### Blue-Green Deployment
```
Current state: BLUE (live) ← 100% traffic
New version:   GREEN (idle)

Steps:
  1. Deploy new version to GREEN environment
  2. Run smoke tests on GREEN
  3. Switch load balancer: 100% traffic → GREEN
  4. Monitor error rates for 5 minutes
  5. If OK → decomission BLUE
     If errors → switch traffic back to BLUE instantly (< 30s rollback)
```

### Canary Release
```
Steps:
  1. Deploy new version to 5% of servers
  2. Send 5% of traffic to new version
  3. Monitor: error rate, latency, business metrics
  4. Gradually increase: 5% → 25% → 50% → 100%
  5. Rollback trigger: error rate > 1% or latency P95 > 2x baseline
```

---

## EVENT-DRIVEN FLOW ARCHITECTURE

### Event Bus Pattern
```typescript
// Define all domain events with versioned schemas
interface DomainEvent<T = unknown> {
  id: string;                    // UUID
  type: string;                  // e.g., 'user.created'
  version: '1.0' | '2.0';       // Schema version
  timestamp: string;             // ISO 8601
  correlationId: string;         // Trace across services
  payload: T;
}

// Strongly typed events
type UserCreatedEvent = DomainEvent<{
  userId: string;
  email: string;
  plan: 'free' | 'pro';
}>;

// Publisher
await eventBus.publish<UserCreatedEvent>({
  type: 'user.created',
  version: '1.0',
  payload: { userId, email, plan },
});

// Subscriber (idempotent — safe to process twice)
eventBus.subscribe('user.created', async (event: UserCreatedEvent) => {
  // Use event.id for deduplication
  const alreadyProcessed = await redis.get(`processed:${event.id}`);
  if (alreadyProcessed) return;

  await sendWelcomeEmail(event.payload.email);
  await redis.set(`processed:${event.id}`, '1', 'EX', 86400);
});
```

---

## BACKGROUND JOBS (BullMQ)

```typescript
// Define job queue
const emailQueue = new Queue('email', { connection: redis });
const emailWorker = new Worker('email', async (job) => {
  switch (job.name) {
    case 'welcome':
      await sendWelcomeEmail(job.data.email);
      break;
    case 'invoice':
      await sendInvoiceEmail(job.data);
      break;
  }
}, {
  connection: redis,
  concurrency: 10,
});

// Add jobs with retry + backoff
await emailQueue.add('welcome', { email: user.email }, {
  attempts: 5,
  backoff: { type: 'exponential', delay: 2000 },  // 2s, 4s, 8s, 16s, 32s
  removeOnComplete: 100,    // Keep last 100 completed jobs for debugging
  removeOnFail: 500,        // Keep last 500 failed jobs for analysis
});

// Dead letter queue monitoring
emailQueue.on('failed', async (job, error) => {
  if (job?.attemptsMade >= job?.opts.attempts!) {
    await alertingService.notify({
      message: `Job ${job?.name} failed permanently`,
      error: error.message,
      jobData: job?.data,
    });
  }
});
```

---

## OBSERVABILITY STACK

### OpenTelemetry (Unified Traces + Metrics + Logs)
```typescript
import { NodeSDK } from '@opentelemetry/sdk-node';
import { getNodeAutoInstrumentations } from '@opentelemetry/auto-instrumentations-node';

const sdk = new NodeSDK({
  traceExporter: new OTLPTraceExporter({ url: process.env.OTEL_EXPORTER_URL }),
  instrumentations: [getNodeAutoInstrumentations()],
});
sdk.start();

// Custom spans for business operations
async function processPayment(orderId: string) {
  return tracer.startActiveSpan('payment.process', async (span) => {
    span.setAttribute('order.id', orderId);
    try {
      const result = await stripe.charge(orderId);
      span.setAttribute('payment.status', 'success');
      return result;
    } catch (error) {
      span.recordException(error as Error);
      span.setStatus({ code: SpanStatusCode.ERROR });
      throw error;
    } finally {
      span.end();
    }
  });
}
```

### Alerting Rules
```
Alert immediately on:
  ├── Error rate > 1% in any 5-minute window
  ├── P95 latency > 2x baseline
  ├── Queue depth > 10,000 jobs
  ├── Database connection pool > 80% utilized
  ├── Memory usage > 90%
  └── Any failed deployment

Alert daily digest:
  ├── Failed job count by queue
  ├── API endpoints with highest error rates
  ├── Slowest database queries
  └── Cost trends (cloud spend, LLM tokens)
```

---

## DURABLE WORKFLOW (Temporal)
```typescript
// For long-running, multi-step processes that must survive crashes
import { proxyActivities, sleep } from '@temporalio/workflow';

// Workflow definition (runs durably — survives server restarts)
export async function onboardingWorkflow(userId: string) {
  const { sendEmail, createAccount, provisionResources } = proxyActivities({
    startToCloseTimeout: '5 minutes',
    retry: { maximumAttempts: 3 },
  });

  // Step 1: Create account
  await createAccount(userId);

  // Step 2: Wait 5 minutes then send welcome email
  await sleep('5 minutes');
  await sendEmail({ type: 'welcome', userId });

  // Step 3: Provision resources
  await provisionResources(userId);

  // Step 4: Wait 24 hours then send activation reminder
  await sleep('24 hours');
  await sendEmail({ type: 'activation-reminder', userId });
}
// This workflow survives server crashes, network failures, deploys
```

---

## WORLD-CLASS STACK
- temporal: https://github.com/temporalio/temporal (⭐ 13k)
- inngest: https://github.com/inngest/inngest (⭐ 4k)
- argo-cd: https://github.com/argoproj/argo-cd (⭐ 19k)
- opentelemetry-js: https://github.com/open-telemetry/opentelemetry-js (⭐ 3k)
- turbo: https://github.com/vercel/turborepo (⭐ 26k)
- playwright: https://github.com/microsoft/playwright (⭐ 68k)

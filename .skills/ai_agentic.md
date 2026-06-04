# Antigravity Skill: AI & Agentic Flows
# Priority: HIGH | Impact: 9/10 | Rating: ⭐⭐⭐⭐½

## ACTIVATION
Load when: integrating any LLM, building AI features, creating agent workflows, or implementing RAG.

---

## CORE RULE
> The best AI systems are not just smart — they are reliable, cost-efficient, and observable.
> Every LLM call must be: typed, validated, cost-bounded, and logged.

---

## AGENTIC DESIGN PATTERNS

### Plan-Act-Reflect (Multi-Step Tasks)
```typescript
// Agent loop pattern
async function agentLoop(task: string) {
  let state = { plan: null, steps: [], result: null };

  // Phase 1: Plan
  state.plan = await llm.plan(task);

  // Phase 2: Act (execute each step)
  for (const step of state.plan.steps) {
    const toolResult = await executeTool(step.tool, step.params);
    state.steps.push({ step, result: toolResult });
  }

  // Phase 3: Reflect (self-evaluate and retry if needed)
  const reflection = await llm.reflect(state);
  if (!reflection.satisfied) {
    return agentLoop(task); // retry with context
  }

  return state.result;
}
```

### RAG (Retrieval-Augmented Generation)
```
Pipeline:
  1. User query → embedding model → query vector
  2. Query vector → vector DB (pgvector / Qdrant) → top-K chunks
  3. Chunks + user query → LLM → grounded answer

Rules:
  ✅ Chunk size: 256-512 tokens with 50-token overlap
  ✅ Embed document title in every chunk for context
  ✅ Re-rank results before sending to LLM (cohere-rerank or cross-encoder)
  ✅ Filter retrieved chunks by relevance score threshold (> 0.7)
  ✅ Always cite source documents in response
  ❌ NEVER send all retrieved docs — top 3-5 highest-relevance only
```

### Tool-Calling
```typescript
// Define tools with strict Zod schemas
const tools = [
  {
    name: 'search_database',
    description: 'Search the product database for items matching the query',
    parameters: z.object({
      query: z.string().min(1).max(200),
      category: z.enum(['electronics', 'clothing', 'food']).optional(),
      limit: z.number().int().min(1).max(20).default(5),
    }),
    execute: async (params) => { /* implementation */ },
  },
];

// Validate all tool outputs before using them
const toolOutput = await tools[0].execute(params);
const validated = ToolOutputSchema.parse(toolOutput); // Always validate
```

### Semantic Caching
```typescript
// Cache LLM responses by semantic similarity
async function cachedLLMCall(query: string) {
  const queryEmbedding = await embed(query);

  // Check cache: find semantically similar cached query
  const cached = await vectorDB.findSimilar(queryEmbedding, {
    threshold: 0.95,  // High threshold — only near-identical queries
    table: 'llm_cache',
  });

  if (cached) {
    return cached.response;  // Return cached response, save tokens
  }

  const response = await llm.complete(query);

  // Cache the result
  await vectorDB.insert({ embedding: queryEmbedding, response, query });

  return response;
}
```

---

## ENFORCEMENT RULES

### Every LLM Call Must Have:
```typescript
const response = await llm.complete({
  model: 'gpt-4o-mini',              // ✅ Explicit model — never use default
  messages: [...],
  temperature: 0.2,                  // ✅ Low temperature for factual tasks
  max_tokens: 500,                   // ✅ Always set a cap — never unlimited
  response_format: { type: 'json_object' }, // ✅ Structured output when possible
  timeout: 30_000,                   // ✅ 30s timeout — never hang indefinitely
});

// ✅ Always validate LLM output
const validated = OutputSchema.safeParse(JSON.parse(response.content));
if (!validated.success) {
  // Handle malformed output — retry or fallback
}
```

### Cost Control
```
✅ Track token usage per request (input + output)
✅ Set hard monthly budget limits per API key
✅ Use smaller models for simple tasks (gpt-4o-mini > gpt-4o for classification)
✅ Implement semantic caching for repeated queries
✅ Compress context before sending (summarize conversation history)
✅ Batch similar requests where API supports it
✅ Log every LLM call: model, tokens, cost, latency, user_id

Alert when:
  - Single request > 10,000 tokens
  - Daily cost > 80% of budget
  - Error rate > 5% in any 5-minute window
```

### Observability
```typescript
// Every LLM call must be traced
const trace = langfuse.trace({
  name: 'rag-query',
  userId: user.id,
  metadata: { query, retrieved_docs: docs.length },
});

const span = trace.span({ name: 'llm-complete' });
const response = await llm.complete(prompt);
span.end({ output: response, usage: response.usage });

// Log: model, tokens, latency, cost, quality_score
```

### Guardrails
```
✅ Input: validate and sanitize all user inputs before LLM
✅ Output: validate schema of all LLM responses (Zod/Pydantic)
✅ Content: filter harmful/policy-violating outputs before returning
✅ PII: scrub personal data from prompts sent to third-party APIs
✅ Prompt injection: detect and block attempts to override system prompts
```

---

## MEMORY SYSTEMS

```
Short-term (within session):
  → Message history in context window
  → Compress with rolling summary after N messages

Long-term (across sessions):
  → Vector DB: semantic search over past interactions
  → Key-value: explicit facts (user preferences, settings)

Entity memory (knowledge graph):
  → Track named entities (people, companies, projects) and their relationships
  → Update on every interaction, retrieve on demand
```

---

## WORLD-CLASS STACK
- langgraph: https://github.com/langchain-ai/langgraph (⭐ 11k)
- vercel-ai-sdk: https://github.com/vercel/ai (⭐ 14k)
- mastra: https://github.com/mastra-ai/mastra (⭐ 12k)
- semantic-kernel: https://github.com/microsoft/semantic-kernel (⭐ 24k)
- pgvector: https://github.com/pgvector/pgvector (⭐ 14k)
- qdrant: https://github.com/qdrant/qdrant (⭐ 22k)
- langfuse: https://github.com/langfuse/langfuse (⭐ 7k) — observability

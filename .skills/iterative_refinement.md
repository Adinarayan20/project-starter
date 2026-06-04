# Antigravity Skill: Iterative Refinement
# Priority: CRITICAL | Impact: 10/10 | Rating: ⭐⭐⭐⭐⭐

## ACTIVATION
Load on: every single task, feature addition, refactoring process, or bug fix.

---

## CORE RULE
> No complex feature is built in a single turn. 
> Break every task down into iterative cycles of creation, critique, refinement, and validation.

---

## THE REFRACTORY LIFECYCLE (THE 4 LAWS)
For every development task, the AI must strictly follow these four steps sequentially:

```
Draft/Foundation ──> Stress-Test/Critique ──> Refine/Optimize ──> Verify/Validate
```

### 1. Draft & Foundation (Establish the Core)
* **Goal:** Write the most basic, functional version first.
* **Rules:** Focus on getting the happy-path working. Avoid premature optimizations or over-engineering. Define data structures and core functions clearly.

### 2. Stress-Test & Critique (Find the Flaws)
* **Goal:** Act as a reviewer to identify weaknesses.
* **Review Checklist:**
  * **Scalability:** Will this scale to millions of users? Is database query efficiency O(1) or O(log n)?
  * **Performance:** Are there memory leaks, unnecessary re-renders, or synchronous bottlenecks?
  * **Security:** Are inputs validated? Are variables parameterized? Are secrets exposed?
  * **UX/Aesthetics:** Does the UI feel responsive, and are touch targets at least 48px?

### 3. Refine & Optimize (Polishing the Output)
* **Goal:** Rewrite the code based on Step 2 findings.
* **Rules:**
  * Apply HSL design tokens, glassmorphism, and responsive media queries.
  * Inject error boundaries, input schemas (Zod), and security headers.
  * Implement caching structures and queueing patterns where needed.

### 4. Verify & Validate (Verify Behavior)
* **Goal:** Guarantee correct operation under boundary states.
* **Rules:** Write unit/integration tests and run the compiler/build to ensure zero errors.

---

## HUMAN-IN-THE-LOOP INTERACTION
* **Early Stops:** Do not write a 1,000-line implementation without showing the architectural design or schema draft first. Stop and request user feedback.
* **Modular Approval:** Ask for approval on the database schema and API endpoints before writing the frontend components or transport handlers.

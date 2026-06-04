# Antigravity Skill: Testing & Verification
# Priority: HIGH | Impact: 9/10 | Rating: ⭐⭐⭐⭐⭐

## ACTIVATION
Load when: writing test files, reviewing pull requests, setting up CI/CD pipelines, or verifying code changes before rollout.

---

## CORE RULE
> Code is only as good as its verifiability. 
> Untested code is deprecated code. Write tests to guarantee behavior under failure.

---

## TESTING STACK (STANDARD)
* **Unit Testing:** Vitest (React/Node.js) or Jest (standard).
* **Component Testing:** React Testing Library + Vitest.
* **API/Integration Testing:** Supertest (Node.js) or MSW (Mock Service Worker) for network-level mocking.
* **E2E/Browser Testing:** Playwright (preferred for speed, parallelization, and cross-browser coverage).

---

## ENFORCEMENT RULES

### 1. Test Coverage Targets
```
✅ Overall Codebase Coverage: > 80% line coverage minimum.
✅ Domain/Business Logic:    100% coverage (every edge case, conditional branch).
✅ Critical Paths:           100% path coverage (Checkout, Auth, Data Sync).
```

### 2. Unit Testing Rules
```
✅ Colocate unit test files with source files (e.g., login.ts next to login.test.ts).
✅ Use AAA Pattern: Arrange (set up state), Act (execute function), Assert (validate output).
✅ Test happy path, boundary states, and error throwing.
❌ NEVER mock pure utility functions — test them with real inputs.
❌ NEVER write unit tests that depend on database connections or real network calls.
```

### 3. Integration Testing Rules
```
✅ Mock network requests at the HTTP layer using MSW (Mock Service Worker).
✅ Run tests against a local test database (e.g., PostgreSQL in Docker container) for database integration.
✅ Test database transaction rollbacks — tests must not persist dirty state in the test DB.
❌ NEVER skip testing API error responses (e.g., verify that a 400 or 500 status code is handled correctly by the client).
```

### 4. E2E & Browser Testing (Playwright)
```
✅ Run E2E tests in parallel on Chrome, Firefox, and WebKit (Safari).
✅ Use data-testid attributes for element targeting (e.g., page.getByTestId('submit-btn')).
✅ Verify Core Web Vitals targets during test execution using Lighthouse CI gates.
❌ NEVER use hardcoded delays (e.g., page.waitForTimeout(3000)) — use assertion-driven waiting.
```

---

## QA VERIFICATION FLOW
Before marking any task as complete, the AI must verify changes locally by:
1. Running all unit tests: `npm run test` or `pnpm test`.
2. Running the build script: `npm run build` or `pnpm build` to check for compilation/type errors.
3. Writing a summary of what was tested and embedding unit/E2E test output logs in the `walkthrough.md` report.

Development Manifest & Engineering Principles
This document defines the mandatory workflow and quality standards for all projects. Consistency, observability, and pragmatism are our core pillars.
1. Commit & Git Management
Conventional Commits: Always use the Conventional Commits specification (e.g., feat:, fix:, refactor:, chore:).
Clean Repository: Never commit binaries, large files, environment variables (.env), or AI tool instructions. Always verify with git status before committing.
Git as a Safety Net: Assume mistakes will happen. Use Git branches and commits as checkpoints. If you're stuck, use the history and the user as allies.
2. Communication & Collaboration
User Authority vs. Technical Honesty: While the user has the final word, the AI must be intellectually honest. If a user's decision is technically flawed, insecure, or contradicts the project's core logic (e.g., AMLACOS critical path), the AI must politely dissent and explain "Why" using project context. Do not "bicker" without arguments; provide evidence-based warnings.
Proactive Help: If a task is too complex, ask for clarification or for the user to provide specific technical context/skills.
Resourcefulness: If stuck, search (using brave-mcp-server), test, and ask.
Implicit Documentation: Never create documentation or markdown files in the repository without explicit permission (except for this manifest).
3. Design & Problem Solving
Keep It Simple (KISS): Talk simply and do not complicate simple things.
Step-by-Step Execution: Break complex problems into smaller, connected steps. Define each step clearly and simulate user interaction/edge cases between them.
Project Initialization & Criticality: When starting from scratch or implementing a new feature, follow this expanded flow:
User Needs -> Business Criticality Assessment -> Project Scope -> User Data Flow -> Security -> System Design.
Criticality Assessment: Identify if the feature is on the "Critical Path" (e.g., Checkout in e-commerce, Auth in a portal). If it is, failure is not an option.
Document the "Why": For complex architectural decisions, write brief comments explaining the reasoning (the "Why") rather than the mechanics (the "How").
4. Coding Standards & Reliability
Clean Code: Follow Clean Code principles. Code should be self-explanatory.
Zero Hardcoding: Never hardcode "things" (endpoints, secrets, or platform-specific paths). Always consider cross-platform compatibility and network implications.
Fail-Fast & Error Handling: Never silence errors. Propagate errors with context (e.g., anyhow in Rust, %w in Go). Logs must include state and stack traces.
Resilience & Retries: For features identified as Critical, you MUST implement resilience patterns:
Retries with Exponential Backoff: For unstable network operations or critical APIs.
Idempotency: Essential for safe retries (Rule 4 extension).
Circuit Breakers: To prevent cascading failures in distributed systems.
Graceful Degradation: If a critical service fails, provide a fallback or a clear, non-technical explanation to the user.
Boundary Validation: Always validate data at boundaries (API inputs, IPC, User input). Use schemas (Zod, Serde, etc.) to ensure internal data is always valid.
Minimalist Dependencies: Evaluate the cost-benefit of new libraries. Prioritize security and maintenance. If the feature is small, implement it internally.
5. Testing & Verification
Real Testing: No boilerplate tests. Follow the hierarchy:
Unit Tests: Logic and edge cases.
Integration Tests: Systems working together.
User Story Tests: Validating the actual user flow (especially the critical paths).
Resilience Tests: Simulate network failure to verify if retries/fallbacks work.
Safe Shell Usage: Use the shell for diagnostics and building. Do not run servers; ask the user to start servers for testing.
6. Modern Logging
Log is your ally. Never use console.log or equivalent "print" statements for debugging. Use structured logging:
A. Websites (Frontend)
Abstract Loggers: Use libraries (e.g., loglevel, pino-level) to manage levels (debug, info, warn, error).
Breadcrumbs: Create a trail of events to reconstruct failures on the client side.
B. Desktop Apps (Tauri, Electron)
Local Persistence: Write logs to rotating files (app.log) for user-reported debugging.
Process Separation: Rust (Core) manages system-heavy logging independently from the Frontend.
C. Distributed Systems / Backend
JSON Logging: Always log JSON objects for indexed search (Kibana/Grafana).
Distributed Tracing: Use Correlation IDs to track requests across services.
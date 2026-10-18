# What I Would Do With One More Week

If I had one additional week, I would prioritize correctness, measurable evaluation and secure integration over adding more features.

**Days 1–2 — Evaluation and RAG quality.** I would run and label a minimum 25-question evaluation set covering factual lookup, source conflicts, derived calculations, refusals, entitlements, SQL safety and escalation. I would measure answer correctness, citation accuracy, refusal accuracy, conflict detection and latency. I would fix duplicate chunks, metadata-only retrieval and citation-to-claim mismatches, then rerun regression tests.

**Day 3 — Entitlements and SQL security.** I would add automated tests for every client/report pair, verify that filtering occurs before retrieved text enters the answer context, and separate any explicitly approved public-preview policy from paid-report entitlements. I would configure a least-privilege read-only database account for analytics, enforce query allow-lists, result limits and query timeouts, and add audit tests for unsafe natural-language requests.

**Day 4 — Agent reliability.** I would improve the router, add structured outputs for each workflow step, validate every numeric claim against citations, test the calculator against hand-computed examples, and ensure ambiguous or low-confidence answers create escalation tickets. I would also add bounded retries and consistent timeout handling.

**Day 5 — CodeIgniter 3 integration.** I would test the chat view inside a real CI3 installation, verify CSRF protection and output escaping, persist thumbs-up/down feedback, and demonstrate graceful fallback when the Python API is unavailable.

**Day 6 — Observability and performance.** I would add request correlation IDs, per-request latency and token/cost logging if an LLM provider is enabled, safe structured audit logs, and caching for repeated questions. I would test p50/p95 latency under concurrent requests and verify that cache keys include the client/entitlement context so one tenant can never receive another tenant's result.

**Day 7 — Delivery hardening.** I would remove all sample secrets, validate the `.env.example`, run the entire test suite from a clean Docker environment, document known limitations, make small reviewable Git commits, and record a 3–5 minute demo showing a normal answer, a source conflict, a derived estimate, injection refusal, entitlement denial, escalation and the CI3 page.

The guiding principle would be to make every answer auditable and every access decision testable. I would rather deliver a smaller system with measured safety properties than claim production readiness without evidence.

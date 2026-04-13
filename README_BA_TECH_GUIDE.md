## Technical guide for Business Analysts working on APIs

Purpose
-------

This file collects practical, technical topics a Business Analyst should understand when collaborating with developers to design, review, and accept APIs. It focuses on what matters for product decisions, acceptance criteria, and clear communication—not deep engineering details.

1 — Core API concepts to know
----------------------------
- Resource & endpoint: the noun (resource) and the URL path where it’s accessed (e.g., /orders).
- HTTP methods and intent:
  - GET: read
  - POST: create
  - PUT/PATCH: update (PUT often full replace; PATCH partial)
  - DELETE: remove
- Request vs Response: who sends what, and where data lives (path/query/header/body).
- Status codes: 200/201/204 for success; 400/401/403/404/409/429/5xx for errors. Know what each means for users.

2 — Contract & documentation (what the BA owns)
-----------------------------------------------
- API contract: the machine-readable spec (OpenAPI) is the source-of-truth. BA supplies the business intent and examples.
- Examples: provide at least one happy-path request and response, plus 1–2 error examples.
- Acceptance criteria: concrete, testable statements (see template below).

3 — Authentication & security
-----------------------------
- Types: API key, Bearer tokens (JWT/OAuth2), mTLS. Ask: who calls this API and how are credentials obtained?
- Data protection: note any PII, encryption, or masking requirements. Flag regulatory needs (e.g., GDPR).

4 — Data shape and validation
-----------------------------
- Types and required fields: clearly state which fields are required and their formats (string, integer, date-time, enum).
- Validation rules: min/max lengths, allowed values, uniqueness rules, business constraints.
- Timestamps, timezones, and canonical formats (ISO 8601).

5 — Error handling and business mapping
--------------------------------------
- Error shape: define a structured error object (code, message, details).
- Map business errors to HTTP codes (e.g., 409 for duplicate order).
- Provide example error payloads for common failures.

6 — Behavioural and functional patterns
--------------------------------------
- Idempotency: which operations are safe to retry (GET, PUT usually idempotent; POST not necessarily).
- Id fields and identifiers: global vs tenant-scoped IDs; GUIDs vs sequential.
- Concurrency: optimistic locking (ETag, version), conflict handling.

7 — Pagination, filtering, sorting
---------------------------------
- Pagination style: offset+limit vs cursor-based. Cursor is better for large or changing datasets.
- Filtering and sorting parameters: what filters are supported and how to express them.

8 — Rate limits and quotas
--------------------------
- Ask about limits and expected handling (429 responses, Retry-After header).
- Understand throttling behavior for bulk operations.

9 — Versioning and compatibility
--------------------------------
- Version strategy: URL versioning (/v1/...), header-based versioning, or Semantic versioning of the spec.
- Backward compatibility: how breaking changes are introduced and communicated.

10 — Environments and deployment
---------------------------------
- Environments: dev (mock), staging (pre-prod), production — know which environment you’ll demo against.
- Mocking: mock servers from OpenAPI are useful for BA acceptance tests when backend not ready.

11 — Observability, logging and monitoring
-----------------------------------------
- Ask how errors, latency, and request volume are monitored. Know where to find logs or dashboards to debug issues seen in demo.

12 — Testing and QA
-------------------
- Acceptance tests: tie each acceptance criterion to a test (mocked or real). Use OpenAPI mocks for automated tests.
- Example-driven tests: use the sample request/response for end-to-end checks.

13 — Non-functional requirements
--------------------------------
- Performance (response time targets), SLAs, data retention, throughput, and expected concurrency.

14 — Governance & lifecycle
--------------------------
- Spec storage: keep OpenAPI in version control and use PR reviews.
- Deprecated APIs: how long will older versions be kept and who notifies consumers?

Quick acceptance criteria template (BA-friendly)
------------------------------------------------
- Given: (precondition / environment)
- When: (action, e.g., POST /orders with payload X)
- Then: (expected response code and response body details)
- And: (side effects, e.g., DB row created, email sent)

Example acceptance criteria
---------------------------
- Given a valid order payload and authenticated user,
- When the client calls POST /orders,
- Then the API responds 201 Created with the order object containing id, status="created", and total>0.
- And the order is retrievable using GET /orders/{id} with status="created".

Communication snippets (copyable)
---------------------------------
- To request a new endpoint from devs:
  "Please implement POST /orders to create an order with fields [customerId, items[], total]. See attached example request/response and acceptance criteria. We need a mock endpoint in dev and OpenAPI added to repo. Expected behavior: 201 on success, 400 on validation errors."
- To request clarification on error handling:
  "What HTTP status should we return when an order references an unknown customer? Proposed: 404 Not Found with error code CUSTOMER_NOT_FOUND."

BA handoff checklist (what to give the devs)
-----------------------------------------
1. One-line purpose for each endpoint
2. Example request JSON (happy path)
3. Example response JSON (happy path)
4. Example error JSON and status codes
5. Field-level validation rules and required fields
6. Authentication and scope/roles required
7. Acceptance criteria and test steps
8. Any performance, retention, or regulatory constraints

Practical tips
--------------
- Start with examples — examples make it easy to reason about data and edge cases.
- Use the Swagger Editor (editor.swagger.io) to paste examples and get interactive docs & mocks.
- Keep the BA draft simple: a table or spreadsheet is fine to start; developers will convert to OpenAPI.
- Save the spec in Git (PRs are the easiest way to review and approve changes).


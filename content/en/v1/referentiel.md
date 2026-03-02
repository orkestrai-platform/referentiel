# OrkestrAI Referential

The OrkestrAI standards is a comprehensive evaluation framework, designed to measure the quality
of code produced in collaboration with artificial intelligence tools. It is not about judging
whether AI was used — **but about verifying that its use was controlled,
supervised and guided by a competent developer**.

Each criterion was designed by senior developers who face the realities of AI-generated code
on a daily basis. Our ambition: to create a common language for evaluating code quality
in the AI era, whether for a project audit, a code review or a
certification process.

The standards are organized into **5 axes** ranging from software architecture
to security, including AI orchestration itself. The requirements are distributed
according to the impact of each domain on overall code quality. Architecture weighs the most
because it is the foundation on which everything else rests.

## Axis 1 — Architecture

The human guided the AI toward a clean, maintainable and scalable architecture.

**Why this axis?** Architecture is the foundation of every software project. Well-architected code
is code that can evolve, be tested and maintained over the long term. This is precisely where AI
needs the most guidance: language models generate code that works in the short term,
but rarely code that fits into a coherent architectural vision. Separation of concerns,
dependency inversion, domain modeling — these fundamental choices cannot be delegated to AI.
They are the territory of the senior developer, the architect, the orchestrator.

### 1.1 Separation of Concerns

This criterion evaluates whether the code is organized into distinct layers with clear responsibilities.
Good separation of concerns is the first sign that a human guided the architecture
rather than letting the AI pile code into a single file. **This is the heaviest criterion
in the standards** because it determines the maintainability of the entire project.

**Concrete example:** In a Symfony project, the maximum score implies that the `src/` directory
is organized by bounded context, each context having its own Domain/, Application/ and Infrastructure/ subdirectories.
Domain entities contain no framework annotations. Repositories are defined as interfaces
in the domain and implemented in the infrastructure.

**How to audit:** Check the file tree. Analyze imports: does the domain import from the infrastructure? Trace the direction of dependencies with a static analysis tool. Verify that controllers contain no business logic.

### 1.2 Dependency Management

This criterion measures the level of coupling between system components. Dependency injection
and inversion of control are pillars of Clean Architecture. Tightly coupled code is
code that cannot be tested in isolation — and is often the result of unsupervised AI generation,
where each class directly instantiates its dependencies.

**Concrete example:** The domain defines `ProjectRepositoryInterface`. The infrastructure provides
`DoctrineProjectRepository`. The binding is done in the service container configuration.
No `new` in the domain for services or repositories.

**How to audit:** Search for `new` in the domain. Check service constructors. Ensure that all external dependencies (DB, API, filesystem) are behind interfaces.

### 1.3 Domain Modeling

The quality of domain modeling reveals whether the developer thought about the business before coding.
AI tends to produce anemic models (simple data containers with getters/setters)
because it is the most common pattern in its training data. A rich domain, with entities
that carry their business logic, immutable Value Objects and well-defined aggregates, is the mark
of a developer who orchestrated the AI with a DDD vision.

**Concrete example:** An `Audit` entity exposes methods like `score()`,
`complete()`, `certificationLevel()` — business logic lives in the domain. Identifiers
are typed Value Objects (`AuditId`, `ProjectId`) rather than plain strings.
The code language reflects the business language.

**How to audit:** Read the domain entities. Verify they carry business logic and not just getters. Look for Value Objects. Verify that naming uses the business vocabulary (ubiquitous language).

### 1.4 Testability

Well-architected code is testable code. This criterion evaluates not only the presence of tests,
but above all the ability of the code to be tested in isolation. AI often generates code that is difficult
to test because it is tightly coupled to infrastructure (database, external APIs). A good orchestrator
structures the code so that the domain can be tested without any external dependency.

**Concrete example:** Domain tests require no database. An `ExamSessionTest`
verifies business rules (score, expiration, levels) by directly instantiating entities.
Integration tests verify that Doctrine repositories work. E2e tests verify user journeys.

**How to audit:** Test ratio. Domain coverage. Presence of mocks/stubs for infrastructure. Test suite execution time. Verify that domain tests pass without a database.

### 1.5 Modularity and Coupling

This criterion evaluates whether the project is divided into autonomous modules with clear boundaries.
AI does not know the bounded contexts of your project — it is up to the developer to define them
and ensure that each module has a clear responsibility, well-defined contracts with
other modules, and minimal cross-dependencies.

**Concrete example:** A project organized into bounded contexts (Audit, Certification, Exam, Security)
where each context has its own entities, repositories and controllers. Communications between contexts
go through shared interfaces in a Shared Kernel, never through direct imports.

**How to audit:** Dependency graph between modules. Check cross-imports. Ensure that no module directly accesses the internal details of another. Use tools like deptrac or phparkitect.

## Axis 2 — AI Orchestration

The human piloted the AI with method, traceability and validation.

**Why this axis?** This is the axis that distinguishes OrkestrAI from any other software quality standard.
It evaluates not the code itself, but the way in which the human and AI collaborated to produce it.
Traceability of generations, systematic human validation, reproducibility of results and control
of the scope entrusted to AI — these are the markers of a professional and responsible use of artificial
intelligence in development. A project can have perfect code; if it was generated without supervision,
it is a house of cards.

### 2.1 Generation Traceability

Can we identify, for each portion of the code, whether it was generated by AI or written manually?
Traceability is essential for maintenance: when a bug occurs in generated code, you need
to be able to find the generation context, the prompt used, and the decisions made. Without traceability,
AI code becomes a black box that is impossible to debug effectively.

**Concrete example:** Commits for AI-generated code include an `[AI]` tag in the message
and a `Co-Authored-By`. A `.prompts/` folder at the project root contains versioned prompts
used for each major feature. A CLAUDE.md file or an ADR documents the orchestration sessions.

**How to audit:** Analyze the git log. Look for AI commit conventions. Verify the existence of a prompts folder. Ask the team to trace the origin of a specific file.

### 2.2 Human Validation

Was the AI-generated code systematically reviewed, understood and validated by a human before being integrated?
Human validation is the keystone of orchestration. A developer who copy-pastes AI code without
understanding it is not orchestrating — they are blindly delegating. Validation implies understanding,
critical review and explicit approval of each generation.

**Concrete example:** Each PR contains a "Code generated by AI" section with the relevant files.
A validation checklist (tests pass, conventions respected, no security flaw, correct business logic)
is checked before merge. PRs without validation are blocked by the repository rules.

**How to audit:** Review PRs and review history. Verify the existence of a validation checklist. Ask the team to describe their AI code validation process.

### 2.3 Reproducibility

Are the AI results reproducible? If the same prompt were run again in the same context,
would a similar result be obtained? Reproducibility comes from documenting prompts,
the context provided to the AI, and the parameters used. It is a fundamental principle of the scientific
approach applied to AI-assisted software engineering.

**Concrete example:** A `.prompts/` folder contains prompt templates organized by domain
(architecture, tests, refactoring). Each template includes the context to provide, the constraints to specify,
and an example of expected results. Prompts are versioned with the code.

**How to audit:** Look for a prompts folder. Verify if templates are versioned. Ask the team to reproduce a past generation. Evaluate the AI workflow documentation.

### 2.4 AI Scope Control

Has the developer clearly defined what the AI can and cannot do in the project?
A good orchestrator knows when to use AI and when to do without it. They define the constraints,
conventions and limits before launching a generation. Architectural decisions,
domain modeling and security choices must never be delegated to AI without supervision.

**Concrete example:** A `CLAUDE.md` file at the project root defines the architectural conventions,
the patterns to use, the naming rules and the prohibitions (no annotations in the domain,
no direct instantiation, tests mandatory). The AI is guided by these constraints at each generation.

**How to audit:** Verify the existence of guidance documents (ADR, CLAUDE.md, conventions). Ask the team what scope was given to the AI. Compare decision documentation with the delivered code.

## Axis 3 — Code Quality

The delivered code is readable, consistent and maintainable — regardless of who wrote it.

**Why this axis?** Code quality is what you see when you open a source file.
It is the most tangible and immediate criterion. Quality code reads like well-written prose:
each variable has an intentional name, each function does one thing, each file follows the same
conventions. AI can generate code of varying quality — it is up to the developer to ensure that the final
result is homogeneous, readable and maintainable. Code quality is also the best indicator of the long-term
health of a project: clean code today means code that will still be understandable in 6 months.

### 3.1 Readability and Naming

Is the code self-documenting? Do variable, function and class names clearly express
their intent? AI often generates generic names (`data`, `result`, `temp`)
or reproduces conventions from documentation examples. A good orchestrator enforces intentional
and consistent naming that reflects the business domain language.

**Concrete example:** Instead of `$data = $repo->get($id)`, the code reads
`$project = $this->projectRepository->findById(ProjectId::fromString($id))`.
Each name reveals the intent. Methods are named after business actions: `complete()`,
`certificationLevel()`, not `process()` or `handle()`.

**How to audit:** Read the source code. Verify the consistency of naming conventions. Search for generic naming patterns. Verify that the code vocabulary matches the business vocabulary.

### 3.2 Complexity

Are functions short and focused? Is cyclomatic complexity under control?
AI tends to generate long functions with many conditional branches, because it
optimizes for completeness rather than simplicity. An orchestrator ensures that each
function operates at a single level of abstraction and remains understandable at a glance.

**Concrete example:** No method exceeds 20 lines. Complex conditions are extracted
into named methods (`isEligibleForCertification()` rather than a long chain of if/else).
PHPStan/Psalm at the maximum level reports no complexity alerts.

**How to audit:** Use static analysis tools (phpstan, phpmd, sonar). Measure cyclomatic complexity. Identify the longest functions. Check nesting levels.

### 3.3 Pattern Consistency

Is the same problem solved the same way throughout the project? One of the most
common flaws of AI-generated code is stylistic inconsistency: each generation may propose a
different pattern for the same type of problem. A good orchestrator enforces conventions and verifies
that the AI follows them consistently.

**Concrete example:** All controllers follow the same pattern (dependency injection, validation, delegation to domain, response).
All repositories implement the same interface. All Value Objects follow the same template (private constructor, factory method, equals).
A `.php-cs-fixer.php` or `phpcs.xml` file normalizes formatting.

**How to audit:** Compare similar files (two controllers, two repositories). Check the linter/formatter config. Ensure CI/CD checks the style.

### 3.4 Controlled Technical Debt

Does the project contain dead code, orphan TODOs, unused dependencies or undocumented hacks?
AI sometimes generates code "just in case" — classes never used, redundant methods,
superfluous imports. A well-orchestrated project is a clean project where every line
of code has a reason to exist and every technical compromise is documented.

**Concrete example:** `grep -r "TODO\|FIXME\|HACK" src/` returns no results.
`composer why` justifies each dependency. The rare technical compromises are documented
in ADRs (Architecture Decision Records) with the reason for the choice and the alternatives considered.

**How to audit:** Search for TODO/FIXME/HACK in the code. Analyze unused dependencies. Identify dead code with static analysis tools. Verify the presence of ADRs for compromises.

## Axis 4 — Performance and Sobriety

The code is efficient and does not waste resources.

**Why this axis?** Performance and sobriety are increasingly critical concerns
in software development. AI does not care about performance — it generates the most "likely" code,
not the most efficient. Common problems with AI-generated code include N+1 queries, superfluous
dependencies, excessive data loading and a complete absence of Green IT considerations. A responsible
orchestrator ensures that the code is not only functional and well-structured, but also lean and
respectful of resources. Eco-design is not a luxury — it is a responsibility.

### 4.1 Query Efficiency

Are data access patterns optimized? One of the most frequent pitfalls of AI code is the
N+1 problem: a loop that executes one query per iteration instead of a single load.
AI does not "see" the performance implications of its choices — it is up to the developer to verify
that queries are efficient, indexed and paginated.

**Concrete example:** The Symfony Profiler shows a reasonable number of queries per page (< 10).
Collections are loaded with JOINs when necessary. Lists are paginated.
Frequently queried columns are indexed. No `findAll()` followed by a PHP filter.

**How to audit:** Use the Symfony profiler (debug toolbar). Count queries per page. Check database indexes. Test with a significant data volume.

### 4.2 Dependency Sobriety

Is every project dependency justified? AI frequently suggests third-party libraries
for simple tasks, creating dependency bloat that weighs down the project, increases the attack
surface and complicates updates. A good orchestrator challenges every `composer require`
and prefers native code when reasonable.

**Concrete example:** The `composer.json` contains only strictly necessary dependencies.
No 10,000-line library for a single utility function. `composer audit` reports
no vulnerabilities. Versions are carefully constrained (no `*`).

**How to audit:** Analyze the composer.json / package.json. Run `composer audit`. Verify that each dependency is actually used. Compare the ratio of business code to dependencies.

### 4.3 Resource Management

Does the code properly manage system resources? Files, connections, memory — every resource
opened must be closed, every allocation must be freed. AI often forgets these details because they
are not necessary for the code to "work" on the surface, but they cause insidious
problems in production.

**Concrete example:** Streams are closed in finally blocks. Lazy loading is used
for large collections. A cache system (HTTP, application) is in place for rarely modified data.
Memory is monitored in the profiler.

**How to audit:** Memory profiling. Review streams and connections. Check cache strategies. Test with a realistic data volume.

### 4.4 Eco-design

Does the project incorporate digital sobriety considerations? At a time when digital technology accounts for
4% of global greenhouse gas emissions, eco-design is a professional duty.
A responsible orchestrator minimizes the footprint of their code: lightweight payloads, compression, HTTP cache,
optimized images, no superfluous requests.

**Concrete example:** CSS is inlined and minified (no 300 KB CSS framework for a simple site).
Images are in WebP format with lazy loading. Cache headers are configured.
Lighthouse shows a performance score > 90. Total page weight is under 500 KB.

**How to audit:** Measure page weight with DevTools. Count the number of requests. Check cache headers. Run a Lighthouse audit. Evaluate the overall sobriety strategy.

## Axis 5 — Security

The code has no known vulnerabilities and protects user data.

**Why this axis?** Security is a cross-cutting concern that permeates all other axes.
If it has a dedicated section, it is because many criteria from other axes contribute
indirectly to security (clean architecture, tests, human validation). Nevertheless, these requirements
are critical: a single security flaw can compromise an entire project. AI is particularly
dangerous in this area because it reproduces vulnerable patterns with an apparent confidence that
can deceive an unwary developer. SQL injection, XSS, plaintext secrets, missing validation
— these are errors that AI commits regularly and that the orchestrator must systematically verify.

### 5.1 OWASP Top 10

Is the code protected against the 10 most common web vulnerabilities? The OWASP standard
is the global reference for application security. The most frequent flaws in
AI-generated code are injections (SQL, XSS, command), missing CSRF, poor
authentication management and exposure of sensitive data in API responses.

**Concrete example:** All queries go through the ORM (no raw SQL). Twig templates
automatically escape variables. Forms include a CSRF token. Security headers
(X-Content-Type-Options, X-Frame-Options, CSP) are configured. User inputs are validated
server-side, never only client-side.

**How to audit:** Run a security scan (OWASP ZAP, Snyk). Manual review of user inputs. Check HTTP security headers. Test forms without JavaScript to validate server-side protection.

### 5.2 Secret Management

Are secrets (passwords, API keys, tokens) properly externalized and protected?
This is one of the most serious and frequent errors in AI-generated code: hardcoded
credentials in the source code, a `.env` file committed by mistake, API keys
visible in the git history. A single leaked secret can compromise an entire system.

**Concrete example:** The `.env` file is in the `.gitignore`. A `.env.example`
documents all required variables with placeholder values. `git log -S "password"` returns
no suspicious results. Production secrets are managed by a vault or server
environment variables, never in the code.

**How to audit:** Search for secret patterns in the code (grep for password, secret, api\_key, token). Check the .gitignore. Scan the git history with a tool like truffleHog or gitleaks. Check the .env.example.

### 5.3 Data Protection

Is personal user data protected in compliance with GDPR and security best
practices? AI has no notion of regulatory compliance. It can log personal data,
store passwords in plaintext, or expose sensitive information in API responses.
It is up to the developer to ensure that every piece of personal data is treated with the respect it deserves.

**Concrete example:** Passwords are hashed with bcrypt/argon2 (never in plaintext or MD5).
Logs contain no emails, names, or identifying IP addresses. An account deletion endpoint
exists and anonymizes all user data. The cookie policy is GDPR compliant.

**How to audit:** Full GDPR audit. Review production logs (look for PII). Verify password hashing. Test account deletion. Check the cookie policy and consent.

## 6. Advanced Operational Orchestration

This section extends axes 1 to 5 with execution-level practices. Its purpose is
to make the referential directly usable for certification preparation, real project
delivery and team-level standardization.

### 6.1 Git, branching and worktrees

AI-assisted development requires strict task isolation. A single developer may run
parallel streams: feature work, urgent fix, security review. Without isolation,
context gets polluted and diffs become non-auditable.

**Recommended practice:**

- one branch per business objective;
- one worktree per active task (`feature/*`, `fix/*`, `hotfix/*`);
- no mixed subjects in the same diff;
- remove worktree once the task is complete.

**Concrete example:**

```bash
git fetch origin
git worktree add ../wt-score-endpoint -b feature/score-endpoint origin/main
cd ../wt-score-endpoint
```

Each AI session runs in a dedicated worktree with an explicit file scope.

**How to audit:**

- review branch and commit granularity;
- confirm one patch = one objective;
- inspect PR readability (no out-of-scope noise);
- verify no unfinished feature remains hidden in a local worktree.

### 6.2 Commit hygiene and decision traceability

AI-generated code is hard to maintain without decision history.
A commit should explain "why", not only "what".

**Recommended practice:**

- atomic commits;
- conventional commit structure (type/scope);
- generation context reference (prompt, ticket, ADR);
- explicit assumptions.

**How to audit:**

- read the latest commits in an active area;
- measure opaque/WIP commit ratio;
- verify links to docs, tickets or decision records.

### 6.3 AI context management

Strong outputs require strong context. Context must be stable, versioned,
and aligned with the target architecture.

**Minimum framing files:**

- `AGENTS.md` (interaction rules and forbidden actions);
- `docs/architecture.md` (technical boundaries);
- `docs/decisions.md` (arbitration history);
- `docs/backlog.md` (short, verifiable tasks);
- `docs/prompts/` (reusable templates).

**Maturity signals:**

- prompts are objective-driven and tied to acceptance criteria;
- AI is explicitly constrained to editable file scopes;
- outputs systematically include risks and test strategy.

### 6.4 Prompt catalog and reproducibility

The referential is tool-agnostic but method-driven.
A prompt catalog makes generations replayable and comparable.

**Suggested template:**

1. Context
2. Objective
3. Constraints
4. Allowed files
5. Acceptance criteria
6. Known risks
7. Verification commands

**How to audit:**

- verify that prompt templates exist and are versioned;
- run a replay on a similar task;
- compare readability, robustness and convention compliance.

### 6.5 Risk-oriented diff review

Reviewing AI patches should prioritize risk over style.

**Suggested order:**

1. Functional bugs
2. Regression risk
3. Security
4. Architectural coupling
5. Technical debt
6. Style/readability

**How to audit:**

- inspect quality and relevance of review comments;
- confirm critical blockers are resolved before merge;
- verify that trade-off decisions are documented.

## 7. Delivery, CI/CD and Operations

### 7.1 CI/CD quality gates

AI-assisted projects must not rely on local validation only.
Critical controls should be automated.

**Minimum gates:**

- lint/format;
- unit and integration tests;
- static analysis;
- dependency security scan;
- reproducible build.

**How to audit:**

- review the CI configuration;
- verify failed checks block merges;
- confirm there is no permanent check bypass.

### 7.2 Migrations and data safety

AI-assisted migrations are high risk when not tested with realistic data.

**Recommended practice:**

- idempotent or strictly controlled migration strategy;
- planned rollback;
- backup before destructive operations;
- post-migration verification scripts.

**How to audit:**

- review recent migrations;
- verify DB integration tests exist;
- verify rollback and recovery strategy.

### 7.3 Observability and diagnostics

A well-orchestrated system is observable. Without structured logs, metrics
and traces, AI-induced issues become hard to explain.

**Expected baseline:**

- structured logs with correlation IDs;
- technical metrics (latency, errors, saturation);
- alerts on critical thresholds;
- incident runbooks.

**How to audit:**

- inspect real log payloads and correlation quality;
- simulate an error and validate diagnosis speed;
- verify that runbooks are actionable and up to date.

### 7.4 Performance and sobriety budgets

Performance requirements should be explicit. "It works" is not enough.

**Examples:**

- p95 response time target;
- max page weight;
- max DB queries per page;
- memory budget on critical flows.

## 8. Advanced Security and Compliance

### 8.1 Software supply chain

Risk is not only in application code, but also in dependencies.

**Expected:**

- minimal and justified dependencies;
- locked versions;
- CVE monitoring;
- secure update procedure.

### 8.2 Secret governance

Beyond storage, organizations must manage rotation, revocation and historical exposure.

**Expected:**

- periodic rotation;
- emergency revocation procedure;
- Git history secret scanning;
- clear environment separation.

### 8.3 Privacy by design

GDPR compliance must be designed up front:

- data minimization;
- explicit retention periods;
- effective deletion rights;
- logs without identifying data.

### 8.4 Governance of data sent to AI tools

Prompts can leak sensitive information if unmanaged.

**Expected:**

- clear policy on what data can be sent in prompts;
- anonymization/pseudonymization when needed;
- no secrets/PII without legal basis and controls;
- periodic practice reviews.

## 9. Certification Preparation Guide

### 9.1 Recommended evidence package

Preparing evidence strongly increases success probability:

- documented architecture;
- backlog and decision excerpts;
- prompt samples and human validations;
- test and quality gate outputs;
- code review examples and corrective actions.

### 9.2 Four-week training plan

### Week 1

- project frame setup;
- commit/PR standards;
- one tested micro-feature.

### Week 2

- separation of concerns hardening;
- risk-oriented review practice;
- testability improvements.

### Week 3

- performance/sobriety work;
- OWASP + secrets hardening;
- incident simulation and diagnosis.

### Week 4

- full mock audit;
- gap remediation;
- technical defense preparation.

### 9.3 Frequent exam mistakes

- theoretical answers disconnected from real code;
- confusion between "working code" and "maintainable code";
- missing AI traceability;
- underestimating security and edge cases;
- lack of concrete evidence (tests, logs, decisions).

### 9.4 Final candidate checklist

- I can explain architectural choices.
- I can justify how AI was framed and constrained.
- I can demonstrate the testing strategy.
- I can prove security risk handling.
- I can show reproducibility evidence.

## 10. Practical Appendices

### 10.1 AI-assisted PR checklist

- explicit business objective;
- respected file scope;
- relevant tests added/updated;
- risks identified and documented;
- explicit human validation before merge.

### 10.2 Quick security review checklist

- input/output validation;
- coherent auth/authz;
- no plaintext secrets;
- non-verbose end-user error exposure;
- no known critical dependency vulnerabilities.

### 10.3 Release go/no-go checklist

- quality gates green;
- migration validated;
- rollback ready;
- observability operational;
- incident runbook up to date.

## 11. Agentic orchestration operating model

This section defines the target way of working for teams using coding
AI agents in professional environments.

### 11.1 Orchestration value chain

Recommended cycle for each change:

1. **Intent**: clarify business need and explicit non-goals.
2. **Plan**: split work into testable, ordered micro-batches.
3. **Execute**: produce one minimal patch per batch.
4. **Verify**: run quality gates and risk-first review.
5. **Decide**: accept, reject, or rework the batch.
6. **Trace**: archive context, prompts, and decisions.

A batch is done only when evidence exists:

- readable diff;
- relevant tests;
- explicit residual risks;
- signed human decision in the PR.

### 11.2 Minimal roles and responsibilities

Recommended roles for an "architect-grade" level:

- **Architect**: defines invariants, boundaries, and allowed dependencies.
- **Lead dev**: drives sequencing and tradeoff arbitration.
- **Developer**: orchestrates the agent in strict scope.
- **Reviewer**: validates robustness, readability, and regressions.
- **Security champion**: validates threats and compliance.

Simplified RACI matrix:

| Activity | Architect | Lead | Dev | Reviewer | Sec |
| --- | --- | --- | --- | --- | --- |
| Define target architecture | R | A | C | I | C |
| Frame prompts and context | C | A | R | I | C |
| Implement batch | I | C | R | I | I |
| Risk review | C | A | C | R | R |
| Merge decision | C | A | C | R | C |

### 11.3 Agentic task input/output contract

**Minimum input**:

- business intent (one paragraph);
- verifiable acceptance criteria;
- allowed files;
- architecture/security constraints;
- validation commands.

**Minimum output**:

- implementation plan;
- patch;
- tests and results;
- residual risks;
- suggested commit message.

### 11.4 Team maturity criteria

"Bronze" level:

- occasional AI usage with manual validation;
- partial, inconsistent conventions.

"Silver" level:

- stable protocol per task type;
- measured quality (tests, lint, static analysis);
- traceability for major decisions.

"Gold" level:

- industrialized orchestration;
- tooled governance of prompts and contexts;
- continuous improvement via postmortems and metrics.

## 12. Prompt protocols and concrete examples

### 12.1 Architecture diagnosis prompt

```text
Goal: identify strong coupling zones in [module].
Context: target architecture [hexagonal / clean / layered].
Constraints: no global rewrite proposals.
Output: top 5 risks, impacts, and remediation order.
```

Expected usage:

- discovery phase;
- remediation backlog creation;
- prioritization before implementation.

### 12.2 Controlled implementation prompt

```text
Goal: implement [feature] in the allowed files.
Constraints:
- no out-of-scope changes;
- no new dependency;
- tests required.
Output:
1) short plan
2) patch
3) tests
4) risks
```

Mandatory human checks:

- consistency with domain model;
- naming convention compliance;
- explicit edge-case verification.

### 12.3 Regression-safe refactor prompt

```text
Goal: reduce complexity in [file/method].
Forbidden: changing functional behavior.
Output: 3-step proposal + non-regression tests.
```

Concrete expected evidence:

- cyclomatic complexity before/after;
- method size before/after;
- unchanged or improved test coverage.

### 12.4 Targeted security review prompt

```text
Context: endpoint [X] with user input.
Task: analyze OWASP risks and propose fixes.
Output: likely vulnerabilities + minimal patches + tests.
```

Operational expectation:

- server-side validation;
- non-verbose error messages;
- auth/authz verification;
- no secret leakage.

### 12.5 Forbidden prompt patterns in production

- sending plaintext secrets;
- sending PII without legal basis;
- asking for security bypasses;
- asking for a "quick fix" without tests.

## 13. Threat model specific to AI agents

### 13.1 Main threats

- **Prompt injection** through untrusted content.
- **Exfiltration** of sensitive data in context.
- **Tool abuse** (actions outside allowed scope).
- **Dependency poisoning** via unverified suggestions.
- **Critical hallucination** in security-sensitive zones.

### 13.2 Risk reduction controls

- strict separation of tool permissions;
- allowlist of commands and editable paths;
- sandbox execution; explicit approval for elevation;
- prompt data redaction/anonymization;
- two-person human validation for security/compliance.

### 13.3 Attack scenario and response

Scenario:

1. a ticket includes malicious instructions;
2. the agent interprets them as valid commands;
3. proposed patch introduces data leakage.

Expected response:

- detect in risk-first diff review;
- reject patch;
- clean context;
- add security non-regression test;
- incident note and prompt guide update.

### 13.4 Recommended control log

For each agentic session:

- session identifier;
- file scope;
- tools used;
- flagged risks;
- final decision and rationale.

## 14. Advanced audit evidence package

### 14.1 Recommended folder structure

```text
audit-evidence/
  01-architecture/
  02-orchestration-ia/
  03-quality-gates/
  04-security/
  05-performance/
  06-runbooks/
  07-postmortems/
```

### 14.2 Minimum evidence by axis

Architecture:

- dependency diagram;
- coded architecture rules (deptrac, etc.);
- major ADRs.

AI orchestration:

- prompt templates;
- sample traced sessions;
- justified rejection examples.

Quality:

- test reports;
- static analysis reports;
- technical debt report.

Security:

- dependency scan;
- secret scan;
- proof of at least one fixed issue.

Performance:

- baseline;
- budgets;
- before/after optimization measures.

### 14.3 Architect-oriented oral defense

Expected questions:

1. Which architecture invariants are non-negotiable?
2. Which tasks are explicitly forbidden for the agent?
3. Where is non-regression proof?
4. What is your documented rollback plan?

## 15. Runbooks and operational procedures

### 15.1 "AI patch incident" runbook

Trigger:

- production regression tied to an agentic patch.

Procedure:

1. freeze merges;
2. isolate faulty commit;
3. rollback per procedure;
4. open postmortem;
5. strengthen missing prompt/control rule.

### 15.2 "Exposed secret" runbook

1. revoke key immediately;
2. invalidate linked sessions/tokens;
3. purge history where applicable;
4. notify impacted parties;
5. document root cause and preventive action.

### 15.3 "Failed migration" runbook

1. stop writes;
2. quick diagnosis;
3. rollback or validated restoration;
4. data integrity verification;
5. progressive restart with stronger monitoring.

### 15.4 Standard postmortem template

Minimum sections:

- factual timeline;
- impact;
- root causes;
- failed barriers;
- corrective actions;
- effectiveness verification date.

## 16. Normative bibliography and trusted sources

The sources below are recommended as shared references.
They should be cited in ADRs, internal guides, and training materials.

### 16.1 Application security and SDLC

- NIST Secure Software Development Framework (SP 800-218):
  [NIST SSDF](https://csrc.nist.gov/pubs/sp/800/218/final)
- OWASP Top 10 (2021):
  [OWASP Top 10](https://owasp.org/Top10/)
- OWASP ASVS:
  [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)
- OWASP Cheat Sheet Series:
  [OWASP Cheat Sheets](https://cheatsheetseries.owasp.org/)
- OWASP Top 10 for LLM Applications:
  [OWASP LLM Top 10](https://owasp.org/www-project-top-10-for-large-language-model-applications/)

### 16.2 Supply chain, provenance, and integrity

- SLSA Framework:
  [SLSA](https://slsa.dev/)
- in-toto framework:
  [in-toto](https://in-toto.io/)
- OpenSSF Scorecard:
  [OpenSSF Scorecard](https://securityscorecards.dev/)
- SPDX specification:
  [SPDX](https://spdx.dev/specifications/)
- CycloneDX SBOM:
  [CycloneDX](https://cyclonedx.org/specification/)

### 16.3 Operations, incident handling, observability

- NIST Computer Security Incident Handling Guide (SP 800-61r2):
  [NIST 800-61r2](https://csrc.nist.gov/pubs/sp/800/61/r2/final)
- OpenTelemetry specification:
  [OpenTelemetry Spec](https://opentelemetry.io/docs/specs/)

### 16.4 Git, traceability, and contribution standards

- Git worktree documentation:
  [git-worktree](https://git-scm.com/docs/git-worktree)
- Conventional Commits 1.0.0:
  [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)
- Keep a Changelog:
  [Keep a Changelog](https://keepachangelog.com/en/1.1.0/)

### 16.5 AI governance and reliability

- NIST AI Risk Management Framework 1.0:
  [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework)
- ISO/IEC 42001 overview (AI management systems):
  [ISO 42001 Overview](https://www.iso.org/standard/81230.html)
- Model Spec (OpenAI):
  [OpenAI Model Spec](https://model-spec.openai.com/)

### 16.6 Product method and testing references

- Twelve-Factor App:
  [12factor](https://12factor.net/)
- Martin Fowler, Test Pyramid:
  [Test Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html)
- CISA Secure by Design:
  [Secure by Design](https://www.cisa.gov/securebydesign)

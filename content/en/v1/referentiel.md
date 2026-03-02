# OrkestrAI Referential Standard v1

_Source: legacy OrkestrAI referential page, converted to Markdown for open-source publication._

# OrkestrAI Standards

100 points. 5 axes. 19 criteria. The quality standard for AI-assisted development orchestrated by the developer.

Standard v1

The OrkestrAI standards is a comprehensive evaluation framework, designed to measure the quality
of code produced in collaboration with artificial intelligence tools. It is not about judging
whether AI was used — **but about verifying that its use was controlled,
supervised and guided by a competent developer**.

Each criterion was designed by senior developers who face the realities of AI-generated code
on a daily basis. Our ambition: to create a common language for evaluating code quality
in the AI era, whether for a project audit, a code review or a
certification process.

The standards are organized into **5 axes** ranging from software architecture
to security, including AI orchestration itself. The 100 points are distributed
according to the impact of each domain on overall code quality. Architecture weighs the most
(30 points) because it is the foundation on which everything else rests.

[Levels](#levels)
[Architecture (30 pts)](#axis-1)
[AI Orchestration (25 pts)](#axis-2)
[Code Quality (20 pts)](#axis-3)
[Performance (15 pts)](#axis-4)
[Security (10 pts)](#axis-5)
[Distribution](#distribution)
[Audit Process](#process)
[FAQ](#faq)

100

total points

5

evaluation axes

19

scoring criteria

## Certification Levels

Each level represents a milestone of mastery in AI code orchestration. The score is calculated out of 100 points, with each point corresponding to a concrete and verifiable practice.

### Not Certified

< 60

Insufficient score. Significant improvements are required before the project can qualify for certification.

### Certified

60 — 74

Mastery of the fundamentals. The code is structured, AI is guided, and the basics of quality are met.

### Certified Pro

75 — 89

Advanced expertise. Solid architecture, methodical orchestration, quality and security are delivered.

### Certified Expert

90 — 100

Excellence across all axes. A reference in AI orchestration. A model for the profession.

## Axis 1 — Architecture

30 points

The human guided the AI toward a clean, maintainable and scalable architecture.

**Why 30 points?** Architecture is the foundation of every software project. Well-architected code
is code that can evolve, be tested and maintained over the long term. This is precisely where AI
needs the most guidance: language models generate code that works in the short term,
but rarely code that fits into a coherent architectural vision. Separation of concerns,
dependency inversion, domain modeling — these fundamental choices cannot be delegated to AI.
They are the territory of the senior developer, the architect, the orchestrator.

### 1.1 Separation of Concerns

0 — 8 pts

This criterion evaluates whether the code is organized into distinct layers with clear responsibilities.
Good separation of concerns is the first sign that a human guided the architecture
rather than letting the AI pile code into a single file. **This is the heaviest criterion
in the standards** because it determines the maintainability of the entire project.

| Score | Criterion |
| --- | --- |
| 0 | Everything in one file / God class / no structure. The AI generated without any architectural guidance. |
| 2 | Basic separation (controllers vs the rest). An intention to structure exists, but it remains superficial. |
| 5 | Identifiable layers (presentation, application, domain, infrastructure). The developer imposed a layered structure on the AI. |
| 8 | Hexagonal or clean architecture applied — dependencies point toward the domain. Inversion of control is consistently respected. |

**Concrete example:** In a Symfony project, the maximum score implies that the `src/` directory
is organized by bounded context, each context having its own Domain/, Application/ and Infrastructure/ subdirectories.
Domain entities contain no framework annotations. Repositories are defined as interfaces
in the domain and implemented in the infrastructure.

**How to audit:** Check the file tree. Analyze imports: does the domain import from the infrastructure? Trace the direction of dependencies with a static analysis tool. Verify that controllers contain no business logic.

### 1.2 Dependency Management

0 — 6 pts

This criterion measures the level of coupling between system components. Dependency injection
and inversion of control are pillars of Clean Architecture. Tightly coupled code is
code that cannot be tested in isolation — and is often the result of unsupervised AI generation,
where each class directly instantiates its dependencies.

| Score | Criterion |
| --- | --- |
| 0 | Direct instantiations everywhere, tight coupling between all layers. Modifying one component requires modifying all the others. |
| 3 | Dependency injection used but inconsistently. Some services are injected, others instantiated manually. |
| 6 | Systematic DI, interfaces for external dependencies, inversion of control respected. The domain depends on nothing external. |

**Concrete example:** The domain defines `ProjectRepositoryInterface`. The infrastructure provides
`DoctrineProjectRepository`. The binding is done in the service container configuration.
No `new` in the domain for services or repositories.

**How to audit:** Search for `new` in the domain. Check service constructors. Ensure that all external dependencies (DB, API, filesystem) are behind interfaces.

### 1.3 Domain Modeling

0 — 6 pts

The quality of domain modeling reveals whether the developer thought about the business before coding.
AI tends to produce anemic models (simple data containers with getters/setters)
because it is the most common pattern in its training data. A rich domain, with entities
that carry their business logic, immutable Value Objects and well-defined aggregates, is the mark
of a developer who orchestrated the AI with a DDD vision.

| Score | Criterion |
| --- | --- |
| 0 | Anemic models, logic in controllers. Entities are just data structures without behavior. |
| 3 | Entities with logic but no Value Objects, no clear aggregates. The domain exists but lacks rigor. |
| 6 | DDD applied: rich entities, immutable Value Objects, aggregates with clear roots, consistent ubiquitous language throughout the code. |

**Concrete example:** An `Audit` entity exposes methods like `score()`,
`complete()`, `certificationLevel()` — business logic lives in the domain. Identifiers
are typed Value Objects (`AuditId`, `ProjectId`) rather than plain strings.
The code language reflects the business language.

**How to audit:** Read the domain entities. Verify they carry business logic and not just getters. Look for Value Objects. Verify that naming uses the business vocabulary (ubiquitous language).

### 1.4 Testability

0 — 5 pts

Well-architected code is testable code. This criterion evaluates not only the presence of tests,
but above all the ability of the code to be tested in isolation. AI often generates code that is difficult
to test because it is tightly coupled to infrastructure (database, external APIs). A good orchestrator
structures the code so that the domain can be tested without any external dependency.

| Score | Criterion |
| --- | --- |
| 0 | No tests. The project cannot prove that its code works correctly. |
| 2 | Tests present but coupled to infrastructure (DB, API). Tests are slow, fragile and hard to maintain. |
| 4 | Isolated domain unit tests. The domain is tested independently of any infrastructure. |
| 5 | Test pyramid respected (unit > integration > e2e), domain tested in complete isolation, significant coverage of business cases. |

**Concrete example:** Domain tests require no database. An `ExamSessionTest`
verifies business rules (score, expiration, levels) by directly instantiating entities.
Integration tests verify that Doctrine repositories work. E2e tests verify user journeys.

**How to audit:** Test ratio. Domain coverage. Presence of mocks/stubs for infrastructure. Test suite execution time. Verify that domain tests pass without a database.

### 1.5 Modularity and Coupling

0 — 5 pts

This criterion evaluates whether the project is divided into autonomous modules with clear boundaries.
AI does not know the bounded contexts of your project — it is up to the developer to define them
and ensure that each module has a clear responsibility, well-defined contracts with
other modules, and minimal cross-dependencies.

| Score | Criterion |
| --- | --- |
| 0 | Everything is interconnected. Modifying one file risks breaking the rest. No boundaries between concepts. |
| 3 | Identifiable modules but cross-dependencies remain. The separation is intentional but incomplete. |
| 5 | Autonomous modules, clear contracts between bounded contexts, low coupling. Each module could be extracted without impacting the others. |

**Concrete example:** A project organized into bounded contexts (Audit, Certification, Exam, Security)
where each context has its own entities, repositories and controllers. Communications between contexts
go through shared interfaces in a Shared Kernel, never through direct imports.

**How to audit:** Dependency graph between modules. Check cross-imports. Ensure that no module directly accesses the internal details of another. Use tools like deptrac or phparkitect.

## Axis 2 — AI Orchestration

25 points

The human piloted the AI with method, traceability and validation.

**Why 25 points?** This is the axis that distinguishes OrkestrAI from any other software quality standard.
It evaluates not the code itself, but the way in which the human and AI collaborated to produce it.
Traceability of generations, systematic human validation, reproducibility of results and control
of the scope entrusted to AI — these are the markers of a professional and responsible use of artificial
intelligence in development. A project can have perfect code; if it was generated without supervision,
it is a house of cards.

### 2.1 Generation Traceability

0 — 7 pts

Can we identify, for each portion of the code, whether it was generated by AI or written manually?
Traceability is essential for maintenance: when a bug occurs in generated code, you need
to be able to find the generation context, the prompt used, and the decisions made. Without traceability,
AI code becomes a black box that is impossible to debug effectively.

| Score | Criterion |
| --- | --- |
| 0 | Impossible to tell what was generated by AI vs written by hand. No convention, no trace. |
| 3 | Mentions in commits or documentation, but not systematic. The traceability effort is partial. |
| 5 | Commit convention identifying AI generations (tag, prefix). The history can be filtered to isolate generated code. |
| 7 | Complete history: documented prompts, generated files identified, prompt versioning. Each generation is reproducible and auditable. |

**Concrete example:** Commits for AI-generated code include an `[AI]` tag in the message
and a `Co-Authored-By`. A `.prompts/` folder at the project root contains versioned prompts
used for each major feature. A CLAUDE.md file or an ADR documents the orchestration sessions.

**How to audit:** Analyze the git log. Look for AI commit conventions. Verify the existence of a prompts folder. Ask the team to trace the origin of a specific file.

### 2.2 Human Validation

0 — 7 pts

Was the AI-generated code systematically reviewed, understood and validated by a human before being integrated?
Human validation is the keystone of orchestration. A developer who copy-pastes AI code without
understanding it is not orchestrating — they are blindly delegating. Validation implies understanding,
critical review and explicit approval of each generation.

| Score | Criterion |
| --- | --- |
| 0 | AI code copy-pasted without review. No evidence of human understanding of the integrated code. |
| 3 | Partial review, no defined process. The developer reviews sometimes but without a method. |
| 5 | Systematic code review of generated code, validation checklist applied to each integration. |
| 7 | Documented process: each AI generation goes through a senior review + tests before merge. The process is verifiable in the history. |

**Concrete example:** Each PR contains a "Code generated by AI" section with the relevant files.
A validation checklist (tests pass, conventions respected, no security flaw, correct business logic)
is checked before merge. PRs without validation are blocked by the repository rules.

**How to audit:** Review PRs and review history. Verify the existence of a validation checklist. Ask the team to describe their AI code validation process.

### 2.3 Reproducibility

0 — 6 pts

Are the AI results reproducible? If the same prompt were run again in the same context,
would a similar result be obtained? Reproducibility comes from documenting prompts,
the context provided to the AI, and the parameters used. It is a fundamental principle of the scientific
approach applied to AI-assisted software engineering.

| Score | Criterion |
| --- | --- |
| 0 | Prompts discarded after use, no memory. Impossible to understand how the code was generated. |
| 3 | Prompts saved but not organized. Traces can be found but without method or structure. |
| 6 | Versioned prompt library, documented context, reproducible result. A new developer can pick up the project's AI workflow. |

**Concrete example:** A `.prompts/` folder contains prompt templates organized by domain
(architecture, tests, refactoring). Each template includes the context to provide, the constraints to specify,
and an example of expected results. Prompts are versioned with the code.

**How to audit:** Look for a prompts folder. Verify if templates are versioned. Ask the team to reproduce a past generation. Evaluate the AI workflow documentation.

### 2.4 AI Scope Control

0 — 5 pts

Has the developer clearly defined what the AI can and cannot do in the project?
A good orchestrator knows when to use AI and when to do without it. They define the constraints,
conventions and limits before launching a generation. Architectural decisions,
domain modeling and security choices must never be delegated to AI without supervision.

| Score | Criterion |
| --- | --- |
| 0 | The AI generated everything without guidance. No constraints were set, no limits defined. |
| 2 | The human defined the broad strokes but the AI had free rein on the detailed implementation. |
| 5 | AI scope clearly defined: the human decides the architecture, the AI implements under explicit documented constraints (CLAUDE.md, ADR, conventions). |

**Concrete example:** A `CLAUDE.md` file at the project root defines the architectural conventions,
the patterns to use, the naming rules and the prohibitions (no annotations in the domain,
no direct instantiation, tests mandatory). The AI is guided by these constraints at each generation.

**How to audit:** Verify the existence of guidance documents (ADR, CLAUDE.md, conventions). Ask the team what scope was given to the AI. Compare decision documentation with the delivered code.

## Axis 3 — Code Quality

20 points

The delivered code is readable, consistent and maintainable — regardless of who wrote it.

**Why 20 points?** Code quality is what you see when you open a source file.
It is the most tangible and immediate criterion. Quality code reads like well-written prose:
each variable has an intentional name, each function does one thing, each file follows the same
conventions. AI can generate code of varying quality — it is up to the developer to ensure that the final
result is homogeneous, readable and maintainable. Code quality is also the best indicator of the long-term
health of a project: clean code today means code that will still be understandable in 6 months.

### 3.1 Readability and Naming

0 — 6 pts

Is the code self-documenting? Do variable, function and class names clearly express
their intent? AI often generates generic names (`data`, `result`, `temp`)
or reproduces conventions from documentation examples. A good orchestrator enforces intentional
and consistent naming that reflects the business domain language.

| Score | Criterion |
| --- | --- |
| 0 | Inconsistent naming, obscure variables, spaghetti code. The code cannot be understood without significant effort. |
| 3 | Generally correct naming, some inconsistencies. The code is readable but not always expressive. |
| 6 | Intentional and uniform naming, self-documenting code, ubiquitous language respected throughout the project. |

**Concrete example:** Instead of `$data = $repo->get($id)`, the code reads
`$project = $this->projectRepository->findById(ProjectId::fromString($id))`.
Each name reveals the intent. Methods are named after business actions: `complete()`,
`certificationLevel()`, not `process()` or `handle()`.

**How to audit:** Read the source code. Verify the consistency of naming conventions. Search for generic naming patterns. Verify that the code vocabulary matches the business vocabulary.

### 3.2 Complexity

0 — 5 pts

Are functions short and focused? Is cyclomatic complexity under control?
AI tends to generate long functions with many conditional branches, because it
optimizes for completeness rather than simplicity. An orchestrator ensures that each
function operates at a single level of abstraction and remains understandable at a glance.

| Score | Criterion |
| --- | --- |
| 0 | Functions of 200+ lines, cyclomatic complexity > 20. The code is a maze of nested conditions. |
| 3 | Complexity under control overall, a few identified hotspots but limited. |
| 5 | Cyclomatic complexity < 10 everywhere, short functions, one level of abstraction per function. The code breathes. |

**Concrete example:** No method exceeds 20 lines. Complex conditions are extracted
into named methods (`isEligibleForCertification()` rather than a long chain of if/else).
PHPStan/Psalm at the maximum level reports no complexity alerts.

**How to audit:** Use static analysis tools (phpstan, phpmd, sonar). Measure cyclomatic complexity. Identify the longest functions. Check nesting levels.

### 3.3 Pattern Consistency

0 — 5 pts

Is the same problem solved the same way throughout the project? One of the most
common flaws of AI-generated code is stylistic inconsistency: each generation may propose a
different pattern for the same type of problem. A good orchestrator enforces conventions and verifies
that the AI follows them consistently.

| Score | Criterion |
| --- | --- |
| 0 | Each file uses a different style. Mix of patterns, contradictory conventions, feels like code written by 10 different people. |
| 3 | Generally consistent style with occasional deviations. The intent for consistency is visible. |
| 5 | One architectural style, conventions applied uniformly, linter/formatter configured and enforced. All similar files look alike. |

**Concrete example:** All controllers follow the same pattern (dependency injection, validation, delegation to domain, response).
All repositories implement the same interface. All Value Objects follow the same template (private constructor, factory method, equals).
A `.php-cs-fixer.php` or `phpcs.xml` file normalizes formatting.

**How to audit:** Compare similar files (two controllers, two repositories). Check the linter/formatter config. Ensure CI/CD checks the style.

### 3.4 Controlled Technical Debt

0 — 4 pts

Does the project contain dead code, orphan TODOs, unused dependencies or undocumented hacks?
AI sometimes generates code "just in case" — classes never used, redundant methods,
superfluous imports. A well-orchestrated project is a clean project where every line
of code has a reason to exist and every technical compromise is documented.

| Score | Criterion |
| --- | --- |
| 0 | TODOs everywhere, dead code, unused dependencies, undocumented hacks. The project accumulates silent debt. |
| 2 | Some residuals but identified and documented. The debt is known and prioritized. |
| 4 | No dead code, no orphan TODOs, clean dependencies, every compromise is documented in an ADR or explicit comment. |

**Concrete example:** `grep -r "TODO\|FIXME\|HACK" src/` returns no results.
`composer why` justifies each dependency. The rare technical compromises are documented
in ADRs (Architecture Decision Records) with the reason for the choice and the alternatives considered.

**How to audit:** Search for TODO/FIXME/HACK in the code. Analyze unused dependencies. Identify dead code with static analysis tools. Verify the presence of ADRs for compromises.

## Axis 4 — Performance and Sobriety

15 points

The code is efficient and does not waste resources.

**Why 15 points?** Performance and sobriety are increasingly critical concerns
in software development. AI does not care about performance — it generates the most "likely" code,
not the most efficient. Common problems with AI-generated code include N+1 queries, superfluous
dependencies, excessive data loading and a complete absence of Green IT considerations. A responsible
orchestrator ensures that the code is not only functional and well-structured, but also lean and
respectful of resources. Eco-design is not a luxury — it is a responsibility.

### 4.1 Query Efficiency

0 — 5 pts

Are data access patterns optimized? One of the most frequent pitfalls of AI code is the
N+1 problem: a loop that executes one query per iteration instead of a single load.
AI does not "see" the performance implications of its choices — it is up to the developer to verify
that queries are efficient, indexed and paginated.

| Score | Criterion |
| --- | --- |
| 0 | N+1 queries, queries in loops, no indexes. Each page triggers hundreds of unnecessary queries. |
| 3 | Queries are correct overall, some missing optimizations but no critical issues. |
| 5 | Optimized queries, eager loading when needed, relevant indexes, systematic pagination, profiling performed. |

**Concrete example:** The Symfony Profiler shows a reasonable number of queries per page (< 10).
Collections are loaded with JOINs when necessary. Lists are paginated.
Frequently queried columns are indexed. No `findAll()` followed by a PHP filter.

**How to audit:** Use the Symfony profiler (debug toolbar). Count queries per page. Check database indexes. Test with a significant data volume.

### 4.2 Dependency Sobriety

0 — 4 pts

Is every project dependency justified? AI frequently suggests third-party libraries
for simple tasks, creating dependency bloat that weighs down the project, increases the attack
surface and complicates updates. A good orchestrator challenges every `composer require`
and prefers native code when reasonable.

| Score | Criterion |
| --- | --- |
| 0 | 50 packages for a CRUD, duplicate dependencies, unused libs. The `vendor/` weighs more than the business code. |
| 2 | Reasonable dependencies but a few identified as superfluous. |
| 4 | Every dependency is justified, no bloat, maintained versions, dependency security audit performed. |

**Concrete example:** The `composer.json` contains only strictly necessary dependencies.
No 10,000-line library for a single utility function. `composer audit` reports
no vulnerabilities. Versions are carefully constrained (no `*`).

**How to audit:** Analyze the composer.json / package.json. Run `composer audit`. Verify that each dependency is actually used. Compare the ratio of business code to dependencies.

### 4.3 Resource Management

0 — 3 pts

Does the code properly manage system resources? Files, connections, memory — every resource
opened must be closed, every allocation must be freed. AI often forgets these details because they
are not necessary for the code to "work" on the surface, but they cause insidious
problems in production.

| Score | Criterion |
| --- | --- |
| 0 | Memory leaks, unclosed files, unreleased connections. The code works in dev but crashes in production. |
| 2 | Correct management overall, no obvious leaks detected. |
| 3 | Explicit resource management, lazy loading, relevant caching. The code is designed to run in production with real volume. |

**Concrete example:** Streams are closed in finally blocks. Lazy loading is used
for large collections. A cache system (HTTP, application) is in place for rarely modified data.
Memory is monitored in the profiler.

**How to audit:** Memory profiling. Review streams and connections. Check cache strategies. Test with a realistic data volume.

### 4.4 Eco-design

0 — 3 pts

Does the project incorporate digital sobriety considerations? At a time when digital technology accounts for
4% of global greenhouse gas emissions, eco-design is a professional duty.
A responsible orchestrator minimizes the footprint of their code: lightweight payloads, compression, HTTP cache,
optimized images, no superfluous requests.

| Score | Criterion |
| --- | --- |
| 0 | No Green IT consideration. 5 MB pages, hundreds of requests, no cache. |
| 2 | No obvious waste. The site is reasonably lightweight without deliberate effort. |
| 3 | Documented technical choices favoring sobriety: compressed payloads, HTTP cache configured, lazy loading of images, CSS/JS minified, no superfluous tracking. |

**Concrete example:** CSS is inlined and minified (no 300 KB CSS framework for a simple site).
Images are in WebP format with lazy loading. Cache headers are configured.
Lighthouse shows a performance score > 90. Total page weight is under 500 KB.

**How to audit:** Measure page weight with DevTools. Count the number of requests. Check cache headers. Run a Lighthouse audit. Evaluate the overall sobriety strategy.

## Axis 5 — Security

10 points

The code has no known vulnerabilities and protects user data.

**Why 10 points?** Security is a cross-cutting concern that permeates all other axes.
If it represents "only" 10 dedicated points, it is because many criteria from other axes contribute
indirectly to security (clean architecture, tests, human validation). Nevertheless, these 10 points
are critical: a single security flaw can compromise an entire project. AI is particularly
dangerous in this area because it reproduces vulnerable patterns with an apparent confidence that
can deceive an unwary developer. SQL injection, XSS, plaintext secrets, missing validation
— these are errors that AI commits regularly and that the orchestrator must systematically verify.

### 5.1 OWASP Top 10

0 — 4 pts

Is the code protected against the 10 most common web vulnerabilities? The OWASP standard
is the global reference for application security. The most frequent flaws in
AI-generated code are injections (SQL, XSS, command), missing CSRF, poor
authentication management and exposure of sensitive data in API responses.

| Score | Criterion |
| --- | --- |
| 0 | SQL injection, XSS, unprotected CSRF. The code is vulnerable to the most basic attacks. |
| 2 | Basic protections in place (ORM with parameterized queries, Twig escaping). The fundamentals are covered. |
| 4 | OWASP Top 10 covered: parameterized queries, CSP headers, CSRF tokens, server-side input validation, rate limiting, security headers configured. |

**Concrete example:** All queries go through the ORM (no raw SQL). Twig templates
automatically escape variables. Forms include a CSRF token. Security headers
(X-Content-Type-Options, X-Frame-Options, CSP) are configured. User inputs are validated
server-side, never only client-side.

**How to audit:** Run a security scan (OWASP ZAP, Snyk). Manual review of user inputs. Check HTTP security headers. Test forms without JavaScript to validate server-side protection.

### 5.2 Secret Management

0 — 3 pts

Are secrets (passwords, API keys, tokens) properly externalized and protected?
This is one of the most serious and frequent errors in AI-generated code: hardcoded
credentials in the source code, a `.env` file committed by mistake, API keys
visible in the git history. A single leaked secret can compromise an entire system.

| Score | Criterion |
| --- | --- |
| 0 | Hardcoded credentials in the code, .env committed to git. Secrets are publicly exposed. |
| 2 | Environment variables used but .env.example incomplete or secrets in the git history. |
| 3 | Externalized secrets, complete .env.example, rigorous .gitignore, no secrets in git history. Secret rotation documented. |

**Concrete example:** The `.env` file is in the `.gitignore`. A `.env.example`
documents all required variables with placeholder values. `git log -S "password"` returns
no suspicious results. Production secrets are managed by a vault or server
environment variables, never in the code.

**How to audit:** Search for secret patterns in the code (grep for password, secret, api\_key, token). Check the .gitignore. Scan the git history with a tool like truffleHog or gitleaks. Check the .env.example.

### 5.3 Data Protection

0 — 3 pts

Is personal user data protected in compliance with GDPR and security best
practices? AI has no notion of regulatory compliance. It can log personal data,
store passwords in plaintext, or expose sensitive information in API responses.
It is up to the developer to ensure that every piece of personal data is treated with the respect it deserves.

| Score | Criterion |
| --- | --- |
| 0 | Personal data exposed, no validation, logs containing PII (Personally Identifiable Information). |
| 2 | Input validation, sensitive data not logged. The basics of GDPR are respected. |
| 3 | GDPR compliant: consent implemented, right to erasure functional, data encrypted at rest, logs cleaned of all PII, retention policy defined. |

**Concrete example:** Passwords are hashed with bcrypt/argon2 (never in plaintext or MD5).
Logs contain no emails, names, or identifying IP addresses. An account deletion endpoint
exists and anonymizes all user data. The cookie policy is GDPR compliant.

**How to audit:** Full GDPR audit. Review production logs (look for PII). Verify password hashing. Test account deletion. Check the cookie policy and consent.

## Points Distribution

Overview of 100 points distributed across 5 evaluation axes. The weighting reflects the impact of each domain on the overall quality of an organically orchestrated AI-assisted project.

pts

## Audit Process

An OrkestrAI audit follows 5 rigorous steps, from initial contact to badge delivery. Each step is designed to ensure an objective, comprehensive and actionable evaluation.

1

### Scoping

The auditor defines the evaluation scope with the project team. This step helps understand the technical and organizational context.

* Project scope and objectives
* Tech stack and AI tools used
* Team size and roles
* Estimated share of AI-generated code
* Specific constraints (regulatory, performance, etc.)

2

### Automated Analysis

Static analysis and security tools provide an initial objective and quantifiable evaluation of the code.

* Static analysis (PHPStan, Psalm, SonarQube)
* Security scan (Snyk, OWASP ZAP, gitleaks)
* Cyclomatic complexity metrics
* Test coverage and assertion quality
* Dependency audit (vulnerabilities, licenses)

3

### Human Review

A senior auditor reads the code, evaluates each criterion and discusses with the team to understand the technical choices.

* In-depth source code reading
* Evaluation of each standards criterion
* Interview with the team about the AI process
* Verification of generation traceability
* Analysis of git history and PRs

4

### Detailed Report

The auditor writes a comprehensive report with the score for each criterion, strengths, areas for improvement and prioritized recommendations.

* Detailed score by axis and criterion
* Identified and highlighted strengths
* Weaknesses with concrete recommendations
* Prioritized action plan for improvement
* Certification level awarded

5

### Certification

OrkestrAI badge awarded if score ≥ 60 — PDF certificate with unique verification code —
Embeddable widget for your website — Valid for 12 months — Annual re-certification recommended

## Frequently Asked Questions

Everything you need to know about the OrkestrAI standards and the certification process.

### Is the use of AI mandatory to be certified?

Yes, the OrkestrAI standards specifically evaluates the quality of code produced in collaboration with AI tools.
Axis 2 (AI Orchestration, 25 points) measures how AI was used, traced and validated.
A project written entirely by hand could not earn these 25 points and would therefore have a maximum score of 75/100.

### Which AI tools are accepted?

All generative AI tools for code are accepted: ChatGPT, Claude, GitHub Copilot, Cursor,
Codeium, Amazon CodeWhisperer, and any other similar tool. What matters is not the tool used
but the way it was orchestrated by the developer.

### What is the difference between the individual exam and the project audit?

The individual exam (30-question MCQ in 60 minutes) certifies a developer's **theoretical knowledge**
of AI orchestration best practices. The project audit certifies the **practical implementation**
of this knowledge on a real project. Both certifications are complementary and independent.

### How is the audit score calculated?

The auditor evaluates each criterion individually according to the detailed scoring grid above.
Each criterion has a minimum score of 0 and a defined maximum score. The total score is the sum
of the 19 criteria, out of 100 points. The auditor justifies each grade with concrete observations
drawn from the source code.

### What happens if I don't reach 60 points?

You still receive the detailed report with the score for each criterion and improvement
recommendations. You can submit your project for a new audit after implementing the
improvements. There is no limit to the number of attempts — the goal is progress, not punishment.

### Will the standards evolve?

Yes. The AI field evolves rapidly and our standards must reflect current best practices.
We plan biannual updates incorporating auditor feedback, new security threats
and evolving AI orchestration practices. Each version
is numbered and changes are documented.

## Ready to be audited?

Have your code evaluated against these standards or take the individual exam to validate your skills.

Create my account
Discover OrkestrAI

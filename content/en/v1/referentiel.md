# OrkestrAI Referential Standard v1

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

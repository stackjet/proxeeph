<!--
Sync Impact Report:
Version Change: 1.1.2 → 1.1.3
Modified Principles:
  - VI. Ticket-Based Task Workflow - Clarified todo.md generation uses both /speckit.specify and /speckit.plan
Added Sections: None
Removed Sections: None
Templates Status:
  ✅ plan-template.md - No changes required
  ✅ spec-template.md - No changes required
  ✅ tasks-template.md - No changes required
Follow-up TODOs: None
-->

# Proxee PH Constitution

## Core Principles

### I. Domain-Driven Design (DDD) Architecture

The platform MUST adhere to Domain-Driven Design principles for modularity and maintainability:

- **Domain Modules**: Each business capability (e.g., Seller, Reviews, Payout, Gigs, Orders) MUST be encapsulated in its own module with clear boundaries
- **Service Layer**: Business logic MUST reside in service classes, not in controllers or models
- **Repository Pattern**: Data access MUST be abstracted through repositories to decouple domain logic from persistence
- **Event-Driven Communication**: Modules MUST communicate through domain events, not direct coupling
- **Bounded Contexts**: Each module maintains its own models and database schema within its bounded context

**Rationale**: DDD ensures the codebase remains scalable and maintainable as the service marketplace grows. Clear boundaries prevent tight coupling and enable independent module development.

### II. Reusability at Appropriate Levels

Code reusability MUST respect architectural boundaries:

- **Domain-Level Reusability**: Functions specific to a domain (e.g., `calculateCommission`, `validateGigRequirements`) MUST remain within their respective modules
- **System-Level Utilities**: Generic utilities (e.g., validation helpers, date formatters, string processors) MUST be placed in shared libraries accessible across all modules
- **No Cross-Domain Dependencies**: Domain modules MUST NOT directly import services or repositories from other domains; use events or shared interfaces instead
- **Explicit Sharing**: Shared code MUST be intentionally designed and documented, not accidentally coupled

**Rationale**: Proper reusability prevents inadvertent tight coupling while maximizing code sharing where appropriate. This maintains module independence while avoiding duplication.

### III. Test-First Development (NON-NEGOTIABLE)

Testing MUST follow a disciplined, test-first approach:

- **Endpoint Testing Mandatory**: Every API endpoint MUST have corresponding integration tests before deployment
- **Test-Driven Development**: Write tests → Get user approval → Verify tests fail → Implement → Tests pass
- **Test Coverage Requirements**:
  - All API routes: Integration tests covering request/response contracts
  - Business logic services: Unit tests for edge cases and validation
  - Domain events: Tests ensuring event handlers execute correctly
- **No Untested Code**: Code without tests MUST NOT be merged to main branches

**Rationale**: Service marketplaces handle sensitive transactions between buyers and sellers. Comprehensive testing prevents regressions, ensures reliability, and builds user trust.

### IV. Service Marketplace Context Awareness

All development MUST align with service marketplace requirements as documented in `tmp/Fiverr-Transition/Fiverr-Transition-Analysis.md`:

- **Reusable Features**: Leverage existing modules (Seller→Provider, Reviews, Payout, Commission, Algolia) with documented adaptations
- **Feature Modifications**: Major modifications (Order workflow, Payment escrow) MUST reference the transition analysis for requirements
- **Feature Removal**: Product-centric features (Inventory, Shipping, Fulfillment) MUST be removed or refactored for services
- **New Features**: Service-specific features (Gigs, Deliveries, Disputes, Custom Offers) MUST follow the architectural patterns established in existing modules

**Rationale**: The transition analysis provides a blueprint for converting product marketplace features to service marketplace features. Adhering to this ensures consistency and prevents scope creep.

### V. MercurJS/MedusaJS Framework Compliance

Development MUST follow MercurJS and MedusaJS best practices:

- **Medusa Patterns**: Use Medusa's service container, workflow engine, and event bus
- **Module Structure**: Follow Medusa's module conventions (models, services, migrations, repositories)
- **API Standards**: Implement RESTful routes following Medusa's route registration patterns
- **Database Migrations**: All schema changes MUST use Medusa's migration system
- **Type Safety**: Leverage TypeScript for type-safe development across all modules

**Rationale**: MercurJS is built on MedusaJS, and following framework conventions ensures compatibility, upgradability, and access to the ecosystem's features and community support.

### VI. Ticket-Based Task Workflow

All implementation tasks MUST follow a structured ticket workflow for traceability and quality:

- **Task Definition**: Tasks are defined in `implementation-tasks/<task-name>.md` files
- **Ticket Pattern Recognition**: When a task follows the pattern `implementation-tasks/<task-name>.md`, the complete ticket workflow MUST be executed
- **Ticket Folder Naming**: Each ticket MUST be created in a timestamped directory using the format `tickets/YYYYMMDDHHMMSS-<descriptive-name>/`
  - Timestamp: Year, month, day, hour, minute, second (e.g., `20251009164530`)
  - Descriptive name: Kebab-case derived from task title or understanding (e.g., `initialize-function`, `add-gig-module`)
  - Example: `tickets/20251009164530-initialize-function/`
- **Ticket Structure**: Each ticket directory MUST contain:
  1. **todo.md**: Implementation plan generated using `/speckit.specify` (to analyze task) followed by `/speckit.plan` (to create plan with tech stack)
  2. **questions.md**: Clarifying questions generated using `/speckit.clarify` command
  3. **user-todo.md**: Manual steps required outside the codebase (database configuration, external service setup, etc.)
  4. **analysis.md**: Post-implementation analysis generated using `/speckit.analyze` command (created ONLY after implementation completion)
- **Todo Generation Process**:
  - `/speckit.specify`: Reads and analyzes the task markdown file from `implementation-tasks/<task-name>.md`
  - `/speckit.plan`: Creates the implementation plan ensuring use of current project tech stack unless task file specifies additional requirements under "Tech Stack Required" section
  - Both commands work together to produce `todo.md`
- **Tech Stack Requirements**:
  - Default tech stack MUST be based on the current project technology (MercurJS, MedusaJS, TypeScript, PostgreSQL, etc.)
  - Additional tech stack requirements MUST only be introduced when explicitly specified in the task file under a "Tech Stack Required" section
  - The `/speckit.plan` command MUST use the existing project tech stack unless the task file specifies additional requirements
- **Workflow Execution Order**:
  1. Create timestamped ticket directory: `tickets/YYYYMMDDHHMMSS-<descriptive-name>/`
  2. Generate todo.md (via `/speckit.specify` then `/speckit.plan`)
  3. Generate questions.md (via `/speckit.clarify`)
  4. Create user-todo.md (manual steps identification)
  5. Implement the task according to todo.md
  6. Generate analysis.md (via `/speckit.analyze`) after implementation
- **Automated Workflow**: The ticket generation process MUST be automated when the `implementation-tasks/<task-name>.md` pattern is recognized

**Rationale**: A structured ticket workflow ensures every task has clear planning, addresses ambiguities upfront, separates developer and user responsibilities, and provides post-implementation validation. Timestamped folders enable chronological tracking and prevent naming conflicts. Tech stack consistency ensures alignment with the existing architecture unless explicitly required otherwise. This improves task clarity, reduces rework, and maintains a complete audit trail of implementation decisions.

## Development Standards

### API Design

- **RESTful Conventions**: All API endpoints MUST follow REST principles (proper HTTP methods, status codes, resource naming)
- **Versioning**: API breaking changes MUST be versioned (e.g., `/v1/`, `/v2/`)
- **Documentation**: All endpoints MUST be documented with OpenAPI/Swagger annotations
- **Error Handling**: Consistent error response format across all endpoints with meaningful error codes and messages
- **Validation**: Input validation MUST occur at the API layer before reaching services

### Data Integrity

- **Database Constraints**: Use database-level constraints (foreign keys, unique constraints, check constraints) to enforce data integrity
- **Transactional Operations**: Multi-step operations affecting data consistency MUST use database transactions
- **Soft Deletes**: Critical entities (Orders, Gigs, Users) MUST use soft deletes to preserve audit trails
- **Audit Logging**: All state changes for critical entities MUST be logged with timestamp and actor

### Security

- **Authentication**: All protected endpoints MUST verify JWT tokens
- **Authorization**: Role-based access control (RBAC) MUST be enforced for vendor, buyer, and admin operations
- **Data Sanitization**: All user inputs MUST be sanitized to prevent injection attacks
- **Sensitive Data**: Payment information, credentials, and PII MUST be handled according to security best practices (encryption at rest, secure transmission)

## Quality Gates

### Pre-Commit

- **Linting**: Code MUST pass ESLint and TypeScript compiler checks
- **Formatting**: Code MUST be formatted with Prettier
- **Type Safety**: No TypeScript `any` types without explicit justification

### Pre-Merge

- **All Tests Pass**: 100% of existing tests MUST pass
- **New Tests Added**: New functionality MUST include corresponding tests
- **Code Review**: At least one peer review approval required
- **No Console Logs**: Remove debug `console.log` statements
- **Ticket Completion**: For tasks following the ticket workflow, all ticket artifacts (todo.md, questions.md, user-todo.md, analysis.md) MUST be complete

### Pre-Deployment

- **Integration Testing**: Full integration test suite MUST pass
- **Migration Testing**: Database migrations MUST be tested on staging environment
- **Performance Check**: No endpoint regressions beyond 200ms p95 latency
- **Documentation Updated**: API documentation and README changes merged

## Governance

### Constitution Authority

This constitution supersedes all other development practices and guidelines. When conflicts arise between this document and other documentation, the constitution takes precedence.

### Amendment Process

1. **Proposal**: Any team member may propose an amendment with justification
2. **Review**: Technical leads review for impact on existing architecture
3. **Approval**: Requires consensus from all technical leads
4. **Migration Plan**: If amendment affects existing code, a migration plan MUST be documented
5. **Version Bump**: Constitution version incremented according to semantic versioning

### Compliance Review

- **Pull Request Reviews**: All PRs MUST be checked against constitution principles
- **Quarterly Audits**: Codebase compliance audit conducted every quarter
- **Violation Handling**: Constitution violations MUST be documented with justification or remediated
- **Team Training**: New team members MUST review constitution during onboarding

### Complexity Justification

When deviating from constitutional principles (e.g., adding complexity, skipping tests), developers MUST:

1. Document the specific violation and context
2. Explain why the deviation is necessary
3. Describe simpler alternatives considered and why they were rejected
4. Get explicit approval from technical leads
5. Add technical debt tracking if the violation is temporary

**Version**: 1.1.3 | **Ratified**: 2025-10-09 | **Last Amended**: 2025-10-09

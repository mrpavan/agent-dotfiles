<!--
  Sync Impact Report:
  Version: 0.0.0 → 1.0.0 (Initial constitution creation)
  Modified Principles: N/A (new file)
  Added Sections: Core Principles, Component Architecture, Development Standards, Testing Requirements, Governance
  Removed Sections: N/A
  Templates requiring updates:
    ✅ plan-template.md - Constitution Check section exists, no changes needed
    ✅ spec-template.md - No constitution-specific references, no changes needed
    ✅ tasks-template.md - No constitution-specific references, no changes needed
  Follow-up TODOs: None
-->

# BMP UI Constitution

## Core Principles

### I. Component Library Architecture
All reusable UI components MUST be placed in `src/@bmp/components/`. Components in this directory MUST be:
- Self-contained and independently testable
- Follow the established naming convention (kebab-case directories)
- Include HTML template, SCSS styles, and TypeScript implementation
- Export through proper module/standalone component patterns

**Rationale**: Centralizing reusable components ensures consistency, reduces duplication, and enables shared component documentation and testing.

### II. Standalone Components (NON-NEGOTIABLE)
All new components MUST be created as standalone components (Angular 18+ pattern). Components MUST:
- Use `standalone: true` in component decorator
- Explicitly import required dependencies via `imports` array
- Avoid NgModule dependencies unless integrating with legacy code
- Follow feature-based organization within modules

**Rationale**: Standalone components reduce boilerplate, improve tree-shaking, and align with Angular's modern architecture direction.

### III. Feature Module Organization
Application features MUST be organized as feature modules under `src/app/modules/`. Each module MUST:
- Contain related components, services, and routes
- Follow domain-driven structure (auth, campaigns, channels, dashboard, etc.)
- Maintain clear separation of concerns between modules
- Use lazy loading for route-based modules when appropriate

**Rationale**: Feature modules enable scalable architecture, clear boundaries, and independent development of application domains.

### IV. TypeScript Strict Mode Compliance
All TypeScript code MUST comply with strict mode settings:
- `strict: true` - All strict type checking enabled
- `noImplicitOverride: true` - Explicit override keyword required
- `noPropertyAccessFromIndexSignature: true` - Safe property access
- `noImplicitReturns: true` - All code paths must return
- `strictInjectionParameters: true` - Dependency injection type safety
- `strictTemplates: true` - Template type checking

**Rationale**: Strict mode prevents runtime errors, improves code quality, and ensures type safety across the application.

### V. Testing Standards (NON-NEGOTIABLE)
All components and services MUST have associated tests. Testing requirements:
- Unit tests using Karma/Jasmine for all components and services
- Minimum coverage thresholds: 45% statements, 30% branches, 40% functions, 45% lines (global)
- Tests MUST be written before or alongside implementation
- Test files MUST use `.spec.ts` suffix and be co-located with source files
- Integration tests required for complex user journeys and cross-module interactions

**Rationale**: Comprehensive testing ensures reliability, prevents regressions, and maintains code quality as the application scales.

## Development Standards

### Styling Architecture
- SCSS MUST be used for all component styles
- Shared styles MUST be placed in `src/@bmp/styles/`
- Use Angular Material theming (azure-blue theme)
- Follow BEM-like naming conventions for component-specific styles
- Avoid global style pollution; scope styles to components

### Authentication & Authorization
- Azure MSAL MUST be used for authentication
- Auth guards MUST protect routes requiring authentication
- Role-based access control (RoleGuard) MUST be implemented for role-protected routes
- Token validation MUST occur on application initialization
- Auth state MUST be managed through AuthService

### Error Handling
- HTTP interceptors MUST handle API errors consistently
- Error messages MUST be user-friendly and actionable
- Toast notifications MUST be used for user-facing errors
- Console errors MUST be logged appropriately for debugging
- Error boundaries MUST prevent application crashes

### State Management
- Services MUST be used for shared state management
- RxJS observables MUST be used for reactive data flows
- Unsubscribe from subscriptions to prevent memory leaks
- Use async pipe in templates when possible

### Branch Naming Convention
Feature branches MUST follow the format: `[JIRA-ID]-fea/title-desc`
- Example: `CP-1627-fea/001-remove-storybook`
- JIRA-ID: The ticket identifier (e.g., CP-1627)
- `fea/`: Prefix indicating a feature branch
- `title-desc`: Descriptive title in kebab-case

**Rationale**: Consistent branch naming enables traceability, automated tooling integration, and clear communication of branch purpose.

## Testing Requirements

### Coverage Thresholds
- Global minimum: 45% statements, 30% branches, 40% functions, 45% lines
- Per-file thresholds: Currently disabled but should be enforced for new code
- Coverage reports MUST be generated for all test runs
- CI/CD MUST enforce coverage thresholds

### Test Organization
- Unit tests: Co-located with source files (`*.spec.ts`)
- Integration tests: Place in appropriate test directories
- E2E tests: Use Angular's e2e testing framework
- Mock data: Centralize in `src/app/testing/mock/`

## Code Quality

### Linting & Formatting
- ESLint/Angular linting MUST pass before commits
- Code formatting MUST be consistent across the codebase
- Pre-commit hooks SHOULD enforce linting rules

### Performance Standards
- Initial bundle size MUST not exceed 2MB (production build)
- Component styles MUST not exceed 1MB per component
- Lazy loading MUST be used for feature modules
- Images and assets MUST be optimized

### Accessibility
- Components MUST follow WCAG 2.1 guidelines
- ARIA labels MUST be used where appropriate
- Keyboard navigation MUST be supported
- Screen reader compatibility MUST be tested

## Governance

This constitution supersedes all other development practices and coding standards. All PRs and code reviews MUST verify compliance with these principles.

**Amendment Procedure**:
1. Proposed amendments MUST be documented with rationale
2. Amendments affecting core principles require team approval
3. Version MUST be incremented according to semantic versioning:
   - MAJOR: Backward incompatible principle changes
   - MINOR: New principles or significant additions
   - PATCH: Clarifications, wording improvements, non-semantic updates
4. All dependent templates and documentation MUST be updated upon amendment

**Compliance Review**:
- Code reviews MUST check constitution compliance
- Automated checks SHOULD enforce technical standards where possible
- Regular audits SHOULD be conducted to ensure ongoing compliance

**Version**: 1.0.2 | **Ratified**: 2025-01-27 | **Last Amended**: 2026-01-02

# Frontend Rules (Angular 20 / NgRx)

Apply these rules only when the MR diff contains frontend files (`*.ts`, `*.html`, `*.css`, `*.scss`, `angular.json`), including repo-prefixed GitLab paths (for example `bmi-pka-web/src/app/modules/...`).

## 1. Always-Apply Frontend Quality Gates

Every frontend diff must pass all six gates:

- **Mandatory NgRx state & side effects**: Manage all server state and asynchronous side effects through NgRx. Components and pages only dispatch actions and select from the store.
- **Strict HTTP boundary**: Never call or inject HTTP API services directly inside a component or page. Inject API services only inside NgRx effects or established infrastructure services.
- **Zero memory leaks**: Clean up every subscription, timer, and DOM listener using `takeUntilDestroyed`, the `async` pipe, or the codebase's established teardown pattern.
- **No performance regressions**: Enforce `ChangeDetectionStrategy.OnPush` (especially on lists), prevent redundant re-renders, and never run heavy computations or function calls inside HTML templates.
- **Client security & permission constants**: Never place secrets in client code or bundles, never introduce XSS (`innerHTML` or `bypassSecurityTrust*` without the official sanitization path), and check permissions via constants + route guards/directives rather than role names.
- **Sibling parity**: Match feature folder structure, i18n key usage, and container-vs-presentational boundaries with sibling features in the same module.

## 2. Glob-Based Frontend Rules (18 Files)

Match each changed file path (ignoring any leading repository folder prefix) against the `Glob` column. Evaluate the changed lines against every matched rule:

| Rule File | Evaluated Criteria | Trigger (`Wajib` / `Glob`) |
| :--- | :--- | :--- |
| **Quality gates** (`frontend/00-quality-gates.mdc`) | Mandatory for all web work: NgRx store/effects boundary, memory leak teardown, `OnPush`, XSS prevention, permission constants. | **Wajib:** Always apply when any TS/HTML/CSS/SCSS file is in the diff |
| **Zero hardcode (wajib)** (`angular-no-hardcode.mdc`) | UI copy, magic numbers, URLs, permissions, and status values must come from constants, enums, or i18n dictionaries. | **Wajib:** Always apply when frontend stack is detected |
| **Konvensi class & import** (`angular-class-ordering.mdc`) | Class member ordering, import ordering, and Angular 20 TypeScript conventions (within quality gate bounds). | **Glob:** `**/*.ts` |
| **Standar component** (`angular-component-standards.mdc`) | Signal `input()`/`output()`, `ChangeDetectionStrategy.OnPush`, container vs presentational split, lifecycle hooks. | **Glob:** `**/*.component.ts`, `**/*.page.ts` |
| **Struktur feature** (`angular-feature-structure.mdc`) | Feature directory layout, lazy routing, resolvers, route guards, standalone components, and zero hardcode. | **Glob:** `src/app/modules/**/*.ts` |
| **Ikon** (`angular-icons.mdc`) | On-demand icon loading via `sb-icon`; never import or bundle full icon packs. | **Glob:** `**/sb-icon/**`, `**/public/assets/icons/**` |
| **Arsitektur library** (`angular-library-architecture.mdc`) | Monorepo library boundaries across `common`, `network`, `storage`, `security`, and UI entry points. | **Glob:** `projects/**/*.ts`, `src/app/shared/**/*.ts` |
| **Batas API NgRx** (`angular-ngrx-api-boundary.mdc`) | API calls strictly inside NgRx effects; pages and components never call HTTP services directly. | **Glob:** `**/modules/**/*.page.ts`, `**/modules/**/*.component.ts` |
| **Observability** (`angular-observability.mdc`) | Client error tracking, structured logging, performance marks, and health check endpoints. | **Glob:** `**/*.ts`, `**/*.service.ts` |
| **Performance** (`angular-performance.mdc`) | Bundle budgets, lazy loading, route preloading, optimized images, and `OnPush` change detection. | **Glob:** `**/*.component.ts`, `**/*.component.html`, `**/angular.json`, `**/*.routes.ts` |
| **PII** (`angular-pii-protection.mdc`) | PII classification, UI/log masking, user consent, browser storage restrictions, transit security, and retention. | **Glob:** `**/*.ts`, `**/*.html` |
| **Security** (`angular-security.mdc`) | XSS prevention, CSP compliance, authentication flows, dependency vulnerability checks, and OWASP controls. | **Glob:** `**/*.ts`, `**/*.html` |
| **Service, HTTP, model** (`angular-service-http-model.mdc`) | Typed `HttpClient` contracts, domain models, constants, enums, and DTO separation. | **Glob:** `**/*.service.ts`, `**/*.model.ts`, `**/*.const.ts`, `**/*.dto.ts` |
| **Shared component** (`angular-shared-components.mdc`) | Shared UI library reuse, Signal API inputs/outputs, base component classes, and pure pipes. | **Glob:** `src/app/shared/**/*.ts`, `src/app/shared/**/*.html` |
| **SSR & hydration** (`angular-ssr-hydration.mdc`) | Server-Side Rendering safety (no direct `window`/`document` access), hydration, prerendering, and zoneless readiness. | **Glob:** `**/*.component.ts`, `**/server.ts`, `**/app.config.server.ts` |
| **State NgRx** (`angular-state-management.mdc`) | Actions, immutable reducers (no in-place state mutation), memoized selectors, effects, Signal integration, zero hardcode. | **Glob:** `**/*.store.ts`, `**/*.effect.ts` |
| **Template & i18n** (`angular-template-i18n.mdc`) | Angular 20 control flow (`@if`, `@for`), reactive forms, i18n translation pipes/keys, and zero hardcoded UI strings. | **Glob:** `**/*.component.html`, `**/*.component.ts` |
| **Testing** (`angular-testing.mdc`) | Unit specs, component specs, E2E specs, test count non-regression, and coverage thresholds. | **Glob:** `**/*.spec.ts`, `**/*.e2e-spec.ts`, `**/test-setup.ts` |

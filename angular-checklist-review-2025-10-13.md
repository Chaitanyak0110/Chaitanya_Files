# Angular Code Review Checklist – InventoryApp (2025-10-13)

Scope: Review focused on Angular structure, patterns, and performance practices across `src/app/**`.

Legend: Pass/Fail • Score 1-10 • Evidence • Suggestions

1) Components are lean (UI logic only)
- Result: Pass • 8/10
- Evidence:
  - `features/dashboard/dashboard.component.ts`: UI state, form handling, invokes `InventoryService` for business operations. `formatHttpError` present but small.
  - `features/auth/login.component.ts`: Uses `UserService` for auth; component retains UI state and form logic.
  - `features/payment/payment.component.ts`: Orchestrates `UserService.buyProduct`, leaves model and persistence in service.
- Suggestions: Move `formatHttpError` to a small shared util to reuse; keep components <200 lines (current HomeComponent is large but purely presentational).

2) Naming follows Angular style guide
- Result: Pass • 9/10
- Evidence:
  - Filenames like `dashboard.component.ts`, `user.service.ts`, `auth.guard.ts` and PascalCase classes.
  - One legacy file `core/models/inventory.models.ts` noted but seemingly unused.
- Suggestions: Delete or archive `core/models/inventory.models.ts` to prevent accidental imports.

3) Templates declarative, minimal logic
- Result: Pass • 8/10
- Evidence:
  - `dashboard.component.ts` inline template uses bindings and event handlers without complex expressions.
  - `home.component.ts` inline template is large but declarative content and classes; minimal logic.
- Suggestions: For readability, consider moving long templates to `.html` files, but not required.

4) RxJS subscriptions managed (avoid leaks)
- Result: Mixed • 6/10
- Evidence:
  - HTTP subscriptions in `dashboard.component.ts` complete automatically (safe).
  - `payment.component.ts` subscribes to `route.params` and `userService.products$` without teardown; component does not implement `OnDestroy` and no `async` pipe used.
- Suggestions: Use `combineLatest` with `takeUntilDestroyed()` or `async` pipe in template. Example: `this.route.params.pipe(takeUntilDestroyed()).subscribe(...)` and `products$ | async`.

5) Presentational components use OnPush and immutable inputs
- Result: Fail • 3/10
- Evidence:
  - No `ChangeDetectionStrategy.OnPush` found across components; default change detection used.
- Suggestions: For non-form heavy/pure presentational components (e.g., Home), enable `changeDetection: ChangeDetectionStrategy.OnPush` and pass immutable inputs.

6) *ngFor trackBy for object lists
- Result: Fail • 4/10
- Evidence:
  - `dashboard.component.ts` and `dashboard.component.html` contain `*ngFor` without `trackBy`.
- Suggestions: Add `trackBy` with stable IDs: `trackBy: (i, item) => item.inventoryID` and for products `trackBy: (i, p) => p.id`.

7) Strict type safety enforced
- Result: Pass • 9/10
- Evidence:
  - `tsconfig.json` has `"strict": true`, `strictTemplates: true`, and other strict flags.
  - A few `any` types: `formatHttpError(err: any)` and `InventoryService` methods returning `Observable<any>` for stock ops.
- Suggestions: Replace `any` with typed `HttpErrorResponse` and strongly type stock responses if API contracts are known.

8) Duplication avoided; shared logic extracted
- Result: Mixed • 7/10
- Evidence:
  - Error formatting duplicated potential across components; currently only in Dashboard.
  - Product defaults defined twice in `UserService` (`init` and `resetProducts`).
- Suggestions: Extract shared constants for default products and a small `error.util.ts` for formatting.

9) Functions concise and focused (<75 lines)
- Result: Pass • 8/10
- Evidence:
  - Component methods are short and action-specific. `HomeComponent` template is very long but not function code.
- Suggestions: None urgent. Consider splitting Home into smaller presentational subcomponents if it grows.

10) Services provided at correct scope
- Result: Pass • 10/10
- Evidence:
  - `@Injectable({ providedIn: 'root' })` used in `UserService` and `InventoryService`.
- Suggestions: None.

---

Summary
- Strong: Strict typing, service boundaries, standalone components, CLI scripts, environment-driven API base URL.
- Needs attention: Subscription teardown for long-lived streams, adopt `OnPush` where beneficial, add `trackBy` to large lists, reduce remaining `any` types, DRY default product constant and error formatting.

Quick Fixes (suggested next PR)
- Add `trackBy` in dashboard list and buyer products grid.
- Switch `HomeComponent` and other pure UIs to `ChangeDetectionStrategy.OnPush`.
- In `PaymentComponent`, replace nested subscriptions with `combineLatest([route.params, products$])` + `takeUntilDestroyed()`.
- Type `formatHttpError(err: HttpErrorResponse)` and `InventoryService.add/removeStock` return types.

Reviewed on branch: V2
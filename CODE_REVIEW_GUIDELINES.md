
# Angular Inventory Management System - Code Review Guidelines

These guidelines outline the standards and best practices for developing the Angular Inventory Management System. Adhering to these principles will ensure consistency, maintainability, performance, and scalability of our codebase.

## 1. Naming Conventions

Consistency in naming is paramount for readability and quick understanding.

*   **Files and Symbols:**
    *   Use `kebab-case` for file names (e.g., `product-list.component.ts`, `inventory.service.ts`).
    *   For symbols within files, use `PascalCase` for classes and `camelCase` for variables, properties, and methods.
    *   Separate feature-specific files with `.` (e.g., `product-list.component.ts`, `product-list.component.html`, `product-list.component.scss`).

*   **Classes:**
    *   Use `PascalCase` (Upper Camel Case) (e.g., `ProductListComponent`, `InventoryService`).
    *   Suffix classes based on their type (e.g., `Component`, `Service`, `Guard`, `Pipe`, `Module`).

*   **Properties and Methods:**
    *   Use `camelCase` (Lower Camel Case) (e.g., `productId`, `loadProducts()`, `isAuthenticated`).

*   **Variables:**
    *   Use `camelCase`. Constants should be `UPPER_SNAKE_CASE` if they are global and truly immutable.

*   **Interface Names:**
    *   Use `PascalCase`, optionally prefixed with `I` (though modern TypeScript often omits `I`) (e.g., `Product`, `InventoryTransaction`, `IProduct`).

*   **Selectors:**
    *   Use a custom prefix for component and directive selectors to avoid naming collisions with native HTML elements and other libraries.
    *   **Project Prefix:** `app-` (e.g., `<app-product-list>`, `<app-inventory-transaction-form>`).
    *   **UI Library Prefix (if applicable):** If we introduce a custom UI component library, use a distinct prefix like `ui-` (e.g., `<ui-button>`).

## 2. File Structure and Organization

A logical and intuitive file structure is essential for discoverability and maintainability.

*   **Feature-Based Modularity:**
    *   Organize code by feature, not by type. Each major feature (e.g., `products`, `inventory`, `dashboard`) should reside in its own dedicated folder.
    *   A feature folder should contain all related components, services, models, routing modules, and potentially `ngrx` state management files relevant to that feature.
    *   Example:
        ```
        src/app/
        ├── core/            // Singleton services, global guards, interceptors
        ├── shared/          // Shared components, pipes, directives
        ├── features/
        │   ├── dashboard/
        │   │   ├── dashboard.component.ts
        │   │   ├── dashboard.component.html
        │   │   └── dashboard.component.scss
        │   ├── products/
        │   │   ├── models/
        │   │   │   └── product.ts
        │   │   ├── services/
        │   │   │   └── product.service.ts
        │   │   ├── components/
        │   │   │   ├── product-list/
        │   │   │   │   ├── product-list.component.ts
        │   │   │   │   └── ...
        │   │   │   └── product-form/
        │   │   │       ├── product-form.component.ts
        │   │   │       └── ...
        │   │   └── products-routing.module.ts
        │   │   └── products.module.ts (if not using all standalone)
        │   └── inventory/
        │       ├── models/
        │       │   └── inventory-transaction.ts
        │       ├── services/
        │       │   └── inventory.service.ts
        │       └── components/
        │           ├── inventory-transaction-form/
        │           └── transaction-list/
        └── app.component.ts
        └── app.module.ts (or app.config.ts with standalone)
        └── app-routing.module.ts
        ```

*   **LIFT Principle (Locate, Identify, Flat, T-DRY):**
    *   **Locate:** Code should be easy to find. Feature-based folders help achieve this.
    *   **Identify:** When you look at a file, you should immediately know what it contains. Clear naming conventions are key.
    *   **Flat:** Keep folder depth as shallow as possible. Avoid excessively nested folders.
    *   **T-DRY (Try to be DRY):** Don't repeat yourself. Share common code through `SharedModule` or standalone shared components.

*   **Standalone Components:**
    *   **Emphasize Standalone:** Prefer creating new components, directives, and pipes as `standalone: true`. This reduces the need for `NgModules` and simplifies the dependency graph.
    *   **`imports` array:** Clearly list all necessary dependencies (`CommonModule`, `FormsModule`, other standalone components/pipes/directives, `RouterModule`) within the component's `imports` array.
    *   **`app.config.ts`:** Leverage `app.config.ts` for providing global services and configuring features instead of `AppModule`.

## 3. Component and Directive Best Practices

Components are the building blocks of our UI. They must be well-structured and efficient.

*   **Single Responsibility Principle (SRP):**
    *   Each component or directive should have one clear, well-defined purpose. Avoid "God components" that handle too many responsibilities.
    *   Components should primarily focus on presentation and user interaction.

*   **Presentation Logic:**
    *   Keep complex business logic out of components. Delegate it to services.
    *   The component class should primarily handle:
        *   Managing component state.
        *   Binding data to the template.
        *   Responding to user events.
        *   Calling services for data operations or business logic.

*   **Input/Output Properties (`@Input()`, `@Output()`):**
    *   Clearly declare all `@Input()` and `@Output()` properties.
    *   Avoid aliasing inputs/outputs (`@Input('alias')`) unless absolutely necessary for clarity or preventing collisions.
    *   Inputs should primarily accept primitive types or observable streams.
    *   Outputs should emit events using `EventEmitter` and typically carry minimal data.

*   **Templates (`.html` files):**
    *   Keep templates clean, declarative, and easy to read.
    *   Avoid complex logic (e.g., intricate calculations, deep conditional nesting) directly in templates. Move such logic to component methods or pipes.
    *   Use directives (`*ngIf`, `*ngFor`, `[ngClass]`) appropriately.

*   **Lifecycle Hooks:**
    *   Implement lifecycle hook interfaces (`OnInit`, `OnDestroy`, `OnChanges`, etc.) for clarity.
    *   Keep lifecycle methods focused on their specific purpose (e.g., `ngOnInit` for initialization, `ngOnDestroy` for cleanup).
    *   Always unsubscribe from observables in `ngOnDestroy` to prevent memory leaks, or use `takeUntil`/`takeWhile` operators.

## 4. Service Best Practices

Services encapsulate reusable business logic and data access.

*   **Single Responsibility Principle (SRP):**
    *   Each service should have a clear, focused purpose (e.g., `ProductService` for product-related API calls, `AuthService` for authentication).

*   **Dependency Injection:**
    *   **Prefer `inject()`:** In newer Angular versions (v14+), favor the `inject` function for injecting dependencies within services, particularly in constructorless services or factories.
    *   **Constructor Injection:** Use constructor parameter injection for components and directives, and in services where `inject()` might complicate testing or setup (e.g., when migrating older code).

*   **Data Services:**
    *   Delegate all communication with the backend API to dedicated data services.
    *   These services should expose methods that return `Observable`s, allowing components to subscribe to data streams.
    *   Handle API errors consistently within services or via HTTP Interceptors.

*   **Provided In:**
    *   Always use `providedIn: 'root'` for application-wide singleton services.
    *   For feature-specific services that should be scoped to a particular feature module, provide them in that feature module (e.g., `providedIn: ProductModule`).

## 5. TypeScript Usage

Leverage TypeScript's features for robust and maintainable code.

*   **Strong Typing:**
    *   Embrace strong typing everywhere: function parameters, return types, variables, object properties.
    *   Avoid using `any` or `object` types unless absolutely necessary (e.g., for parsing dynamic JSON from an external, untyped source, with clear casting afterwards).
    *   Use interfaces or types to define the structure of data objects (e.g., `Product`, `InventoryTransaction`).

*   **Strict Mode:**
    *   Ensure `strict: true` is enabled in `tsconfig.json`. This enables a suite of strict type-checking options that catch potential errors early.

*   **Immutability:**
    *   Promote immutability for predictability and easier debugging, especially when working with component `@Input()` properties and state management.
    *   Avoid direct mutation of objects or arrays passed as inputs or retrieved from state. Instead, create new instances with updated values.

## 6. Performance and Optimization

Deliver a fast and responsive user experience.

*   **Lazy Loading:**
    *   Implement lazy loading for feature modules (or route-level components if using standalone components extensively) to reduce the initial bundle size and speed up application load time.
    *   Only load code when it's actually needed by the user.

*   **`trackBy` with `*ngFor`:**
    *   Always use a `trackBy` function with `*ngFor` directives when rendering lists of objects. This helps Angular optimize rendering by identifying unique items and re-rendering only those that have changed, improving performance.

*   **Signals and Zoneless Architecture (Future-Proofing):**
    *   For new features or refactoring, leverage Angular Signals for fine-grained reactivity and state management.
    *   Consider adopting a zoneless change detection strategy (via `provideZoneChangeDetection` with `false`) when fully migrating to Signals for improved performance and simpler change detection reasoning.

*   **Ahead-of-Time (AOT) Compilation:**
    *   Ensure AOT compilation is always used for production builds. AOT compiles Angular HTML and TypeScript code into efficient JavaScript code during the build phase, resulting in smaller bundle sizes and faster rendering.

## 7. Code Quality and Maintenance

Maintain a clean, understandable, and testable codebase.

*   **Linting:**
    *   Enforce linting rules using ESLint (configured via `.eslintrc.json`).
    *   All code must pass linting checks without warnings or errors before merging.
    *   Use `npm run lint` regularly and configure VS Code to lint on save.

*   **Documentation:**
    *   Document public APIs (classes, methods, interfaces) using JSDoc comments to explain their purpose, parameters, and return values.
    *   Add comments for complex logic or non-obvious code sections.
    *   Keep README files updated.

*   **Testing:**
    *   **Unit Tests:** Write unit tests for all services, utility functions, and complex component logic.
    *   **Component Tests:** Write focused unit/integration tests for components, especially those with significant user interaction or complex state.
    *   **Coverage:** Aim for reasonable test coverage (e.g., 80% line coverage for new code) to ensure reliability.

*   **Error Handling:**
    *   Implement robust error handling mechanisms at all layers:
        *   **Backend:** Return meaningful HTTP status codes and error messages.
        *   **API Services:** Catch HTTP errors and transform them into user-friendly messages or log them.
        *   **Components:** Display error messages to the user when API calls fail.
        *   **Global Error Handler:** Implement an `ErrorHandler` in Angular to catch and log unhandled client-side errors.

*   **Accessibility (A11y):**
    *   Ensure all components are built with accessibility in mind. Use semantic HTML, ARIA attributes where necessary, and ensure keyboard navigability.

*   **Security:**
    *   Be mindful of security best practices: sanitize user input, prevent XSS and CSRF attacks (Angular's built-in protections are a good start but don't replace vigilance).
    *   Never store sensitive information in client-side code.


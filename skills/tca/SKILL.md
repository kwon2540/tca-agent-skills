---
name: tca
description: Build, refactor, review, debug, or test Swift features that use Point-Free's Composable Architecture (TCA), including reducers, observable state, view actions, dependencies, effects and cancellation, bindings, presentation and stack navigation, shared state, and TestStore. Use for TCA migrations and deprecation audits, especially Swift 6 concurrency or preparation for TCA 2.0.
---

# TCA

Apply current Composable Architecture patterns while matching the version and conventions of the target project. Treat the bundled examples as focused references, not files to copy wholesale.

## Establish the baseline

1. Inspect `Package.swift` and `Package.resolved` before changing code.
2. Record the resolved TCA version, Swift tools version, and deployment targets.
3. If the request depends on “latest,” migration status, or deprecated APIs, verify the newest stable tag and migration guide in the official `pointfreeco/swift-composable-architecture` repository.
4. Treat these references as audited against TCA 1.26.0, released June 8, 2026. Re-verify them when the project resolves a newer version.
5. Read only the references relevant to the task, plus any directly composed parent or child pattern.

## Implement modern TCA

- Model features with `@Reducer`, `@ObservableState`, an `Action` enum, and `Reduce`/reducer-builder composition.
- Observe `StoreOf<Feature>` directly from SwiftUI. Derive bindings from observable stores.
- Use `ViewAction` and `@ViewAction(for:)` when a feature should distinguish actions sent by its view from internal responses, child actions, and delegate actions. Keep this separation optional for simple features.
- Define external work as `@Dependency` values. Prefer `@DependencyClient` plus `DependencyKey` for custom clients, and provide deterministic test values.
- Express async work with `.run`, send results back as actions, use clocks for time, and give long-running work stable cancellation IDs.
- Compose child reducers with `Scope`, `.ifLet`, `.forEach`, or stack composition before handling parent logic that depends on child actions.
- Model presentation with `@Presents` and `PresentationAction`. For enum destinations on TCA 1.25+, scope the projected destination first, then select the case.
- Mutate `@Shared` values through their projected lock, such as `$value.withLock { ... }`.
- Keep state mutations synchronous in reducers. Move I/O, clocks, notifications, sockets, and other outside-world work into effects and dependencies.
- Preserve Swift 6 isolation and `Sendable` correctness. Do not silence concurrency diagnostics with unchecked conformance unless the wrapped implementation is independently synchronized.
- Add or update `TestStore` coverage for state transitions, received actions, cancellation, navigation, and dependency behavior when production logic changes.

## Avoid legacy patterns in new code

- Do not introduce `SystemEnvironment`, three-generic-parameter closure reducers, `ViewStore`, `WithViewStore`, `IfLetStore`, or `ForEachStore`.
- Do not introduce `@BindingState`, `TaskResult`, old `AnyCasePath` APIs, closure-scoped views, direct `Reducer.reduce`, or `Store.withState`.
- Do not sequence or transform effects with `Effect.concatenate`, `Effect.map`, animation/transaction effect operators, scheduler debounce, or scheduler throttle. Sequence directly in `.run`, call `send(_:animation:)` or `send(_:transaction:)`, and use a clock plus cancellation.
- Do not scope presentation from a non-projected destination key path when the installed TCA version supports projected enum scoping.
- Do not copy demo-only helpers such as `AboutView`, `FactClient`, or preview scaffolding unless the target owns equivalent types.
- Do not enable `ComposableArchitecture2DeprecationOverloads` permanently; use it temporarily for migration because it can increase compile time. Keep `ComposableArchitecture2Deprecations` enabled when the project intentionally tracks TCA 2.0 readiness.

## Choose references

### Getting started and composition

- Basic reducer/store: [getting-started-counter.md](references/getting-started-counter.md)
- View-only actions and `@ViewAction`: [view-actions.md](references/view-actions.md)
- Alerts and confirmation dialogs: [getting-started-alerts-and-confirmation-dialogs.md](references/getting-started-alerts-and-confirmation-dialogs.md)
- Animation: [getting-started-animations.md](references/getting-started-animations.md)
- Binding basics: [getting-started-bindings-basics.md](references/getting-started-bindings-basics.md)
- Form bindings: [getting-started-bindings-forms.md](references/getting-started-bindings-forms.md)
- Parent/child composition: [getting-started-composition-two-counters.md](references/getting-started-composition-two-counters.md)
- Focus state: [getting-started-focus-state.md](references/getting-started-focus-state.md)
- Optional child state: [getting-started-optional-state.md](references/getting-started-optional-state.md)

### Effects and dependencies

- Async requests: [effects-basics.md](references/effects-basics.md)
- Explicit cancellation: [effects-cancellation.md](references/effects-cancellation.md)
- View-lifetime effects: [effects-long-living.md](references/effects-long-living.md)
- Pull to refresh: [effects-refreshable.md](references/effects-refreshable.md)
- Modern dependency clients: [effects-system-environment.md](references/effects-system-environment.md)
- Timers and clocks: [effects-timers.md](references/effects-timers.md)
- Web sockets and async streams: [effects-websocket.md](references/effects-websocket.md)

### Navigation

- Stack navigation: [navigation-stack.md](references/navigation-stack.md)
- Multiple enum destinations: [navigation-multiple-destinations.md](references/navigation-multiple-destinations.md)
- Navigate while loading: [navigation-navigate-and-load.md](references/navigation-navigate-and-load.md)
- Navigate and load from lists: [navigation-lists-navigate-and-load.md](references/navigation-lists-navigate-and-load.md)
- Load then present a sheet: [navigation-sheet-load-then-present.md](references/navigation-sheet-load-then-present.md)
- Present a sheet then load: [navigation-sheet-present-and-load.md](references/navigation-sheet-present-and-load.md)

### Shared and reusable state

- In-memory sharing: [shared-state-in-memory.md](references/shared-state-in-memory.md)
- User defaults: [shared-state-user-defaults.md](references/shared-state-user-defaults.md)
- File persistence: [shared-state-file-storage.md](references/shared-state-file-storage.md)
- Multi-step onboarding: [shared-state-onboarding.md](references/shared-state-onboarding.md)
- Recursive reducers: [higher-order-reducers-recursion.md](references/higher-order-reducers-recursion.md)
- Reusable favoriting: [higher-order-reducers-reusable-favoriting.md](references/higher-order-reducers-reusable-favoriting.md)

## Verify

1. Build the narrowest affected package or scheme.
2. Run relevant tests; use deterministic clocks and dependency overrides instead of sleeping in tests.
3. Search changed code for the legacy APIs listed above.
4. When auditing for TCA 2.0, enable `ComposableArchitecture2Deprecations`, then temporarily enable `ComposableArchitecture2DeprecationOverloads` and resolve warnings.
5. Report the resolved TCA version, references used, verification commands, and any remaining migration warnings.

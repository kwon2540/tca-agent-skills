# View Actions

Use `ViewAction` to distinguish actions a view can send from actions reserved for effects, child features, presentation, and parent delegation. This is useful for non-trivial features where action provenance and a narrow view-facing interface improve maintainability. It is optional for small features.

## Contents

- Pattern guidelines
- Reducer and SwiftUI example
- Bindings inside view actions
- Testing
- Common mistakes

## Pattern guidelines

- Conform the feature's outer `Action` to `ViewAction` and add `case view(View)`.
- Add `@CasePathable` to the nested `View` enum when tests or composition use case key paths such as `\.view.buttonTapped`.
- Put only events emitted directly by the view in the nested `View` enum, including button taps, binding changes, and view lifecycle events such as `.task`.
- Keep effect responses, child and presentation actions, and delegate actions in the outer enum.
- Annotate the SwiftUI view or UIKit view controller with `@ViewAction(for: Feature.self)`. Its store property must be named `store`.
- Call `send(.event)` from the annotated view. The macro wraps the event in `.view` and warns when the view calls `store.send` directly.
- Treat the macro as an API boundary and diagnostic aid, not a security boundary: code can still access the store directly.

## Reducer and SwiftUI example

```swift
import ComposableArchitecture
import SwiftUI

@Reducer
struct SignInFeature: Sendable {
  @ObservableState
  struct State: Equatable {
    var isRequestInFlight = false
  }

  enum Action: Sendable, ViewAction {
    case response(Result<String, any Error>)
    case view(View)

    @CasePathable
    enum View: Sendable {
      case signInButtonTapped
      case task
    }
  }

  var body: some Reducer<State, Action> {
    Reduce { state, action in
      switch action {
      case .view(.signInButtonTapped):
        state.isRequestInFlight = true
        return .run { send in
          await send(.response(.success("token")))
        }

      case .view(.task):
        return .none

      case .response(.success):
        state.isRequestInFlight = false
        return .none

      case .response(.failure):
        state.isRequestInFlight = false
        return .none
      }
    }
  }
}

@ViewAction(for: SignInFeature.self)
struct SignInView: View {
  let store: StoreOf<SignInFeature>

  var body: some View {
    Button("Sign in") {
      send(.signInButtonTapped)
    }
    .disabled(store.isRequestInFlight)
    .task {
      await send(.task).finish()
    }
  }
}
```

The view can send `SignInFeature.Action.View` values through the generated `send` method, but it cannot accidentally pass `.response` to that method. Effects continue to send outer actions such as `.response`.

## Bindings inside view actions

When the feature uses synthesized bindings, make the nested `View` action bindable and point `BindingReducer` at the outer `.view` case:

```swift
@Reducer
struct ProfileFeature {
  @ObservableState
  struct State: Equatable {
    var displayName = ""
  }

  enum Action: ViewAction {
    case saveResponse(Result<Void, any Error>)
    case view(View)

    @CasePathable
    enum View: BindableAction {
      case binding(BindingAction<State>)
      case saveButtonTapped
    }
  }

  var body: some Reducer<State, Action> {
    BindingReducer(action: \.view)
    Reduce { state, action in
      switch action {
      case .view(.binding):
        return .none
      case .view(.saveButtonTapped):
        return .none
      case .saveResponse:
        return .none
      }
    }
  }
}

@ViewAction(for: ProfileFeature.self)
struct ProfileView: View {
  @Bindable var store: StoreOf<ProfileFeature>

  var body: some View {
    Form {
      TextField("Display name", text: $store.displayName)
      Button("Save") {
        send(.saveButtonTapped)
      }
    }
  }
}
```

`@Bindable` store writes are automatically wrapped in `.view(.binding(...))` when the outer action conforms to `ViewAction` and its nested view action conforms to `BindableAction`.

## Testing

Test the full feature domain. Send view actions through the outer `.view` case and receive internal actions separately:

```swift
import Testing

@MainActor
@Test
func signIn() async {
  let store = TestStore(initialState: SignInFeature.State()) {
    SignInFeature()
  }

  await store.send(\.view.signInButtonTapped) {
    $0.isRequestInFlight = true
  }
  await store.receive(\.response.success) {
    $0.isRequestInFlight = false
  }
}
```

For bindable view actions, use case key paths such as `store.send(\.view.binding.displayName, "Blob")` and assert the resulting state mutation.

## Common mistakes

- Do not put effect responses under `Action.View`; the view should not be able to manufacture them through the generated `send` API.
- Do not call `store.send(.view(...))` from a type annotated with `@ViewAction`; use `send(...)` so macro diagnostics and the narrow action interface remain effective.
- Do not write `BindingReducer()` when `BindableAction` is nested under `Action.View`; use `BindingReducer(action: \.view)`.
- Do not annotate a view unless it owns a `StoreOf<Feature>` property named `store`.

# Effects - Dependencies

Use TCA's dependency system for system-wide and feature-specific dependencies. `SystemEnvironment`, closure-based reducers with an environment generic, schedulers stored in an environment, and `WithViewStore` are legacy patterns and must not be introduced in new code.

## Pattern guidelines

- Declare system dependencies such as `continuousClock`, `date`, `dismiss`, and `uuid` with `@Dependency`.
- Model feature-specific outside-world work with `@DependencyClient` and register it through `DependencyKey` and `DependencyValues`.
- Put live implementations outside feature modules when that avoids compiling networking or platform frameworks into feature code.
- Supply deterministic implementations in tests through `TestStore` dependency configuration.
- Prefer clocks and async sequences to scheduler-based effects.

## Code reference

```swift
import ComposableArchitecture
import Foundation

@DependencyClient
struct FactClient: Sendable {
  var fetch: @Sendable (Int) async throws -> String
}

extension FactClient: DependencyKey {
  static let liveValue = Self(
    fetch: { number in
      let (data, _) = try await URLSession.shared.data(
        from: URL(string: "https://number-trivia.com/\(number)/trivia")!
      )
      return String(decoding: data, as: UTF8.self)
    }
  )

  static let testValue = Self()
}

extension DependencyValues {
  var factClient: FactClient {
    get { self[FactClient.self] }
    set { self[FactClient.self] = newValue }
  }
}

@Reducer
struct Feature {
  @ObservableState
  struct State: Equatable {
    var count = 0
    var fact: String?
  }

  enum Action: Equatable {
    case factButtonTapped
    case factResponse(String)
  }

  @Dependency(\.factClient) var factClient
  @Dependency(\.uuid) var uuid

  var body: some ReducerOf<Self> {
    Reduce { state, action in
      switch action {
      case .factButtonTapped:
        return .run { [count = state.count] send in
          await send(.factResponse(try await self.factClient.fetch(count)))
        }

      case let .factResponse(fact):
        state.fact = fact
        return .none
      }
    }
  }
}
```

## Test override

```swift
let store = TestStore(initialState: Feature.State()) {
  Feature()
} withDependencies: {
  $0.factClient.fetch = { "\($0) is a good number." }
  $0.uuid = .incrementing
}
```

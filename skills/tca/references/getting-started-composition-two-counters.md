# Getting Started - Composition - Two Counters

This demonstrates how to compose small, independent features (like counters) into larger parent features using `Scope` and `store.scope`.

## Pattern Guidelines
- **Child States**: Include child states as properties on the parent `State`.
- **Child Actions**: Nest child actions as cases in the parent `Action` enum.
- **Scope Reducer**: Use the `Scope` reducer in the parent's `body` composition to run a child reducer on its focused slice of state and actions.
- **View Scoping**: Use `store.scope(\.childState, action: \.childAction)` in parent views to hand off a scoped store to child views.

## Code Reference
```swift
import ComposableArchitecture
import SwiftUI

private let readMe = """
  This screen demonstrates how to take small features and compose them into bigger ones using \
  reducer builders and the `Scope` reducer, as well as the `scope` operator on stores.

  It reuses the domain of the counter screen and embeds it, twice, in a larger domain.
  """

@Reducer
struct TwoCounters {
  @ObservableState
  struct State: Equatable {
    var counter1 = Counter.State()
    var counter2 = Counter.State()
  }

  enum Action {
    case counter1(Counter.Action)
    case counter2(Counter.Action)
  }

  var body: some Reducer<State, Action> {
    Scope(\.counter1, action: \.counter1) {
      Counter()
    }
    Scope(\.counter2, action: \.counter2) {
      Counter()
    }
  }
}

struct TwoCountersView: View {
  let store: StoreOf<TwoCounters>

  var body: some View {
    Form {
      Section {
        AboutView(readMe: readMe)
      }

      HStack {
        Text("Counter 1")
        Spacer()
        CounterView(store: store.scope(\.counter1, action: \.counter1))
      }

      HStack {
        Text("Counter 2")
        Spacer()
        CounterView(store: store.scope(\.counter2, action: \.counter2))
      }
    }
    .buttonStyle(.borderless)
    .navigationTitle("Two counters demo")
  }
}

#Preview {
  NavigationStack {
    TwoCountersView(
      store: Store(initialState: TwoCounters.State()) {
        TwoCounters()
      }
    )
  }
}
```

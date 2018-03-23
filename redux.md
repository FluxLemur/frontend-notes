# Notes on Redux 

## [Redux Intro](https://redux.js.org/introduction)
- Motivation: it's hard to manage state and rendering in the same place
  - Libraries like React handle the view layer
  - Redux handles state management
- Core Idea
  - App state is an object
  - Dispatch actions to change state
  - Write a list of reducers the perform `(state, action) -> (next state)`
  - Write one "global reducer"
- Three principles
  - Single source of truth: The state of your whole application is stored
    in an object tree within a single store.
  - State is read-only: The only way to change the state is to emit an action.
  - Changes are made with pure functions: to specify how the state tree is
    transformed by actions, you write pure reducers.

## Actions
- The only source of information for the store
- Send with `store.dispatch()`
- Must have `type`
- There are no constraints on contents of the action, other than `type`.
  However, it is recommended to follow
  [Flux Standard Action](https://github.com/redux-utilities/flux-standard-action)
  - FSAs *must* be a plain js object and have `type`
  - FSAs *may* have `error` (bool), `payload` (any value), and `meta` (any value)
- Action creator (e.g `addTodo` and `completeTodo`)
- Bound action creator (`const boundAddTodo = text => dispatch(addTodo(text))`)

## Reducers
- A reducer should stay pure. Never do any of the following:
  - Mutate its arguments
  - Perform side effects like API calls and routing transitions
  - Call non-pure functions, e.g. Date.now() or Math.random()
- Initial call to reducer, Redux passes `undefined` for the state
- 

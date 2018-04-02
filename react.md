# Notes from the [React Tutorial](https://reactjs.org/docs/introducing-jsx.html)

## Hello World
```
ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('root')
)
```

## JSX
- React extends Javascript syntax with embedded HTML.
- `const element = <h1>Hello world</h1>` is not valid in vanilla JS
- This embedded syntax is just sugar -- you can do the same thing with
  a call to `React.createElement('div', null, 'Hello world')`
- You can write JS expressions within HTML blocks with `{}`
  - e.g. `<h1>Page {myObj.pagename}: {1+1}</h1>`
- Prevents injection attacks by escaping all values
  ```
  const title = response.potentiallyMaliciousInput
  // This is safe:
  const element = <h1>{title}</h1>
  ```
- JSX elements become regular JS objects. Something similar to:
  ```
  const element = {
    type: 'h1',
    props: {
      className: 'greeting',
      children: 'Hello, world'
    }
  }
  ```

## Rendering Elements
- You can render an element into the DOM like so:
  - Your html
    ```
    <div id="root"></div>
    ```
  - Your JS
    ```
    const element = <h1>Hello, world</h1>
    ReactDOM.render(element, document.getElementById('root'))
    ```
- React elements are *immutable*. To change an element, you render a new one
  and React will just render the difference.

## Components and Props
- Conceptually, components are like JS functions that accept arbitrary inputs
  ("props") and return a React element.
  ```
  function Welcome(props) {
    return <h1>Hello, {props.name}</h1>
  }
  ```
- With ES6 classes you can (and should) have:
  ```
  class Welcome extends React.Component {
    render() {
      return <h1>Hello, {this.props.name}</h1>
    }
  }
  ```
- We can create elements from user-definied components.
  ```
  const element = <Welcome name="lemur" />
  // passes `props = {name: 'lemur'}` into the Welcome constructor
  ```
- Components can be composed!
  ```
  function App() {
    return (
      <div>
        <Welcome name="lemur" />
        <Welcome name="elephant" />
        <Welcome name="walrus" />
      </div>
    )
  }
  ```
- In general, try to keep your components modular.
- *Props are read-only.* A component should not change them.

## State and Lifecycle
- Sometimes you want your component to have some notion of internal state
  that can change. For that, there is *state*.
- *State* is private and fully controlled by the component.
- Do not modify state directly, use `setState()`. (Only in the constructor
  should you assign `this.state`).
- State updates my be asynchronous, so *do not* use `this.props` or
  `this.state` directly in set state.
  - BAD:
    ```
    this.setState({
      counter: this.state.counter + this.props.increment,
    })
    ```
  - GOOD:
    ```
    this.setState((prevState, props) => ({
      counter: prevState.counter + props.increment
    }))
    ```
- State updates are merged. In `setState` you can return a subset of the state.
- State is local to a component (parents and children cannot access it).
- Dataflow is unidirectional, because a component can only pass state down via
  the props of a child. e.g. `<FormattedDate date={this.state.date} />`
- For example, say you want to make a Clock component that updates itself,
  so your main app can look like this:
  ```
  ReactDOM.render(
    <Clock />,
    document.getElementById('root')
  )
  ```
  - Initialize state in the constructor
  ```
  constructor(props) {
    super(props)
    this.state = {date: new Date()}
  }
  ```
  - use `componentDidMount()` to intialize a timer that calls `tick`:
  ```
  tick() {
    this.setState({date: new Date()})
  }
  ```
  - use `componentWillUnmount()` to reset the timer

## Handling Events
- Pass a JS function, like so:
  ```
  <button onClick={this.handleClick}>
    {this.state.isToggleOn ? 'ON' : 'OFF'}
  </button>
  ```
- You must `bind` a member function in order to access `this` inside a callback.
  - Example:
    ```
    constructor(props) {
      super(props)
      this.state = {isToggleOn: true}

      // This binding is necessary to make `this` work in the callback
      this.handleClick = this.handleClick.bind(this)
    }

    handleClick() {
      this.setState(prevState => ({
        isToggleOn: !prevState.isToggleOn
      }))
    }
    ```

## Conditional Rendering
- Expressions with components just work:
  ```
  if (isLoggedIn) {
    return <UserGreeting />
  }
  return <GuestGreeting />
  ```
- Return `null` in `render()` to render nothing. (Note that this doesn't affect
  the component lifecycle. `componentWillUpdate` and `componentDidUpdate` are
  still called.)

## Lists and Keys
- Create a list like so:
  ```
  const numbers = [1, 2, 3, 4, 5]
  const listItems = numbers.map((number) =>
    <li key={number.toString()}>
      {number}
    </li>
  )
  const element = <ul>listItems</ul>
  ```
  - NOTE we add a attribute `key` to the `<li>` tag.
- Keys allow React to identify which items change in a list. They should
  uniquely identify each element.
- When extracting a component of a list, you *must* add `key` as an attribute
  at the same level as the list.
  - e.g. we put key on the `ListItem` not the `<li>` tag
  ```
  function ListItem(props) {
    return <li>{props.value}</li>;
  }

  function NumberList(props) {
    const numbers = props.numbers;
    const listItems = numbers.map((number) =>
      <ListItem key={number.toString()}
                value={number} />

    );
    return (
      <ul>
        {listItems}
      </ul>
    );
  }
  ```
- Keys must be unique only amonst siblings (not globally)

## Forms
- TODO

## Lifting State Up
- TODO

## Composition vs. Inheritance
- TODO

## Thinking in React
- TODO

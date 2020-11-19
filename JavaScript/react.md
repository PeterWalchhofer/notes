# React
## Virtual Dom
Problem that react solves: Updates in UI. If just one part of a component changes, you had to keep track of what exactly changed and rerender it. This ge very complex in many applications. With react, the view component just recalculates the whole component, gives it to react and react looks up the changes and rerenders only them.

## Components
- CSS can be only used for component
- Very nice auslagerung:
  `<body>
  <myComponent/></body>`


# Basics

## Init react app
```
1. npm install create-react-app -g
2. create-react-app 'your-app-name'
3. cd 'your-app-name'
4. npm start
```

## Render
Imports
```
import React from 'react';
import ReactDOM from 'react-dom';
import App from "./App.js"; # Root component
```
In the index-js file there is a call on
```
ReactDOM.render(
    <React.StrictMode>
    <App />
    </React.StrictMode>,
  , document.getElementById("root")
``` 
- only one element may be in there, e.g. the whole app, or a div

## Functional components
In index. js  (or in the App.js) make new Method that returns that html code that previously was hardcoded in the render method.
```
function MyApp() {
    return (
        <ul>
            <li> Hi </li>
        </ul>
    )
}
```

Components can render other components. This is just the entrypoint.

Convention: One component per file
- Important: `import react` in every file (.jsx)
- `export default MyApp`
- in index.js: `import "./MyApp.js"`
- Convention: `app.js` in same dir --> root component
- Tip: make a components folder in src for other components than app (e.g. Footer)

Variables have to be wrapped in curly braces:
```
<h1> Hello {firstName} </h1>
```
### Nesting jsx Elements
In App.js:
```
import Footer from "./Footer.js";

function App(){
  return (
    <div>
    <Footer/> #THIS
    </div>
  )
```
If we have an array of components, we can render them by simply:
```
let jokeComponents = ...
return(
  <div>
    {jokeComponents}
  </div>
)
```

### Styling Components
Use className when meaning class. ClassName is from the DOM Api and does add classes.
```
function Header(){
    return (
        <header className="navbar"> 
        </header>
    )
}
```
Style attributes not like in HTML inline:
```
  <h1 style="color:black"> # NOT WORKING
  <h1 style = { {color: "black", backgroundColor="green"} }>
```
Two curly braces:
1. for JS wrapping
2. For the actual JS object

backgroundColor instead of background-color because dashes illegal in JS: CamelCase

Inline styles often used: can change style-object that later is injected into the html/jsx structure.

## Props
Function parameters are added via html tags in jsx:
```
function Contacts() {
    return (
        <div className="contacts">
            <ContactCard 
                name="Mr. Whiskerson" 
                phone="(212) 555-1234" 
            />)}
```
Name and phone are now injected into:
```
function ContactCard(props) {
    return (
        <div className="contact-card">
            <h3>{props.name}</h3>
            <p>Phone: {props.phone}</p>
        </div>
    )
}
```

We also can pass objects of course. E.g. let contact = props.contact

## Functional vs Class Components
Functional Components cannot do everything class based ones can do (State, Lifecycle)

This is the same as the app component returning the div (no render function needed.)

```
class App extends React.Component {
    render() {
        return (
            <div>
                <h1>Code goes here</h1>
            </div>
        )
    }
}
```
Attention: do not use "function" keyword for render.
However, when using props, we have to write `this.props`

# State
In order to handle states, we need class based components.

We need to create a constructor and create a state varaible. 
```
  class App extends React.Component {
    constructor() {
        super()
        this.state = {
            answer: "Yes"
        }
    }
  ```

  We can pass information to subcomponents via props as we already know.
  ```
  render() {
        return (
            <div>
                <ChildComponent answer={this.state.answer}/>
            </div>
        )
    }
```
REACT MAGIC: if state changes, subcomponent will als be updated

## Events
instead of vanillay js:
 ```
 <button on="myFunction()">Click me</button>
 ```
 We can use
 ```
 <button onClick={handleClick}>Click me</button>
 ```
 Where handleClick is a function,
 However we should NOT change our state directly via

 ```
 function handleClick(){
   this.state.answer="No"   # wrong
 }

 function handleClick(){
   this.setState({answer: "No"})   
 
 ```
 But by passing an object that defines the new state. This is used to completly forget the old state and apply a new one.
 But before, in order to make this work, we have to bind this method to our class:

```
this.handleClick = this.handleClick.bind(this)
```
THIS IS NOT NECESSARY WHEN USING ARROW FUNCTIONS

If we want to change state in respect of the previous state, we can pass a function
 ```
 
handleClick(){
    this.setState((prevState) => {
        return {
            count: prevState.count + 1
        }
    })
}
```
If we pass this state.count to a subcomponent, it will be rerendered at every change.

## Handling component traversion
HandleChange Method defined in TodoList class, which is passed to the subcomponent TodoItem via
```
 const todoItems = this.state.todos.map((el) =>
  <TodoItem key = {el.id} item={el}
  handleChange={this.handleChange} />)
```

In the subcomponent, the handleChangeEvent can be triggered by

```
<div className="todo-item">
  <input type="checkbox" onChange={() =>
  props.handleChange(props.item.id)} 
  checked={props.item.completed}>
  <p>{props.item.text}</p>
</div>
```
--> anonymous function that calls the function

# Lifecyle
## Lifecycle methods
### `render()`
Can be invoked many times (we did use it already)

### `componentDidMount()`
is called when component is rendered first time --> calling API for example

Example: We can make a isLoading attribute in the state, whcih is initially false and then updated when componentDidMount() is called. We can pass state.isLoading to a subcomponent that reacts to it.

### `componentWillReceiveProps(nexProps)` DEPRECATED
"when someone gives you a gift"
runs when parent component passes props to child
```
componentWillReceiveProps(nextProps) {
if (nextProps.whatever !== this.props.whatever) {
    // do something important here
}
    }
```

### `shouldComponentUpdate()`
Normally rerenders EVERY time something changes. This can be blocked for performance issues

### `componentWillUnmount()`
Cleanup or taredown when deleting component vom DOM. E.g. event listener

### `static getDerivedStateFromProps()`
static method!
return new updated states based on props
mostly not needed.

### `getSnapshotBeforeUpdate()`
Create Backup of the current way things are.
 https://reactjs.org/docs/react-component.html#getsnapshotbeforeupdate

### `componentDidUpdate()`
Only invoked if component re-renderd, but NOT when mounted.

## Conditional Rendering
Often used:
```
this.state.unreadMessages.length > 0 
&& 
<h2>You have {this.state.unreadMessages.length} unread messages!</h2>
```
h2 only rendered when first condition `true`

# Forms
Normally, forms have their own state. This is not desireable, as we want to use the component's state in combination with forms. 

To update state, just pass method to `<input onChange={"method"}>`. This will add to event to it.

We can save property according to input field via 
brackets [...].
```
this.setState({
    [event.target.name]: event.target.value 
    }
```

One important aspect of react is that state is the "single place of truth". So inputs should have a value property that is directed by the state:
```
<input value={this.state.lastName}/>
```

In react `<textarea></textarea>` are  selfclosing with value attribut.

For checkboxes, we have a "checked" attribute. We also have to augument or rewrite the handleChange method, because it does not use "value".

Radio buttons are a mix of checkboxes and texts,

`<button>` are like submit input fields. If they are inside a form, they act like a submit button in react.

There is a library that makes it far easier to create inputs.



# Container and Component Architecture
Problem: Render function may get very long.

Idea: Seperate rendering and business logic. Presentational Component vs. Business Component.

One component for handling state with methods that augument it and to another one for presentational
- Pass whole state to subcontainer
  - `<FormContainer {...this.state}>` recommended
  - `<FormContainer data={this.state}...>`
  
However, this is not always good and hooks solve the problem much better

# Modern JS
```
/**
 * Other modern/advanced React features/topics to learn:
 * 
 * Official React Context API - https://reactjs.org/docs/context.html
 * Error Boundaries - https://reactjs.org/docs/error-boundaries.html
 * render props - https://reactjs.org/docs/render-props.html
 * Higher Order Components - https://reactjs.org/docs/higher-order-components.html
 * React Router - https://reacttraining.com/react-router/core/guides/philosophy
 * React Hooks - https://reactjs.org/docs/hooks-intro.html
 * React lazy, memo, and Suspense - https://reactjs.org/blog/2018/10/23/react-v-16-6.html
 */
```

# Hooks
Class components made useless. They "hook into" state and lifecycle methods of components without using glasses. Improve readability and organzation of components.

## `useState()`
Returns an array with state variables.
```
# COUNTER APP
function Hooks() {
    const [count, setCount] = useState(0)

    function increment() {
        setCount(prevCount => prevCount + 1)
    }
    return (
        <div>
            <h1>{count}</h1>
            <button onClick={increment}>Change!</button>
        </div>
    )
}

```
Array destructuring is used: First element as variable

## `useEffect()`
Replaces:
- `componentDidMount`
- `componentDidUpdate`
- `componentWillUnmount`

Hook that allows producing side effects. Everything that reaches outside effect (API call, DOM manipulation, event listener)

```
const [color, setColor] = useState("")

useEffect(() => {
        setColor( randomcolor())
    },[count])

<h1 style={{color: color}}>{count}</h1>
```
We set color as state variable using `useState()`. After that we wrote `useEffect()` which watches if the state variable in the state changed. We have to give that var into an array. Otherwise --> endless loop (State updated ->rerender -> state updated->..)

If we want to run the method only on mounting, we transmit an empty array
```
 useEffect(() => {
        setColor(randomcolor())
    }, [])
```

When component unmounts, it cleans up automatically, which is nice. However, in some cases this does not happen (e.g. socket susbscription). Then we have to call `setInterval()` inside the useEffect method.
Setinterval() just makes recalls a method after a certain time period.
```
useEffect(() => {
    const intervalId = setInterval(()=>{
        setCount(prevCount => prevCount + 1)
    }, 1000)
    return () => clearInterval(intervalId)
}, [])
```

If we return a function from useEffect it will be called when unmounting.

## `setReducer()`
We use this method if we do not actually imperatively define what to do, but to pass an action type like "PARSEDATA" that declaratively calls actions.

Similar to useState() we write:
```
const [state, dispatch] = useReducer(reducer, defaultState)
```
`reducer` is a function that takes the old state`s action attribute and does something with it and returns new state.
```
reducer (state,action) => {
    if (action.type === "TESTING")
        return {
            ...state,
            people:["thomas"]
        }
}

const defaultState = {
    people: [].
    isModalOpen:false.
    modalContent:"hallo"
}
```
Inside jsx/html elements we use `state.people` for example.

In order to update something, we use the dispatch methode like that:
```
handleSubmit() {
    if (name)
        dispatch( {type: "TESTING})
}
```
Now the reducer function is called, with the state as its input and the action as the `type` attribute. Attention: reducer MUST return state object.

# Context API
## Basic Probem
"Prop Drilling": Passing props from top component down to sub-sub-sub component. The intermediate(s) components may  not need the prop at all ---> takes time and less reuasability, because functional parameters need to be augumented.

Exmaple
1. Person app (has removePerson() function)
2. Person List
3. Person Element (has a Button dad executes removePerson())

If we want to delete person from app, we need also pass props to list, but it does not need it.

Redux (for more ocmplex apps) and the context API solve this

## Context API
Create context outside of component and us jsx tags to create ContextProvider. In the value attribute, we can pass anything we want.

```
const PersonContext = React.createContext();
// two compontne - Provider, Consumer (not used anymore, cause hooks)


function App() {

  return (
    <div className="App">

      <Header />
      <MemeGenerator />
      <PersonContext.Provider value="hallo">
        <Hooks />
      </PersonContext.Provider>
    </div>
  );
}
```

To consume it, we can use `useContext`:
```
import {PersonContext} from "../App.js"

function PersonElement(){
    const data =  useContext(PersonContext)
    console.log(data);

    return <div>
        <p>I am a Person: {data}</p>
    </div>
}
```

Attention: We have to export the PersonContext from the App and import it in the sub-sub component. 
```
export {App, PersonContext};
```
# PropTypes
Typechecking for props that get passed as function parameters.
```
MemeGenerator.propTypes ={randomImg:PropTypes.string}
```
Google for details.
We can set defaults via 
```
MemeGenerator.defaultProps({topText: "LALA"})
```

# Routers
Used for single page applications to map an endpoint.
```
npm install react-router-dom
```
Import 
```

import {BrowserRouter as Router, Route, Switch} from "react-router-dom"
```
In index.js
```
 <BrowserRouter  path="/">
        <App />
      </BrowserRouter>
```

In the App.js
```
 return (
    <div className="App">

      <Route exact path="/">
      <Header />
      <MemeGeneratorNew />
      </Route>

      <Route path="/person">
      <PersonContext.Provider value="hallo">
        <Hooks />
      </PersonContext.Provider>
      </Route>

    </div>
  );
```
If we do not use exact, the MemeGenerator app gets rendered as well as the person component.
OFten we do Link an error page. This is done by placing a `Router` at the very end that matches `path="*"`

`<Switch>` renders the first component that is matched. This allows to do nested routes.

```
 <Switch>
         <Route exact path="/" component={Home} />
         <Route path="/login" component={Login} />
         <Route path="/explore" component={Explore} />
       </Switch>),
```
inside `Home` we can route further.

## Link
To href websites we use: 
```
<Link to="/person">Person </Link>
```
This will then be a `<a href"..."> person </a>` in the html.
Don't forget to import it:
```
import {Link} from "react-router-dom"
```

There also as hook that allows accessing params in a sub-component.
```
const {id} = useParams()
```
Remarks: i think this makes sense when a url is sent to someone. Without the params it is not possible do check which entity is queried. Otherwise, when clicking a component, I could pass probs and show the correct page.
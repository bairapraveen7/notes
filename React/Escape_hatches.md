#### Referencing values with refs
- when you want a component to remember some information, but that shouldn't trigger re-render, you can use a ref
##### Adding ref to your component
- You can add a ref to your component by importing useref hook from react
```
import { useref } from 'react'
```
- call useref hook and pass initial value that u want to reference as only argument
```
const ref = useref(0);
```
- useref returns an object like this
```
{
current: 0
}
```
- You can access current value of ref by ref.current, it is intentionally mutable, you can change it anyway you like it, React doesn't track it
- You can useref to point to a function/object/string anything
##### Example: stopwatch
- We will build a stopwatch that has a start and stop button, start button starts the timer, stop button stops it.
- So, a basic necessity for stop watch setInterval which should update time every second.
- As the updation has to be appeared on screen, so we need to render the screen every second by updating state variable.
- So far, So good
- But when I click stop, the interval which is updating now state variable has to be stopped, remember we can stop the interval only by its id which is returned at the time of its creation.
- So, the id of Setinterval should not be changed for every re-render, so it must be ref variable right.
- With the help of ref and state variable, you can build a stopwatch, check [here](https://react.dev/learn/referencing-values-with-refs#example-building-a-stopwatch) for full code
##### Difference between refs and state
- Refs are an escape hatch you won't need often, even though they offer less strict offers like mutating etc
-  1. returns 
	- useref returns { currentvalue: somevalue }
	- useState(initialvalue) returns [state,setState]
- ref doesn't trigger re-render where as state triggers re-render
- ref variables are mutable where as state variables are immutable(you must use setState function)
- `You shouldn't read ref's current value while rendering, You can read state anytime, however each render has its own snapshot of state` Not so sure what this is?
##### When to use ref
- Typically you use ref when your component needs to step outside react and communicate with external API's - often a browser API that won't affect react component appearance
	- Storing timeout ID's
	- Storing and manipulating DOM elements
	- Storing other objects that aren't necessary to calculate JSX
##### Best practises for refs
- ##### Treat refs as escape hatch
	- Use refs only when ur react code deals much with external API, if you are dealing much refs think again of ur approach
- ##### Don't read or write ref.current while rendering
	- if some info is needed during rendering, use state instead, it becomes difficult to predict the value of ref as when its get modified or not.
	- As long as the object you are mutating isn't used for rendering, React doesn't care what do you do with it
- As said, refs can be pointed to any value, most common case is DOM elements
- [Challenge 4](https://react.dev/learn/referencing-values-with-refs#challenges) is interesting

#### Manipulating DOM with refs
- React automatically modifies DOM to match your render output
- There is no built in way to manipulate the DOM directly in React
- So, you need a ref to DOM node to manipulate it (focus,scroll)
##### Getting a ref to the node
- use a useref hook and initialise it
```
import { useRef } from 'react';
const myRef = useRef(null);
```
- pass your ref as the ref attribute to the jsx tag for which you want to get DOM node
```
<div ref={myRef}>
```
- By above statement, React puts the div elements in myRef.current
- You can access this DOM node from your event handlers and use built in browser API on it
```
myRef.current.scrollIntoView();
```
##### Example: Focus Input
- Declare inputRef with useRef hook
- Pass it as `<input ref={inputRef}></input>`(puts input in inputRef.current)
- In the handleFunction, read the input DOM and call focus(), inputRef.current.focus()
##### Example: Scrolling into an element
- Same like above but the browser API method is scrollIntoView, you can use this method after making a DOM element in ref
- If there are more ref elements, there is a mechanism of creating one single ref and storing all the other refs as elements of single ref in a map data structure
- Implementation is somewhat complext, check it [here](https://react.dev/learn/manipulating-the-dom-with-refs#example-scrolling-to-an-element)
##### Accessing another component DOM nodes
- If you try to put ref on a component, by default you will set it to null, you will get this error
```
Warning: Function components cannot be given refs. Attempts to access this ref will fail. Did you mean to use React.forwardRef()?
```
- By default, React does not let a component access the DOM nodes of any component, not even own component
- If its by choice, that a component want to expose its nodes, it can do so by props
	- `<Myinput ref={currentref} />`
	- Myinput component is declared using forwardRef, this opts it into receiving the inputRef from above as the second ref argument which is declared after props
	```
	const MyInput = forwardRef((props, ref) => {  
	return <input {...props} ref={ref} />;  
	});
	```
	- Myinput itself passes the ref it received to the `<input>` inside it 
- In design systems, it is a common pattern for low-level components like buttons, inputs and so on to forward their refs to DOM nodes, high level components try not to expose thier DOM structure
- There is a [section](https://react.dev/learn/manipulating-the-dom-with-refs#accessing-another-components-dom-nodes) about imperative handle about restricting the parent too much control 
##### When React attaches refs
- In React, every update is split into two phases
	- During render, React calls your component what should be on the screen
	- During commit, React applies changes to DOM
- React sets ref.current during the commit, In rendering phase, DOM nodes have not been updated yet, so ref.current is null
- Read about [flushSync](https://react.dev/learn/manipulating-the-dom-with-refs#when-react-attaches-the-refs) whenever you get time
##### Best practices for DOM manipulation with refs
- Any non-destructive actions like focusing, scrolling does not encounter any problems, don't try to modify DOM manually
- Avoid changing DOM nodes managed by React like removing a DOM node, adding/modifying etc
- This is not a obstacle, you still can add or remove nodes from tree but React should not have any reason to touch what you are dealing with
##### Concepts I missed
1. flushSync
		1. Sometimes, you want to keep two setStates together like 
	```
	setIndex()
	setColor()
	```
	2. If we want to change the color of a DOM element whose index we set by setIndex, then we want the DOM to be changed right after the first call
	3. But we know already that, state update calls are batched and called at once for processing fast
	4. So, if we keep the first call in flushSync, DOM will be updated right after it.
	5. Here is the syntax for it 
```
flushSync(() => {
      setText('');
      setTodos([ ...todos, newTodo]);      
    });
```
2. List of refs
	1. when there are lot of elements in `<li></li>`, if we want to track these elements by ref, it is hard to initialise that many useRefs
	2. So, what is advised to do, instead create a map for a ref variable
	```
	const ref = useRef();
	ref.current = new Map();
	```
	3. Next point you need to know is , you might have been giving ref as a attribute for a DOM element, but ref takes a callback with an implicit argument as underlying element
	4. So, we can keep the id as key, node as value in this reference variable and get it done like
	```
	ref={(node) => {
		const map = getMap();
		if (node) {
		  map.set(cat.id, node);
		} else {
		  map.delete(cat.id);
		}
	  }}
	```

#### Synchronising with effects
- Effects let you run some code after rendering so that you can synchronize your component with some systems outside React
##### Effects vs event handlers
- ###### Rendering code
	- Where you take the state and props and return the jsx you want to see on screen, Like math formula it should calculate result nothing else `(pure)`
- ###### Event handlers
	- Nested functions that does things rather than calculations, update an input field, submit a post Request, navigate a user to other screen
	- A single word (side effect)
- Effects let you specify the side effects that are caused by rendering itself, rather than a particular event. Suppose, take a scenario
- Connecting to a server when chatRoom(component) appears on screen , ipudu idhi execute avvalante , ee event undadhu, maretla execute avthadhi -> effects
- Effects run at the end of commit after screen updates
- Effect code is something going out of React component, so Don't rush to use it, Think before doing
##### How to write an effect
	- Declare an effect(By default your effect will run after every render)
	- Specify the effect dependecies (Most effects must run only when they are needed rather than after every render)
	- Some effects need to specify how to stop, undo or clean up whatever they are doing. 

##### Step 1
- Declare an effect and use it
```
import { useEffect } from 'react';
function MyComponent() {      
      useEffect(() => {});      
      return <div />;    
      }   
```
-  useEffect delays a piece of code from running until that render is reflected on screen
```
const ref = useRef(null);
  if (isPlaying) {
    ref.current.play();  // Calling these while rendering isn't allowed.
  } else {
    ref.current.pause(); // Also, this crashes.
  }
```
- In above scenario, we are trying to modify DOM element while rendering, ila endhuku cheyodhante, manam jsx return chese varaku em DOM establish chesthamo React ki thelidhu, so nuvvu ref.current.play() ante daniki em artham aythadhi, deeni gurchi matladuthunnavo
```
useEffect(() => {  
if (isPlaying) {  
ref.current.play();  
} else {  
ref.current.pause();  
}  
});
```
- By wrapping the code in the useEffect, we are making it to run only after render
- Effects run after every render, codes like these will run infinite times
```
const [count, setCount] = useState(0);  
useEffect(() => {  
setCount(count + 1);  
});
```
##### Step 2
- Synchronising with external system is not always instant, we skip unless its necessary, a beautiful example a animation should be faded only for the first render, not for all subsequent renders
- So, you can give [] as dependency array for useEffect, but it will give error as your code clearly depends on isPlaying, so [isPlaying] is good
- You are explicitly saying useEffect to run the code only if the isPlaying is different from the previous instance
- React will skip re-running the effect only if all of the dependencies you specify have exactly same values as the last render
- React compares dependency values by Object.is comparision
	- ##### Note
		- You can't keep dependencies on your own. 
		- React gives lint error if the dependecies doesn't match with the code it has
- FYI
```
useEffect(() => {  

// This runs after every render  

});  

  
useEffect(() => {  

// This runs only on mount (when the component appears)  

}, []);  

  
useEffect(() => {  

// This runs on mount *and also* if either a or b have changed since the last render  

}, [a, b]);
```

##### Step 3
- Lets say we kept this code for some server connection
```
useEffect(() => {  
const connection = createConnection();  
connection.connect();  
}, []);
```
- It is allowed [] as dependency as useEffect doesn't need any external variable to run again
- But ironically, it runs two times, Its a delebirate feature of React.
- Lets say you have a chat room, you got connected to it went some other page, came back , then got one more connection with out closing another connection
- Entire above statement scenario is based on the code I displayed, so React displays it twice to say that there is bug when you see two times connecting....
- You can add clean up function like this 
```
useEffect(() => {  
const connection = createConnection();  
connection.connect();  
return () => {  
connection.disconnect();  
};  
}, []);
```
- From above, when ever component gets unmounted, return (cleanup code ) gets called
- ##### Never ever change React strict mode which remounts again, instead try to think what changes to do for the code, to run in same way for both times
##### How to handle effect firing twice in development
 - ##### There are some common patterns of useEffect mentioned [here](https://react.dev/learn/synchronizing-with-effects#controlling-non-react-widgets), check it out
	- I will brief them, if you are interested, visit that link
	 - 1. Controlling non-react widgets: like map fragment if you want to add when the component is rendered, no issue, even if it is called twice, antha farak padadhu, same will be displayed again
		 - But if there is a api which can't allow calling twice, you must close it in the effect
	 - 2. Subscribing to events: Any subscription to events must be cancelled in the effect, addEventListener must have removeEventListener
	 - 3. Triggering animations: if your effect made up something, your clean up code should set it default
		 - style.opacity = 1 in effect, style.opacity = 0 in cleanup
	 - 4. Fetching Data: if your effect fetches something, cleanup should abort the fetch or ignore its result
		 - You can't undo a network request, but the fetch after effect should not effect your app
		 - As fetch request is called two times, it is suggesting to keep a variable that turns to false after first request, preventing you to not to call again
	- 5. Sending analytics: like the code 
	```
	useEffect(() => {  
	logVisit(url); // Sends a POST request  
	}, [url]);
	```
			- It doesn't matter how many times logVisit is called
			- Try not to think too much, for the effects which doesn't show user visible behaviour diff if run once or twice

##### What are not effects
- Initializing an application
	```
	if (typeof window !== 'undefined') { // Check if we're running in the browser.  
	checkAuthToken();  
	loadDataFromLocalStorage();  
	}
	```
	- This type of code should only run for once, not even twice, it must be outside component, not in effect
- Buying a product: You don't want to buy a product twice, this is something which is to be handled by an event, not an effect
	- oka vidhamga cheppalante, strictmode rendu sarlu run chesi manaki chepthundhi edhi ekkado undalo, manchidhe
##### Some extra points
- React always cleans up the previous Render effects before the next Render effects
- Each effect captures state variable value from that render
- #####  if you want to return anything from your useEffect hook, that must be a function not anything
- An [example](https://react.dev/learn/synchronizing-with-effects#putting-it-all-together) to understand effect better
- [Challenge 4](https://react.dev/learn/synchronizing-with-effects#challenges) is really interesting
- Debugging challenge 4: 
	- see, at first what we are doing is we kept ignore false, then after the request is over , we are making it true as cleanup
	- In this scenario, what is happening when two requests came upto set state variable, the one which resolves faster changes ignore variable to true preventing the other variable not to effect these
	- As usual for the next render, the variables are set as usual and information can be received
#### You might not need an effect
- If there was no external system involved, if you want to update the component state when some props or state change, you shouldn't need an effect
##### How to remove unnecessary effects
- You don't need Effects to transform data for rendering: when any state variable changes, you might be tempted to use effect escape, but the chronology of execution would be changing state variable, rendering the DOM, then executing effect, if there is any change in state variable whole process repeats, the unnecessary re-renders
	- So, transforming data anedhi render chesthunnappudu cheskochu kadha, daniki effect idhatha endhuku waste
- You don't need effects to handle user events: There will considerable delay in what has raised the event, and the handling, so it is not a good choice.
##### Updating state based on props or state
- This is the statement we are studying from beginning, when something can be calculated from other variables, don't keep in useEffect
```
useEffect(() => {  
setFullName(firstName + ' ' + lastName);  
}, [firstName, lastName]);
```
- In the above code, firstName, lastName are state variables, how inefficient the above would be, which re-renders the whole page and so
##### Caching expensive calculations
```
function TodoList({ todos, filter }) {  

const [newTodo, setNewTodo] = useState('');  

// 🔴 Avoid: redundant state and unnecessary Effect  

const [visibleTodos, setVisibleTodos] = useState([]);  

useEffect(() => {  

setVisibleTodos(getFilteredTodos(todos, filter));  

}, [todos, filter]);  

//
```
- In the above code, we feel tempted to use effect for the filtered items, but it is inefficient and redundant, 
- We can just find filtered items in a rendering phase
```
function TodoList({ todos, filter }) {  
	const [newTodo, setNewTodo] = useState('');  
	// ✅ This is fine if getFilteredTodos() is not slow.  
	const visibleTodos = getFilteredTodos(todos, filter);  
}
```
- Here, getFilteredTodos may be an expensive calculation, what can you do is to use a useMemo hook, 
```
const visibleTodos = useMemo(() => {   
  return getFilteredTodos(todos, filter);  
}, [todos, filter]);
```
- This tells React you don't want inner function to re-run unless todos/filter has been changed
- if todos/filter has been same, it returns same value as previous one
	- ##### An expensive calculation
	- Most of the calculations we do are simple, In general, unless you are looping over thousands of objects its probably not expensive
	- There are two methods, console.time() and console.timeEnd() this will say how much time the code in between them took, it is expensive only if we took more than 1ms, try to use same string in both functions
##### Resetting all state when prop changes
- 
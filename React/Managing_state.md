- React provides a declarative way of manipulating UI, rather than manipulating UI pieces directly
- That is , you describe the different states the component can be in, just switching between them, based on the user input
#### How declarative compares to imperative
- An example of how UI changes in response to user actions
	1. when you type something in form, submit button becomes enabled
	2. when you press submit, form and button becomes disabled, spinner appears
	3. If network request succeds, form is hidden, "Thank you" message appears
	4. If request fails, form reappears with error message
- In imperative style, above corresponds to how you implement interaction, you have to write exact instructions to manipulate UI depending on what just happened
- In this style, form is built without React, using browser DOM
- check the example [here](https://react.dev/learn/reacting-to-input-with-state), to know more
- Its called imperative because you have to "command" each element, from spinner to button, telling computer how to update it
- ###### In React, you don't directly manipulate UI, meaning you don't enable, disable, show or hide components directly.Instead you declare what you want to show, React figures how to do it
#### Thinking about UI declaratively
1. Identify your components different visual states
2. Determine what trigger those state changes
3. Represent the state in memory using useState
4. Remove any non-essential state variables
5. Connect the event handlers to set the state
##### Step 1
- Let's take an example of what the different states for a form
	- Empty,Typing,Submitting,Success,Error
	- Try mocking, display all visual states at once or try displaying each state once see how the app is reacting for it
##### Step 2
- You can trigger state updates for two kind of inputs
	- ***Human inputs*** like clicking a button, typing in a field, navigating a link
	- ***Computer inputs*** like network response arriving, a timeout completing etc
	- In both cases, you must set state variables update the UI
		- Changing the text input should switch it from Empty to Typing state
		- Clicking on submit button , switch it to submitting state
		- Successful network response should switch it to success state
		- Failed network response should switch it to error state
- Visualise for you:
			- ![[responding_to_input_flow.webp]]
##### Step 3
- Represent visual states of your component memory with useState, try to keep as less states as possible
- Start adding all the states, then think which are redundant and can be removed
```
const [isEmpty, setIsEmpty] = useState(true);  
const [isTyping, setIsTyping] = useState(false);  
const [isSubmitting, setIsSubmitting] = useState(false);  
const [isSuccess, setIsSuccess] = useState(false);  
const [isError, setIsError] = useState(false);
```

##### Step 4
- Your goal is to prevent the cases where state in memory doesn't represent any valid UI that you'd want a user to see
- You need to ask some questions to prevent states: 
	- ***Does this state cause paradox***: isTyping and isSubmitting can't both be true, you can think of like a parent state and dump all these in it
	- Like a status variable with { isTyping, isSubmitting, isError }
	- ***Is the same information available in another state already***: isEmpty and isTyping can't be true at the same time, separating them may create bugs
	- You can remove isEmpty and check answer.length === 0
	- ***Can you get same information from inverse of state variable***: isError is not needed becoz you can check error!=null
- Then you can boil down to following states
```
const [answer, setAnswer] = useState('');
const [error, setError] = useState(null);
const [status, setStatus] = useState('typing'); // 'typing', 'submitting', or 'success'

```

##### Step 5
- We need to create event handlers to update the state

#### Choosing the state structure
- Structuring state well can make a difference between a component that is pleasant to modify and one that is constant source of bugs
- ***Principles for structuring state***
	 1. ***Group related state***
	 2. ***Avoid contradiction in state***
	 3. ***Avoid redundant state***
	 4. ***Avoid duplication in state***
	 5. ***Avoid deeply nested state***
##### Step 1
- example: 1
```
const [x, setX] = useState(0);  
const [y, setY] = useState(0);
```

- example: 2
```
const [position, setPosition] = useState({ x: 0, y: 0 });
```
- Technically it is good idea to change a variables as a component if they change together, or there will be a sync issue
- Hope already you know if the state variable is an object, we can change other variable by copying the entire object rather than changing a single variable, Refer [[adding_interactivity]] for any doubts
##### Step 2
```
const [isSending, setIsSending] = useState(false);
const [isSent, setIsSent] = useState(false);
```
- From the above code, it is for sure, isSending and isSent can never be true at the same time
- Then why the two variables are created for it, Rather create a single variable status to keep (typing/sending/sent) as three of them can't happen at same time
- If some variables are declared for readability, like
	```
	const isSending = status == 'sending'
	const isSent = status == 'sent'
	```
	- We need not to bother about these variables as these are not state variables

##### Step 3
- This has been discussed many times, if you can calculate some information from props or existing state, you should not put the info in component state
```
const [firstName, setFirstName] = useState('');
const [lastName, setLastName] = useState('');
const [fullName, setFullName] = useState('');
```
- In the above example, fullName is redundant, as it should be always in sync with firstName and lastName
###### Don't mirror props state
- First of all, Mirroring is nothing but making the props variable as state
```
function Message({ messageColor }) {  
const [color, setColor] = useState(messageColor);
```
- The problem with the above code is as the new updates to messageColor will not be reflected in color, so it is better to take it as local variable
```
function Message({ messageColor }) {  
const color = messageColor;
```
- But this is your intention not to capture any updates of prop variable , then you can happily take it as a state variable.
```
function Message({ initialColor }) {  
// The `color` state variable holds the *first* value of   `initialColor`.  
// Further changes to the `initialColor` prop are ignored.  
const [color, setColor] = useState(initialColor);
```

##### Step 4
- Its very important to understand what is duplicated state, and what is not , Understand the below code 
```
const initialItems = [
  { title: 'pretzels', id: 0 },
  { title: 'crispy seaweed', id: 1 },
  { title: 'granola bar', id: 2 },
];

export default function Menu() {
  const [items, setItems] = useState(initialItems);
  const [selectedItem, setSelectedItem] = useState(
    items[0]
  );
```
- In above, items and selectedItem is redundant, endhuku ante, ipudu okavela first item name change chesthe, second selected item evadu change chesthadu
- This is duplication of code, and you need to avoid this , solution follows
```
const [selectedId, setSelectedId] = useState(0);
```
- Replace item with id, then it is no more redundant, make sure id is unique.
- Now in this scenario, if there is any change happens to item, it is reflected even in the selected item as the change re-renders, automatically updates selectedId
##### Step 5
- Check out the data [here](https://react.dev/learn/choosing-the-state-structure#avoid-deeply-nested-state), to see what is deeply - nested object
- In that object, when you wanted to delete a deeply nested place, we need to copy all the objects way up, and then perform the action
- But, you need to understand, if you flatten the deeply nested object, then deleting an item would be very easy, The data is so large as I can't provide here, but check in that link to see how you can flatten it and do the things
- I could not able to breif u here, but it is very important to practise this step as it involves much reasoning, one of the toughest I have seen

#### Sharing state between components
- If you want more components to change when a state changes, best approach is to move the state to the parent of both components, and pass data via props
##### Lifting state up by example
- Let us say panel is a component, and accordian is a parent, accordian contains two panels
- Accordian 
	- Panel A
	- Panel B
- Each has a boolean isActive to say whether its content is visible or not
- Observe the figure to understand independence between panels
										- ![[sharing_state_child_clicked.webp]]
- The other panel remains inactive when first panel is clicked
- So, to co-ordinate between these two panels, you need to life state up to parent component
	- Remove state from child component
	- Pass hardcored data from parent to child component
	- Add state to common parent , pass it down together with event handlers
##### Step 1
- Remove this from Panel component
```
const [isActive, setIsActive] = useState(false);
```
- Instead add isActive in props list of Panel component
```
function Panel({ title, children, isActive }) {
```
##### Step 2
- Locate the closest common parent of both child components is main task
- Make Accordian component pass hardcoded value to Panel component
##### Step 3
- As the requirement is making a single component as active, it should be the one controlling which is active, so it is imp to create a state for the parent component
```
const [activeIndex, setActiveIndex] = useState(0);
```
- But, here is the catch you need to give panel the authority to instruct parent , that it wants to be active
- So, parent has to send the eventHandler function too to the child Panel, where the function is executed in parent
```
function Panel({
  title,
  children,
  isActive,
  onShow
}) {
```
- Here, onShow is the event handler to be given to the child, so you should pass eventHandler and the result of it as props
##### Controlled and Uncontrolled components
- Controlled components are the one which are operational only by the props sent by parent, parent fully controls the component
- Uncontrolled components are the one which can have their own state , they can figure out everything on thier own
- But usually, most of the components are blend of both, it is important to figure out which properties to be controlled, which are to be uncontrolled
- ###### A single source of truth for each state says that , each state will have one component that controls it , you should not duplicate it , maintain a single source......
#### Preserving and Resetting State
- State is isolated between components, React keeps track of which state belongs to which component based on their place in UI tree

##### State is tied to a position in the render tree
- If a component has a state variable, if we make many instances from that we will get isolated states from those two components
```
import { useState } from 'react';

export default function App() {
  return (
    <div>
      <Counter />
      <Counter />
    </div>
  );
}

function Counter() {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```
- In the above code, Its sure Counter component has a state variable, if we make many instances of counter like 
	- 1. <Counter />
	- 2. <Counter />
- First Counter State variable does not interlink with the state variable of second Component
- React will keep the state around for as long as you render same component at the same position in tree
- Check thie [example](https://react.dev/learn/preserving-and-resetting-state#state-is-tied-to-a-position-in-the-tree), and see how state is re initialised to when component appears again 
- ###### When React removes component, it destroys its state
- When you tick "Render the second counter", a second counter and its state are initialized from scratch and added to DOM
##### Same component at same position preserves state
```
{isFancy ? (
	<Counter isFancy={true} /> 
	) : (
	<Counter isFancy={false} /> 
	)}
```
- In this scenario, do you think state variables are reset when the isFancy is toggled , no if you draw a tree it would be like this 
					![[preserving_state_same_component.webp]]
- Here counter component is at the same place whether isFancy is true of false, so it assumes same component irrespective of isFancy variable
- Its the same component at the same position, so from React's perspective its the same counter
###### Pitfall
- The position in UI tree, not in the JSX markup that matters to React
```
 if (isFancy) {
    return (
      <div>
        <Counter isFancy={true} />
        <label>
          <input
            type="checkbox"
            checked={isFancy}
            onChange={e => {
              setIsFancy(e.target.checked)
            }}
          />
          Use fancy styling
        </label>
      </div>
    );
  }
  return (
    <div>
      <Counter isFancy={false} />
      <label>
        <input
          type="checkbox"
          checked={isFancy}
          onChange={e => {
            setIsFancy(e.target.checked)
          }}
        />
        Use fancy styling
      </label>
    </div>
```
- Its just the if-else condition in React, but the tree is same, only change in jsx so the state variable Remains same, understand!!!!
- Both of Counter tags rendered at the same position, React doesn't know where you place the conditions in your function, All it sees is the tree you return
##### Different components at the same position reset state
- If you switch between different component types at the same position. Becoz, when you use different component at the same place, React destroys the state and removes the component
- When you render a different component in same position, it resets the state of its entire subtree
```
{isFancy ? (
	<div>
	  <Counter isFancy={true} /> 
	</div>
  ) : (
	<section>
	  <Counter isFancy={false} />
	</section>
  )}
```
- When a child div was removed from the DOM, the whole tree below it was destroyed(including the counter and state) as well
- ##### As a rule of thumbup, if you want to preserve the state between re-renders, structure of your tree needs to match up from one render to another
- This is why you should not nest component function definitions, for every render of component, function inside will change with respect to previous function, so React resets all state below. To prevent this, declare component functions at top level, don't nest their functions
##### Resetting state at the same position
- There will be times, even though tree doesn't change, we need the state to change , so  there are two ways to reset the state 
	- Render components in different positions
	- Give each component an explicit identity with key
- ###### Rendering a component in different positions
	```
	{isPlayerA &&
		<Counter person="Taylor" />
	  }
	  {!isPlayerA &&
		<Counter person="Sarah" />
	  }
	```
	- Each counter's state gets destroyed each time when its removed from the DOM
	- ikkada emaithundhi ani meeru konchem confuse avvochu, ante deeniki if else pedha theda em undhi ani, how this will change DOM tree ani ila
	- but paina unna code lo rendu components render ayye avakasham undhi, kani if else lo only one component
	- so, if else lo tree change avvadhu, paina rasina conditional rendering code lo tree change avthadhi 
	- anduke, state reset aipothadhi, meeku baga artham kavadam kosam nenoka diagram istha chudandi

							![[preserving_state_diff_position_p2.webp]]
- ###### Resetting state with a key
	- Keys aren't just for lists, You can use keys to make React distinguish between any components. This way, React will know which counter has been rendered and which is not
	```
	isPlayerA? (
	 <Counter key="Taylor" person="Taylor" />
	  ) : (
		<Counter key="Sarah" person="Sarah" />
	```
	- Switching between Taylor and Sarah does not preserve state, as you gave them different keys
	- Remember that keys are not globally unique, they only specify the position within the parent
##### Resetting a form with key
- Mostly this approach would be useful, when there is a chat, like its the same chat space, but when you change the recepients, the content must also change so the chat must be given a key id which distinguises between different chats
- The above statement is very important, please mind it, or check the [challenges](https://react.dev/learn/preserving-and-resetting-state#challenges) 2,3,4 to understand it better

#### Extracting State Login into Reducer
- Components with many state updates spread across many event handlers can get overwhelming.
##### Consolidate state logic with a reducer
- As the component grow in complexity, it is harder to see at a glance all the different ways in which components state get updated
- A quick glance on the problem
```
 function handleAddTask(text) {
    setTasks([
      ...tasks,
      {
        id: nextId++,
        text: text,
        done: false,
      },
    ]);
  }

  function handleChangeTask(task) {
    setTasks(
      tasks.map((t) => {
        if (t.id === task.id) {
          return task;
        } else {
          return t;
        }
      })
    );
  }

  function handleDeleteTask(taskId) {
    setTasks(tasks.filter((t) => t.id !== taskId));
  }

```
- To reduce this complexity, keep all your logic in one easy-to-access place, you can move that state logic into a single function outside your component(`reducer`)
- You can migrate from useState to useReducer in 3 steps
	- Move from setting state to dispatching actions
	- write a reducer function
	- Use the reducer from your component
##### Step 1
- Remove all state setting logic
- Instead of telling React "what to do" by setting state, you specify "what the user just did" by dispatching "actions" from your event handlers
- So, instead of setting tasks via an event handler, you're dispatching an 'added/changed/deleted a task'
- The object you pass to dispatch is called an "action"
```
dispatch(
{
	type: 'deleted',
	id: taskId,
}
)
its a regular Javascript object, you decide what to put in it, it should contain minimal info abt what happened
```
- As said, it can have any shape, its common to give type, pass any additional info like that
##### Step 2
- A reducer function is where you will put your state logic, it takes 2 arguments, current state and action object
```
function yourReducer(state,action){}
```
- To move your state setting logic from event handlers to reducer function, you will
	- Declare current state (tasks) as first argument
	- Declare action object as second argument
	- Return next state from reducer
```
Here is all the state setting logic migrated to a reducer function
switch (action.type) {  
case 'added': {  
	return [  
	...tasks,  
	{  
	id: action.id,  
	text: action.text,  
	done: false,  
	},  
	];  
}  
case 'changed': {  
return tasks.map((t) => {  
if (t.id === action.task.id) {  
return action.task;  
} else {  
return t;  
}  
});  
}  
case 'deleted': {  
return tasks.filter((t) => t.id !== action.id);  
}  
default: { 
throw Error('Unknown action: ' + action.type);  
}
```
- It is recommended to use switch case statements in reducer function as it's easy to read
- We need to use return in case, if not it executes next case statements even if condition fails and also use {} so that variables declared inside them doesn't clash with code in other case statements
- How the reducer function [here](https://react.dev/learn/extracting-state-logic-into-a-reducer#step-2-write-a-reducer-function) is nice deep dive
- `To give a glance on how it works, we call reduce method of any object like actions.reduce that takes two arguments , one is a function(reducer) to handle events, other is initial state, the function takes result(ante last ki return chesedhi dheene, plus at first it is initialised to the initial state), actions lo unna oka oka element ni second argument laga theeskuntam. In this way it keeps on reducing`
##### Step 3
- Use reducer in your component
- Replace 
- const [tasks,setTasks] = useState(initialTasks) with const[tasks,dispatch] = useReducer(tasksreducer,initialTasks)
- useReducer Hook takes two arguments, a reducer function and initial state , it returns stateful value, dispatch function
##### Comparing useState and useReducer
- ***Code Size***: generally code size is less in useState, while useReducer asks to write reducer function and dispatch actions, when more event handlers pop up, it becomes easy with useReducer 
- ***Readability***: usestate is very readable when state updates are simple, but difficult to scan when they get complex, in this scenario, useReducer separates event handling and updating code
- ***Debugging***: debugging is easy with useReducer as it points the error directly to the action, which in correspondance with dispatch action. but in useState it is difficult to identify where is the bug, (we are talking about bug not error)
- ***Testing***: Said that reducer is a pure function, it is always easily exported to test in isolation
- Ippatiki antha doubt unte reducer function ela work avthundhi ani, just deeni chudandi
```
const arr = [1, 2, 3, 4, 5];  
const sum = arr.reduce(  
(result, number) => result + number  
);
idhela work avthe, reducer ala work avthadhi
```

##### Writing reducers well
- `Reducers must be pure`: Reducers also run during rendering , so they must be pure, same inputs must always result in same output. They should not do any action that affects the component, they should change arrays without mutations
- `Each action describes a single user interaction, even if that leads to multiple changes in data`: for example, reset_form may dispatch many actions like set_name, set_email etc. But it is good if it dispatches one action reset_form rather than five actions.
##### Writing concise reducers with immer
- As we know, immer provides with a special draft object which is safe to mutate
- check the code [here](https://react.dev/learn/extracting-state-logic-into-a-reducer#writing-concise-reducers-with-immer), for more details regarding immer
#### Passing Data Deeply with context
- Usually you will pass data from parent to child component via props, if the child components are so deep, passing props may become verbose a little
- Contexts lets the parent component make some info available to any component in the tree below it - no matter how deep
- With out Contexts, there would be a situation called `prop drilling`, it is the process of passing props through multiple levels of nested components even though they don't need it
##### Context: an alternative to passing props
- Context lets a parent component provide data to the entire tree below it.
- Lets take a scenario
```
<Section>  
<Heading level={3}>About</Heading>  
<Heading level={3}>Photos</Heading>  
<Heading level={3}>Videos</Heading>  
</Section>
```
- In the above code, each of the heading needs the prop level
- We all think it would be better if we pass level prop to Section rather for each heading
- `There should be some way for a child to ask for data from somewhere above in the tree`
- You can do it with context, by three steps
	- Create a context (call it anything 'Level context')
	- Use that context from the component that needs the data(Heading will use it in the above scenario)
	- Provide that context from the component that specifies the data(Section will provide Levelcontext)
##### Step 1
- You need to export it from the file, so that your components can use it
```
import { CreateContext } from 'react';
export const LevelContext = createContext(1);
```
- 1 is the default value you can pass, you can pass any value for it
##### Step 2
- Import the useContext from React and your context
```
import { useContext } from 'react';  
import { LevelContext } from './LevelContext.js';
```
- Read the value from context you just imported, Levelcontext
```
export default function Heading({ children }) {  
const level = useContext(LevelContext);  
// ...  
}
```
- useContext is a hook, just like a useState and useReducer, you can call a hook only after a component(not inside loops or conditions ), 
- From above it is clear that , useContext tells react that heading component wants to read Levelcontext
- Now you think that prop has to be passed from level like this and yes
```
<Section level={3}>  
<Heading>About</Heading>  
<Heading>Photos</Heading>  
<Heading>Videos</Heading>  
</Section>
```
##### Step 3
- Till above also, it won't work we need to provide the context for it to work
- Wrap them with context provider to provide LevelContext to them
```
<section className="section">  
<LevelContext.Provider value={level}>  
{children}  
</LevelContext.Provider>  
</section>
```
- This tells React that, if any component asks for LevelContext give them this level, The component will use the nearest `<LevelContextProvider>` in the UI tree above it.
-  The Overview would be : 
	- You pass a level prop to the section
	- Section wraps its children into <LevelContext.provider value={level} >
	- Heading asks the closest value of levelContext above with useContext(levelContext)
##### Using and providing context from the same context
- Currently, we have to specify each section level manually
```
<Section level={1}>  
	...  

<Section level={2}>  
	...  
<Section level={3}>
```
- If you just look at the code above, component can look at context above, so as each section rests in another section, it can look at the context above as well
- So, you can pass {level + 1 } down automatically to the below component by just taking from the above component
```
<section className="section">  
<LevelContext.Provider value={level + 1}>  
{children}  
</LevelContext.Provider>  
</section>
```
- There is an example, just look at [this](https://react.dev/learn/passing-data-deeply-with-context#using-and-providing-context-from-the-same-component) code for better understanding
- What's important is , it can able to read only value provided by previous Component or the near provider it seems
##### Context passes through intermediate components
- Even if there are any nested components inside they will also will have access to the level automatically
- Contexts let you write components that adapt to their surroundings and display themselves differently on where they are being rendered
- Context can remind us of css property, you can specify color:green for a div , and all the elements inside div will have same color as green, you can override this only when you specify different color for it
- Same like CSS, different properties like color and background color don't override each other, Similarly different React contexts don't override each other
- Each context that you create like createContext is completely separate from the ones, ties together components using and providing that particular context
```
<Section>
    <Heading>My Profile</Heading>
      <Post
        title="Hello traveller!"
        body="Read about my adventures."
      />
    <AllPosts />
</Section>
```
- In the above code, a context has been created and provided by section component, then we added AllPosts inside the section , AllPosts also had section component inside it, the section component inside allposts will get level from the Section component above it.
##### Before use context
- Just becoz you need to pass props several level deep, doesn't mean you should use context
- Consider these alternatives before using context
	- Start by passing props, when you pass props, deep down the components, it may feel slog, but gives an idea which component uses which props
	- It is advising that to have specific code for a component rather than rendering it where ever we want
	```
	const Layout = ({ posts }) => { // Some layout code 
		return <div>{/* Rendering posts directly in Layout */}</div>; 
	}; // Main component 
	const MainComponent = ({ posts }) => { 
		return <Layout posts={posts} />; 
	};
	```
	- In Layout component, we are rendering posts directly rather we create component of Post, directly pass it like that 
	```
	const Posts = ({ posts }) => { // Rendering posts 
	return ( <div> {posts.map(post => ( <div key={post.id}>{post.title}</div> ))} </div> ); }; 
	// Layout component
	const Layout = ({ children }) => { // Some layout code return <div>{children}</div>; };
	```
	- Where you have children directly passing it to the Posts component
#### Scaling up with reducer and context
##### Combining reducer with context
- Its simple lesson, when you use reducer to group all the event handling logic into single logic
- When you use context to provide data as deep as possible through any component
- It becomes relatively easy, when you use context for passing the components the dispatch function and the state variable.
- We need not to have prop drilling if we don't use context for passing state variable and setfunction for it
### Thought process behind a react app

- These are the three steps we normally do 
1. Break the app into components
	 2. Describe visual states for each app
	 3. connect your components together, so that data flows through them

#### Implement a UI in react app

- There are five steps for the implementation of UI
	1. Break the UI into component hierarchy
		- every design splits as follows
 
		```mermaid
			graph TD;
			A[Design]
			B[Programming]
			B1["` 1.component comes in programming, it should ideally do one thing
			2.if complexity grows, a new component to be created`"]
			C[Css]
			L[Design Layers]
			A --> B --> B1
			A --> C
			A --> L
		```
          
				- For the following, data:

		```
		[  
		{ category: "Fruits", price: "$1", stocked: true, name: "Apple" },  
		{ category: "Fruits", price: "$1", stocked: true, name: "Dragonfruit" },  
		]
		```

				- you will have the following heirarchy of components
		 ![[s_thinking-in-react_ui_outline.png]]

	1.  Static version of React 
	            - its just build of components with connection  between them by passing data as props
	            - Don't use state, as it is for interactivity, just build components and that's it
	            
		| Thinking | Typing |  version |
		| -------  | ------ |  ------  |
		|  High    |  Low   |  dynamic |
		| Low      | High   |  static  |
		
	 1.  Interactivity by states
		 - Its very important to know, which is state, which is props 
		  for example: 
		  -  any thing which changes and can't be computed is state, all others are not states, so 
		  - The original list of products
					  - The search text the user has entered
					  - The value of the checkbox
					  - The filtered list of products
			In here, products original list doesn't change, filtered products can be  computed from it , so both are not states , 
			remaining all are states, as they change over time and can't be computed
              
			```mermaid
			graph LR;
			A[props] --> B[arguments passed to the function]
			C[state] --> D[Its a component memory]
			```

	1.  Placing of state
		  - React follows one way data flow i.e from parent to child component
  ```mermaid
  graph TD;
  A[state]
  A --> B["`take each component,
  see what is being rendered`"]
  A --> C["` Always try to put state in 
  common parent of components `"]
  A --> D["`doubt not to create a parent component to hold state, if nothing above works`"]
  ```
	- here, ex: if there is a search component with state and filtere products that's gonna use the search result
	- managing of state must be in the parent component of search and filter products, as these both should be rendered
	- after state managing , parent component gives results as props
- Here is the code for your review [[example_for_understanding_states]]

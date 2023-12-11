## Key points

- After I created the tic tac toe app, I observed following things
	1. You need to keep all the state manageable things in the root file
	2. Like, we will come from the start 
	
		### component hierarchy
	     
		 ```mermaid
		   graph TD;
		     A[Squares with 9 boxes] --> B[Game with squares in it] --> C[Board with game in it]
		```
	
	  1. If you see here, why did we not put entire code in squares component, rather keep three different components
	  2. Lets explore the functionality in three of the components
	
	```mermaid 
	graph LR;
	A[components] --> B[Square]
	A[components] --> C[Board]
	A[components] --> D[Game]
	squareFunctionality["` 1.It has to do something when clicked `"]
	boardFunctionality["` 1.Handles when a square box is clicked
	            2.Checks if there is any winner
	            3.Checks whose turn is next`"]
	gameFunctionality["` 1.Stores history in an array
	2.When a certain event is clicked, changes board to that event time
	3.Automatically setsup the currentMove`"]
	B --> squareFunctionality
	C --> boardFunctionality
	D --> gameFunctionality
	```
	
	
	5. Manamikkada em chesthunnam ante , evariki ee functionality meedha command undho, aa functionality ni, aa component lo pedthunnam
	6. But, it is always a good practise, to keep every hook in parent component, as we know how it is getting rendered, here is the excerpt
	
	```
	Placing the `history` state into the `Game` component will let you remove the `squares` state from its child `Board` component. Just like you “lifted state up” from the `Square` component into the `Board` component, you will now lift it up from the `Board` into the top-level `Game` component. This gives the `Game` component full control over the `Board`’s data and lets it instruct the `Board` to render previous turns from the `history`.
	```
	
	7. So, what happened in the whole tic tac toe is , it just got the data from the particular component and it passed the data to the component which is taking decisions on to what to display
	8. so square and board accumulated the data, gave to game on what to display, That's it.
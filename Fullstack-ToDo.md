# Fullstack ToDo App

## Overview
- Create a fullstack application capable of performing CRUD (Create, Read, Update, Delete) operations in the context of a toDo list.
- The toDo application should be capable of the following:
	- Client-Side
		- Have a home page that gets the toDo list and displays a quick view of the toDo's. Each toDo in the home page should be a ToDoCard which can mark itself as completed via PUT request or can be deleted from the database via DELETE request.
		- Have a create toDo form page that will send a POST request to the server to create a new toDo.
		- Have a navbar layout that allows a user to navigate between pages.
	- Server-Side
		- /todo/all route that fetches all toDo's
		- /todo/create-one route that creates a new toDo
		- /todo/update-one/:id route that updates a toDo
		- /todo/delete/:id route that deletes a toDo

## High Level Instructions
- Create two new repos for server and client: 
	- fullstack-todos-client for the client code 
	- fullstack-todos-server for the server code
		- This repository should be initialized with node .gitignore
- Create a new local folder called fullstack-todos and clone both repositories into this folder.
- Add the two repository git links to populi.

### 1) Project Setup

#### Server-Side
- Initialize the project using express-generator
	- npx express-generator -e
- NPM Install dotenv, mongodb, uuidv4, cors, nodemon
- Update npm start script to use nodemon instead of node
- Create a .env file in the project root
- Change express server port to 4000 using the .env file
- Add Mongo Connection env vars to .env file
Note: For this project we will set the database env var to "todoDB"
- Create the mongo.js file in the project root and add the mongo connection code
- Add the boilerplate code for dotenv, mongo and cors to app.js
- Run npm start to test that your server is connected to mongo and is up and running

#### Client-Side
- Initialize the project using create-react-app
	- npx create-react-app .
- NPM Install react router
- Create a .env.local file in the project root
- Add the REACT_APP_URL_ENDPOINT env var to .env.local set to http://localhost:4000
- Add the urlEndpoint variable to App.js in the global scope above ```<App/>```

### 2) Basic Scaffolding 
- _Approach_: Our first step will be to serve some mock data server side (not coming from mongo yet) so that we can create our basic display components client side. After we get things working at a base level, we will begin implementing features.

#### Server-Side
- Create a simple todo's router file and add it to express in app.js
- Add the following mock data as a global variable in the todo's router file
```
const mockTodos = [{
	id: "4387f4d8-aeac-4559-9f1b-3c5d537c955c",
	title: "Implement Fullstack ToDo List",
	description: "Implement the fullstack todo list application.",
	isComplete: false,
	priority: "High",
	creationDate: new Date(),
	lastModified: new Date(),
	completedDate: null
}, {
	id: "e365f13c-4c1d-4ee1-8a66-3dbbbab71f0d",
	title: "Create /all route for mock data",
	description: "Create an express route that will respond with the mock todo list.",
	isComplete: false,
	priority: "High",
	creationDate: new Date(),
	lastModified: new Date(),
	completedDate: null
}, {
	id: "08dd1f20-7d31-4120-89ed-343d4006a7cb",
	title: "Create a home page in the client",
	description: "Create a Home Page in React that will display all the todos.",
	isComplete: false,
	priority: "High",
	creationDate: new Date(),
	lastModified: new Date(),
	completedDate: null
}, {
	id: "98a06f8f-50c9-4832-9d2d-daa45543db00",
	title: "Create the todo card component",
	description: "Create a react ToDoCard component that will be rendered for each todo on the home page.",
	isComplete: false,
	priority: "Medium",
	creationDate: new Date(),
	lastModified: new Date(),
	completedDate: null
}, {
	id: "7c5d70bb-2a00-4009-9bb8-1bb163fb501f",
	title: "Test basic application with mock data",
	description: "Visit the client Home Page to see the todo's displayed as a list.",
	isComplete: false,
	priority: "Medium",
	creationDate: new Date(),
	lastModified: new Date(),
	completedDate: null
}]
```
- Create a /todos/all GET route that will respond with the mockTodos
- _Optional_: Test the /todos/all route using Postman

#### Client-Side
- Create a Layouts, Pages and Components folders in the ./src folder
- Create the following files and react components in those files: 
	- _Note_: For now, keep these as basic react components.
	- Create a new layout called GlobalLayout
	- Create a new component called NavBar
	- Create a new component called ToDoCard
	- Create a new page called HomePage
- Create a new browser router in the body of ```<App/>``` and add a new RouteProvider to the JSX of ```<App/>```.
- Set the "/" route in the browser router to render the ```<GlobalLayout/>``` as the element.
- Set the first child route of GlobalLayout to render ```<HomePage/>``` as the element. Set this route as the index route.
- Import NavBar into the GlobalLayout file and add an instance of ```<NavBar/>``` into the JSX of ```<GlobalLayout/>```.
- Import Outlet from react-router-dom and add the ```<Outlet/>``` component to ```<GlobalLayout/>``` under ```<NavBar/>```
- Add an h1 header to the jsx of ```<HomePage/>``` that says 'Fullstack ToDo Application'.
- Create a new state variable to ```<App/>``` called toDoList to hold our todos, it should be initialized to an empty array.
- Create a new useEffect in the body of ```<App/>``` that fetches the mockTodos from the /todos/all API route and saves them to the toDoList state variable.
- Pass toDoList as a prop into <HomePage/> (the browser router element).
- In ```<HomePage/>```, map through the todo's in the toDoList prop and return a ```<ToDoCard/>``` for each todo. 
- Inside the mapper function that is generating the ```<ToDoCard/>```'s, pass the toDo as a prop into ```<ToDoCard/>```.
- In ```<ToDoCard/>``` display the following toDo information:
	- The toDo title in an h2 tag.
	- The text "ID:" and the toDo id in a p tag.
	- The text "Description:" and the toDo description in a p tag.
	- The text "Priority:" and the toDo priority in a p tag.
	- The text "Is Complete:" and the text "Complete" if toDo.isComplete is true or the text "Incomplete" if toDo.isComplete is false in a p tag.
	- The text "Creation Date:" and the toDo creationDate.toString() in a p tag.
	- The text "Last Modified:" and the toDo lastModified.toString() in a p tag.
	- The text "Completed Date::" and the toDo completedDate in a p tag which only renders if the toDo completedDate is not null (truthy). 
- If you implemented the above properly, you should see the mockTodos display on the Home Page as a list of ToDoCards.

### 3) Post and Persist
- _Approach_: To switch over to mongo and have new toDo's be persisted in our database, we will create a POST route server side and hook it up to a form submission client side.

#### Server-Side
- Create a new POST route /todos/create-one that receives the following information from the request body and saves it to the mongo todos collection.
	- title => type string
	-	description => type string
	-	priority => type string
- The following fields should be generated in the API route when creating the toDo.
	- id => initialized to new uuid (using uuidv4)
	- isComplete => initialized to false
	- creationDate => initialized to a new date
	- lastModified => initialized to a new date
	- completedDate => initialized to null
- _Note_: When creating this new todo, the database (todoDB) and the collection (todos) will be created automatically when the first todo is inserted. 
- _Recommended_: Test this route using Postman before implementing the client-side form.

#### Client-Side
- Create a new page called ToDoFormPage with an h1 that says "Create ToDo Form". 
- In ./src/App.js:
	- Add ToDoFormPage as a child route of "/" with the path "/todo-form".
	- Pass urlEndpoint as a prop into ToDoFormPage in App.js. 
- In ./src/NavBar.js:
	- Add ```<Link/>``` components to the ```<NavBar/>``` that links to the HomePage and the ToDoFormPage.
		- "/" => Home
		- "/todo-form" => ToDo Form
		- _Recommendation_: Add this css class to App.css so that the links are easier to see:
			- a { 
					color: white;
					margin: 0 10px;
				}
- In ./src/Pages/ToDoFormPage:
	- Add state variables and input fields for:
		- title => should be a text input field
		- description => should be a textarea field
		- priority => should be a option and select dropdown with the following options:
			- High
			- Medium
			- Low
	- Create a function called handleCreateToDo that sends a POST request to the API route /todos/create-one. The body should include the state variables:
		- title
		- description
		- priority
	- Add a button that says "Create ToDo" and have it call handleCreateToDo onClick.
	- Add a programmatic redirect to "/" using react-router-dom in the Create ToDo button onClick handler.

### 3.5) Refactor /all for Mongo
- _Approach_: Now that we are able to create new toDo's in our database, we can switch the /todos/all route to use mongo instead of the mockdata.

#### Server-Side
- Switch the /todos/all route to find all toDo's in the mongo database and send those in the response instead of mocktodos.
- _Optional_: Add some todo's to the database so we can see a list of todo's client-side.
- If you implemented the above properly, you should be able to fill in the toDo Form fields, create a new toDo and be redirect to the Home Page. 

### 4) Mark ToDo Complete
- _Approach_: We will add functionality to the toDo card so that a user can mark a toDo as complete.

#### Client-Side
- In App, pass urlEndpoint as a prop into ```<HomePage/>```, then in ```<HomePage/>```, pass the urlEndpoint prop into ```<ToDoCard/>``` (inside the mapper function).
- Add a new function to the body of ```<ToDoCard/>``` called handleSetToDoComplete.
- handleSetToDoComplete should make a PUT request to the /todos/update-one/:id route where the id of the toDo is set in the url in place of :id. This request should send isComplete in the request body with a value OPPOSITE to the current value of isComplete in the toDo. E.G. If toDo.isComplete is currently true, the value of isComplete: false should be sent to the server in the request body. 
- Add a new button to ```<ToDoCard/>``` that says 'Toggle Complete' that calls handleSetToDoComplete in the onClick handler function.

#### Server-Side
- Create a new PUT route /todos/update-one/:id 
- The /todos/update-one/:id route should:
	- Check that the isComplete field in the request body is defined.
	- If isComplete is defined and is true, update the toDo in the database to have isComplete equal to true and completionDate equal to a new date.
	- If isComplete is defined and is false, update the toDo in the database to have isComplete equal to false and completionDate equal to null.

### 5) Delete ToDo
- _Approach_: We will add functionality to the toDo card so that a user can delete a todo.

#### Server-Side
- Create a new DELETE route /todos/delete-one/:id 
- The /todos/delete-one/:id route should:
	- Find the toDo in the database whose id matches the id param and deletes it. 

#### Client-Side
- Add a new function to the body of ```<ToDoCard/>``` called handleDeleteToDo.
- handleDeleteToDo should make a DELETE request to the /todos/delete-one/:id route where the id of the toDo is set in the url in place of :id.
	- _Note_: Since we are not sending a body with this request, we do not need to include the body or headers field in the fetch options; but we DO need to set the method to "DELETE".
- Add a button to the jsx of ```<ToDoCard/>``` that says 'Delete ToDo' that calls handleDeleteToDo.

### 6) Refetch Data
- _Approach_: Now that we have the ability to update and delete toDo's, we will add functionality so that the client automatically refreshes the toDo's when an update occurs.

#### Client-Side
- Add a new state variable in App called shouldRefetch initialized to false.
- Add shouldRefetch as a dependency to the useEffect that is fetching the toDo's from the /todos/all API route.
- Pass setShouldRefetch as a prop into ```<HomePage/>``` and ```<ToDoFormPage/>```.
- In ```<HomePage/>```, pass setShouldRefetch as a prop into ```<ToDoCard/>```.
- In the functions handleCreateToDo, handleSetToDoComplete and handleDeleteToDo, set shouldRefetch to true at the top of the function and set shouldRefetch to false at the bottom. 
- If you implemented the above properly, you should be able to click "Toggle Complete" or "Delete ToDo" and see the toDo/toDoList automatically update in the browser. You should also be able to submit a new toDo using the toDo Form and see the new toDo automatically appear on the Home Page.





**********STRETCH_GOALS**********


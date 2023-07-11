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
- Add Mongoose Connection env vars to .env file
	- _Note_: For this project we will set the database env var to "todoDB"
- Create the mongoose.js file in the project root and add the mongoose connection code
- Add the boilerplate code for dotenv, mongo and cors to app.js
- Run npm start to test that your server is connected to mongodb and is up and running

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
- Add a Todo model with these fields
```
	id: it is a string, use uuid v4
	title: string, required
	description: string, required
	isComplete: boolean, default is false
	priority: string, enum: ["low", "medium", "high"], default to medium
	creationDate: Date, default date now
	lastModified: Date, default date now
	completedDate: Date, no default
```
- Create a /todos/all GET route that will respond with all the todos using the .find method from mongoose.
- _Optional_: Test the /todos/all route using Postman

#### Client-Side
- Create a Layouts, Pages and Components folders in the ./src folder
- Create the following files and react components in those files: 
	- _Note_: For now, keep these as basic react components.
	- Create a new component called NavBar
	- Create a new component called ToDoCard
	- Create a new page called HomePage
- Create a new browser router in the body of ```index.js``` and add a new RouteProvider with router.
- Set the "/" children in the browser router to render the ```<App/>``` as the element.
- Set the first child route of App to render ```<Home/>``` as the element. Set this route as the index route.
- Import NavBar into the App.js file and add an instance of ```<NavBar/>``` into the JSX of ```<App/>```.
- Import Outlet from react-router-dom and add the ```<Outlet/>``` component to ```<App/>``` under ```<NavBar/>```
- Add an h1 header to the jsx of ```<HomePage/>``` that says 'Fullstack ToDo Application'.
- Create a new state variable to ```<App/>``` called toDoList to hold our todos, it should be initialized to an empty array.
- Create a new useEffect in the body of ```<App/>``` that fetches the todos from the /todos/all API route and saves them to the toDoList state variable.
- Pass toDoList into ```<HomePage/>``` using context.
- In ```<HomePage/>```, map through the todo's in the toDoList by grabbing it using useOutletContext and return a ```<ToDoCard/>``` for each todo. 
- Inside the mapper function that is generating the ```<ToDoCard/>```'s, pass the toDo as a prop into ```<ToDoCard/>```.
- In ```<ToDoCard/>``` display the following toDo information:
	- The toDo title in an h2 tag.
	- The text "ID:" and the toDo id in a p tag.
	- The text "Description:" and the toDo description in a p tag.
	- The text "Priority:" and the toDo priority in a p tag.
	- The text "Is Complete:" and the text "Complete" if toDo.isComplete is true or the text "Incomplete" if toDo.isComplete is false in a p tag.
	- The text "Creation Date:" and the toDo creationDate in a p tag.
	- The text "Last Modified:" and the toDo lastModified in a p tag.
	- The text "Completed Date::" and the toDo completedDate in a p tag which only renders if the toDo completedDate is not null (truthy). 

### 3) Post and Persist

#### Server-Side
- Create a new POST route /todos/create-one that receives the following information from the request body and saves it to the mongo todos collection.
	- title => type string
 	- description => type string
  	- priority => type string
- The following fields will be generated in the API route when creating the toDo.
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
	- If isComplete is defined in the request body and is true, update the toDo in the database to have isComplete equal to true and completedDate equal to a new date.
	- If isComplete is defined  in the request body and is false, update the toDo in the database to have isComplete equal to false and completedDate equal to null.

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

### 1) Create-One Server-Side Validation
- Add validation checks to the /todos/create-one route that checks to see that:
	- title, description are defined and that they are strings
	- priority is one of three possible values "High", "Medium" or "Low"
- If any of these validation checks fail, then a success: false response should be sent to the client along with a message that details the validation error.
- If the client receives a success: false response and the payload has a message, the toDo Form should display the error message to the user in the form and should NOT redirect to "/". Instead, the user should be able to resubmit the form after entering valid data. Only once the success: true response is received from the server should the user be redirected to "/"

### 2) Improved /all Route
- _Approach_: In order to have our /all route handle the query options limit, page, sortBy and sortOrder, we'll have to modify the url string in the ```<App/>``` useEffect that is fetching all the blogs. We'll also need to add input fields to the Home Page for these state variables so that the user can modify them. We will send these parameters back to the server in the url as query params. Then server-side we'll need to update the mongo query in the /all route to be able to handle these parameters.

#### Client-Side
- _Optional_: The following instructions for creating limit, page, sortBy and order in ```<HomePage/>``` can also be implemented in a new component that is imported into ```<HomePage/>``` such as ```<HomePageOptionBar>```. The state and input fields would live in ```<HomePageOptionBar>``` and the handler function for updating the /all fetch url string would be passed through the ```<HomePage/>``` props and into ```<HomePageOptionBar>```. Whether or not you implement it in this way or keep the inputs inside the ```<HomePage/>``` component is up to you.
- Add state variables to ```<HomePage/>``` for limit (initialized to 10), page (initialized to 1), sortBy (initialized to "creationDate") and order (initialized to "asc"). 
- Add number input fields to ```<HomePage/>``` that set values for limit and page. These input fields should get their value directly from the state variable (I.E. they are controlled components).
- Add select/dropdown menus to ```<HomePage/>``` that set values for sortBy and order. Order should only have 3 values: blank, "asc" and "desc". SortBy should have values for every field on the toDo: id, title, description, isComplete, priority, creationDate, lastModified and completedDate.
- In ```<App/>```, add a new state variable called getAllParams.
- In the body of ```<App/>```, add a new function called generateGetAllParams that takes in limit, page, sortBy and order as parameters. This function should create a query param string (everything after the ? in the url) and set getAllParams to that string. 
- Set getAllParams as a dependency in the /all useEffect.
- Refactor the fetch url in the /all useEffect to concatenate getAllParams to the end of the url string.
- Pass generateGetAllParams as a prop into ```<HomePage/>``` and call it with the arguments limit, page, sortBy and order in the onChange handlers of the input fields. 
	- _Alternative_: An alternative approach to this instruction is to create a useEffect in ```<HomePage/>``` that calls generateGetAllParams in the effect function. The dependency variables of this useEffect would be limit, page, sortBy and order. Thus, whenever the state variables limit, page, sortBy or order are changed by user input, the useEffect will call generateGetAllParams to set the getAllParams state variable in ```<App/>```.

#### Server-Side
- Update the /todos/all route to get the url query params limit, page, sortBy and order from the url.
- Update the mongo .find() query to include limit, skip and sorting functionality. 
	- _Note_: Mongo does not handle the page option natively. You will have to convert the page parameter to skip and then pass the converted value into .skip(). You can calculate skip by multiplying page by limit and subtracting 1. 

### 3) Update ToDo Fields
- _Approach_: In order to update the data such as the title or description for our toDo's, we could create a whole new form for it. However, a better user experience would be to allow the user to edit a toDo directly on the Home Page and then send the new data as a PUT to the server. Thus, we will be implementing input fields and an update button on the ```<ToDoCard/>``` that will conditionally render based upon a new isEditable state variable we will add to the ```<ToDoCard/>```. If implemented properly, we should be able to click an edit button on the toDo Card, have the input fields render in place of the static text fields, update the data in the input fields and then click the Update ToDo button to update the toDo data.

#### Server-Side
- Add functionality to the /todos/update-one/:id route that will set the title, description and priority of the toDo in Mongo if these fields are defined in the request body.

#### Client-Side
- In ```<ToDoCard/>```, implement the following:
	- Add a new state variable called isEditing initialized to false. We will use this variable to conditionally render input fields to the toDo Card. 
	- Add three new state variables called title, description and priority. These state variables should be initialized to the current values of the todo title, description and priority.
		- _Discussion_: One issue we have is that the data for the toDo is being stored in the toDoList on ```<App/>``` and is being passed down to ```<ToDoCard/>``` through props. We could update the toDo data in the toDoList, but that would not persist it since we need to save the data in mongo. What we will do instead is store the values of these new input fields for title, description and priority in the state of ```<ToDoCard/>```. Then we will make a PUT request to send this data to the server, after which we will call the refetch functionality on the /all route to update the toDoList. If it works, then we should be able to edit the toDo data directly on the ```<ToDoCard/>``` and have that new data appear on the page after the refetch triggers.
	- Add three new input fields, a text input for title, a text area for description and a select/option dropdown for priority. Each of these three input fields should ONLY (conditionally) render to the page if isEditing is true. Additionally, refactor the title, description and priority elements that were already rendering to the page to be rendered only if isEditing is false. Place the input fields next to the elements that they are replacing. This will have the effect of rendering either the static elements or the input fields depending on if the user is editing the toDo Card or not.
	- Add a new button to the bottom of ```<ToDoCard/>``` that only renders if isEditing is false. The button should display the text "Edit ToDo" and the onClick handler should set isEditing to true.
	- Add a new button to the bottom of ```<ToDoCard/>``` that only renders if isEditing is true. The button should display the text "Update ToDo" and the onClick handler should set isEditing to false.
	- _Quick-Test_: If you implemented the above correctly, you should be able to click on the "Edit ToDo" button for any toDo on the Home Page. The static elements for title, description and priority should be replaced by their respective input fields and they should be editable. 
	- Add a new function in the body of ```<ToDoCard/>``` called handleUpdateToDo. handleUpdateToDo should make a PUT request to the server at the /todos/update-one/:id route and should send the title, description and priority in the body (the current values of the state variables). Additionally, set the shouldRefetch variable to true (using setShouldRefetch) at the top of handleUpdateToDo and set it to false at the bottom (after the fetch call completes).
	- Call handleUpdateToDo in the onClick handler of the Update ToDo button.
- If all of the above was implemented correctly, you should be able to click "Edit ToDo", update some of the fields, then click "Update ToDo" to see the data refresh on the page with the updated values you entered in.





# 2.1 Loading Todos

> Objective: As a user, I want to see all the todo entries I have added previously, so that I know what outstanding tasks I have.

In this exercise, we'll learn how to interact with our backend server using API request libraries, and state and effect hooks. We will be retrieving todo entries that we have created in the previous backend exercises.

---
## Importing our hooks
Before we start, let's insert code to import the state and effect hooks from React. We do this by adding the following code at the top of `src/screens/Todo.js`.

```js
import { useState, useEffect } from 'react';
```
## Create a TodoItem component

Remember that in the `src/screens/Todo.js` file, the `Todo` component controls our Todo table. 

Now, let's create a new component to display our existing todo items, and name it `TodoItem`.

```js
function TodoItem(props) {

  return (
    <>
      <tr>
        <td>
          <FormCheck
            checked={true}
          />
        </td>
        <td width={"100%"}>{props.description}</td>
      </tr>
    </>
  );
}
```
## Create state variables
A `useState` hook lets us track state in our current component. For our `TodoItem` component, notice that we have a `FormCheck` element. We can create a new state variable called `done` to track whether the todo item is done. 

Add a new state variable called `done` and use that to toggle whether the item should be checked or not. Your code should now look like this: 
```js
function TodoItem(props) {
  const [done, setDone] = useState(props.done);

  return (
    <>
      <tr>
        <td>
          <FormCheck
            checked={done}
          />
        </td>
        <td width={"100%"}>{props.description}</td>
      </tr>
    </>
  );
}
```

To track the current list of todo items in our application, let's create another state variable called `todoItems`. Insert the following into your `Todo` component: 

```js
const [todoItems, setTodoItems] = useState({});
```

Notice that this variable has an initial value of `{}`, otherwise known as an empty object. To fill this up with the real list of todos, we'll use an effect hook.

## Fetching todos with effect hook

The `useEffect` hook allows you to perform 'side effects' in your components. Side effects are actions that are outside the scope of ReactJS. Some examples include: 
* fetching data via an API, 
* or directly updating the DOM.

For this exercise, we want to fetch the todos from our backend by calling a GET API. We will be using a library called axios to make our API calls. 

```js
import axios from "axios";
```

Let's start with a declaring a function called `populateTodos`, which fetches our todo items from our backend API endpoint, and updates our state variable with the data fetched.

```js
const populateTodos = () => {
axios.get(`${CONFIG.API_ENDPOINT}`)
    .then((result) => {
    setTodoItems(result.data);
    })
}
```

Since we're using the `CONFIG.API_ENDPOINT` variable, make sure to import our config variables as well:
```js
import CONFIG from "../config";
```

Now, let's use an effect hook to call our API upon initial render, and whenever we add or modify our list of todo items in our app. We do this by adding `todoItems` into the dependency array of our effect hook.

```js
useEffect(() => {
    populateTodos();
}, [todoItems]);
  ```

## Loading todo items into the table
We've fetched our todo items into a state variable called `todoItems`, but now we want to display it in our app using a bunch of `TodoItem` component instances.

We can do this using React's **Map** function:
```jsx
{/* Iterate through todoItems list to create new rows */}
{Object.keys(todoItems).map((item) => (
    // Forwards items to TodoItem element as props
    <TodoItem
    key={todoItems[item].id}
    id={todoItems[item].id}
    done={todoItems[item].done}
    description={todoItems[item].description}
    refreshToDos={populateTodos}
    />
))}
```

Reload your page and you should be able to see your existing todos, listed out in the table! 🎉
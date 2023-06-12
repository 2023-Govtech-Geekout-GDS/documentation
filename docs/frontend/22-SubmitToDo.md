# 2.2 Submitting Todos

> 💡 If you haven't completed Exercise 2.1 Loading Todos, get up to speed by switching to the `checkpoint-1` branch!

```
git checkout checkpoint-1
```

> Objective: As a user, I would like to see if my todo list has changed since the last time I checked, so that I can stay updated on my tasks.

In this exercise, we will learn how to add new todo entries using event handlers and POST requests.

---

## Making it click

Right now, we have an `Add` button, but it isn't doing anything. Let's make it reactive!

Locate the code for the 'Add' button in `Todo.js`. Add a new `onClick` function to it, which gets called every time the button is clicked.

```js
  <Button size="sm" variant="primary" onClick={submitNewTodo}>
    Add
  </Button>
```

Now let's define what our function `submitNewTodo` does:

```js
const submitNewTodo = () => {
  // Validation to ensure entry is not empty
  if (newTodoDescription.trim() !== "") {
    const newTodo = {
      description: newTodoDescription,
    };
    axios.post(`${CONFIG.API_ENDPOINT}`, newTodo).then(() => {
      // Does below action after request has been made 
      populateTodos();
    })
  } else {
    alert("Invalid Todo input!");
  }
}
```
The function `axios.post` makes a network call to our backend API to submit the new todo item.

After submitting the new todo item, we want to update our table to show the latest list of todos. So, we call our existing `populateTodos()` function.

Now, test the new changes out. Do you notice something wrong? After submitting a new todo, the form still remains as it is, with the old todo description. Let's fix that in the next subsection.

## Resetting the Todo Form

To control the state of the todo description field, we create a new state variable called `newTodoDescription`.

```js
const [newTodoDescription, setNewTodoDescription] = useState("");
```

Then, we ensure that our `input` element reflects the todo description by adding the following code to it: 

```js
value={newTodoDescription}
```

We also want to update our state variable when the user types or edits the todo description. We do this by setting an `onChange` callback function:

```js
onChange={(event) => {
    setNewTodoDescription(event.currentTarget.value);
}}
```

Our `input` element should now look like this:

```js
<input
  className="text table-input"
  placeholder="Enter new to-do here"
  id="newTodoDescription"
  type="text"
  value={newTodoDescription}
  onChange={(event) => {
    setNewTodoDescription(event.currentTarget.value);
  }}
>
</input>
```

We've set the current value of the `input` field to `newTodoDescription`, and handle any changes by the user with the `onChange` event function.

🎉 Congratulations, your text input field now resets and changes successfully!
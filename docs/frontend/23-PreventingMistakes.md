# 2.3 Preventing Mistakes

> 💡 If you haven't completed Exercise 1.2, get up to speed by switching to the `checkpoint-2` branch!

> `git checkout checkpoint-2`

> As a user, I would want to ensure my Todo items are valid, before adding them into the list

In this exercise, we will add a simple form of validation on our inputs.

Notice that currently, if you do not provide any inputs (empty field) and click submit, it goes through, adding an entry that is empty. We want to prevent such situations from occuring.

## Adding Validations

Let start by making sure our inputs are validated before we commit them.

**Before** a Todo input is committed, we need to perform some from of checks. If the checks pass, we can proceed to commit it. Otherwise, we should alert the user.

Search for the location where this validation should be added (Hint: look at `submitNewTodo`).

```js
const submitNewTodo = () => {
  setIsLoading(true);
  const newTodo = {
    description: newTodoDescription,
  };
  axios.post(`${CONFIG.API_ENDPOINT}`, newTodo).then(() => {
    // Does below action after request has been made
    populateTodos();
    setNewTodoDescription("");
  })
  setIsLoading(false)
}
```

Notice in the current `submitNewTodo` function, no validation is done. We immediately create a new Todo item and call the API. We can add a conditional check, specifically for the case we are dealing with, which are empty values.

We can add a validation like this:

```js
const submitNewTodo = () => {
  setIsLoading(true);
  // Validation to ensure entry is not empty
  if (newTodoDescription.trim() !== "") {
    const newTodo = {
      description: newTodoDescription,
    };
    axios.post(`${CONFIG.API_ENDPOINT}`, newTodo).then(() => {
      // Does below action after request has been made
      populateTodos();
      setNewTodoDescription("");
    })
  }
  setIsLoading(false)
}
```

Now, empty inputs aren't added into your Todo list.
We can further improve the validation by ensuring white spaces aren't allowed as well; introducing, the Javascript `trim` method

Lastly, we should provide some form of feedback to the user in the event they specify an invalid input.

We can introduce a basic alert box here `alert("message")`, but we can introduce nicer alert/validation banners using external libraries!

## Solution

**1. Update the `submitNewTodo` method in `src/screens/Todo.js` to the following**

```js
// POST request to submit new ToDo entry
const submitNewTodo = () => {
  setIsLoading(true);
  // Validation to ensure entry is not empty
  if (newTodoDescription.trim() !== "") {
    const newTodo = {
      description: newTodoDescription,
    };
    axios.post(`${CONFIG.API_ENDPOINT}`, newTodo).then(() => {
      // Does below action after request has been made
      populateTodos();
      setNewTodoDescription("");
    })
  } else {
    // Show alert box with error message
    alert("Invalid Todo input!");
  }
  setIsLoading(false)
}
```
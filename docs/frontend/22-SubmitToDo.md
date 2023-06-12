# 2.2 Submitting To Do

> 💡 If you haven't completed Exercise 2.1: FetchToDo, get up to speed by switching to the `checkpoint-1` branch!

> `git checkout checkpoint-1`

> As a user, I would like to see if my todo list has changed since the last time I checked, so that I can stay updated on my tasks.

> It would be awesome to also have a visual indication of the system working on my request. ^\_^

In the this exercise, we will learn how to use write a request to add new todo entries using event handlers

---

## Adding a button

Let's start by adding a button.

```js
<Button size="sm" variant="primary">
  Add
</Button>
```

The button can be placed anywhere, we've decided to put it just below the to-do list.

```js
          </Table>
        </Form>
        {/* to implement: button */}
      </div>
    </Container>
```

These Buttons are standard SGDS components and come in different styles! Check them out [here](https://react.designsystem.tech.gov.sg/?path=/docs/components-button--default-story).

---

## Making it click

When a button is `clicked`, it fires the attached `onClick(event)` handler.
Start by defining a new callback:

```js
const submitNewTodo = () => {
  console.log("submitted new to-do!");
};
```

Then hook it up to the button:

```js
<Button
  size="sm"
  variant="primary"
  onClick={/* insert callback function here */}
>
  Add
</Button>
```

When refresh is clicked, you should now see a log entry appear in the console!

Instead of calling `console.log()`, let's make a call to the backend:

```js
const submitNewTodo = () => {
  console.log("submitting new to-do...");

  const newTodo = {
    description: newTodoDescription,
  };
  axios.post(`${CONFIG.API_ENDPOINT}`, newTodo);

  console.log("submitted new to-do!");
};
```

The function `axios.post` is making a network call to our backend API to submit the new `todo item`.

After submitting the new `todo item`, let's update our table to show the latest list of `todos`. We have created a function `populateTodos` for this purpose. Similarly, make an call for this, and remove the new to-do description.

```js
function submitNewTodo() {
  const newTodo = {
    description: newTodoDescription,
  };
  axios.post(`${CONFIG.API_ENDPOINT}`, newTodo).then(() => {
    // Does below action after request has been made
    /* call populateTodos here */
    setNewTodoDescription("");
  });
}
```

But how does setNewTodoDescription work? Let's find out in the next section!

---

## Solution

**1. Include the following method in `src/screens/Todo.js` after `populateTodos` function**

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
    });
  } else {
    alert("Invalid Todo input!");
  }
  setIsLoading(false);
};
```

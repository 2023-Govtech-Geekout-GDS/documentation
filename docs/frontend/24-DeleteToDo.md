# 2.4 Deleting Todos

> 💡 If you haven't completed Exercise 2.3, get up to speed by switching to the `checkpoint-3` branch!

```
git checkout checkpoint-3
```
Sometimes you realise that certain todo items are no longer valid... Or maybe you got lazy and don't feel like tackling them anymore! What could you do in such cases?
> Objective: As a user, I want to be able to delete items off the todo list.

## Deleting the todo item

Write a function to delete a todo item.

Now that we've gone through how to load, submit, and update todos, we'll let you attempt this exercise on your own 😃.

Use the delete endpoint from the backend to achieve this task. Remember to reload the todo list after deleting!

```js
const deleteTodoItem = () => {
  // delete item here
  // refresh to-do list here
};
```
We will be adding a delete icon to enable the deletion action. Let's complete the `onClick` action to call the `deleteTodoItem` function.

```js
<>
  <tr>
    <td>
      <FormCheck
        onChange={(event) => updateTodoItem(event.currentTarget.checked)}
        checked={done}
      />
    </td>
    <td width={"100%"}>{props.description}</td>
    <td>
      <img
        alt="delete-icon"
        src={crossIcon}
        onClick={/* insert delete function here */}
        className="delete-icon"
      />
    </td>
  </tr>
</>
```

## Solution

**1. Include the following `deleteTodoItem` method in `src/screens/Todo.js`.**

```js
const deleteTodoItem = () => {
  axios.delete(`${CONFIG.API_ENDPOINT}/${props.id}`)
    .then(() => {
      // Calls for re-render of component after deletion
      props.refreshToDos();
    })
}
```

**2. Add the deletion icon along with the `onClick` listener to trigger a todo entry deletion.**

```js
<>
  <tr>
    <td>
      <FormCheck
        onChange={(event) => updateTodoItem(event.currentTarget.checked)}
        checked={done}
      />
    </td>
    <td width={"100%"}>{props.description}</td>
    <td>
      <img
        alt="delete-icon"
        src={crossIcon}
        onClick={deleteTodoItem}
        className="delete-icon"
      />
    </td>
  </tr>
</>
```

🎉 Congratulations! You're now able to load, submit, update and delete items from your todo list!
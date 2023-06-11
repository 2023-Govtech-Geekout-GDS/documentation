# 2.4 Deleting To do

> 💡 If you haven't completed Exercise 2.3, get up to speed by switching to the `checkpoint-3` branch!

> `git checkout checkpoint-3`

> Could you update the cross icon to delete the to-do item when clicked?

## Deleting the todo item

Use the delete endpoint from the backend to achieve this task. Remember to reload the to-do list after deleting!

```js
const deleteTodoItem = () => {
  // delete item here
  // refresh to-do list here
};
```

We will be adding a delete icon to enable the deletion action. Lets complete the onClick action to call the deleteTodoItem function

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
// DELETE request to remove entry
const deleteTodoItem = () => {
  axios.delete(`${CONFIG.API_ENDPOINT}/${props.id}`).then(() => {
    // Calls for re-render of component after deletion
    props.refreshToDos();
  });
};
```

**2. Add the deletion icon along with the onClick listener to trigger a todo entry deletion**

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

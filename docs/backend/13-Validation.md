# 1.3 Extra: Validating data

> This is an optional exercise

## Failing the Test First

Run the following command. Maybe it will provide you some clue on how to implement? 🤔

```
npm run test:extra
```
The total should be `3 failed, 10 passed, 13 total`. Can you figure out why it is failing?

## Things to Do

Another vital component in backend applications is validation - whenever data can be modified, there should be measures to ensure that it does not get corrupted.

For example, with the current way we update our TodoList it is possible to send a `"PUT /todos/{id}"` that looks like this:

Request to `PUT /api/todos/{id of existing Todo}`

```
{
    id: "I can put anything here, I can even put the UUID of another todo"
    description: "Regular description"
    done: false
}

```

Another simple example would be if we wanted to prevent certain data from being deleted. (e.g. A todo with the description "Improve backend")

To prevent these scenarios, let's implement two extra checks to our app:

1. When a PUT request is made, if the `id` in the path and body do not match then return a code 409[^1] error with the message `UUID in path and body do not match`
3. When a DELETE request is made, if the `description` of the Todo is `Improve backend` - return a code 405[^2] error with the message `This todo cannot be deleted`

### Extra checkpoint
You can use the command `npm run test:extra` to see if you've validated these scenarios correctly.

[^1]: [HTTP code 409 - Conflict](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/409)
[^2]: [HTTP code 405 - Method Not Allowed](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/409)

---

## Solution

**1. Replace the following `deleteTodoById` method in `routes/method.js`**
> 💡 Explanation: We are doing a check to see if the description is "Improve backend" and return an error message if it is. This means that it cannot be deleted.
```
export async function deleteTodoById(req, res) {
  const { id } = req.params;
  if (id in todoList) {
    const entryToDelete = todoList[id];
    if (entryToDelete.description === "Improve backend") {
      return res.status(405).json(messageJson("This todo cannot be deleted"));
    } else {
      delete todoList[id];
      return res.status(200).json();
    }
  } else {
    return res.status(400).json(ERROR_MSGS.NO_SUCH_UUID);
  }
}
```

**2. Replace the following `updateTodoById` method in `routes/method.js`**
> 💡 Explanation: We are doing a check to see if the `id` in the path and body matches and return an error message if it doesn't.
```
export async function updateTodoById(req, res) {
  const { id } = req.params;
  const updatedTodo = req.body;
  if (id !== req.body.id) {
    return res.status(409).json(ERROR_MSGS.UUID_MISMATCH);
  } else if (id in todoList) {
    todoList[id] = { ...todoList[id], ...updatedTodo };
    return res.status(200).send();
  } else {
    return res.status(400).json(messageJson("UUID does not exist"));
  }
}
```

**3. Run the tests**

```
npm run test:extra
```

The total should be `13 passed, 13 total`

---

> 🚩 Are you stuck? Fear not! You can proceed on by checking out to the next checkpoint 😀
```
git checkout checkpoint-4
```
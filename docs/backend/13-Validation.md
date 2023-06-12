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

**1. Add the following methods to `routes/method.js`**
> 💡 Explanation: We are creating a method that calls the boredapi to get an activity in JSON format and create a ToDo item with the information.
```
export async function createRandomTodo(_req, res) {
  try {
    const responseJson = await fetch(
      'http://www.boredapi.com/api/activity'
    ).then((apiResponse) => apiResponse.json());
    const randomActivity = responseJson['activity'];
    const randomTodo = {
      id: v4(),
      description: randomActivity,
      done: false,
    };
    todoList[randomTodo.id] = randomTodo;
    return res.status(200).json(randomTodo);
  } catch (e) {
    // AbortError not exported in node-fetch V2
    const errorMessage = messageJson('Request from external api timed out');
    return res.status(500).json(errorMessage);
  }
}
```

**2. Add the following methods to `routes/index.js`**
> 💡 Explanation: We are creating a new route called `/todos/random` which links to the `createRandomTodo` when called.

```
forwardRouter.post("/todos/random", createRandomTodo);
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
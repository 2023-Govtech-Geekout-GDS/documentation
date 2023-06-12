# 1.0 Completing the CRUD API

So far, the front-end app has been supported by a backend which provides persistence. As a warm-up, we're going to complete an implementation of the [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) interface.

Navigate to `backend/src/routes`. You'll find the following files:

| File      | Description |
| ----------- | ----------- |
| `index.js`      | A cosmetic holder for all routes / methods supported. Most importantly, it does `default export` of an `express.Router()`, which will be used by the App  |
| `methods.js`   | Create, Read and Delete have been implemented here |
| `newMethods.js`   | You will implement 2 remaining methods (Read single and Update) here |

## Setting up
Install all the package dependencies required using the command
```
npm install
```

## Base checkpoint
Run the tests for the base checkpoint
```
npm run test:0
```

At this point you should have `8 skipped, 5 passed, 13 total` but as we progress through today's walkthrough we will be running more tests.

## Failing the Test First

Run the following command. Maybe it will provide you some clue on how to implement? 🤔

```
npm run test:1
```
The total should be `4 failed, 4 skipped, 5 passed, 13 total`. Can you figure out why it is failing?

## Things to Do

### Update
Right now, `updateTodoById` is stubbed out, returning the HTTP code for Not Implemented.

Edit the code - it should modify the `todoList` and return `200` upon success. For more details, navigate to the `swagger.json` documentation to check intended behaviour. Or, if you prefer to use a UI, start up Docker compose and find the docs [locally hosted](http://localhost:3001/swagger).

> **At this point you only need to implement return codes 200 and 400**

Feel free to reference the implementation of `deleteTodoById` - the necessary code is very similar!

```
export async function deleteTodoById(req, res) {
  const { id } = req.params;
  if (id in todoList) {
    delete todoList[id];
    return res.status(200).json(todoList);
  } else {
    return res.status(400).json({ message: "UUID does not exist" });
  }
}
```

When your code is complete, `npm run test:1`. You should find that 2 more tests (`"PUT /todos/{id}"`) are passing!

The total should now be `2 failed, 3 skipped, 7 passed, 12 total`. Ignore the errors for now, we will be addressing them below.

### Read
In `index.js`, you'll notice that we expose a Read method as a `GET` call to `/todos`. But while this API provides the entire Todo list, it's quite unusual that a frontend will want everything at once. More typically, frontends will request for a single object, as specified by their `id`.

Implement this new route. For experienced devs, this will be super easy, but just in case, here's a checklist of files you should be touching:

| File      | Necessary work |
| ----------- | ----------- |
| `newMethods.js`   | Export a new function called `getTodoById` out of this file. The signature will be identical to the previous functions. Remember to handle error cases! |
| `index.js`      | You'll need to add a new route on `todoRouter` to `GET` the todo item with its `id`, and import the new method you wrote in `newMethods.js` |


Once more, when your code is complete, `npm run test:1` to verify. With the `"GET /todos/{id}"` tests passing, the total should now be `4 skipped, 9 passed, 13 total`

---

## Solution

**1. Add the following code to `routes/newMethod.js`**
```
import { todoList } from "./methods.js";

export async function updateTodoById(req, res) {
  const { id } = req.params;
  const updatedTodo = req.body;
  if (id in todoList) {
      todoList[id] = {...todoList[id], ...updatedTodo};
      return res.status(200).send();
  } else {
      return res.status(400).json({ message: "UUID does not exist" });
  }
}

export async function getTodoById(req, res) {
    const { id } = req.params;
    if (id in todoList) {
      return res.status(200).json(todoList[id]);
    } else {
      return res.status(400).json({ message: "UUID does not exist" });
    }
}
```

**2. Add the following route to `routes/index.js`**
> 💡 Explanation: This will route the calls to `/todos/:id` to the method `getTodoById`

```
forwardRouter.get("/todos/:id", getTodoById);
```

**3. Run the tests**

```
npm run test:1
```

The total should be `0 failed, 4 skipped, 9 passed, 13 total`

---

> 🚩 Are you stuck? Fear not! You can proceed on by checking out to the next checkpoint 😀
```
git checkout checkpoint-1
```
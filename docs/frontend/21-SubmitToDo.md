# 2.1 Submitting To Do

> As a user, I would like to see if my todo list has changed since the last time I checked, so that I can stay updated on my tasks.
>
> It would be awesome to also have a visual indication of the system working on my request. ^\_^

In this exercise, we will add an "Add" button to the Todo application.

---

## Adding a button

Let's start by adding a button.

```tsx
<Button size="sm" variant="primary">
  Add
</Button>
```

The button can be placed anywhere, we've decided to put it just below the to-do list.

```tsx
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

```tsx
async function submitNewTodo() {
  console.log("submitted new to-do!");
}
```

Then hook it up to the button:

```tsx
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

```tsx
async function submitNewTodo() {
  console.log("submitting new to-do...");

  const newTodo = {
    description: newTodoDescription,
  };
  await axios.post(`${CONFIG.API_ENDPOINT}/todos`, newTodo);

  console.log("submitted new to-do!");
}
```

The _asynchronous_ function `axios.post` is making a network call to our backend API to submit the new `todo item`. The `await` keyword means that these statements will block, happening _sequentially_ one after another. That means that the second log entry is created only after the refresh is complete.

After submitting the new `todo item`, let's update our table to show the latest list of `todos`. We have created a function `populateTodos` for this purpose. Similarly, make an `asynchronous` call for this, and remove the new to-do description.

```tsx
async function submitNewTodo() {
  const newTodo = {
    description: newTodoDescription,
  };
  await axios.post(`${CONFIG.API_ENDPOINT}/todos`, newTodo);
  await /* call populateTodos here */
  setNewTodoDescription("");
}
```

But how does setNewTodoDescription work? Let's find out in the next section!

---

## Making Progress

Visual feedback is an important way of communicating with the user.

A progress indicator satisfies the user's need to know that the system received, and is processing, their request.

Start by adding a new piece of state to the Todo component:

```tsx
const [isLoading, setIsLoading] = useState(false);
```

Creating state in this way is useful because React can 'hook' into every modification to the state and help keep track of what changed / needs to be rendered again.

When calling the `useState()` hook, React gives you a read-only variable (with the default parameter specified in the `useState<T>(default: T)` call), and a setter you can call like this: `setIsLoading(true)`.

Let's hook up the `isLoading` state to the button. When the button is loading, we want to display the text `loading...` and disable the button from getting clicked.

```tsx
<Button
  size="sm"
  variant="primary"
  onClick={submitNewTodo}
  disabled={/* insert boolean refresh state here */}
>
  {isLoading ? "loading..." : "Add"}
</Button>
```

When the button is clicked, it should set the loading indicator with `setIsLoading(true)`.

```tsx
const [isLoading, setIsLoading] = useState(false);
async function submitNewTodo() {
  /* start the loading here */
  const newTodo = {
    description: newTodoDescription,
  };
  await axios.post(`${CONFIG.API_ENDPOINT}/todos`, newTodo);
  await populateTodos();
  setNewTodoDescription("");
  /* stop the loading here */
}
```

The isLoading animation starts before starting the actual submitting operation. When the `await populateTodos()` synchronously completes, the refresh animation stops.

On a local network, this refresh happens too quickly to be perceptible, so we're waiting a couple hundred milliseconds to ~~charge our capacitors~~ queue up the request.

# 2.3 Updating Todos

> 💡 If you haven't completed Exercise 2.2, get up to speed by switching to the `checkpoint-2` branch!

```
git checkout checkpoint-2
```

> Objective: As a user, I want to be able to check the items so that I can track the state of each item in the todo list.

In this exercise, we will allow the user to check done items off the todo list.

---

## Making use of Done state variable

Observe that there is a checkbox beside every todo item. Try clicking on any of the checkboxes. What happens?

Ans: Nothing :/

Let's fix that.

Recall our `done` state variable that we created in our `TodoItem` component:

```js
const [done, setDone] = useState(props.done);
```

This initialises a state for each item to the `done` variable using the `props.done` defined for each todo item. `setDone` provides us a setter method to update the state when we want to.

Let's make use of this state variable to keep track of each checkbox being marked/unmarked.

First we will need to hook a call to an `updateTodoItem` method when a checkbox is being clicked/changed.

```js
onChange={(event) => updateTodoItem(event.currentTarget.checked)}
```

Add an `onChange` listener above to the checkbox input that you have created. This will call the `updateTodoItem` method with the latest done state every time the checkbox is clicked.
Now we will need to link the `done` state variable to the checkbox so that the state is reflected correctly as it is currently not being used.

```js
checked = { done };
```

Add the above to the checkbox input you created. This ensures that the checkbox input reflects the `done` state variable correctly.

You should end up with the following `<FormCheck>` code:

```js
<FormCheck
  onChange={(event) => updateTodoItem(event.currentTarget.checked)}
  checked={done}
/>
```

## Persist the checkbox state

Now try refreshing the page. You'll realise that any checkbox changes made are lost after refreshing the page. So the next thing we have to do is to persist the state through the backend.

### Updating the backend

With every checkbox change, the method `updateTodoItem` is called, with the latest `done` state passed in as an argument.

Now, let's define what `updateTodoItem` does:
1. Update the `done` state variable
2. Update the backend

```js
const updateTodoItem = (done) => {
  setDone(done)
  axios.put(`${CONFIG.API_ENDPOINT}/${props.id}`, {
    id: props.id,
    description: props.description,
    done: done,
  });
}
```

🎉 Congratulations, now your state is persistent and refreshing will no longer pose a problem!
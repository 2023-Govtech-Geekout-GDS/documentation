# 2.3 Updating To Do

> 💡 If you haven't completed Exercise 2.1, get up to speed by switching to the `checkpoint-2` branch!

> `git checkout checkpoint-2`

Visual feedback is an important way of communicating with the user.

> As a user, I would want be able to check the items so that I can track the state of each item in the to do list

In this exercise, we will add a check box for each item to the Todo application.

---

## Adding a Checkbox

Lets start by adding a input checkbox. We can use the form checkbox from the SGDS component library.

```js
<FormCheck />
```

This checkbox should be placed in line with the item description display for easier reference

```js
return (
  <>
    <tr>
      <td>{/* insert checkbox here  */}</td>
      <td width={"100%"}>{props.description}</td>
    </tr>
  </>
);
```

Here we are using default html input tag with the checkbox type

---

## Creating a state for each checkbox

With the checkbox create we will want to keep track of the state when checkbox is click to done.
Let's use the useState hook as before.

```js
const [done, setDone] = useState(props.done);
```

Here we are initializing a state for each item to the `done` variable and we are using the `props.done` from `TodoItemProps` which is a type defined for each todo item.

`setDone` provides us a setter method to update the state when we want to.

## Making use of Done state variable

Now let's make use of the state variable created to keep track of each checkbox being marked/unmarked.

First we will need to hook a call to the `updateTodoItem` method when checkbox is being clicked/changed.

```js
onChange={(event) => updateTodoItem(event.currentTarget.checked)}
```

Add `onChange` code above to the checkbox input that you have created. This will call the `updateTodoItem` method to update the `done` state every time the checkbox is clicked.
Now we will need to link the `done` state variable to the checkbox so that the state is reflected correctly as it is currently not being used.

```js
checked = { done };
```

Add the above to the checkbox input you created. This would ensure that the checkbox input is reflecting using the `done` state variable.

You should be seeing a similar block of code as below

```js
<td>
  <FormCheck /* Insert code here */ />
</td>
```

## Persist the checkbox state

Now try refreshing the page and you will realise that although you can check the box but upon refresh the state is not persistent. So the next thing we have to do is to persist the state through back end means.

### When to update Backend

First we will need to track changes made for the checkbox for each item so that we know when to send the update to the back end.

Now to update `updateTodoItem` to use the done state variable and send the changes to our backend server

```js
const updateTodoItem = (done) => {
  setDone(done);
  axios.put(`${CONFIG.API_ENDPOINT}/${props.id}`, {
    /* Insert code here */
  });
};
```

Congratulations, now your state is persisted and refreshing will no longer pose a problem.

## Solution

**1. Include the following `updateTodoItem` method in `src/screens/Todo.js` after `const [done, setDone] = useState(props.done);`**

```js
const updateTodoItem = (done) => {
  setDone(done);
  axios.put(`${CONFIG.API_ENDPOINT}/${props.id}`, {
    id: props.id,
    description: props.description,
    done: done,
  });
};
```

**2. Update the table to include the FormCheck component for todo update**

```js
<tr>
  <td>
    <FormCheck
      onChange={(event) => updateTodoItem(event.currentTarget.checked)}
      checked={done}
    />
  </td>
  <td width={"100%"}>{props.description}</td>
</tr>
```

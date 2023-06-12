# 2.5 Stretch Goals

> 💡 If you haven't completed Exercise 2.3, get up to speed by switching to the `checkpoint-4` branch!

```
git checkout checkpoint-4
```
Finished already? That was fast.

If you're looking for a challenge, try implementing a bonus feature:

Currently, the list is assumed to be the todo list of the day. Could you implement a datepicker component in the banner that allows different lists to be displayed according to the chosen date?

**Hint: SGDS component library**

```js
    <Container>
      <div className="has-background-gradient">
        <h2>Today</h2>
        {today.toLocaleDateString("en-UK", dateOptions) /* replace this display with a datepicker */}
      </div>
```

Tip: You may use the `bonus` branch in the backend repository to help with the task. For an added challenge, make the necessary changes to the backend yourself!

## Solution
**1. Replace the code `import { Container, Button, Form, FormCheck } from "@govtechsg/sgds-react";' with**

```js
import { Container, Button, Form, FormCheck, DatePicker } from "@govtechsg/sgds-react";
```

**2. Replace the code `{today.toLocaleDateString("en-UK", dateOptions)}` in `src/screens/Todo.js` with**


```js
<DatePicker initialValue={today} />
```
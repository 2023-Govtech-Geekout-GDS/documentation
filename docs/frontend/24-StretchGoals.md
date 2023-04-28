# 2.4 Stretch Goals

> 💡 If you haven't completed Exercise 1.3, get up to speed by switching to the `frontend/checkpoint-3` branch!

> `git checkout frontend/checkpoint-3`

Finished already? That was fast.

If you're looking for a challenge, try implementing a bonus feature:

Currently, the list is assumed to be the to-do list of the day. Could you implement a datepicker component in the banner that allows different lists to be displayed according to the chosen date?

```tsx

    <Container>
      <div className="has-background-gradient">
        <h2>Today</h2>
        {today.toLocaleDateString("en-UK", dateOptions) /* replace this display with a datepicker */}
      </div>
```

Tip: You may use the `bonus` branch in the backend repository to help with the task. For an added challenge, make the necessary changes to the backend yourself!

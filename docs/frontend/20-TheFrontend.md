# 2.0 The Frontend

This topic covers a couple of basic things about frontend development. Along the way, we'll cover concepts like components, state, hooks, and events.

In the first exercise, we'll learn the different components that makes up the todo application.

To spin up the local development environment, navigate to the main folder and the following:

Begin the exercise with the branch `checkpoint-0`:

- Run `git checkout checkpoint-0` on your terminal, and let's begin!
- Run `npm install` to install the application dependencies
- Run `npm start` to look at the todo app interface

Spend some time to experiment with the `todo.js` application code. Remove components to see how it affects the user interface.

- Navigate to `src/App.js`
- Observe the Code Structure. Below shows the base code used to prepare page navigation between pages

```js
<BrowserRouter>
  <NavigationBar />
  <Routes>
    <Route path="/todo" />
    <Route path="/" />
  </Routes>
</BrowserRouter>
```

- Navigate to `src/screens/todo.js`
- Observe the Code Structure. Below shows the base code used to setup the todo table component.

```js
<Container>
  <Form>
    <Table>
      <thead>
        <tr>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td></td>
        </tr>
      </tbody>
    </Table>
  </Form>
  <Button></Button>
</Container>
```

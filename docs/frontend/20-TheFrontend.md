# 2.0 The Frontend

This topic covers a couple of basic things about frontend development. Along the way, we'll learn about concepts like components, state, and hooks.

In the first exercise, we'll learn the different components that makes up the todo application.

To spin up the local development environment, navigate to the main folder and the following:

Begin the exercise with the branch `checkpoint-0`:

- Run `git checkout checkpoint-0` on your terminal
- Run `npm install` to install the application dependencies
- Run `npm start` to look at the todo app interface

Now, let's spend some time exploring the application code!

- Navigate to `src/App.js`
- Observe the Code Structure. Below shows the base code used to prepare page navigation between pages

```js
<BrowserRouter>
  <NavigationBar />
  <Routes>
    <Route path="/todo" />
    <Route path="/" 
    element={...}
    />
  </Routes>
</BrowserRouter>
```

- Navigate to `src/screens/Todo.js`
- Observe the Code Structure for what is being returned. Below shows the base code used to setup the Todo table component.

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

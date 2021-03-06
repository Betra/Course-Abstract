| [< Previous abstract](6%20Avoiding%20Array%20Mutations.md) | [Back To React Folder](https://github.com/Betra/Course-Abstract/blob/master/Egghead/Dan%20Abramov%20-%20Redux/) | [Next abstract >](8%20Todo%20Reducer.md) |
| ---------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- | ---------------------------------------- |


## Avoiding Object Mutations

With `.assign()`

```js
const testToggleTodos = () => {
  const todoBefore = {
    id: 0,
    text: "Learn Redux",
    isCompleted: false
  };
  const todoAfter = {
    id: 0,
    text: "Learn Redux",
    isCompleted: true
  };
};

//This func
const toggleTodos = todo => {
  todo.isCompleted = !todo.isCompleted;
  return todo;
};
```

And this is bad, cuz it tries to mutate our Object;

Simpliest solution would be:

```js
const toggleTodos = todo => {
  return {
    id: todo.id,
    text: todo.text,
    isCompleted: !todo.isCompleted
  };
};
```

Yet we can forget to update that from time to time. This is why there are a nice ES6 method called `Object.assign()`

```js
const toggleTodos = todo => {
  return Object.assign({}, todo, {
    isCompleted: !todo.isCompleted
  });
};
```

Perfect!

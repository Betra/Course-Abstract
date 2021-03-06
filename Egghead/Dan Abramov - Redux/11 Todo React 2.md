| [< Previous abstract](10%20Todo%20React%201.md) | [Back To React Folder](https://github.com/Betra/Course-Abstract/blob/master/Egghead/Dan%20Abramov%20-%20Redux/) | [Next abstract >](10%20Todo%20React%203.md) |
| ----------------------------------------------- | --------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |


# React List Example: Toggling A Todo

We've already built `isCompleted` switch, now let's handle the rest:

In `TodoApp`, where we render our todos, let's modify:

```js
<ul>
  {/* Just display todos */}
  {this.props.todos.map(todo => (
    <li
      key={todo.id}
      // Adding onClick dispatching
      onClick={() => {
        store.dispatch({
          type: "TOGGLE_TODO",
          id: todo.id
        });
      }}
    >
    {todo.text}
    </li>
  ))}
</ul>
</div>
```

And overline completed todos:

```js
<li ...
  style={{
    textDecoration: todo.isCompleted ? 'line-through' : 'none'
  }}
  >
  {todo.text}
  </li>
```

Man, that's all

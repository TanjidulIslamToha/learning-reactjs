# Common React Mistakes

## 📋 Table of Contents
- [Forgetting Keys in Lists](#forgetting-keys-in-lists)
- [Using class Instead of className](#using-class-instead-of-classname)
- [Not Handling Loading States](#not-handling-loading-states)
- [Missing Error Boundaries](#missing-error-boundaries)
- [Incorrect Hook Usage](#incorrect-hook-usage)

---

## 🔑 Forgetting Keys in Lists

### ❌ Bad: Missing Keys

```jsx
// ❌ Bad - No keys
function UserList({ users }) {
  return (
    <ul>
      {users.map(user => (
        <li>{user.name}</li> // React warning!
      ))}
    </ul>
  );
}
```

### ✅ Good: Always Provide Keys

```jsx
// ✅ Good - Unique keys
function UserList({ users }) {
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

---

## 🎨 Using class Instead of className

### ❌ Bad: HTML class Attribute

```jsx
// ❌ Bad - class is reserved in JavaScript
function Component() {
  return <div class="container">Content</div>; // Error!
}
```

### ✅ Good: Use className

```jsx
// ✅ Good - className for CSS classes
function Component() {
  return <div className="container">Content</div>;
}
```

---

## ⏳ Not Handling Loading States

### ❌ Bad: No Loading State

```jsx
// ❌ Bad - No loading indicator
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    fetchUser(userId).then(setUser);
  }, [userId]);
  
  return <div>{user.name}</div>; // Error if user is null!
}
```

### ✅ Good: Handle Loading

```jsx
// ✅ Good - Loading state
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    setLoading(true);
    fetchUser(userId)
      .then(setUser)
      .finally(() => setLoading(false));
  }, [userId]);
  
  if (loading) return <div>Loading...</div>;
  if (!user) return <div>User not found</div>;
  
  return <div>{user.name}</div>;
}
```

---

## 🛡️ Missing Error Boundaries

### ❌ Bad: No Error Handling

```jsx
// ❌ Bad - App crashes on error
function App() {
  return (
    <div>
      <ComponentThatMightError />
    </div>
  );
}
```

### ✅ Good: Error Boundary

```jsx
// ✅ Good - Error boundary
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }
  
  static getDerivedStateFromError(error) {
    return { hasError: true };
  }
  
  componentDidCatch(error, errorInfo) {
    console.error('Error:', error, errorInfo);
  }
  
  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}

function App() {
  return (
    <ErrorBoundary>
      <ComponentThatMightError />
    </ErrorBoundary>
  );
}
```

---

## 🎣 Incorrect Hook Usage

### ❌ Bad: Hooks in Conditions

```jsx
// ❌ Bad - Hooks must be at top level
function Component({ condition }) {
  if (condition) {
    const [state, setState] = useState(0); // Error!
  }
  
  return <div>Content</div>;
}
```

### ✅ Good: Hooks at Top Level

```jsx
// ✅ Good - Hooks always at top level
function Component({ condition }) {
  const [state, setState] = useState(0);
  
  if (condition) {
    // Use state here
  }
  
  return <div>Content</div>;
}
```

### ❌ Bad: Hooks in Loops

```jsx
// ❌ Bad - Hooks in loop
function Component({ items }) {
  return (
    <div>
      {items.map(item => {
        const [state, setState] = useState(0); // Error!
        return <div key={item.id}>{item.name}</div>;
      })}
    </div>
  );
}
```

### ✅ Good: Extract to Component

```jsx
// ✅ Good - Component with hooks
function Item({ item }) {
  const [state, setState] = useState(0);
  return <div>{item.name}</div>;
}

function Component({ items }) {
  return (
    <div>
      {items.map(item => (
        <Item key={item.id} item={item} />
      ))}
    </div>
  );
}
```

---

## 🔄 Not Using Functional Updates

### ❌ Bad: Stale State in Updates

```jsx
// ❌ Bad - Stale state
function Component() {
  const [count, setCount] = useState(0);
  
  const increment = () => {
    setCount(count + 1);
    setCount(count + 1); // Uses stale count, only increments once
  };
  
  return <button onClick={increment}>{count}</button>;
}
```

### ✅ Good: Functional Updates

```jsx
// ✅ Good - Functional update
function Component() {
  const [count, setCount] = useState(0);
  
  const increment = () => {
    setCount(prev => prev + 1);
    setCount(prev => prev + 1); // Uses latest state, increments twice
  };
  
  return <button onClick={increment}>{count}</button>;
}
```

---

## 📤 Not Cleaning Up Effects

### ❌ Bad: No Cleanup

```jsx
// ❌ Bad - Memory leak
function Component() {
  useEffect(() => {
    const subscription = subscribe();
    // No cleanup - subscription never unsubscribes
  }, []);
  
  return <div>Component</div>;
}
```

### ✅ Good: Cleanup Function

```jsx
// ✅ Good - Proper cleanup
function Component() {
  useEffect(() => {
    const subscription = subscribe();
    
    return () => {
      subscription.unsubscribe(); // Cleanup
    };
  }, []);
  
  return <div>Component</div>;
}
```

---

## 🎯 Missing Dependencies in useEffect

### ❌ Bad: Missing Dependencies

```jsx
// ❌ Bad - Missing userId in dependencies
function Component({ userId }) {
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    fetchUser(userId).then(setUser);
  }, []); // Should include userId
  
  return <div>{user?.name}</div>;
}
```

### ✅ Good: Include All Dependencies

```jsx
// ✅ Good - All dependencies included
function Component({ userId }) {
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    fetchUser(userId).then(setUser);
  }, [userId]); // Includes userId
  
  return <div>{user?.name}</div>;
}
```

---

## 🎨 Not Handling Empty States

### ❌ Bad: No Empty State

```jsx
// ❌ Bad - Shows nothing when empty
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>{todo.text}</li>
      ))}
    </ul>
  );
  // Empty array shows nothing
}
```

### ✅ Good: Empty State Handling

```jsx
// ✅ Good - Shows message when empty
function TodoList({ todos }) {
  if (todos.length === 0) {
    return <p>No todos yet. Add one to get started!</p>;
  }
  
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>{todo.text}</li>
      ))}
    </ul>
  );
}
```

---

## 📚 Key Takeaways

**Common Mistakes:**
1. ❌ Forgetting keys in lists
2. ❌ Using `class` instead of `className`
3. ❌ Not handling loading states
4. ❌ Missing error boundaries
5. ❌ Incorrect hook usage (conditions, loops)
6. ❌ Not using functional updates
7. ❌ Not cleaning up effects
8. ❌ Missing dependencies in useEffect
9. ❌ Not handling empty states

**Solutions:**
1. ✅ Always provide keys
2. ✅ Use `className` for CSS
3. ✅ Show loading indicators
4. ✅ Use error boundaries
5. ✅ Hooks at top level only
6. ✅ Use functional updates
7. ✅ Always cleanup effects
8. ✅ Include all dependencies
9. ✅ Handle empty states

---

**Remember:** Learning from common mistakes helps you write more robust React code!


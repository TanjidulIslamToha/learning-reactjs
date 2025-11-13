# React Anti-Patterns

## 📋 Table of Contents
- [State Mutations](#state-mutations)
- [Creating Components in Render](#creating-components-in-render)
- [Using Index as Key](#using-index-as-key)
- [Forgetting Cleanup](#forgetting-cleanup)
- [Prop Drilling](#prop-drilling)
- [Over-optimization](#over-optimization)

---

## 🚫 State Mutations

### ❌ Bad: Mutating State Directly

```jsx
// ❌ Bad - Mutating state
function Component() {
  const [user, setUser] = useState({ name: 'Alice', age: 25 });
  
  const updateAge = () => {
    user.age = 26; // Don't mutate!
    setUser(user); // React won't detect change
  };
  
  return <button onClick={updateAge}>Update Age</button>;
}
```

### ✅ Good: Immutable Updates

```jsx
// ✅ Good - Create new object
function Component() {
  const [user, setUser] = useState({ name: 'Alice', age: 25 });
  
  const updateAge = () => {
    setUser({ ...user, age: 26 }); // New object
  };
  
  return <button onClick={updateAge}>Update Age</button>;
}
```

---

## 🏗️ Creating Components in Render

### ❌ Bad: Component Inside Render

```jsx
// ❌ Bad - New component on every render
function App() {
  const [count, setCount] = useState(0);
  
  function Child() { // Created on every render!
    return <div>Child</div>;
  }
  
  return (
    <div>
      <button onClick={() => setCount(c => c + 1)}>{count}</button>
      <Child />
    </div>
  );
}
```

### ✅ Good: Component Outside

```jsx
// ✅ Good - Component defined outside
function Child() {
  return <div>Child</div>;
}

function App() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <button onClick={() => setCount(c => c + 1)}>{count}</button>
      <Child />
    </div>
  );
}
```

---

## 🔑 Using Index as Key

### ❌ Bad: Index as Key (When Items Can Reorder)

```jsx
// ❌ Bad - Index as key when items can change
function TodoList({ todos }) {
  const [items, setItems] = useState(todos);
  
  return (
    <ul>
      {items.map((todo, index) => (
        <li key={index}>
          {todo.text}
          <button onClick={() => removeTodo(index)}>Delete</button>
        </li>
      ))}
    </ul>
  );
}
// Problem: If you delete item at index 1, all items after shift
// React thinks they're the same components
```

### ✅ Good: Stable Unique Keys

```jsx
// ✅ Good - Stable unique ID
function TodoList({ todos }) {
  const [items, setItems] = useState(todos);
  
  return (
    <ul>
      {items.map(todo => (
        <li key={todo.id}>
          {todo.text}
          <button onClick={() => removeTodo(todo.id)}>Delete</button>
        </li>
      ))}
    </ul>
  );
}
```

---

## 🧹 Forgetting Cleanup

### ❌ Bad: No Cleanup in useEffect

```jsx
// ❌ Bad - Memory leak!
function Component() {
  useEffect(() => {
    const interval = setInterval(() => {
      console.log('Tick');
    }, 1000);
    // No cleanup - interval runs forever!
  }, []);
  
  return <div>Component</div>;
}
```

### ✅ Good: Proper Cleanup

```jsx
// ✅ Good - Cleanup function
function Component() {
  useEffect(() => {
    const interval = setInterval(() => {
      console.log('Tick');
    }, 1000);
    
    return () => {
      clearInterval(interval); // Cleanup!
    };
  }, []);
  
  return <div>Component</div>;
}
```

---

## 📤 Prop Drilling

### ❌ Bad: Passing Props Through Many Levels

```jsx
// ❌ Bad - Prop drilling
function App() {
  const user = { name: 'Alice' };
  return <Header user={user} />;
}

function Header({ user }) {
  return <Navigation user={user} />; // Doesn't need user
}

function Navigation({ user }) {
  return <UserMenu user={user} />; // Doesn't need user
}

function UserMenu({ user }) {
  return <div>{user.name}</div>; // Actually needs user
}
```

### ✅ Good: Use Context

```jsx
// ✅ Good - Context API
const UserContext = createContext();

function App() {
  const user = { name: 'Alice' };
  return (
    <UserContext.Provider value={user}>
      <Header />
    </UserContext.Provider>
  );
}

function Header() {
  return <Navigation />;
}

function Navigation() {
  return <UserMenu />;
}

function UserMenu() {
  const user = useContext(UserContext);
  return <div>{user.name}</div>;
}
```

---

## ⚡ Over-Optimization

### ❌ Bad: Memoizing Everything

```jsx
// ❌ Bad - Unnecessary memoization
const SimpleButton = React.memo(function SimpleButton({ onClick, children }) {
  return <button onClick={onClick}>{children}</button>;
});

const SimpleText = React.memo(function SimpleText({ text }) {
  return <p>{text}</p>;
});

// Memoization has overhead - only use when needed
```

### ✅ Good: Optimize When Needed

```jsx
// ✅ Good - Only memoize expensive components
const ExpensiveChart = React.memo(function ExpensiveChart({ data }) {
  // Complex rendering logic
  return <div>{/* expensive rendering */}</div>;
});

// Simple components don't need memoization
function SimpleButton({ onClick, children }) {
  return <button onClick={onClick}>{children}</button>;
}
```

---

## 🔄 Infinite Loops

### ❌ Bad: useEffect Without Dependencies

```jsx
// ❌ Bad - Infinite loop!
function Component() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    setCount(count + 1); // Updates state
    // No dependency array = runs every render
    // State update triggers re-render
    // Re-render triggers effect again
    // Infinite loop!
  });
  
  return <div>{count}</div>;
}
```

### ✅ Good: Proper Dependencies

```jsx
// ✅ Good - Empty deps = runs once
function Component() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    setCount(1); // Only runs once on mount
  }, []); // Empty dependency array
  
  return <div>{count}</div>;
}
```

---

## 🎯 Stale Closures

### ❌ Bad: Stale State in Callbacks

```jsx
// ❌ Bad - Stale closure
function Component() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    const interval = setInterval(() => {
      setCount(count + 1); // Uses stale count value
    }, 1000);
    
    return () => clearInterval(interval);
  }, []); // Empty deps, count is stale
  
  return <div>{count}</div>;
}
```

### ✅ Good: Functional Updates

```jsx
// ✅ Good - Functional update
function Component() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    const interval = setInterval(() => {
      setCount(prev => prev + 1); // Always uses latest state
    }, 1000);
    
    return () => clearInterval(interval);
  }, []); // No need for count in deps
  
  return <div>{count}</div>;
}
```

---

## 📝 Storing Derived Values in State

### ❌ Bad: Storing Computed Values

```jsx
// ❌ Bad - Storing derived value
function Component({ firstName, lastName }) {
  const [fullName, setFullName] = useState('');
  
  useEffect(() => {
    setFullName(`${firstName} ${lastName}`);
  }, [firstName, lastName]);
  
  return <div>{fullName}</div>;
}
```

### ✅ Good: Compute Directly

```jsx
// ✅ Good - Compute directly
function Component({ firstName, lastName }) {
  const fullName = `${firstName} ${lastName}`;
  return <div>{fullName}</div>;
}

// Or useMemo if computation is expensive
function Component({ firstName, lastName }) {
  const fullName = useMemo(
    () => `${firstName} ${lastName}`,
    [firstName, lastName]
  );
  return <div>{fullName}</div>;
}
```

---

## 🎨 Inline Object Creation

### ❌ Bad: Creating Objects in Render

```jsx
// ❌ Bad - New object every render
function Component({ user }) {
  return (
    <Child
      config={{ theme: 'dark', size: 'large' }} // New object each render
      user={user}
    />
  );
}

// Child re-renders every time even if props didn't change
```

### ✅ Good: Stable References

```jsx
// ✅ Good - Stable reference
const CONFIG = { theme: 'dark', size: 'large' };

function Component({ user }) {
  return <Child config={CONFIG} user={user} />;
}

// Or useMemo
function Component({ user }) {
  const config = useMemo(() => ({
    theme: 'dark',
    size: 'large'
  }), []);
  
  return <Child config={config} user={user} />;
}
```

---

## 📚 Key Takeaways

**Avoid:**
1. ❌ Mutating state directly
2. ❌ Creating components in render
3. ❌ Using index as key when items can reorder
4. ❌ Forgetting cleanup in useEffect
5. ❌ Prop drilling (use Context)
6. ❌ Over-optimizing prematurely
7. ❌ Infinite loops in useEffect
8. ❌ Stale closures
9. ❌ Storing derived values in state
10. ❌ Creating objects in render

**Do:**
1. ✅ Update state immutably
2. ✅ Define components outside render
3. ✅ Use stable unique keys
4. ✅ Always cleanup effects
5. ✅ Use Context for shared state
6. ✅ Measure before optimizing
7. ✅ Use proper dependencies
8. ✅ Use functional updates
9. ✅ Compute derived values directly
10. ✅ Use stable references

---

**Remember:** Understanding anti-patterns helps you write better React code!


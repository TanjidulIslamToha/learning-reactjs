# State Management Best Practices

## 📋 Table of Contents
- [State Location](#state-location)
- [State Structure](#state-structure)
- [State Updates](#state-updates)
- [When to Use State](#when-to-use-state)
- [State Lifting](#state-lifting)
- [Context vs Props](#context-vs-props)

---

## 📍 State Location

### ✅ Good: State Close to Where It's Used

```jsx
// ✅ Good - Local state for component-specific data
function Counter() {
  const [count, setCount] = useState(0);
  // Only this component needs count
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

### ✅ Good: Lift State When Shared

```jsx
// ✅ Good - Lifted state for shared data
function App() {
  const [user, setUser] = useState(null);
  // Both Header and Profile need user
  return (
    <>
      <Header user={user} />
      <Profile user={user} />
    </>
  );
}
```

### ❌ Bad: State in Wrong Place

```jsx
// ❌ Bad - State too high up when only one child needs it
function App() {
  const [count, setCount] = useState(0);
  return (
    <>
      <Header />
      <Counter count={count} setCount={setCount} />
      <Footer />
    </>
  );
}

// ✅ Better - State where it's needed
function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

---

## 🏗️ State Structure

### ✅ Good: Flat, Normalized State

```jsx
// ✅ Good - Flat structure
const [user, setUser] = useState({
  id: '1',
  name: 'Alice',
  email: 'alice@example.com',
  avatar: 'avatar.jpg'
});

// ✅ Good - Separate related state
const [users, setUsers] = useState([]);
const [selectedUserId, setSelectedUserId] = useState(null);
const selectedUser = users.find(u => u.id === selectedUserId);
```

### ❌ Bad: Nested, Denormalized State

```jsx
// ❌ Bad - Too nested, hard to update
const [state, setState] = useState({
  users: {
    list: [],
    selected: {
      user: {
        profile: {
          name: 'Alice',
          settings: {
            theme: 'dark'
          }
        }
      }
    }
  }
});

// Updating requires deep nesting
setState({
  ...state,
  users: {
    ...state.users,
    selected: {
      ...state.users.selected,
      user: {
        ...state.users.selected.user,
        profile: {
          ...state.users.selected.user.profile,
          settings: {
            ...state.users.selected.user.profile.settings,
            theme: 'light'
          }
        }
      }
    }
  }
});
```

### ✅ Better: Normalized Structure

```jsx
// ✅ Better - Flat, normalized
const [users, setUsers] = useState([]);
const [selectedUserId, setSelectedUserId] = useState(null);
const [userSettings, setUserSettings] = useState({});

// Easy to update
setUserSettings({
  ...userSettings,
  [selectedUserId]: { theme: 'light' }
});
```

---

## 🔄 State Updates

### ✅ Good: Immutable Updates

```jsx
// ✅ Good - Create new objects/arrays
const [user, setUser] = useState({ name: 'Alice', age: 25 });

// Update object
setUser({ ...user, age: 26 });

// ✅ Good - Update arrays immutably
const [items, setItems] = useState([1, 2, 3]);

// Add item
setItems([...items, 4]);

// Remove item
setItems(items.filter(item => item !== 2));

// Update item
setItems(items.map(item => item === 2 ? 20 : item));
```

### ❌ Bad: Mutating State

```jsx
// ❌ Bad - Mutating state directly
const [user, setUser] = useState({ name: 'Alice', age: 25 });
user.age = 26; // Don't do this!
setUser(user); // React won't detect the change

// ❌ Bad - Mutating arrays
const [items, setItems] = useState([1, 2, 3]);
items.push(4); // Don't do this!
setItems(items); // React won't detect the change
```

### ✅ Good: Functional Updates

```jsx
// ✅ Good - Functional update when state depends on previous
const [count, setCount] = useState(0);

// Multiple updates
setCount(prev => prev + 1);
setCount(prev => prev + 1); // Works correctly

// ✅ Good - Complex updates
const [todos, setTodos] = useState([]);

setTodos(prevTodos => {
  const newTodos = prevTodos.map(todo =>
    todo.id === id ? { ...todo, completed: !todo.completed } : todo
  );
  return newTodos;
});
```

---

## 🎯 When to Use State

### ✅ Good: Use State For

```jsx
// ✅ Good - User input
const [input, setInput] = useState('');

// ✅ Good - UI state (modals, dropdowns)
const [isOpen, setIsOpen] = useState(false);

// ✅ Good - Data that changes
const [count, setCount] = useState(0);

// ✅ Good - Loading/error states
const [loading, setLoading] = useState(false);
const [error, setError] = useState(null);
```

### ❌ Bad: Don't Use State For

```jsx
// ❌ Bad - Derived values (use useMemo instead)
const [fullName, setFullName] = useState('');
useEffect(() => {
  setFullName(`${firstName} ${lastName}`);
}, [firstName, lastName]);

// ✅ Better - Derived value
const fullName = useMemo(() => `${firstName} ${lastName}`, [firstName, lastName]);

// ❌ Bad - Props (just use props)
const [user, setUser] = useState(props.user);

// ✅ Better - Use props directly
function Component({ user }) {
  return <div>{user.name}</div>;
}

// ❌ Bad - Constants (use const instead)
const [API_URL, setAPI_URL] = useState('https://api.example.com');

// ✅ Better - Constant
const API_URL = 'https://api.example.com';
```

---

## 📤 State Lifting

### ✅ Good: Lift State Up

```jsx
// ✅ Good - State lifted to common parent
function App() {
  const [filter, setFilter] = useState('all');
  
  return (
    <>
      <FilterControls filter={filter} onFilterChange={setFilter} />
      <ProductList filter={filter} />
    </>
  );
}

function FilterControls({ filter, onFilterChange }) {
  return (
    <div>
      <button onClick={() => onFilterChange('all')}>All</button>
      <button onClick={() => onFilterChange('active')}>Active</button>
    </div>
  );
}

function ProductList({ filter }) {
  const filteredProducts = products.filter(/* ... */);
  return <div>{/* render products */}</div>;
}
```

### ❌ Bad: Duplicated State

```jsx
// ❌ Bad - Same state in multiple places
function FilterControls() {
  const [filter, setFilter] = useState('all');
  // ...
}

function ProductList() {
  const [filter, setFilter] = useState('all'); // Duplicated!
  // ...
}
```

---

## 🎣 useState vs useReducer

### ✅ Good: useState for Simple State

```jsx
// ✅ Good - Simple state with useState
function Counter() {
  const [count, setCount] = useState(0);
  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);
  return (
    <div>
      <button onClick={decrement}>-</button>
      <span>{count}</span>
      <button onClick={increment}>+</button>
    </div>
  );
}
```

### ✅ Good: useReducer for Complex State

```jsx
// ✅ Good - Complex state with useReducer
const todoReducer = (state, action) => {
  switch (action.type) {
    case 'ADD':
      return [...state, action.payload];
    case 'TOGGLE':
      return state.map(todo =>
        todo.id === action.payload
          ? { ...todo, completed: !todo.completed }
          : todo
      );
    case 'DELETE':
      return state.filter(todo => todo.id !== action.payload);
    default:
      return state;
  }
};

function TodoList() {
  const [todos, dispatch] = useReducer(todoReducer, []);
  
  return (
    <div>
      <button onClick={() => dispatch({ type: 'ADD', payload: newTodo })}>
        Add
      </button>
      {/* ... */}
    </div>
  );
}
```

---

## 🎯 Context vs Props vs State

### When to Use Each

```jsx
// ✅ useState - Component-specific state
function Counter() {
  const [count, setCount] = useState(0);
  // Only this component needs it
}

// ✅ Props - Parent to child, 1-2 levels
function App() {
  const user = { name: 'Alice' };
  return <Profile user={user} />; // Direct child
}

// ✅ Context - Shared across many components
function App() {
  return (
    <ThemeProvider>
      <Header /> {/* Needs theme */}
      <Main />   {/* Needs theme */}
      <Footer /> {/* Needs theme */}
    </ThemeProvider>
  );
}
```

---

## 🎨 State Patterns

### ✅ Good: Controlled Components

```jsx
// ✅ Good - Controlled input
function Input() {
  const [value, setValue] = useState('');
  return (
    <input
      value={value}
      onChange={(e) => setValue(e.target.value)}
    />
  );
}
```

### ✅ Good: Multiple State Variables

```jsx
// ✅ Good - Separate concerns
function Form() {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [errors, setErrors] = useState({});
  
  // Each state is independent
}
```

### ✅ Good: Combined State When Related

```jsx
// ✅ Good - Related state together
function Form() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    password: ''
  });
  
  const updateField = (field, value) => {
    setFormData(prev => ({ ...prev, [field]: value }));
  };
}
```

---

## 📚 Key Takeaways

1. **Keep state close** to where it's used
2. **Lift state up** when multiple components need it
3. **Use flat structure** - avoid deep nesting
4. **Update immutably** - create new objects/arrays
5. **Use functional updates** when state depends on previous
6. **Don't store derived values** in state
7. **useState for simple**, useReducer for complex
8. **Context for global**, props for local

---

## ✅ State Management Checklist

Before adding state, ask:

- [ ] Does this data change over time?
- [ ] Is this data only needed in this component?
- [ ] Can this be derived from props or other state?
- [ ] Is the state structure flat and normalized?
- [ ] Am I updating state immutably?
- [ ] Is state in the right place (local vs lifted vs context)?

---

**Remember:** Good state management makes your app predictable and maintainable!


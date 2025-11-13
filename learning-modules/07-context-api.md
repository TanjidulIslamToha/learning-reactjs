# Module 07 · Context API

## 🎯 Learning Objectives
By the end of this module, you will:
- Understand what Context API is and when to use it
- Create and provide context
- Consume context with useContext hook
- Avoid prop drilling
- Combine multiple contexts
- Know when NOT to use Context

---

## 📚 What is Context API?

**Context API** lets you share data across components without passing props through every level (avoiding "prop drilling").

### The Problem: Prop Drilling

```jsx
// ❌ Prop drilling - passing data through many levels
function App() {
  const user = { name: "Alice", role: "admin" };
  return <Header user={user} />;
}

function Header({ user }) {
  return <Navigation user={user} />;
}

function Navigation({ user }) {
  return <UserMenu user={user} />;
}

function UserMenu({ user }) {
  return <div>Welcome, {user.name}</div>;
}
```

**Problem:** `user` is passed through components that don't need it!

### The Solution: Context

```jsx
// ✅ Using Context - direct access
const UserContext = createContext();

function App() {
  const user = { name: "Alice", role: "admin" };
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
  return <div>Welcome, {user.name}</div>;
}
```

---

## 🏗️ Creating Context

### Step 1: Create Context

```jsx
import { createContext } from 'react';

const ThemeContext = createContext();
export default ThemeContext;
```

### Step 2: Provide Context

```jsx
import ThemeContext from './ThemeContext';

function App() {
  const theme = 'dark';
  
  return (
    <ThemeContext.Provider value={theme}>
      <Header />
      <Main />
      <Footer />
    </ThemeContext.Provider>
  );
}
```

### Step 3: Consume Context

```jsx
import { useContext } from 'react';
import ThemeContext from './ThemeContext';

function Header() {
  const theme = useContext(ThemeContext);
  
  return (
    <header className={`header-${theme}`}>
      <h1>My App</h1>
    </header>
  );
}
```

---

## 🎨 Complete Example: Theme Context

### Creating the Context

```jsx
// src/context/ThemeContext.js
import { createContext, useState, useContext } from 'react';

const ThemeContext = createContext();

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  
  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  };
  
  const value = {
    theme,
    toggleTheme
  };
  
  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within ThemeProvider');
  }
  return context;
}
```

### Using the Context

```jsx
// src/App.js
import { ThemeProvider } from './context/ThemeContext';
import Header from './components/Header';
import Main from './components/Main';

function App() {
  return (
    <ThemeProvider>
      <Header />
      <Main />
    </ThemeProvider>
  );
}
```

```jsx
// src/components/Header.js
import { useTheme } from '../context/ThemeContext';

function Header() {
  const { theme, toggleTheme } = useTheme();
  
  return (
    <header className={`header-${theme}`}>
      <h1>My App</h1>
      <button onClick={toggleTheme}>
        Switch to {theme === 'light' ? 'dark' : 'light'} mode
      </button>
    </header>
  );
}
```

---

## 📦 User Context Example

### Creating User Context

```jsx
// src/context/UserContext.js
import { createContext, useState, useContext } from 'react';

const UserContext = createContext();

export function UserProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  const login = async (email, password) => {
    setLoading(true);
    try {
      // Simulate API call
      const response = await fetch('/api/login', {
        method: 'POST',
        body: JSON.stringify({ email, password })
      });
      const userData = await response.json();
      setUser(userData);
    } catch (error) {
      console.error('Login failed:', error);
    } finally {
      setLoading(false);
    }
  };
  
  const logout = () => {
    setUser(null);
  };
  
  const value = {
    user,
    loading,
    login,
    logout,
    isAuthenticated: !!user
  };
  
  return (
    <UserContext.Provider value={value}>
      {children}
    </UserContext.Provider>
  );
}

export function useUser() {
  const context = useContext(UserContext);
  if (!context) {
    throw new Error('useUser must be used within UserProvider');
  }
  return context;
}
```

### Using User Context

```jsx
// src/components/Navbar.js
import { useUser } from '../context/UserContext';

function Navbar() {
  const { user, logout, isAuthenticated } = useUser();
  
  return (
    <nav>
      {isAuthenticated ? (
        <>
          <span>Welcome, {user.name}</span>
          <button onClick={logout}>Logout</button>
        </>
      ) : (
        <a href="/login">Login</a>
      )}
    </nav>
  );
}
```

---

## 🎯 Multiple Contexts

You can use multiple contexts in one app:

```jsx
function App() {
  return (
    <ThemeProvider>
      <UserProvider>
        <LanguageProvider>
          <Header />
          <Main />
        </LanguageProvider>
      </UserProvider>
    </ThemeProvider>
  );
}
```

### Consuming Multiple Contexts

```jsx
function Component() {
  const { theme } = useTheme();
  const { user } = useUser();
  const { language } = useLanguage();
  
  return (
    <div className={`component-${theme}`}>
      <p>Hello, {user.name}!</p>
      <p>Language: {language}</p>
    </div>
  );
}
```

---

## 🔄 Context with State Management

### Task Context Example

```jsx
// src/context/TaskContext.js
import { createContext, useContext, useReducer } from 'react';

const TaskContext = createContext();

const taskReducer = (state, action) => {
  switch (action.type) {
    case 'ADD_TASK':
      return {
        ...state,
        tasks: [...state.tasks, action.payload]
      };
    case 'TOGGLE_TASK':
      return {
        ...state,
        tasks: state.tasks.map(task =>
          task.id === action.payload
            ? { ...task, completed: !task.completed }
            : task
        )
      };
    case 'DELETE_TASK':
      return {
        ...state,
        tasks: state.tasks.filter(task => task.id !== action.payload)
      };
    default:
      return state;
  }
};

export function TaskProvider({ children }) {
  const [state, dispatch] = useReducer(taskReducer, {
    tasks: []
  });
  
  const addTask = (task) => {
    dispatch({ type: 'ADD_TASK', payload: task });
  };
  
  const toggleTask = (id) => {
    dispatch({ type: 'TOGGLE_TASK', payload: id });
  };
  
  const deleteTask = (id) => {
    dispatch({ type: 'DELETE_TASK', payload: id });
  };
  
  const value = {
    tasks: state.tasks,
    addTask,
    toggleTask,
    deleteTask
  };
  
  return (
    <TaskContext.Provider value={value}>
      {children}
    </TaskContext.Provider>
  );
}

export function useTasks() {
  const context = useContext(TaskContext);
  if (!context) {
    throw new Error('useTasks must be used within TaskProvider');
  }
  return context;
}
```

---

## ⚠️ When NOT to Use Context

**Don't use Context for:**
1. **Frequently changing data** - Causes all consumers to re-render
2. **Local component state** - Use useState instead
3. **Props that are only used by direct children** - Just pass props
4. **Data that changes often** - Consider state management library

**Use Context for:**
- Theme (dark/light mode)
- User authentication
- Language/locale
- Global settings
- Data that rarely changes

---

## 📝 Hands-On Tutorial: Building a Notification System

### Step 1: Notification Context

```jsx
// src/context/NotificationContext.js
import { createContext, useContext, useState } from 'react';

const NotificationContext = createContext();

export function NotificationProvider({ children }) {
  const [notifications, setNotifications] = useState([]);
  
  const addNotification = (message, type = 'info') => {
    const id = Date.now();
    const notification = { id, message, type };
    
    setNotifications(prev => [...prev, notification]);
    
    // Auto-remove after 3 seconds
    setTimeout(() => {
      removeNotification(id);
    }, 3000);
  };
  
  const removeNotification = (id) => {
    setNotifications(prev => prev.filter(n => n.id !== id));
  };
  
  const value = {
    notifications,
    addNotification,
    removeNotification
  };
  
  return (
    <NotificationContext.Provider value={value}>
      {children}
    </NotificationContext.Provider>
  );
}

export function useNotifications() {
  const context = useContext(NotificationContext);
  if (!context) {
    throw new Error('useNotifications must be used within NotificationProvider');
  }
  return context;
}
```

### Step 2: Notification Component

```jsx
// src/components/NotificationContainer.js
import { useNotifications } from '../context/NotificationContext';

function NotificationContainer() {
  const { notifications, removeNotification } = useNotifications();
  
  return (
    <div className="notification-container">
      {notifications.map(notification => (
        <div
          key={notification.id}
          className={`notification notification-${notification.type}`}
          onClick={() => removeNotification(notification.id)}
        >
          {notification.message}
        </div>
      ))}
    </div>
  );
}
```

### Step 3: Using Notifications

```jsx
// src/components/ProductForm.js
import { useNotifications } from '../context/NotificationContext';

function ProductForm() {
  const { addNotification } = useNotifications();
  const [name, setName] = useState('');
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    
    try {
      await saveProduct({ name });
      addNotification('Product saved successfully!', 'success');
      setName('');
    } catch (error) {
      addNotification('Failed to save product', 'error');
    }
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Product name"
      />
      <button type="submit">Save</button>
    </form>
  );
}
```

---

## 🎯 Practice Exercises

### Exercise 1: Shopping Cart Context
Create a shopping cart context that:
- Stores cart items
- Provides add/remove/update functions
- Calculates total price
- Persists to localStorage

### Exercise 2: Auth Context
Build an authentication context with:
- User state (logged in/out)
- Login/logout functions
- Protected route component
- Auto-logout on token expiry

### Exercise 3: Settings Context
Create a settings context for:
- Theme (light/dark)
- Language
- Notifications enabled/disabled
- Persist settings to localStorage

---

## ❌ Common Mistakes

1. **Not providing default value:**
   ```jsx
   // ❌ Wrong - No default
   const context = useContext(MyContext);
   
   // ✅ Right - Check if context exists
   const context = useContext(MyContext);
   if (!context) {
     throw new Error('Must be used within Provider');
   }
   ```

2. **Creating context value object inline:**
   ```jsx
   // ❌ Wrong - New object every render
   <Context.Provider value={{ theme, setTheme }}>
   
   // ✅ Right - Memoize or use state
   const value = useMemo(() => ({ theme, setTheme }), [theme]);
   <Context.Provider value={value}>
   ```

---

## 🚀 Real-World Application

Context is used for:
- **Authentication**: User state across app
- **Theming**: Dark/light mode
- **Internationalization**: Language settings
- **Shopping Carts**: Cart state
- **Notifications**: Global notification system

---

## 📚 Key Takeaways

1. Context avoids prop drilling
2. Create context with createContext
3. Provide context with Provider
4. Consume with useContext hook
5. Create custom hooks for cleaner API
6. Don't use for frequently changing data
7. Combine multiple contexts when needed

---

## 🔗 Next Steps

- ✅ Complete all practice exercises
- ✅ Build the notification system
- ✅ Experiment with multiple contexts
- ✅ Read `learning-modules/08-custom-hooks.md` next

---

## 💡 Reflection Questions

1. Can I explain when to use Context?
2. Do I know how to create and provide context?
3. Can I consume context with useContext?
4. Do I understand when NOT to use Context?

If yes to all, you're ready for custom hooks! 🎉


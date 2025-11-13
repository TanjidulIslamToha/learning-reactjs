# Module 08 · Custom Hooks

## 🎯 Learning Objectives
By the end of this module, you will:
- Understand what custom hooks are
- Create reusable custom hooks
- Share logic between components
- Follow custom hook naming conventions
- Combine multiple hooks in custom hooks
- Test custom hooks

---

## 📚 What are Custom Hooks?

**Custom hooks** are JavaScript functions that:
- Start with `use` (e.g., `useCounter`, `useFetch`)
- Can call other hooks
- Allow you to extract component logic into reusable functions

### Why Custom Hooks?

**Problem:** Duplicated logic across components

```jsx
// Component 1
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(data => {
        setUser(data);
        setLoading(false);
      });
  }, [userId]);
  
  // ... rest of component
}

// Component 2 - Same logic duplicated!
function UserCard({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(data => {
        setUser(data);
        setLoading(false);
      });
  }, [userId]);
  
  // ... rest of component
}
```

**Solution:** Extract to custom hook

```jsx
// Custom hook
function useUser(userId) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(data => {
        setUser(data);
        setLoading(false);
      });
  }, [userId]);
  
  return { user, loading };
}

// Component 1
function UserProfile({ userId }) {
  const { user, loading } = useUser(userId);
  // ... rest of component
}

// Component 2
function UserCard({ userId }) {
  const { user, loading } = useUser(userId);
  // ... rest of component
}
```

---

## 🎣 Basic Custom Hook

### useCounter Hook

```jsx
// src/hooks/useCounter.js
import { useState } from 'react';

function useCounter(initialValue = 0, step = 1) {
  const [count, setCount] = useState(initialValue);
  
  const increment = () => setCount(prev => prev + step);
  const decrement = () => setCount(prev => prev - step);
  const reset = () => setCount(initialValue);
  
  return { count, increment, decrement, reset };
}

export default useCounter;
```

### Using the Hook

```jsx
import useCounter from './hooks/useCounter';

function Counter() {
  const { count, increment, decrement, reset } = useCounter(0, 1);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}
```

---

## 🌐 useFetch Hook

```jsx
// src/hooks/useFetch.js
import { useState, useEffect } from 'react';

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    let cancelled = false;
    
    async function fetchData() {
      try {
        setLoading(true);
        setError(null);
        
        const response = await fetch(url);
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const result = await response.json();
        
        if (!cancelled) {
          setData(result);
        }
      } catch (err) {
        if (!cancelled) {
          setError(err.message);
        }
      } finally {
        if (!cancelled) {
          setLoading(false);
        }
      }
    }
    
    fetchData();
    
    return () => {
      cancelled = true;
    };
  }, [url]);
  
  return { data, loading, error };
}

export default useFetch;
```

### Using useFetch

```jsx
import useFetch from './hooks/useFetch';

function UserProfile({ userId }) {
  const { data: user, loading, error } = useFetch(
    `https://api.example.com/users/${userId}`
  );
  
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return <div>User not found</div>;
  
  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  );
}
```

---

## 🎨 useLocalStorage Hook

```jsx
// src/hooks/useLocalStorage.js
import { useState, useEffect } from 'react';

function useLocalStorage(key, initialValue) {
  // Get from localStorage or use initial value
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(error);
      return initialValue;
    }
  });
  
  // Update localStorage when state changes
  useEffect(() => {
    try {
      window.localStorage.setItem(key, JSON.stringify(storedValue));
    } catch (error) {
      console.error(error);
    }
  }, [key, storedValue]);
  
  const setValue = (value) => {
    try {
      // Allow value to be a function (like useState)
      const valueToStore = value instanceof Function 
        ? value(storedValue) 
        : value;
      setStoredValue(valueToStore);
    } catch (error) {
      console.error(error);
    }
  };
  
  return [storedValue, setValue];
}

export default useLocalStorage;
```

### Using useLocalStorage

```jsx
import useLocalStorage from './hooks/useLocalStorage';

function Settings() {
  const [theme, setTheme] = useLocalStorage('theme', 'light');
  const [language, setLanguage] = useLocalStorage('language', 'en');
  
  return (
    <div>
      <select value={theme} onChange={(e) => setTheme(e.target.value)}>
        <option value="light">Light</option>
        <option value="dark">Dark</option>
      </select>
      
      <select value={language} onChange={(e) => setLanguage(e.target.value)}>
        <option value="en">English</option>
        <option value="es">Spanish</option>
      </select>
    </div>
  );
}
```

---

## ⏱️ useDebounce Hook

```jsx
// src/hooks/useDebounce.js
import { useState, useEffect } from 'react';

function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);
  
  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);
    
    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);
  
  return debouncedValue;
}

export default useDebounce;
```

### Using useDebounce

```jsx
import { useState } from 'react';
import useDebounce from './hooks/useDebounce';
import useFetch from './hooks/useFetch';

function SearchProducts() {
  const [searchTerm, setSearchTerm] = useState('');
  const debouncedSearchTerm = useDebounce(searchTerm, 500);
  
  const { data: products, loading } = useFetch(
    debouncedSearchTerm 
      ? `/api/products?search=${debouncedSearchTerm}`
      : null
  );
  
  return (
    <div>
      <input
        type="text"
        value={searchTerm}
        onChange={(e) => setSearchTerm(e.target.value)}
        placeholder="Search products..."
      />
      {loading && <p>Searching...</p>}
      {products && (
        <ul>
          {products.map(product => (
            <li key={product.id}>{product.name}</li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

---

## 🖱️ useClickOutside Hook

```jsx
// src/hooks/useClickOutside.js
import { useEffect, useRef } from 'react';

function useClickOutside(callback) {
  const ref = useRef();
  
  useEffect(() => {
    function handleClickOutside(event) {
      if (ref.current && !ref.current.contains(event.target)) {
        callback();
      }
    }
    
    document.addEventListener('mousedown', handleClickOutside);
    return () => {
      document.removeEventListener('mousedown', handleClickOutside);
    };
  }, [callback]);
  
  return ref;
}

export default useClickOutside;
```

### Using useClickOutside

```jsx
import { useState } from 'react';
import useClickOutside from './hooks/useClickOutside';

function Dropdown() {
  const [isOpen, setIsOpen] = useState(false);
  const ref = useClickOutside(() => setIsOpen(false));
  
  return (
    <div ref={ref} className="dropdown">
      <button onClick={() => setIsOpen(!isOpen)}>
        Menu
      </button>
      {isOpen && (
        <div className="dropdown-menu">
          <a href="#">Item 1</a>
          <a href="#">Item 2</a>
          <a href="#">Item 3</a>
        </div>
      )}
    </div>
  );
}
```

---

## 📝 Hands-On Tutorial: Building useToggle Hook

### Step 1: Basic useToggle

```jsx
// src/hooks/useToggle.js
import { useState } from 'react';

function useToggle(initialValue = false) {
  const [value, setValue] = useState(initialValue);
  
  const toggle = () => setValue(prev => !prev);
  const setTrue = () => setValue(true);
  const setFalse = () => setValue(false);
  
  return [value, toggle, setTrue, setFalse];
}

export default useToggle;
```

### Step 2: Using useToggle

```jsx
import useToggle from './hooks/useToggle';

function Modal() {
  const [isOpen, toggle, open, close] = useToggle(false);
  
  return (
    <div>
      <button onClick={open}>Open Modal</button>
      
      {isOpen && (
        <div className="modal">
          <h2>Modal Title</h2>
          <p>Modal content</p>
          <button onClick={close}>Close</button>
        </div>
      )}
    </div>
  );
}
```

---

## 🎯 Practice Exercises

### Exercise 1: useWindowSize Hook
Create a hook that:
- Tracks window width and height
- Updates on resize
- Cleans up event listener

### Exercise 2: usePrevious Hook
Create a hook that:
- Stores the previous value of a variable
- Useful for comparing old vs new values

### Exercise 3: useForm Hook
Build a form hook that:
- Manages form state
- Handles validation
- Provides submit handler
- Returns errors

---

## ❌ Common Mistakes

1. **Not following naming convention:**
   ```jsx
   // ❌ Wrong - Doesn't start with 'use'
   function counter() { ... }
   
   // ✅ Right
   function useCounter() { ... }
   ```

2. **Calling hooks conditionally:**
   ```jsx
   // ❌ Wrong
   if (condition) {
     const [state, setState] = useState();
   }
   
   // ✅ Right
   const [state, setState] = useState();
   if (condition) {
     // use state
   }
   ```

---

## 🚀 Real-World Application

Custom hooks are used for:
- **Data Fetching**: useFetch, useQuery
- **Form Management**: useForm, useField
- **UI State**: useToggle, useModal
- **Browser APIs**: useLocalStorage, useGeolocation
- **Animations**: useAnimation, useSpring

---

## 📚 Key Takeaways

1. Custom hooks start with `use`
2. Extract reusable logic into hooks
3. Hooks can call other hooks
4. Share logic between components
5. Follow React hooks rules
6. Test hooks independently

---

## 🔗 Next Steps

- ✅ Complete all practice exercises
- ✅ Build the custom hooks examples
- ✅ Create your own custom hooks
- ✅ Read `learning-modules/09-routing.md` next

---

## 💡 Reflection Questions

1. Can I create a custom hook?
2. Do I know when to extract logic into hooks?
3. Can I combine multiple hooks in a custom hook?
4. Do I follow the naming convention?

If yes to all, you're ready for routing! 🎉


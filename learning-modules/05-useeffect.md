# Module 05 · Side Effects with useEffect

## 🎯 Learning Objectives
By the end of this module, you will:
- Understand what side effects are
- Master the `useEffect` hook
- Know when to use dependency arrays
- Handle cleanup in effects
- Fetch data from APIs
- Avoid common pitfalls (infinite loops, stale closures)

---

## 📚 What are Side Effects?

**Side effects** are operations that affect something outside the component:
- Fetching data from an API
- Setting up subscriptions
- Manually changing the DOM
- Setting timers
- Logging to console

**Pure functions** (components) shouldn't have side effects. That's why React provides `useEffect`.

---

## 🎣 The useEffect Hook

`useEffect` lets you perform side effects in function components.

### Basic Syntax

```jsx
import { useEffect } from 'react';

useEffect(() => {
  // Side effect code here
}, [dependencies]); // Optional dependency array
```

### Simple Example: Document Title

```jsx
function Page({ title }) {
  useEffect(() => {
    document.title = title;
  }, [title]); // Runs when title changes
  
  return <h1>{title}</h1>;
}
```

---

## 🔄 Dependency Array

The dependency array controls **when** the effect runs.

### No Dependency Array (Runs Every Render)

```jsx
function Component() {
  useEffect(() => {
    console.log('Runs on every render');
  }); // No array = runs every time
  
  return <div>Component</div>;
}
```

**⚠️ Warning:** This can cause infinite loops if you update state inside!

### Empty Dependency Array (Runs Once)

```jsx
function Component() {
  useEffect(() => {
    console.log('Runs only once on mount');
  }, []); // Empty array = runs once
  
  return <div>Component</div>;
}
```

**Use cases:**
- Fetching initial data
- Setting up subscriptions
- One-time setup

### With Dependencies (Runs When Dependencies Change)

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    // Fetch user when userId changes
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(data => setUser(data));
  }, [userId]); // Runs when userId changes
  
  return <div>{user?.name}</div>;
}
```

---

## 🌐 Fetching Data

### Basic Data Fetching

```jsx
function UserList() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    async function fetchUsers() {
      try {
        setLoading(true);
        const response = await fetch('https://api.example.com/users');
        const data = await response.json();
        setUsers(data);
        setError(null);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }
    
    fetchUsers();
  }, []); // Run once on mount
  
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

### Fetching with Parameters

```jsx
function PostDetail({ postId }) {
  const [post, setPost] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    let cancelled = false;
    
    async function fetchPost() {
      setLoading(true);
      try {
        const response = await fetch(`/api/posts/${postId}`);
        const data = await response.json();
        
        if (!cancelled) {
          setPost(data);
        }
      } catch (error) {
        if (!cancelled) {
          console.error('Failed to fetch post:', error);
        }
      } finally {
        if (!cancelled) {
          setLoading(false);
        }
      }
    }
    
    fetchPost();
    
    return () => {
      cancelled = true; // Cleanup: cancel if component unmounts
    };
  }, [postId]); // Re-fetch when postId changes
  
  if (loading) return <div>Loading post...</div>;
  if (!post) return <div>Post not found</div>;
  
  return (
    <article>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </article>
  );
}
```

---

## 🧹 Cleanup Function

Cleanup prevents memory leaks and cancels ongoing operations.

### Timer Cleanup

```jsx
function Timer() {
  const [seconds, setSeconds] = useState(0);
  
  useEffect(() => {
    const interval = setInterval(() => {
      setSeconds(prev => prev + 1);
    }, 1000);
    
    // Cleanup: clear interval when component unmounts
    return () => {
      clearInterval(interval);
    };
  }, []); // Run once on mount
  
  return <div>Timer: {seconds}s</div>;
}
```

### Subscription Cleanup

```jsx
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  
  useEffect(() => {
    // Simulate WebSocket subscription
    const subscription = subscribeToRoom(roomId, (message) => {
      setMessages(prev => [...prev, message]);
    });
    
    // Cleanup: unsubscribe when component unmounts or roomId changes
    return () => {
      subscription.unsubscribe();
    };
  }, [roomId]);
  
  return (
    <div>
      {messages.map(msg => (
        <div key={msg.id}>{msg.text}</div>
      ))}
    </div>
  );
}
```

### Event Listener Cleanup

```jsx
function WindowSize() {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight
  });
  
  useEffect(() => {
    function handleResize() {
      setSize({
        width: window.innerWidth,
        height: window.innerHeight
      });
    }
    
    window.addEventListener('resize', handleResize);
    
    // Cleanup: remove event listener
    return () => {
      window.removeEventListener('resize', handleResize);
    };
  }, []); // Run once on mount
  
  return (
    <div>
      Window size: {size.width} x {size.height}
    </div>
  );
}
```

---

## ⚠️ Common Pitfalls

### 1. Infinite Loops

```jsx
// ❌ Wrong - Infinite loop!
function Component() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    setCount(count + 1); // Updates state, triggers re-render, runs again...
  }); // No dependency array = runs every render
  
  return <div>{count}</div>;
}

// ✅ Right - Use dependency array
useEffect(() => {
  setCount(0); // Only runs once
}, []);

// ✅ Right - Use functional update if needed
useEffect(() => {
  setCount(prev => prev + 1); // Still wrong if no deps!
}, []); // But at least it only runs once
```

### 2. Stale Closures

```jsx
// ❌ Wrong - Stale closure
function Counter() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    const interval = setInterval(() => {
      setCount(count + 1); // Uses stale count value
    }, 1000);
    
    return () => clearInterval(interval);
  }, []); // Empty deps, count is stale
  
  return <div>{count}</div>;
}

// ✅ Right - Use functional update
useEffect(() => {
  const interval = setInterval(() => {
    setCount(prev => prev + 1); // Always uses latest state
  }, 1000);
  
  return () => clearInterval(interval);
}, []); // No need for count in deps
```

### 3. Missing Dependencies

```jsx
// ❌ Wrong - Missing userId in dependencies
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    fetchUser(userId).then(setUser);
  }, []); // Should include userId!
  
  return <div>{user?.name}</div>;
}

// ✅ Right - Include all dependencies
useEffect(() => {
  fetchUser(userId).then(setUser);
}, [userId]); // Now it re-fetches when userId changes
```

---

## 🎯 Multiple Effects

You can have multiple `useEffect` hooks in one component:

```jsx
function Dashboard({ userId }) {
  const [user, setUser] = useState(null);
  const [notifications, setNotifications] = useState([]);
  const [windowWidth, setWindowWidth] = useState(window.innerWidth);
  
  // Effect 1: Fetch user data
  useEffect(() => {
    fetchUser(userId).then(setUser);
  }, [userId]);
  
  // Effect 2: Fetch notifications
  useEffect(() => {
    fetchNotifications(userId).then(setNotifications);
  }, [userId]);
  
  // Effect 3: Track window size
  useEffect(() => {
    function handleResize() {
      setWindowWidth(window.innerWidth);
    }
    
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);
  
  return (
    <div>
      <h1>Welcome, {user?.name}</h1>
      <p>Window width: {windowWidth}px</p>
      <p>Notifications: {notifications.length}</p>
    </div>
  );
}
```

---

## 📝 Hands-On Tutorial: Building a Data Fetcher

### Step 1: Custom Hook for Data Fetching

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
  }, [url]); // Re-fetch when URL changes
  
  return { data, loading, error };
}
```

### Step 2: Using the Custom Hook

```jsx
// src/components/UserProfile.js
import useFetch from '../hooks/useFetch';

function UserProfile({ userId }) {
  const { data: user, loading, error } = useFetch(
    `https://api.example.com/users/${userId}`
  );
  
  if (loading) return <div>Loading user...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return <div>User not found</div>;
  
  return (
    <div className="user-profile">
      <img src={user.avatar} alt={user.name} />
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  );
}
```

### Step 3: Post List with useFetch

```jsx
function PostList() {
  const { data: posts, loading, error } = useFetch(
    'https://api.example.com/posts'
  );
  
  if (loading) return <div>Loading posts...</div>;
  if (error) return <div>Error loading posts: {error}</div>;
  
  return (
    <div>
      <h2>Posts</h2>
      {posts?.map(post => (
        <article key={post.id}>
          <h3>{post.title}</h3>
          <p>{post.excerpt}</p>
        </article>
      ))}
    </div>
  );
}
```

---

## 🎯 Practice Exercises

### Exercise 1: Live Clock
Create a component that:
- Shows current time
- Updates every second
- Cleans up the interval properly

### Exercise 2: Search with Debounce
Create a search component that:
- Fetches results from an API
- Waits 500ms after user stops typing
- Cancels previous requests if new search starts

### Exercise 3: Online Status
Create a component that:
- Shows if user is online/offline
- Listens to online/offline events
- Cleans up event listeners

---

## ❌ Common Mistakes

1. **Forgetting cleanup:**
   ```jsx
   // ❌ Wrong - Memory leak
   useEffect(() => {
     const interval = setInterval(() => {}, 1000);
     // No cleanup!
   }, []);
   
   // ✅ Right
   useEffect(() => {
     const interval = setInterval(() => {}, 1000);
     return () => clearInterval(interval);
   }, []);
   ```

2. **Including everything in dependencies:**
   ```jsx
   // ❌ Wrong - Unnecessary re-runs
   useEffect(() => {
     console.log('Runs too often');
   }, [count, name, email, age, ...]); // Too many deps
   
   // ✅ Right - Only what's needed
   useEffect(() => {
     console.log('Runs when count changes');
   }, [count]);
   ```

---

## 🚀 Real-World Application

useEffect is used for:
- **API Calls**: Fetching data on mount or when props change
- **Subscriptions**: WebSocket, Firebase, etc.
- **Timers**: Countdowns, intervals
- **DOM Manipulation**: Third-party libraries
- **Analytics**: Tracking page views, events

---

## 📚 Key Takeaways

1. useEffect handles side effects
2. Empty dependency array = runs once on mount
3. Include dependencies that effect uses
4. Always cleanup subscriptions, timers, event listeners
5. Use functional updates to avoid stale closures
6. Multiple effects are fine - separate concerns

---

## 🔗 Next Steps

- ✅ Complete all practice exercises
- ✅ Build the useFetch custom hook
- ✅ Experiment with different side effects
- ✅ Read `learning-modules/06-conditional-lists.md` next

---

## 💡 Reflection Questions

1. Can I explain when useEffect runs?
2. Do I know how to prevent infinite loops?
3. Can I properly cleanup effects?
4. Do I understand dependency arrays?

If yes to all, you're ready for conditional rendering and lists! 🎉


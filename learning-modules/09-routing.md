# Module 09 · React Router

## 🎯 Learning Objectives
By the end of this module, you will:
- Set up React Router in your app
- Create routes and navigate between pages
- Use URL parameters and query strings
- Handle nested routes
- Create protected routes
- Implement 404 pages

---

## 📚 What is React Router?

**React Router** enables navigation in React apps without page refreshes (Single Page Application - SPA).

### Installation

```bash
npm install react-router-dom
```

### Basic Setup

```jsx
// src/index.js
import { BrowserRouter } from 'react-router-dom';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')).render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
);
```

---

## 🛣️ Basic Routing

### Setting Up Routes

```jsx
// src/App.js
import { Routes, Route } from 'react-router-dom';
import Home from './pages/Home';
import About from './pages/About';
import Contact from './pages/Contact';

function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/about" element={<About />} />
      <Route path="/contact" element={<Contact />} />
    </Routes>
  );
}
```

### Navigation with Link

```jsx
import { Link } from 'react-router-dom';

function Navigation() {
  return (
    <nav>
      <Link to="/">Home</Link>
      <Link to="/about">About</Link>
      <Link to="/contact">Contact</Link>
    </nav>
  );
}
```

### Programmatic Navigation

```jsx
import { useNavigate } from 'react-router-dom';

function LoginForm() {
  const navigate = useNavigate();
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    // Login logic...
    navigate('/dashboard'); // Navigate after login
  };
  
  return (
    <form onSubmit={handleSubmit}>
      {/* form fields */}
      <button type="submit">Login</button>
    </form>
  );
}
```

---

## 🎯 URL Parameters

### Dynamic Routes

```jsx
// App.js
<Routes>
  <Route path="/users/:userId" element={<UserProfile />} />
</Routes>
```

### Reading Parameters

```jsx
// pages/UserProfile.js
import { useParams } from 'react-router-dom';

function UserProfile() {
  const { userId } = useParams();
  
  return <div>User ID: {userId}</div>;
}
```

### Multiple Parameters

```jsx
// Route
<Route path="/posts/:postId/comments/:commentId" element={<CommentDetail />} />

// Component
function CommentDetail() {
  const { postId, commentId } = useParams();
  return (
    <div>
      <p>Post: {postId}</p>
      <p>Comment: {commentId}</p>
    </div>
  );
}
```

---

## 🔍 Query Strings

### Reading Query Parameters

```jsx
import { useSearchParams } from 'react-router-dom';

function ProductList() {
  const [searchParams, setSearchParams] = useSearchParams();
  const category = searchParams.get('category');
  const sort = searchParams.get('sort');
  
  return (
    <div>
      <p>Category: {category}</p>
      <p>Sort: {sort}</p>
      <button onClick={() => setSearchParams({ category: 'electronics' })}>
        Filter Electronics
      </button>
    </div>
  );
}
```

---

## 🏗️ Nested Routes

### Layout Component

```jsx
// components/Layout.js
import { Outlet, Link } from 'react-router-dom';

function Layout() {
  return (
    <div>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
      </nav>
      <main>
        <Outlet /> {/* Child routes render here */}
      </main>
      <footer>Footer</footer>
    </div>
  );
}
```

### Nested Route Setup

```jsx
// App.js
<Routes>
  <Route element={<Layout />}>
    <Route index element={<Home />} />
    <Route path="about" element={<About />} />
    <Route path="products" element={<Products />}>
      <Route index element={<ProductList />} />
      <Route path=":productId" element={<ProductDetail />} />
    </Route>
  </Route>
</Routes>
```

---

## 🔒 Protected Routes

### Protected Route Component

```jsx
// components/ProtectedRoute.js
import { Navigate } from 'react-router-dom';
import { useUser } from '../context/UserContext';

function ProtectedRoute({ children }) {
  const { isAuthenticated } = useUser();
  
  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }
  
  return children;
}
```

### Using Protected Routes

```jsx
// App.js
<Routes>
  <Route path="/login" element={<Login />} />
  <Route
    path="/dashboard"
    element={
      <ProtectedRoute>
        <Dashboard />
      </ProtectedRoute>
    }
  />
</Routes>
```

---

## 🚫 404 Page

```jsx
// pages/NotFound.js
import { Link } from 'react-router-dom';

function NotFound() {
  return (
    <div>
      <h1>404 - Page Not Found</h1>
      <p>The page you're looking for doesn't exist.</p>
      <Link to="/">Go Home</Link>
    </div>
  );
}
```

```jsx
// App.js
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="/about" element={<About />} />
  <Route path="*" element={<NotFound />} /> {/* Catch all */}
</Routes>
```

---

## 🎨 Active Links

### Using NavLink

```jsx
import { NavLink } from 'react-router-dom';

function Navigation() {
  return (
    <nav>
      <NavLink 
        to="/" 
        className={({ isActive }) => isActive ? 'active' : ''}
      >
        Home
      </NavLink>
      <NavLink 
        to="/about"
        style={({ isActive }) => ({
          color: isActive ? 'red' : 'blue'
        })}
      >
        About
      </NavLink>
    </nav>
  );
}
```

---

## 📝 Hands-On Tutorial: Building a Blog App

### Step 1: Route Setup

```jsx
// App.js
import { Routes, Route } from 'react-router-dom';
import Layout from './components/Layout';
import Home from './pages/Home';
import BlogList from './pages/BlogList';
import BlogPost from './pages/BlogPost';
import About from './pages/About';
import NotFound from './pages/NotFound';

function App() {
  return (
    <Routes>
      <Route element={<Layout />}>
        <Route index element={<Home />} />
        <Route path="blog" element={<BlogList />} />
        <Route path="blog/:postId" element={<BlogPost />} />
        <Route path="about" element={<About />} />
        <Route path="*" element={<NotFound />} />
      </Route>
    </Routes>
  );
}
```

### Step 2: Blog Post Component

```jsx
// pages/BlogPost.js
import { useParams, Link, useNavigate } from 'react-router-dom';
import { useState, useEffect } from 'react';

function BlogPost() {
  const { postId } = useParams();
  const navigate = useNavigate();
  const [post, setPost] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    fetch(`/api/posts/${postId}`)
      .then(res => res.json())
      .then(data => {
        setPost(data);
        setLoading(false);
      })
      .catch(() => navigate('/blog'));
  }, [postId, navigate]);
  
  if (loading) return <div>Loading...</div>;
  if (!post) return <div>Post not found</div>;
  
  return (
    <article>
      <Link to="/blog">← Back to Blog</Link>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </article>
  );
}
```

---

## 🎯 Practice Exercises

### Exercise 1: E-commerce Routes
Create routes for:
- Home page
- Product list
- Product detail (with ID)
- Shopping cart
- Checkout (protected)

### Exercise 2: User Dashboard
Build routes with:
- Login page
- Protected dashboard
- User profile
- Settings page
- Redirect to login if not authenticated

### Exercise 3: Multi-step Form
Create a form with routes:
- Step 1: Personal info (/form/step1)
- Step 2: Address (/form/step2)
- Step 3: Review (/form/review)
- Navigation between steps

---

## ❌ Common Mistakes

1. **Forgetting BrowserRouter:**
   ```jsx
   // ❌ Wrong
   <App />
   
   // ✅ Right
   <BrowserRouter>
     <App />
   </BrowserRouter>
   ```

2. **Using <a> instead of <Link>:**
   ```jsx
   // ❌ Wrong - Causes page refresh
   <a href="/about">About</a>
   
   // ✅ Right
   <Link to="/about">About</Link>
   ```

3. **Not handling 404:**
   ```jsx
   // ❌ Wrong - No catch-all route
   <Routes>
     <Route path="/" element={<Home />} />
   </Routes>
   
   // ✅ Right
   <Routes>
     <Route path="/" element={<Home />} />
     <Route path="*" element={<NotFound />} />
   </Routes>
   ```

---

## 🚀 Real-World Application

Routing is used for:
- **Multi-page Apps**: Navigation between sections
- **E-commerce**: Product pages, checkout flow
- **Dashboards**: Different views and settings
- **Blogs**: Post pages, categories
- **Admin Panels**: Protected routes

---

## 📚 Key Takeaways

1. Wrap app in BrowserRouter
2. Use Routes and Route for routing
3. Use Link for navigation (not <a>)
4. useNavigate for programmatic navigation
5. useParams for URL parameters
6. useSearchParams for query strings
7. Outlet for nested routes
8. Always include 404 route

---

## 🔗 Next Steps

- ✅ Complete all practice exercises
- ✅ Build the blog app example
- ✅ Experiment with nested routes
- ✅ Review all modules and start building projects!

---

## 💡 Reflection Questions

1. Can I set up React Router?
2. Do I know how to navigate between pages?
3. Can I use URL parameters?
4. Do I understand nested routes?

If yes to all, you're ready to build real projects! 🎉


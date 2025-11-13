# Module 02 · Components & Props

## 🎯 Learning Objectives
By the end of this module, you will:
- Understand function components vs class components
- Master props: passing data, default values, prop types
- Build reusable, composable components
- Organize components in a project structure
- Use props for parent-child communication

---

## 📚 What are Components?

Components are **reusable pieces of UI** that return JSX. They're like JavaScript functions that return HTML.

### Function Components (Modern Approach)

```jsx
// Simple component
function Welcome() {
  return <h1>Welcome to React!</h1>;
}

// Arrow function component (also valid)
const Welcome = () => {
  return <h1>Welcome to React!</h1>;
};

// One-liner (implicit return)
const Welcome = () => <h1>Welcome to React!</h1>;
```

**All three are equivalent!** Use whichever style you prefer (or your team prefers).

---

## 🎁 Understanding Props

**Props** (short for properties) are how you pass data from parent to child components. Think of them as function arguments.

### Basic Props

```jsx
// Child component (receives props)
function Greeting({ name, age }) {
  return (
    <div>
      <h2>Hello, {name}!</h2>
      <p>You are {age} years old.</p>
    </div>
  );
}

// Parent component (passes props)
function App() {
  return (
    <div>
      <Greeting name="Alice" age={25} />
      <Greeting name="Bob" age={30} />
    </div>
  );
}
```

**Key Points:**
- Props are **read-only** (immutable)
- Props are passed down from parent to child
- Props can be strings, numbers, objects, arrays, functions, etc.

---

## 📦 Different Types of Props

### String Props

```jsx
function Title({ text }) {
  return <h1>{text}</h1>;
}

// Usage
<Title text="Welcome" />
```

### Number Props

```jsx
function Counter({ initialValue }) {
  return <p>Count starts at: {initialValue}</p>;
}

// Usage
<Counter initialValue={10} />
```

### Boolean Props

```jsx
function Button({ disabled, loading }) {
  return (
    <button disabled={disabled}>
      {loading ? 'Loading...' : 'Click me'}
    </button>
  );
}

// Usage
<Button disabled={true} loading={false} />
// Shorthand for true
<Button disabled loading />
```

### Object Props

```jsx
function UserCard({ user }) {
  return (
    <div>
      <h3>{user.name}</h3>
      <p>{user.email}</p>
      <p>Role: {user.role}</p>
    </div>
  );
}

// Usage
const user = {
  name: "Alice",
  email: "alice@example.com",
  role: "Developer"
};
<UserCard user={user} />
```

### Array Props

```jsx
function SkillList({ skills }) {
  return (
    <ul>
      {skills.map((skill, index) => (
        <li key={index}>{skill}</li>
      ))}
    </ul>
  );
}

// Usage
<SkillList skills={["React", "JavaScript", "CSS"]} />
```

### Function Props (Callbacks)

```jsx
function Button({ onClick, label }) {
  return <button onClick={onClick}>{label}</button>;
}

// Usage
function App() {
  const handleClick = () => {
    alert('Button clicked!');
  };
  
  return <Button onClick={handleClick} label="Click me" />;
}
```

---

## 🎯 Default Props

Provide fallback values when props are missing:

```jsx
function Greeting({ name, greeting = "Hello" }) {
  return <h2>{greeting}, {name}!</h2>;
}

// Usage
<Greeting name="Alice" />           // "Hello, Alice!"
<Greeting name="Bob" greeting="Hi" />  // "Hi, Bob!"
```

### Using Default Parameters

```jsx
function ProductCard({ 
  name, 
  price = 0, 
  inStock = true,
  discount = 0 
}) {
  const finalPrice = price * (1 - discount);
  
  return (
    <div>
      <h3>{name}</h3>
      <p>Price: ${finalPrice.toFixed(2)}</p>
      <p>Status: {inStock ? "In Stock" : "Out of Stock"}</p>
    </div>
  );
}
```

---

## 🧩 Component Composition

Break down complex components into smaller pieces:

### Example: User Profile

```jsx
// Small, focused components
function Avatar({ src, alt }) {
  return <img src={src} alt={alt} className="avatar" />;
}

function UserInfo({ name, email }) {
  return (
    <div className="user-info">
      <h3>{name}</h3>
      <p>{email}</p>
    </div>
  );
}

function UserActions({ onEdit, onDelete }) {
  return (
    <div className="user-actions">
      <button onClick={onEdit}>Edit</button>
      <button onClick={onDelete}>Delete</button>
    </div>
  );
}

// Composed component
function UserProfile({ user, onEdit, onDelete }) {
  return (
    <div className="user-profile">
      <Avatar src={user.avatar} alt={user.name} />
      <UserInfo name={user.name} email={user.email} />
      <UserActions onEdit={onEdit} onDelete={onDelete} />
    </div>
  );
}
```

**Benefits:**
- Easier to test
- More reusable
- Easier to maintain
- Better organization

---

## 📁 Component Organization

### Recommended Structure

```
src/
  components/
    Button/
      Button.js
      Button.css
      Button.test.js
    Card/
      Card.js
      Card.css
    UserProfile/
      UserProfile.js
      UserProfile.css
      Avatar.js
      UserInfo.js
  pages/
    HomePage.js
    AboutPage.js
  App.js
  index.js
```

### Example: Organized Button Component

```jsx
// src/components/Button/Button.js
import './Button.css';

function Button({ 
  children, 
  variant = 'primary', 
  size = 'medium',
  onClick,
  disabled = false 
}) {
  const className = `btn btn-${variant} btn-${size}`;
  
  return (
    <button 
      className={className}
      onClick={onClick}
      disabled={disabled}
    >
      {children}
    </button>
  );
}

export default Button;
```

```css
/* src/components/Button/Button.css */
.btn {
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-weight: 500;
  transition: all 0.2s;
}

.btn-primary {
  background-color: #007bff;
  color: white;
}

.btn-secondary {
  background-color: #6c757d;
  color: white;
}

.btn-small {
  padding: 6px 12px;
  font-size: 14px;
}

.btn-medium {
  padding: 10px 20px;
  font-size: 16px;
}

.btn-large {
  padding: 14px 28px;
  font-size: 18px;
}

.btn:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}
```

---

## 🎨 Children Prop

The `children` prop is special - it contains whatever is between opening and closing tags:

```jsx
function Card({ children, title }) {
  return (
    <div className="card">
      {title && <h3>{title}</h3>}
      <div className="card-content">
        {children}
      </div>
    </div>
  );
}

// Usage
<Card title="User Info">
  <p>Name: Alice</p>
  <p>Email: alice@example.com</p>
</Card>
```

**What gets passed as `children`?**
```jsx
<Card>
  <p>This is children</p>
  <button>This too</button>
</Card>
// children = [<p>, <button>]
```

### Multiple Children

```jsx
function Layout({ header, sidebar, main, footer }) {
  return (
    <div className="layout">
      <header>{header}</header>
      <aside>{sidebar}</aside>
      <main>{main}</main>
      <footer>{footer}</footer>
    </div>
  );
}

// Usage
<Layout
  header={<h1>My App</h1>}
  sidebar={<nav>Menu</nav>}
  main={<p>Content</p>}
  footer={<p>Copyright 2024</p>}
/>
```

---

## 📝 Hands-On Tutorial: Building a Reusable Card System

### Step 1: Base Card Component

```jsx
// src/components/Card/Card.js
import './Card.css';

function Card({ children, className = '' }) {
  return (
    <div className={`card ${className}`}>
      {children}
    </div>
  );
}

export default Card;
```

### Step 2: Specialized Card Components

```jsx
// src/components/Card/ProductCard.js
import Card from './Card';

function ProductCard({ product, onAddToCart }) {
  return (
    <Card className="product-card">
      <img src={product.image} alt={product.name} />
      <h3>{product.name}</h3>
      <p className="price">${product.price}</p>
      <button onClick={() => onAddToCart(product.id)}>
        Add to Cart
      </button>
    </Card>
  );
}

export default ProductCard;
```

```jsx
// src/components/Card/UserCard.js
import Card from './Card';

function UserCard({ user }) {
  return (
    <Card className="user-card">
      <img src={user.avatar} alt={user.name} />
      <h3>{user.name}</h3>
      <p>{user.email}</p>
      <p className="role">{user.role}</p>
    </Card>
  );
}

export default UserCard;
```

### Step 3: Using the Cards

```jsx
// src/App.js
import ProductCard from './components/Card/ProductCard';
import UserCard from './components/Card/UserCard';

function App() {
  const products = [
    { id: 1, name: "Laptop", price: 999, image: "laptop.jpg" },
    { id: 2, name: "Phone", price: 699, image: "phone.jpg" }
  ];
  
  const users = [
    { name: "Alice", email: "alice@example.com", role: "Admin", avatar: "alice.jpg" },
    { name: "Bob", email: "bob@example.com", role: "User", avatar: "bob.jpg" }
  ];
  
  const handleAddToCart = (productId) => {
    console.log(`Adding product ${productId} to cart`);
  };
  
  return (
    <div>
      <h1>Products</h1>
      {products.map(product => (
        <ProductCard 
          key={product.id}
          product={product}
          onAddToCart={handleAddToCart}
        />
      ))}
      
      <h1>Users</h1>
      {users.map(user => (
        <UserCard key={user.email} user={user} />
      ))}
    </div>
  );
}
```

---

## 🎯 Practice Exercises

### Exercise 1: Reusable Button Component
Create a `Button` component that accepts:
- `variant` (primary, secondary, danger)
- `size` (small, medium, large)
- `children` (button text)
- `onClick` (handler function)

Make it fully reusable with proper styling.

### Exercise 2: Modal Component
Create a `Modal` component that:
- Accepts `isOpen` prop to show/hide
- Has `title` and `children` props
- Has `onClose` callback
- Renders a backdrop and centered content

### Exercise 3: Form Field Component
Create a `FormField` component that:
- Accepts `label`, `type`, `value`, `onChange`
- Shows error message if provided
- Has consistent styling

---

## ❌ Common Mistakes

1. **Mutating props:**
   ```jsx
   // ❌ Wrong
   function Component({ user }) {
     user.name = "Changed"; // Never do this!
     return <p>{user.name}</p>;
   }
   
   // ✅ Right - Props are read-only
   function Component({ user }) {
     return <p>{user.name}</p>;
   }
   ```

2. **Not using key prop in lists:**
   ```jsx
   // ❌ Wrong
   {items.map(item => <Item data={item} />)}
   
   // ✅ Right
   {items.map(item => <Item key={item.id} data={item} />)}
   ```

3. **Forgetting to pass required props:**
   ```jsx
   // ❌ Wrong - Missing required prop
   <UserCard /> // user prop is required
   
   // ✅ Right
   <UserCard user={userData} />
   ```

---

## 🚀 Real-World Application

Props are used everywhere:
- **E-commerce**: Product cards, shopping cart items
- **Social Media**: Post components, user profiles
- **Dashboards**: Widget components, data displays
- **Forms**: Input fields, validation messages

---

## 📚 Key Takeaways

1. Components are reusable UI pieces
2. Props pass data from parent to child
3. Props are read-only (immutable)
4. Use default parameters for optional props
5. Break complex components into smaller ones
6. `children` prop contains content between tags
7. Organize components in folders by feature

---

## 🔗 Next Steps

- ✅ Complete all practice exercises
- ✅ Build the Card component system
- ✅ Experiment with different prop types
- ✅ Read `learning-modules/03-state-usestate.md` next

---

## 💡 Reflection Questions

1. Can I create a reusable component with props?
2. Do I understand the difference between props and state?
3. Can I compose multiple components together?
4. Do I know when to use the `children` prop?

If yes to all, you're ready to learn about state! 🎉


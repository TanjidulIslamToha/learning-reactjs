# Module 01 · JSX Fundamentals

## 🎯 Learning Objectives
By the end of this module, you will:
- Master JSX syntax and rules
- Understand how JSX compiles to JavaScript
- Use expressions, attributes, and fragments correctly
- Handle events in JSX
- Style components with inline styles and CSS

---

## 📚 What is JSX?

JSX (JavaScript XML) is a syntax extension that lets you write HTML-like code in JavaScript. It's **not** HTML - it gets transformed into JavaScript function calls.

### JSX vs HTML vs JavaScript

```jsx
// This JSX:
const element = <h1 className="title">Hello, {name}!</h1>;

// Gets transformed to this JavaScript:
const element = React.createElement(
  'h1',
  { className: 'title' },
  'Hello, ',
  name,
  '!'
);
```

**Key Point:** JSX is syntactic sugar that makes React code more readable!

---

## 🎨 JSX Syntax Rules

### Rule 1: Must Return a Single Root Element

```jsx
// ❌ Wrong - Multiple root elements
function Component() {
  return (
    <h1>Title</h1>
    <p>Paragraph</p>
  );
}

// ✅ Right - Single root element
function Component() {
  return (
    <div>
      <h1>Title</h1>
      <p>Paragraph</p>
    </div>
  );
}

// ✅ Also Right - Using Fragment (no extra div)
function Component() {
  return (
    <>
      <h1>Title</h1>
      <p>Paragraph</p>
    </>
  );
}
```

### Rule 2: Use `className` Instead of `class`

```jsx
// ❌ Wrong
<div class="container">

// ✅ Right
<div className="container">
```

**Why?** `class` is a reserved word in JavaScript!

### Rule 3: Self-Closing Tags Need `/`

```jsx
// ❌ Wrong
<img src="image.jpg">
<input type="text">

// ✅ Right
<img src="image.jpg" />
<input type="text" />
```

### Rule 4: Use `{}` for JavaScript Expressions

```jsx
function Greeting({ name, age }) {
  const greeting = "Hello";
  const isAdult = age >= 18;
  
  return (
    <div>
      <p>{greeting}, {name}!</p>
      <p>Age: {age}</p>
      <p>Status: {isAdult ? "Adult" : "Minor"}</p>
      <p>Next year: {age + 1}</p>
    </div>
  );
}
```

**What can go inside `{}`?**
- Variables
- Expressions (`age + 1`)
- Function calls
- Ternary operators
- Array methods (map, filter, etc.)

**What CANNOT go inside `{}`?**
- `if` statements (use ternary or `&&`)
- `for` loops (use `map` instead)
- Object definitions (unless it's an expression)

---

## 🔤 Embedding Expressions

### Variables

```jsx
function Welcome() {
  const userName = "Alice";
  const currentYear = 2024;
  
  return (
    <div>
      <h1>Welcome, {userName}!</h1>
      <p>Current Year: {currentYear}</p>
    </div>
  );
}
```

### Calculations

```jsx
function PriceDisplay({ price, tax }) {
  const total = price + (price * tax);
  
  return (
    <div>
      <p>Price: ${price}</p>
      <p>Tax: ${(price * tax).toFixed(2)}</p>
      <p><strong>Total: ${total.toFixed(2)}</strong></p>
    </div>
  );
}
```

### Function Calls

```jsx
function formatDate(date) {
  return new Date(date).toLocaleDateString();
}

function EventCard({ event }) {
  return (
    <div>
      <h3>{event.title}</h3>
      <p>Date: {formatDate(event.date)}</p>
    </div>
  );
}
```

### Conditional Rendering

```jsx
// Method 1: Ternary Operator
function UserStatus({ isLoggedIn, userName }) {
  return (
    <div>
      {isLoggedIn ? (
        <p>Welcome back, {userName}!</p>
      ) : (
        <p>Please log in</p>
      )}
    </div>
  );
}

// Method 2: Logical AND (for simple cases)
function Notification({ message, show }) {
  return (
    <div>
      {show && <p>{message}</p>}
    </div>
  );
}

// Method 3: Early Return
function UserProfile({ user }) {
  if (!user) {
    return <p>Loading...</p>;
  }
  
  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  );
}
```

---

## 🎯 JSX Attributes

### Standard HTML Attributes

```jsx
function ImageCard() {
  return (
    <img 
      src="photo.jpg"
      alt="Description"
      width={300}
      height={200}
    />
  );
}
```

**Note:** Numeric values don't need quotes, strings do!

### Data Attributes

```jsx
function ProductCard({ product }) {
  return (
    <div data-product-id={product.id} data-category={product.category}>
      <h3>{product.name}</h3>
    </div>
  );
}
```

### Boolean Attributes

```jsx
function Form() {
  return (
    <form>
      <input type="checkbox" checked={true} />
      <input type="text" disabled={false} />
      <button type="submit">Submit</button>
    </form>
  );
}

// Shorthand for true
<input type="checkbox" checked />
<input disabled />
```

---

## 🎨 Styling in JSX

### Method 1: Inline Styles (Object)

```jsx
function StyledBox() {
  const boxStyle = {
    backgroundColor: 'blue',
    color: 'white',
    padding: '20px',
    borderRadius: '8px',
    // CSS properties use camelCase
    fontSize: '18px',  // Not font-size
    marginTop: '10px'  // Not margin-top
  };
  
  return <div style={boxStyle}>Styled Content</div>;
}

// Or inline
function InlineStyled() {
  return (
    <div style={{
      backgroundColor: 'red',
      padding: '15px'
    }}>
      Inline Styled
    </div>
  );
}
```

### Method 2: CSS Classes

```jsx
// Component
function Card() {
  return (
    <div className="card">
      <h2 className="card-title">Title</h2>
      <p className="card-text">Content</p>
    </div>
  );
}

// App.css
.card {
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 20px;
  margin: 10px;
}

.card-title {
  color: #333;
  font-size: 24px;
}

.card-text {
  color: #666;
  line-height: 1.6;
}
```

### Method 3: Conditional Classes

```jsx
function Button({ isActive, isDisabled }) {
  const buttonClass = `btn ${isActive ? 'btn-active' : ''} ${isDisabled ? 'btn-disabled' : ''}`;
  
  // Or using a helper library like 'clsx'
  return <button className={buttonClass}>Click me</button>;
}

// Better approach with template literals
function Button({ variant, size }) {
  return (
    <button className={`btn btn-${variant} btn-${size}`}>
      Click me
    </button>
  );
}
```

---

## 🖱️ Event Handling

### Basic Event Handlers

```jsx
function Button() {
  const handleClick = () => {
    alert('Button clicked!');
  };
  
  return <button onClick={handleClick}>Click me</button>;
}
```

### Event Handlers with Parameters

```jsx
function ProductList({ products }) {
  const handleAddToCart = (productId) => {
    console.log(`Adding product ${productId} to cart`);
  };
  
  return (
    <div>
      {products.map(product => (
        <div key={product.id}>
          <h3>{product.name}</h3>
          <button onClick={() => handleAddToCart(product.id)}>
            Add to Cart
          </button>
        </div>
      ))}
    </div>
  );
}
```

### Common Events

```jsx
function FormExample() {
  const handleSubmit = (e) => {
    e.preventDefault(); // Prevent form submission
    console.log('Form submitted');
  };
  
  const handleChange = (e) => {
    console.log('Input changed:', e.target.value);
  };
  
  const handleMouseEnter = () => {
    console.log('Mouse entered');
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input 
        type="text"
        onChange={handleChange}
        onFocus={() => console.log('Focused')}
        onBlur={() => console.log('Blurred')}
      />
      <button 
        type="submit"
        onMouseEnter={handleMouseEnter}
      >
        Submit
      </button>
    </form>
  );
}
```

---

## 📝 Hands-On Tutorial: Building a Card Component

### Step 1: Create the Component Structure

```jsx
// src/components/ProductCard.js
function ProductCard({ product }) {
  return (
    <div className="product-card">
      <img src={product.image} alt={product.name} />
      <h3>{product.name}</h3>
      <p className="price">${product.price}</p>
      <p className="description">{product.description}</p>
      <button onClick={() => console.log('Added to cart')}>
        Add to Cart
      </button>
    </div>
  );
}

export default ProductCard;
```

### Step 2: Add Conditional Rendering

```jsx
function ProductCard({ product }) {
  const isOnSale = product.discount > 0;
  const salePrice = product.price * (1 - product.discount);
  
  return (
    <div className="product-card">
      {isOnSale && <span className="badge">SALE!</span>}
      <img src={product.image} alt={product.name} />
      <h3>{product.name}</h3>
      <div className="price-section">
        {isOnSale ? (
          <>
            <p className="price old-price">${product.price}</p>
            <p className="price sale-price">${salePrice.toFixed(2)}</p>
          </>
        ) : (
          <p className="price">${product.price}</p>
        )}
      </div>
      <p className="description">{product.description}</p>
      <button 
        onClick={() => console.log('Added to cart')}
        disabled={!product.inStock}
      >
        {product.inStock ? 'Add to Cart' : 'Out of Stock'}
      </button>
    </div>
  );
}
```

### Step 3: Add Styling

```css
/* App.css */
.product-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 16px;
  margin: 16px;
  max-width: 300px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  transition: transform 0.2s;
}

.product-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 4px 8px rgba(0,0,0,0.2);
}

.product-card img {
  width: 100%;
  height: 200px;
  object-fit: cover;
  border-radius: 4px;
}

.price {
  font-size: 24px;
  font-weight: bold;
  color: #333;
}

.old-price {
  text-decoration: line-through;
  color: #999;
  font-size: 18px;
}

.sale-price {
  color: #e74c3c;
}

.badge {
  background-color: #e74c3c;
  color: white;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 12px;
  font-weight: bold;
}
```

---

## 🎯 Practice Exercises

### Exercise 1: User Card with Conditional Rendering
Create a component that displays user information:
- Show "Online" badge if user is online
- Display different colors based on user role (admin, user, guest)
- Show "Verified" checkmark if email is verified

**Solution:**
```jsx
function UserCard({ user }) {
  return (
    <div className="user-card">
      <div className="user-header">
        <img src={user.avatar} alt={user.name} />
        {user.isOnline && <span className="badge online">Online</span>}
        {user.isVerified && <span className="badge verified">✓</span>}
      </div>
      <h3>{user.name}</h3>
      <p className={`role role-${user.role}`}>{user.role}</p>
      <p>{user.email}</p>
    </div>
  );
}
```

### Exercise 2: Dynamic List with Events
Create a todo list where:
- Each item has a delete button
- Clicking delete removes the item
- Show a message when list is empty

### Exercise 3: Calculator Display
Create a component that:
- Shows numbers and operations
- Highlights the current operation
- Displays result in a different style

---

## ❌ Common Mistakes

1. **Forgetting curly braces for expressions:**
   ```jsx
   // ❌ Wrong
   <p>Count: count</p>
   
   // ✅ Right
   <p>Count: {count}</p>
   ```

2. **Using `if` statements directly in JSX:**
   ```jsx
   // ❌ Wrong
   {if (condition) <p>Hello</p>}
   
   // ✅ Right
   {condition && <p>Hello</p>}
   ```

3. **Not using `key` prop in lists:**
   ```jsx
   // ❌ Wrong
   {items.map(item => <div>{item.name}</div>)}
   
   // ✅ Right
   {items.map(item => <div key={item.id}>{item.name}</div>)}
   ```

4. **Using `class` instead of `className`:**
   ```jsx
   // ❌ Wrong
   <div class="container">
   
   // ✅ Right
   <div className="container">
   ```

---

## 🚀 Real-World Application

JSX is used everywhere in React:
- **Forms**: Input fields, buttons, validation messages
- **Lists**: Product catalogs, user feeds, search results
- **Navigation**: Menus, breadcrumbs, tabs
- **Cards**: User profiles, product displays, article previews

---

## 📚 Key Takeaways

1. JSX is JavaScript, not HTML
2. Always return a single root element (or Fragment)
3. Use `{}` to embed JavaScript expressions
4. Use `className` instead of `class`
5. Event handlers use camelCase (`onClick`, not `onclick`)
6. Inline styles are objects with camelCase properties

---

## 🔗 Next Steps

- ✅ Complete all practice exercises
- ✅ Build the ProductCard component
- ✅ Experiment with different styling approaches
- ✅ Read `learning-modules/02-components-props.md` next

---

## 💡 Reflection Questions

1. Can I explain how JSX differs from HTML?
2. Do I know when to use `{}` in JSX?
3. Can I write conditional rendering in multiple ways?
4. Do I understand event handling syntax?

If yes to all, you're ready for components and props! 🎉


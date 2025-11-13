# Module 03 · State with useState

## 🎯 Learning Objectives
By the end of this module, you will:
- Understand what state is and why it's important
- Master the `useState` hook
- Handle different types of state (strings, numbers, objects, arrays)
- Update state correctly (immutability)
- Manage multiple state variables
- Understand when to lift state up

---

## 📚 What is State?

**State** is data that can change over time. When state changes, React re-renders the component to reflect the new data.

### Props vs State

| Props | State |
|-------|-------|
| Passed from parent | Managed within component |
| Read-only | Can be updated |
| Immutable | Mutable (via setter) |
| External data | Internal data |

**Simple Analogy:**
- **Props** = Settings you receive (like a TV remote's buttons)
- **State** = Current channel/volume (changes when you press buttons)

---

## 🎣 The useState Hook

`useState` is a React hook that lets you add state to function components.

### Basic Syntax

```jsx
import { useState } from 'react';

function Component() {
  const [state, setState] = useState(initialValue);
  //     ^      ^        ^              ^
  //     |      |        |              |
  //     |      |        |              Initial value
  //     |      |        Hook function
  //     |      Setter function
  //     Current state value
}
```

### Simple Example: Counter

```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
      <button onClick={() => setCount(count - 1)}>
        Decrement
      </button>
      <button onClick={() => setCount(0)}>
        Reset
      </button>
    </div>
  );
}
```

**What happens:**
1. Initial render: `count = 0`
2. Click "Increment": `setCount(1)` → React re-renders → `count = 1`
3. Click again: `setCount(2)` → React re-renders → `count = 2`

---

## 🔢 Different Types of State

### String State

```jsx
function Greeting() {
  const [name, setName] = useState('');
  
  return (
    <div>
      <input 
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Enter your name"
      />
      {name && <p>Hello, {name}!</p>}
    </div>
  );
}
```

### Number State

```jsx
function ScoreBoard() {
  const [score, setScore] = useState(0);
  
  return (
    <div>
      <h2>Score: {score}</h2>
      <button onClick={() => setScore(score + 10)}>+10</button>
      <button onClick={() => setScore(score - 5)}>-5</button>
      <button onClick={() => setScore(0)}>Reset</button>
    </div>
  );
}
```

### Boolean State

```jsx
function Toggle() {
  const [isOn, setIsOn] = useState(false);
  
  return (
    <div>
      <p>Status: {isOn ? 'ON' : 'OFF'}</p>
      <button onClick={() => setIsOn(!isOn)}>
        Toggle
      </button>
    </div>
  );
}
```

### Object State

```jsx
function UserForm() {
  const [user, setUser] = useState({
    name: '',
    email: '',
    age: 0
  });
  
  const updateField = (field, value) => {
    setUser({
      ...user,        // Spread existing properties
      [field]: value  // Update specific field
    });
  };
  
  return (
    <div>
      <input
        type="text"
        placeholder="Name"
        value={user.name}
        onChange={(e) => updateField('name', e.target.value)}
      />
      <input
        type="email"
        placeholder="Email"
        value={user.email}
        onChange={(e) => updateField('email', e.target.value)}
      />
      <input
        type="number"
        placeholder="Age"
        value={user.age}
        onChange={(e) => updateField('age', parseInt(e.target.value))}
      />
      <pre>{JSON.stringify(user, null, 2)}</pre>
    </div>
  );
}
```

**Important:** Always spread the existing object when updating!

### Array State

```jsx
function TodoList() {
  const [todos, setTodos] = useState([]);
  const [input, setInput] = useState('');
  
  const addTodo = () => {
    if (input.trim()) {
      setTodos([...todos, input]);  // Add new item
      setInput('');                  // Clear input
    }
  };
  
  const removeTodo = (index) => {
    setTodos(todos.filter((_, i) => i !== index));  // Remove item
  };
  
  return (
    <div>
      <input
        type="text"
        value={input}
        onChange={(e) => setInput(e.target.value)}
        onKeyPress={(e) => e.key === 'Enter' && addTodo()}
      />
      <button onClick={addTodo}>Add</button>
      
      <ul>
        {todos.map((todo, index) => (
          <li key={index}>
            {todo}
            <button onClick={() => removeTodo(index)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

---

## 🔄 Updating State Correctly

### ❌ Common Mistakes

```jsx
// ❌ Wrong - Mutating state directly
const [user, setUser] = useState({ name: 'Alice' });
user.name = 'Bob';  // Don't do this!
setUser(user);      // React won't detect the change

// ✅ Right - Create new object
setUser({ ...user, name: 'Bob' });

// ❌ Wrong - Mutating array
const [items, setItems] = useState([1, 2, 3]);
items.push(4);  // Don't do this!
setItems(items);

// ✅ Right - Create new array
setItems([...items, 4]);
```

### Functional Updates

When the new state depends on the previous state, use a function:

```jsx
// ❌ Wrong - Might use stale state
function Counter() {
  const [count, setCount] = useState(0);
  
  const incrementTwice = () => {
    setCount(count + 1);  // count might be stale
    setCount(count + 1);  // Same value, only increments once!
  };
}

// ✅ Right - Use functional update
function Counter() {
  const [count, setCount] = useState(0);
  
  const incrementTwice = () => {
    setCount(prev => prev + 1);  // Always uses latest state
    setCount(prev => prev + 1);  // Increments twice correctly
  };
}
```

**When to use functional updates:**
- State updates based on previous state
- Multiple rapid updates
- Updates in closures (callbacks, effects)

---

## 🎯 Multiple State Variables

You can have multiple `useState` calls in one component:

```jsx
function Form() {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [age, setAge] = useState(0);
  const [isSubmitting, setIsSubmitting] = useState(false);
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    setIsSubmitting(true);
    
    // Simulate API call
    await new Promise(resolve => setTimeout(resolve, 1000));
    
    console.log({ name, email, age });
    setIsSubmitting(false);
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Name"
      />
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email"
      />
      <input
        type="number"
        value={age}
        onChange={(e) => setAge(parseInt(e.target.value))}
        placeholder="Age"
      />
      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Submitting...' : 'Submit'}
      </button>
    </form>
  );
}
```

**When to combine vs separate:**
- **Separate**: Values are independent (name, email, age)
- **Combine**: Values are related and updated together (user object)

---

## 📝 Hands-On Tutorial: Building a Shopping Cart

### Step 1: Product List with Add to Cart

```jsx
function ProductList({ products, onAddToCart }) {
  return (
    <div>
      <h2>Products</h2>
      {products.map(product => (
        <div key={product.id} className="product">
          <h3>{product.name}</h3>
          <p>${product.price}</p>
          <button onClick={() => onAddToCart(product)}>
            Add to Cart
          </button>
        </div>
      ))}
    </div>
  );
}
```

### Step 2: Shopping Cart Component

```jsx
function ShoppingCart({ cart, onRemove, onUpdateQuantity }) {
  const total = cart.reduce((sum, item) => 
    sum + (item.price * item.quantity), 0
  );
  
  return (
    <div className="cart">
      <h2>Shopping Cart</h2>
      {cart.length === 0 ? (
        <p>Your cart is empty</p>
      ) : (
        <>
          {cart.map(item => (
            <div key={item.id} className="cart-item">
              <span>{item.name}</span>
              <span>${item.price}</span>
              <input
                type="number"
                min="1"
                value={item.quantity}
                onChange={(e) => onUpdateQuantity(
                  item.id, 
                  parseInt(e.target.value)
                )}
              />
              <button onClick={() => onRemove(item.id)}>Remove</button>
            </div>
          ))}
          <div className="total">
            <strong>Total: ${total.toFixed(2)}</strong>
          </div>
        </>
      )}
    </div>
  );
}
```

### Step 3: Main App with State Management

```jsx
function App() {
  const [cart, setCart] = useState([]);
  
  const products = [
    { id: 1, name: "Laptop", price: 999 },
    { id: 2, name: "Phone", price: 699 },
    { id: 3, name: "Tablet", price: 399 }
  ];
  
  const addToCart = (product) => {
    setCart(prevCart => {
      const existingItem = prevCart.find(item => item.id === product.id);
      
      if (existingItem) {
        // Update quantity if item exists
        return prevCart.map(item =>
          item.id === product.id
            ? { ...item, quantity: item.quantity + 1 }
            : item
        );
      } else {
        // Add new item
        return [...prevCart, { ...product, quantity: 1 }];
      }
    });
  };
  
  const removeFromCart = (productId) => {
    setCart(prevCart => prevCart.filter(item => item.id !== productId));
  };
  
  const updateQuantity = (productId, quantity) => {
    if (quantity <= 0) {
      removeFromCart(productId);
      return;
    }
    
    setCart(prevCart =>
      prevCart.map(item =>
        item.id === productId
          ? { ...item, quantity }
          : item
      )
    );
  };
  
  return (
    <div className="app">
      <ProductList products={products} onAddToCart={addToCart} />
      <ShoppingCart
        cart={cart}
        onRemove={removeFromCart}
        onUpdateQuantity={updateQuantity}
      />
    </div>
  );
}
```

---

## 🎯 Practice Exercises

### Exercise 1: Like Counter
Create a component with:
- A like button
- A counter showing number of likes
- A heart icon that changes color when liked
- Ability to unlike

### Exercise 2: Color Picker
Create a component that:
- Has three sliders (red, green, blue)
- Shows a color preview
- Displays the RGB values
- Has a random color button

### Exercise 3: Todo App
Build a todo app with:
- Add new todos
- Mark todos as complete
- Delete todos
- Filter (all, active, completed)
- Count of remaining todos

---

## ❌ Common Mistakes

1. **Mutating state directly:**
   ```jsx
   // ❌ Wrong
   const [items, setItems] = useState([1, 2, 3]);
   items.push(4);
   setItems(items);
   
   // ✅ Right
   setItems([...items, 4]);
   ```

2. **Using stale state in updates:**
   ```jsx
   // ❌ Wrong
   setCount(count + 1);
   setCount(count + 1);  // Only increments once
   
   // ✅ Right
   setCount(prev => prev + 1);
   setCount(prev => prev + 1);  // Increments twice
   ```

3. **Forgetting to spread objects:**
   ```jsx
   // ❌ Wrong
   setUser({ name: 'Bob' });  // Loses other properties
   
   // ✅ Right
   setUser({ ...user, name: 'Bob' });
   ```

---

## 🚀 Real-World Application

State is used for:
- **Forms**: Input values, validation errors
- **UI State**: Modals, dropdowns, tabs
- **User Data**: Shopping carts, favorites, settings
- **Application Data**: Lists, filters, search results

---

## 📚 Key Takeaways

1. State is data that can change
2. Use `useState` hook to add state
3. Always update state immutably (create new objects/arrays)
4. Use functional updates when state depends on previous state
5. You can have multiple `useState` calls
6. State changes trigger re-renders

---

## 🔗 Next Steps

- ✅ Complete all practice exercises
- ✅ Build the shopping cart example
- ✅ Experiment with different state types
- ✅ Read `learning-modules/04-events-forms.md` next

---

## 💡 Reflection Questions

1. Can I explain the difference between props and state?
2. Do I know when to use functional state updates?
3. Can I update objects and arrays immutably?
4. Do I understand when to use multiple state variables?

If yes to all, you're ready for events and forms! 🎉


# Module 06 · Conditional Rendering & Lists

## 🎯 Learning Objectives
By the end of this module, you will:
- Render components conditionally
- Display lists of data correctly
- Use keys properly in lists
- Handle empty states
- Filter and transform data before rendering
- Avoid common list rendering mistakes

---

## 🔀 Conditional Rendering

Conditional rendering shows different UI based on conditions.

### Method 1: Ternary Operator

```jsx
function UserGreeting({ isLoggedIn }) {
  return (
    <div>
      {isLoggedIn ? (
        <h1>Welcome back!</h1>
      ) : (
        <h1>Please log in</h1>
      )}
    </div>
  );
}
```

### Method 2: Logical AND (&&)

```jsx
function Notification({ message, show }) {
  return (
    <div>
      {show && <div className="notification">{message}</div>}
    </div>
  );
}

// Usage
<Notification message="Success!" show={true} />
<Notification message="Error" show={false} />
```

**⚠️ Warning:** Be careful with falsy values!
```jsx
// ❌ Problem - 0 renders as 0
{count && <div>Count: {count}</div>}

// ✅ Fix - Use boolean
{count > 0 && <div>Count: {count}</div>}
```

### Method 3: Early Return

```jsx
function UserProfile({ user }) {
  if (!user) {
    return <div>Loading...</div>;
  }
  
  if (user.error) {
    return <div>Error loading user</div>;
  }
  
  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  );
}
```

### Method 4: Switch-like Pattern

```jsx
function StatusBadge({ status }) {
  const getStatusComponent = () => {
    switch (status) {
      case 'pending':
        return <span className="badge pending">Pending</span>;
      case 'approved':
        return <span className="badge approved">Approved</span>;
      case 'rejected':
        return <span className="badge rejected">Rejected</span>;
      default:
        return <span className="badge unknown">Unknown</span>;
    }
  };
  
  return getStatusComponent();
}
```

### Method 5: Variable Assignment

```jsx
function ProductCard({ product }) {
  let badge = null;
  
  if (product.isNew) {
    badge = <span className="badge new">New</span>;
  } else if (product.onSale) {
    badge = <span className="badge sale">Sale</span>;
  }
  
  return (
    <div className="product-card">
      {badge}
      <h3>{product.name}</h3>
      <p>${product.price}</p>
    </div>
  );
}
```

---

## 📋 Rendering Lists

### Basic List

```jsx
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>{todo.text}</li>
      ))}
    </ul>
  );
}
```

### List with Index (When No ID)

```jsx
function NumberList({ numbers }) {
  return (
    <ul>
      {numbers.map((number, index) => (
        <li key={index}>{number}</li>
      ))}
    </ul>
  );
}
```

**⚠️ Note:** Using index as key is okay if:
- List is static (doesn't reorder)
- Items don't have unique IDs
- Items don't change

### Complex List Items

```jsx
function ProductList({ products }) {
  return (
    <div className="product-list">
      {products.map(product => (
        <div key={product.id} className="product-card">
          <img src={product.image} alt={product.name} />
          <h3>{product.name}</h3>
          <p className="price">${product.price}</p>
          {product.onSale && (
            <span className="badge">On Sale!</span>
          )}
          <button>Add to Cart</button>
        </div>
      ))}
    </div>
  );
}
```

---

## 🔑 Understanding Keys

**Keys** help React identify which items changed, were added, or removed.

### Why Keys Matter

```jsx
// ❌ Wrong - No keys
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map(todo => (
        <li>{todo.text}</li>  // React warning!
      ))}
    </ul>
  );
}

// ✅ Right - With keys
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>{todo.text}</li>
      ))}
    </ul>
  );
}
```

### Key Rules

1. **Keys must be unique** among siblings
2. **Keys should be stable** (don't change between renders)
3. **Keys should be predictable** (not random)

```jsx
// ❌ Wrong - Random keys
{todos.map(todo => (
  <li key={Math.random()}>{todo.text}</li>
))}

// ❌ Wrong - Index when items can reorder
{todos.map((todo, index) => (
  <li key={index}>{todo.text}</li>
))}

// ✅ Right - Stable, unique ID
{todos.map(todo => (
  <li key={todo.id}>{todo.text}</li>
))}
```

---

## 🎨 Filtering Lists

### Filter Before Render

```jsx
function TodoList({ todos, filter }) {
  const filteredTodos = todos.filter(todo => {
    if (filter === 'active') return !todo.completed;
    if (filter === 'completed') return todo.completed;
    return true; // 'all'
  });
  
  return (
    <ul>
      {filteredTodos.map(todo => (
        <li key={todo.id}>{todo.text}</li>
      ))}
    </ul>
  );
}
```

### Search Filter

```jsx
function ProductSearch({ products, searchTerm }) {
  const filteredProducts = products.filter(product =>
    product.name.toLowerCase().includes(searchTerm.toLowerCase())
  );
  
  return (
    <div>
      {filteredProducts.length === 0 ? (
        <p>No products found</p>
      ) : (
        filteredProducts.map(product => (
          <div key={product.id}>{product.name}</div>
        ))
      )}
    </div>
  );
}
```

---

## 🎯 Empty States

Always handle empty lists gracefully:

```jsx
function TodoList({ todos }) {
  if (todos.length === 0) {
    return (
      <div className="empty-state">
        <p>No todos yet. Add one to get started!</p>
      </div>
    );
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

### Loading States

```jsx
function UserList({ users, loading, error }) {
  if (loading) {
    return <div>Loading users...</div>;
  }
  
  if (error) {
    return <div>Error: {error}</div>;
  }
  
  if (users.length === 0) {
    return <div>No users found</div>;
  }
  
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

## 📝 Hands-On Tutorial: Building a Filterable Product List

### Step 1: Product List Component

```jsx
function ProductList({ products, category, searchTerm }) {
  // Filter by category
  let filtered = products.filter(product => {
    if (category && category !== 'all') {
      return product.category === category;
    }
    return true;
  });
  
  // Filter by search term
  if (searchTerm) {
    filtered = filtered.filter(product =>
      product.name.toLowerCase().includes(searchTerm.toLowerCase())
    );
  }
  
  // Empty state
  if (filtered.length === 0) {
    return (
      <div className="empty-state">
        <p>No products found</p>
        <p>Try adjusting your filters</p>
      </div>
    );
  }
  
  // Render list
  return (
    <div className="product-grid">
      {filtered.map(product => (
        <div key={product.id} className="product-card">
          {product.onSale && (
            <span className="badge sale">Sale</span>
          )}
          <img src={product.image} alt={product.name} />
          <h3>{product.name}</h3>
          <p className="price">${product.price}</p>
          <button>Add to Cart</button>
        </div>
      ))}
    </div>
  );
}
```

### Step 2: Complete Filterable List App

```jsx
function ProductApp() {
  const [products] = useState([
    { id: 1, name: "Laptop", category: "electronics", price: 999, onSale: false },
    { id: 2, name: "T-Shirt", category: "clothing", price: 20, onSale: true },
    { id: 3, name: "Phone", category: "electronics", price: 699, onSale: false },
    { id: 4, name: "Jeans", category: "clothing", price: 50, onSale: true },
  ]);
  
  const [category, setCategory] = useState('all');
  const [searchTerm, setSearchTerm] = useState('');
  
  return (
    <div className="product-app">
      <div className="filters">
        <input
          type="text"
          placeholder="Search products..."
          value={searchTerm}
          onChange={(e) => setSearchTerm(e.target.value)}
        />
        <select
          value={category}
          onChange={(e) => setCategory(e.target.value)}
        >
          <option value="all">All Categories</option>
          <option value="electronics">Electronics</option>
          <option value="clothing">Clothing</option>
        </select>
      </div>
      
      <ProductList
        products={products}
        category={category}
        searchTerm={searchTerm}
      />
    </div>
  );
}
```

---

## 🎯 Practice Exercises

### Exercise 1: User Directory
Create a component that:
- Displays a list of users
- Filters by role (admin, user, guest)
- Searches by name
- Shows "No users found" when empty
- Highlights active users

### Exercise 2: Todo App with Filters
Build a todo app with:
- Add/remove todos
- Filter: All, Active, Completed
- Search todos
- Show count of active todos
- Clear completed button

### Exercise 3: Product Catalog
Create a product catalog that:
- Displays products in a grid
- Filters by category and price range
- Sorts by price (low to high, high to low)
- Shows product count
- Empty state when no matches

---

## ❌ Common Mistakes

1. **Missing keys:**
   ```jsx
   // ❌ Wrong
   {items.map(item => <div>{item.name}</div>)}
   
   // ✅ Right
   {items.map(item => <div key={item.id}>{item.name}</div>)}
   ```

2. **Using index as key when items can reorder:**
   ```jsx
   // ❌ Wrong - Breaks when reordering
   {todos.map((todo, index) => (
     <TodoItem key={index} todo={todo} />
   ))}
   
   // ✅ Right
   {todos.map(todo => (
     <TodoItem key={todo.id} todo={todo} />
   ))}
   ```

3. **Not handling empty states:**
   ```jsx
   // ❌ Wrong - Shows nothing
   {items.map(item => <div>{item.name}</div>)}
   
   // ✅ Right
   {items.length === 0 ? (
     <p>No items</p>
   ) : (
     items.map(item => <div key={item.id}>{item.name}</div>)
   )}
   ```

---

## 🚀 Real-World Application

Conditional rendering and lists are used for:
- **E-commerce**: Product catalogs, search results
- **Social Media**: News feeds, comment threads
- **Dashboards**: Data tables, metric cards
- **Forms**: Dynamic form fields, validation messages

---

## 📚 Key Takeaways

1. Use ternary or && for simple conditionals
2. Use early return for complex conditionals
3. Always provide keys in lists
4. Keys should be stable and unique
5. Filter data before rendering
6. Always handle empty states
7. Combine filtering and searching

---

## 🔗 Next Steps

- ✅ Complete all practice exercises
- ✅ Build the filterable product list
- ✅ Practice with different conditional patterns
- ✅ Read `learning-modules/07-context-api.md` next

---

## 💡 Reflection Questions

1. Can I render components conditionally?
2. Do I know when to use keys in lists?
3. Can I filter and search lists?
4. Do I handle empty states properly?

If yes to all, you're ready for Context API! 🎉


# Props Patterns Best Practices

## 📋 Table of Contents
- [Prop Types](#prop-types)
- [Default Props](#default-props)
- [Prop Destructuring](#prop-destructuring)
- [Children Prop](#children-prop)
- [Render Props](#render-props)
- [Prop Drilling Solutions](#prop-drilling-solutions)

---

## 🎯 Prop Types

### ✅ Good: Define Prop Types

```jsx
import PropTypes from 'prop-types';

function UserCard({ user, onFollow, showAvatar }) {
  return (
    <div>
      {showAvatar && <img src={user.avatar} alt={user.name} />}
      <h3>{user.name}</h3>
      <button onClick={() => onFollow(user.id)}>Follow</button>
    </div>
  );
}

UserCard.propTypes = {
  user: PropTypes.shape({
    id: PropTypes.string.isRequired,
    name: PropTypes.string.isRequired,
    avatar: PropTypes.string,
  }).isRequired,
  onFollow: PropTypes.func.isRequired,
  showAvatar: PropTypes.bool,
};

UserCard.defaultProps = {
  showAvatar: true,
};
```

### ✅ Good: TypeScript Alternative

```tsx
interface UserCardProps {
  user: {
    id: string;
    name: string;
    avatar?: string;
  };
  onFollow: (id: string) => void;
  showAvatar?: boolean;
}

function UserCard({ user, onFollow, showAvatar = true }: UserCardProps) {
  // ...
}
```

---

## 🎁 Default Props

### ✅ Good: Provide Sensible Defaults

```jsx
function Button({ 
  children, 
  variant = 'primary', 
  size = 'medium',
  disabled = false,
  onClick 
}) {
  return (
    <button
      className={`btn btn-${variant} btn-${size}`}
      disabled={disabled}
      onClick={onClick}
    >
      {children}
    </button>
  );
}
```

### ❌ Bad: No Defaults for Optional Props

```jsx
// ❌ Bad - Undefined can cause issues
function Button({ variant, size, onClick }) {
  return (
    <button className={`btn-${variant} btn-${size}`} onClick={onClick}>
      Click
    </button>
  );
}

// If variant is undefined, className becomes "btn-undefined"
```

---

## 📦 Prop Destructuring

### ✅ Good: Destructure in Parameters

```jsx
// ✅ Good - Clean and readable
function UserCard({ user, onFollow, showAvatar = true }) {
  return (
    <div>
      <h3>{user.name}</h3>
      <button onClick={() => onFollow(user.id)}>Follow</button>
    </div>
  );
}
```

### ✅ Good: Destructure Complex Props

```jsx
// ✅ Good - Destructure nested props
function ProductCard({ 
  product: { name, price, image },
  onAddToCart 
}) {
  return (
    <div>
      <img src={image} alt={name} />
      <h3>{name}</h3>
      <p>${price}</p>
      <button onClick={onAddToCart}>Add to Cart</button>
    </div>
  );
}
```

### ❌ Bad: Accessing Props Directly

```jsx
// ❌ Bad - Less readable
function UserCard(props) {
  return (
    <div>
      <h3>{props.user.name}</h3>
      <button onClick={() => props.onFollow(props.user.id)}>Follow</button>
    </div>
  );
}
```

---

## 👶 Children Prop

### ✅ Good: Using Children

```jsx
// ✅ Good - Flexible component with children
function Card({ title, children }) {
  return (
    <div className="card">
      {title && <h2>{title}</h2>}
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

### ✅ Good: Multiple Children Slots

```jsx
// ✅ Good - Named children slots
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
  footer={<p>Copyright</p>}
/>
```

---

## 🎨 Render Props Pattern

### ✅ Good: Render Props for Flexibility

```jsx
// ✅ Good - Render prop pattern
function DataFetcher({ url, children }) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [url]);
  
  return children({ data, loading, error });
}

// Usage
<DataFetcher url="/api/users">
  {({ data, loading, error }) => {
    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;
    return <UserList users={data} />;
  }}
</DataFetcher>
```

---

## 🚫 Prop Drilling Solutions

### ❌ Bad: Prop Drilling

```jsx
// ❌ Bad - Passing props through many levels
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

### ✅ Good: Context Solution

```jsx
// ✅ Good - Use Context
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

## 🎯 Prop Patterns

### ✅ Good: Compound Components

```jsx
// ✅ Good - Compound component pattern
function Tabs({ children }) {
  const [activeTab, setActiveTab] = useState(0);
  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      {children}
    </TabsContext.Provider>
  );
}

function TabsList({ children }) {
  return <div className="tabs-list">{children}</div>;
}

function Tab({ index, children }) {
  const { activeTab, setActiveTab } = useContext(TabsContext);
  return (
    <button
      className={activeTab === index ? 'active' : ''}
      onClick={() => setActiveTab(index)}
    >
      {children}
    </button>
  );
}

// Usage
<Tabs>
  <TabsList>
    <Tab index={0}>Tab 1</Tab>
    <Tab index={1}>Tab 2</Tab>
  </TabsList>
</Tabs>
```

### ✅ Good: Controlled vs Uncontrolled

```jsx
// ✅ Good - Controlled component
function ControlledInput({ value, onChange }) {
  return (
    <input
      value={value}
      onChange={(e) => onChange(e.target.value)}
    />
  );
}

// ✅ Good - Uncontrolled component
function UncontrolledInput({ defaultValue, onBlur }) {
  const inputRef = useRef();
  
  const handleBlur = () => {
    onBlur?.(inputRef.current.value);
  };
  
  return (
    <input
      ref={inputRef}
      defaultValue={defaultValue}
      onBlur={handleBlur}
    />
  );
}
```

---

## 🔄 Prop Updates

### ✅ Good: Memoizing Callbacks

```jsx
// ✅ Good - Memoize callbacks passed as props
function Parent() {
  const [count, setCount] = useState(0);
  
  const handleClick = useCallback(() => {
    console.log('Clicked');
  }, []); // Empty deps = stable reference
  
  return <Child onClick={handleClick} />;
}

function Child({ onClick }) {
  return <button onClick={onClick}>Click</button>;
}
```

### ❌ Bad: Creating Functions in Render

```jsx
// ❌ Bad - New function on every render
function Parent() {
  const [count, setCount] = useState(0);
  
  return (
    <Child 
      onClick={() => console.log('Clicked')} // New function each render
    />
  );
}
```

---

## 📚 Key Takeaways

1. **Define prop types** - Use PropTypes or TypeScript
2. **Provide defaults** - Make optional props safe
3. **Destructure props** - Cleaner and more readable
4. **Use children** - Make components flexible
5. **Avoid prop drilling** - Use Context when needed
6. **Memoize callbacks** - Prevent unnecessary re-renders
7. **Choose controlled/uncontrolled** - Based on use case

---

## ✅ Props Checklist

Before finalizing props:

- [ ] Are prop types defined?
- [ ] Do optional props have defaults?
- [ ] Are props destructured in parameters?
- [ ] Is children prop used when appropriate?
- [ ] Are callbacks memoized?
- [ ] Is prop drilling avoided (use Context if needed)?
- [ ] Are props named clearly and consistently?

---

**Remember:** Good prop patterns make components reusable and maintainable!


# Performance Optimization Best Practices

## 📋 Table of Contents
- [React.memo](#reactmemo)
- [useMemo](#usememo)
- [useCallback](#usecallback)
- [Code Splitting](#code-splitting)
- [Virtualization](#virtualization)
- [Image Optimization](#image-optimization)

---

## 🎯 React.memo

### ✅ Good: Memoize Expensive Components

```jsx
// ✅ Good - Memoize when props rarely change
const ExpensiveComponent = React.memo(function ExpensiveComponent({ data }) {
  // Expensive rendering logic
  return <div>{/* complex UI */}</div>;
});

// Only re-renders when data prop changes
```

### ✅ Good: Custom Comparison

```jsx
// ✅ Good - Custom comparison function
const UserCard = React.memo(
  function UserCard({ user, onFollow }) {
    return (
      <div>
        <h3>{user.name}</h3>
        <button onClick={() => onFollow(user.id)}>Follow</button>
      </div>
    );
  },
  (prevProps, nextProps) => {
    // Only re-render if user.id changes
    return prevProps.user.id === nextProps.user.id;
  }
);
```

### ❌ Bad: Overusing React.memo

```jsx
// ❌ Bad - Memoizing simple components
const SimpleButton = React.memo(function SimpleButton({ onClick, children }) {
  return <button onClick={onClick}>{children}</button>;
});

// Simple components don't benefit from memoization
// The comparison cost might exceed render cost
```

---

## 💾 useMemo

### ✅ Good: Memoize Expensive Calculations

```jsx
// ✅ Good - Expensive calculation
function ProductList({ products, filter }) {
  const filteredProducts = useMemo(() => {
    return products.filter(product => {
      // Expensive filtering logic
      return product.name.toLowerCase().includes(filter.toLowerCase());
    });
  }, [products, filter]); // Only recalculate when deps change
  
  return (
    <div>
      {filteredProducts.map(product => (
        <ProductCard key={product.id} product={product} />
      ))}
    </div>
  );
}
```

### ✅ Good: Memoize Object/Array Props

```jsx
// ✅ Good - Stable object reference
function Parent() {
  const [count, setCount] = useState(0);
  
  const config = useMemo(() => ({
    theme: 'dark',
    size: 'large',
    enabled: true
  }), []); // Stable reference
  
  return <Child config={config} />;
}
```

### ❌ Bad: Memoizing Simple Values

```jsx
// ❌ Bad - Simple calculation doesn't need memoization
function Component({ a, b }) {
  const sum = useMemo(() => a + b, [a, b]); // Unnecessary!
  return <div>{sum}</div>;
}

// ✅ Better - Just calculate directly
function Component({ a, b }) {
  const sum = a + b;
  return <div>{sum}</div>;
}
```

---

## 🎣 useCallback

### ✅ Good: Memoize Callbacks Passed to Children

```jsx
// ✅ Good - Stable callback reference
function Parent() {
  const [count, setCount] = useState(0);
  const [items, setItems] = useState([]);
  
  const handleAdd = useCallback((item) => {
    setItems(prev => [...prev, item]);
  }, []); // Stable reference
  
  return (
    <div>
      <Child onAdd={handleAdd} />
      <p>Count: {count}</p>
    </div>
  );
}

// Child won't re-render when count changes
const Child = React.memo(function Child({ onAdd }) {
  return <button onClick={() => onAdd('new item')}>Add</button>;
});
```

### ❌ Bad: Unnecessary useCallback

```jsx
// ❌ Bad - Callback not passed to memoized component
function Parent() {
  const handleClick = useCallback(() => {
    console.log('clicked');
  }, []);
  
  return <button onClick={handleClick}>Click</button>;
  // No benefit - button isn't memoized
}
```

---

## 📦 Code Splitting

### ✅ Good: Lazy Loading Routes

```jsx
// ✅ Good - Lazy load routes
import { lazy, Suspense } from 'react';

const Home = lazy(() => import('./pages/Home'));
const About = lazy(() => import('./pages/About'));
const Contact = lazy(() => import('./pages/Contact'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
      </Routes>
    </Suspense>
  );
}
```

### ✅ Good: Lazy Load Heavy Components

```jsx
// ✅ Good - Lazy load heavy components
const Chart = lazy(() => import('./components/Chart'));

function Dashboard() {
  const [showChart, setShowChart] = useState(false);
  
  return (
    <div>
      <button onClick={() => setShowChart(true)}>Show Chart</button>
      {showChart && (
        <Suspense fallback={<div>Loading chart...</div>}>
          <Chart />
        </Suspense>
      )}
    </div>
  );
}
```

---

## 📊 Virtualization

### ✅ Good: Virtualize Long Lists

```jsx
// ✅ Good - Use react-window for long lists
import { FixedSizeList } from 'react-window';

function ProductList({ products }) {
  const Row = ({ index, style }) => (
    <div style={style}>
      <ProductCard product={products[index]} />
    </div>
  );
  
  return (
    <FixedSizeList
      height={600}
      itemCount={products.length}
      itemSize={200}
      width="100%"
    >
      {Row}
    </FixedSizeList>
  );
}
```

### ❌ Bad: Rendering All Items

```jsx
// ❌ Bad - Renders all 10,000 items
function ProductList({ products }) {
  return (
    <div>
      {products.map(product => (
        <ProductCard key={product.id} product={product} />
      ))}
    </div>
  );
}
// Causes performance issues with large lists
```

---

## 🖼️ Image Optimization

### ✅ Good: Lazy Load Images

```jsx
// ✅ Good - Lazy load images
function Image({ src, alt }) {
  const [isLoaded, setIsLoaded] = useState(false);
  const imgRef = useRef();
  
  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setIsLoaded(true);
          observer.disconnect();
        }
      },
      { threshold: 0.1 }
    );
    
    if (imgRef.current) {
      observer.observe(imgRef.current);
    }
    
    return () => observer.disconnect();
  }, []);
  
  return (
    <div ref={imgRef}>
      {isLoaded && <img src={src} alt={alt} />}
    </div>
  );
}
```

### ✅ Good: Use Appropriate Image Sizes

```jsx
// ✅ Good - Responsive images
function ResponsiveImage({ src, alt }) {
  return (
    <img
      src={src}
      srcSet={`${src}?w=400 400w, ${src}?w=800 800w, ${src}?w=1200 1200w`}
      sizes="(max-width: 600px) 400px, (max-width: 1200px) 800px, 1200px"
      alt={alt}
      loading="lazy"
    />
  );
}
```

---

## 🚀 Bundle Optimization

### ✅ Good: Tree Shaking

```jsx
// ✅ Good - Import only what you need
import { useState, useEffect } from 'react';
import { debounce } from 'lodash-es'; // ES modules support tree shaking

// ❌ Bad - Import entire library
import _ from 'lodash'; // Imports everything
```

### ✅ Good: Analyze Bundle

```bash
# Install bundle analyzer
npm install --save-dev webpack-bundle-analyzer

# Analyze bundle size
npm run build
npx webpack-bundle-analyzer build/static/js/*.js
```

---

## ⚡ Rendering Optimization

### ✅ Good: Avoid Unnecessary Re-renders

```jsx
// ✅ Good - Split components to prevent re-renders
function App() {
  const [count, setCount] = useState(0);
  const [user, setUser] = useState({ name: 'Alice' });
  
  return (
    <div>
      <Counter count={count} onIncrement={() => setCount(c => c + 1)} />
      <UserProfile user={user} />
    </div>
  );
}

// Counter only re-renders when count changes
// UserProfile only re-renders when user changes
```

### ❌ Bad: Everything in One Component

```jsx
// ❌ Bad - Entire component re-renders
function App() {
  const [count, setCount] = useState(0);
  const [user, setUser] = useState({ name: 'Alice' });
  
  return (
    <div>
      <button onClick={() => setCount(c => c + 1)}>{count}</button>
      <div>{user.name}</div>
    </div>
  );
  // Both re-render when either state changes
}
```

---

## 📚 Key Takeaways

1. **Measure first** - Use React DevTools Profiler
2. **Memoize wisely** - Only when there's actual benefit
3. **Code split** - Lazy load routes and heavy components
4. **Virtualize lists** - For long lists (1000+ items)
5. **Optimize images** - Lazy load and use appropriate sizes
6. **Avoid premature optimization** - Profile before optimizing
7. **Split components** - Prevent unnecessary re-renders

---

## ✅ Performance Checklist

Before optimizing:

- [ ] Have you measured the performance issue?
- [ ] Is the component actually slow?
- [ ] Are you memoizing unnecessarily?
- [ ] Have you tried code splitting?
- [ ] Are images optimized?
- [ ] Are long lists virtualized?
- [ ] Is the bundle size reasonable?

---

## 🎯 When to Optimize

**Optimize when:**
- ✅ Profiler shows slow renders
- ✅ Users report lag
- ✅ Bundle size is large
- ✅ Lists have 1000+ items

**Don't optimize when:**
- ❌ No performance issues
- ❌ Premature optimization
- ❌ Simple components
- ❌ Small datasets

---

**Remember:** Measure first, optimize second. Premature optimization is the root of all evil!


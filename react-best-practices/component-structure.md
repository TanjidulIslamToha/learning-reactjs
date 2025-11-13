# Component Structure Best Practices

## 📋 Table of Contents
- [Component Organization](#component-organization)
- [File Structure](#file-structure)
- [Component Size](#component-size)
- [Single Responsibility](#single-responsibility)
- [Code Ordering](#code-ordering)

---

## 🏗️ Component Organization

### ✅ Good: Small, Focused Components

```jsx
// ✅ Good - Small, focused component
function UserAvatar({ user, size = 'medium' }) {
  return (
    <img
      src={user.avatar}
      alt={user.name}
      className={`avatar avatar-${size}`}
    />
  );
}

function UserName({ user }) {
  return <h3>{user.name}</h3>;
}

function UserCard({ user }) {
  return (
    <div className="user-card">
      <UserAvatar user={user} />
      <UserName user={user} />
    </div>
  );
}
```

### ❌ Bad: Large, Monolithic Component

```jsx
// ❌ Bad - Too much responsibility
function UserCard({ user }) {
  return (
    <div className="user-card">
      <img src={user.avatar} alt={user.name} className="avatar" />
      <h3>{user.name}</h3>
      <p>{user.email}</p>
      <p>{user.bio}</p>
      <div className="stats">
        <span>Posts: {user.posts.length}</span>
        <span>Followers: {user.followers}</span>
        <span>Following: {user.following}</span>
      </div>
      <button onClick={() => handleFollow(user.id)}>Follow</button>
      <button onClick={() => handleMessage(user.id)}>Message</button>
    </div>
  );
}
```

---

## 📁 File Structure

### Recommended Structure

```
components/
  UserCard/
    UserCard.js
    UserCard.css
    UserCard.test.js
    index.js
```

### Component File Template

```jsx
// UserCard.js
import React from 'react';
import PropTypes from 'prop-types';
import './UserCard.css';

/**
 * UserCard component displays user information
 * @param {Object} user - User object with name, email, avatar
 * @param {Function} onFollow - Callback when follow button is clicked
 */
function UserCard({ user, onFollow }) {
  // 1. Hooks
  const [isFollowing, setIsFollowing] = React.useState(false);
  
  // 2. Event handlers
  const handleFollow = () => {
    setIsFollowing(!isFollowing);
    onFollow?.(user.id);
  };
  
  // 3. Computed values
  const followButtonText = isFollowing ? 'Unfollow' : 'Follow';
  
  // 4. Early returns
  if (!user) return null;
  
  // 5. Render
  return (
    <div className="user-card">
      <img src={user.avatar} alt={user.name} />
      <h3>{user.name}</h3>
      <button onClick={handleFollow}>{followButtonText}</button>
    </div>
  );
}

// 6. PropTypes
UserCard.propTypes = {
  user: PropTypes.shape({
    id: PropTypes.string.isRequired,
    name: PropTypes.string.isRequired,
    email: PropTypes.string.isRequired,
    avatar: PropTypes.string.isRequired,
  }).isRequired,
  onFollow: PropTypes.func,
};

// 7. Default props
UserCard.defaultProps = {
  onFollow: () => {},
};

export default UserCard;
```

### Index File Pattern

```jsx
// index.js
export { default } from './UserCard';
export { default as UserCardSkeleton } from './UserCardSkeleton';
```

---

## 📏 Component Size

### ✅ Good: 50-200 Lines

```jsx
// ✅ Good - ~80 lines, clear and focused
function ProductCard({ product, onAddToCart }) {
  const [isHovered, setIsHovered] = useState(false);
  
  const handleAddToCart = () => {
    onAddToCart(product.id);
  };
  
  return (
    <div
      className="product-card"
      onMouseEnter={() => setIsHovered(true)}
      onMouseLeave={() => setIsHovered(false)}
    >
      <img src={product.image} alt={product.name} />
      <h3>{product.name}</h3>
      <p className="price">${product.price}</p>
      <button onClick={handleAddToCart}>Add to Cart</button>
    </div>
  );
}
```

### ❌ Bad: 500+ Lines

```jsx
// ❌ Bad - Too large, hard to maintain
function ProductCard({ product, onAddToCart, onWishlist, onCompare, ... }) {
  // 200+ lines of logic
  // Multiple responsibilities
  // Hard to test
  // Hard to understand
}
```

**Rule of Thumb:** If a component exceeds 200 lines, consider splitting it.

---

## 🎯 Single Responsibility Principle

### ✅ Good: One Responsibility

```jsx
// ✅ Good - Only handles user display
function UserProfile({ user }) {
  return (
    <div>
      <img src={user.avatar} alt={user.name} />
      <h2>{user.name}</h2>
      <p>{user.bio}</p>
    </div>
  );
}

// ✅ Good - Only handles user editing
function UserEditForm({ user, onSave }) {
  const [formData, setFormData] = useState(user);
  
  const handleSubmit = (e) => {
    e.preventDefault();
    onSave(formData);
  };
  
  return <form onSubmit={handleSubmit}>...</form>;
}
```

### ❌ Bad: Multiple Responsibilities

```jsx
// ❌ Bad - Does too many things
function UserComponent({ user }) {
  // Displays user
  // Edits user
  // Fetches user data
  // Handles authentication
  // Manages user settings
  // ... too much!
}
```

---

## 📝 Code Ordering

### Recommended Order

```jsx
function Component({ props }) {
  // 1. Imports (if any local imports needed)
  
  // 2. Constants
  const MAX_ITEMS = 10;
  
  // 3. Hooks (useState, useEffect, custom hooks)
  const [state, setState] = useState();
  const { data } = useCustomHook();
  
  // 4. Derived state / Computed values
  const filteredItems = items.filter(item => item.active);
  
  // 5. Event handlers
  const handleClick = () => {
    // ...
  };
  
  // 6. Effects
  useEffect(() => {
    // ...
  }, []);
  
  // 7. Early returns
  if (loading) return <Loading />;
  if (error) return <Error />;
  
  // 8. Render
  return (
    <div>
      {/* JSX */}
    </div>
  );
}
```

---

## 🎨 Component Composition

### ✅ Good: Compose Small Components

```jsx
function ProductCard({ product }) {
  return (
    <Card>
      <ProductImage src={product.image} alt={product.name} />
      <ProductInfo>
        <ProductName>{product.name}</ProductName>
        <ProductPrice price={product.price} />
      </ProductInfo>
      <ProductActions>
        <AddToCartButton productId={product.id} />
        <WishlistButton productId={product.id} />
      </ProductActions>
    </Card>
  );
}
```

### ❌ Bad: One Large Component

```jsx
// ❌ Bad - Everything in one component
function ProductCard({ product }) {
  return (
    <div className="card">
      <div className="image-container">
        <img src={product.image} />
        <div className="overlay">...</div>
      </div>
      <div className="info">
        <h3>{product.name}</h3>
        <p>{product.description}</p>
        <span>${product.price}</span>
        {/* 100+ more lines */}
      </div>
    </div>
  );
}
```

---

## 🔄 Reusability

### ✅ Good: Reusable Component

```jsx
// ✅ Good - Flexible and reusable
function Button({ 
  children, 
  variant = 'primary', 
  size = 'medium',
  onClick,
  disabled = false,
  ...props 
}) {
  return (
    <button
      className={`btn btn-${variant} btn-${size}`}
      onClick={onClick}
      disabled={disabled}
      {...props}
    >
      {children}
    </button>
  );
}
```

### ❌ Bad: Too Specific

```jsx
// ❌ Bad - Too specific, not reusable
function SubmitLoginFormButton() {
  return (
    <button
      className="blue-button-medium"
      onClick={() => {
        // Hardcoded logic
      }}
    >
      Submit Login Form
    </button>
  );
}
```

---

## 📚 Key Takeaways

1. **Keep components small** - 50-200 lines is ideal
2. **One responsibility** - Each component should do one thing
3. **Compose, don't complicate** - Build complex UIs from simple components
4. **Follow consistent structure** - Hooks, handlers, render in order
5. **Make components reusable** - Accept props for flexibility
6. **Organize files properly** - One component per folder
7. **Use index files** - Clean imports

---

## ✅ Checklist

Before considering a component "done", check:

- [ ] Component is under 200 lines
- [ ] Has a single, clear responsibility
- [ ] Props are well-defined
- [ ] Code is organized (hooks, handlers, render)
- [ ] Component is reusable where appropriate
- [ ] File structure follows conventions
- [ ] Component is testable
- [ ] No unnecessary complexity

---

**Remember:** Good component structure makes code easier to understand, test, and maintain!


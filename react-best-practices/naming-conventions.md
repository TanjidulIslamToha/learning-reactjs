# Naming Conventions Best Practices

## 📋 Table of Contents
- [Component Names](#component-names)
- [Variable Names](#variable-names)
- [Function Names](#function-names)
- [File Names](#file-names)
- [Hook Names](#hook-names)
- [Constant Names](#constant-names)

---

## 🧩 Component Names

### ✅ Good: PascalCase, Descriptive

```jsx
// ✅ Good - Clear, descriptive
function UserProfile() { }
function ProductCard() { }
function NavigationMenu() { }
function ShoppingCartItem() { }

// ✅ Good - Abbreviations are fine if clear
function APIError() { }
function HTTPRequest() { }
```

### ❌ Bad: Unclear or Wrong Case

```jsx
// ❌ Bad - Wrong case
function userProfile() { }
function product_card() { }

// ❌ Bad - Too vague
function Card() { } // What kind of card?
function Item() { } // What item?

// ❌ Bad - Abbreviations that aren't clear
function UProf() { } // Unclear
function ProdCard() { } // Could be better
```

---

## 📝 Variable Names

### ✅ Good: camelCase, Descriptive

```jsx
// ✅ Good - Clear and descriptive
const userName = 'Alice';
const productList = [];
const isLoggedIn = true;
const userCount = 10;
const hasPermission = false;

// ✅ Good - Boolean prefixes: is, has, should, can
const isLoading = false;
const hasError = true;
const shouldRender = true;
const canEdit = false;

// ✅ Good - Array/Collection suffixes
const users = [];
const productList = [];
const errorMessages = [];
```

### ❌ Bad: Unclear or Single Letters

```jsx
// ❌ Bad - Single letters (except in loops)
const u = 'Alice';
const p = [];
const x = true;

// ❌ Bad - Unclear abbreviations
const usr = 'Alice';
const prodLst = [];
const lg = true;

// ❌ Bad - Not descriptive
const data = {};
const temp = '';
const stuff = [];
```

---

## 🎣 Hook Names

### ✅ Good: Start with "use"

```jsx
// ✅ Good - Custom hooks start with "use"
function useCounter() { }
function useFetch() { }
function useLocalStorage() { }
function useAuth() { }
function useDebounce() { }

// ✅ Good - Built-in hooks
const [count, setCount] = useState(0);
const user = useContext(UserContext);
const data = useMemo(() => compute(), [deps]);
```

### ❌ Bad: Missing "use" Prefix

```jsx
// ❌ Bad - Doesn't follow convention
function counter() { }
function fetchData() { }
function getLocalStorage() { }
```

---

## 🔧 Function Names

### ✅ Good: camelCase, Verb-Based

```jsx
// ✅ Good - Action verbs
function handleClick() { }
function onSubmit() { }
function fetchUserData() { }
function calculateTotal() { }
function validateEmail() { }
function formatDate() { }

// ✅ Good - Event handlers: handle* or on*
function handleSubmit() { }
function handleChange() { }
function onClick() { }
function onFocus() { }

// ✅ Good - Boolean returns: is*, has*, can*
function isValid() { }
function hasPermission() { }
function canEdit() { }
```

### ❌ Bad: Nouns or Unclear

```jsx
// ❌ Bad - Nouns instead of verbs
function user() { }
function data() { }
function calculation() { }

// ❌ Bad - Unclear
function doStuff() { }
function process() { }
function thing() { }
```

---

## 📁 File Names

### ✅ Good: Match Component Name

```jsx
// File: UserProfile.js
export default function UserProfile() { }

// File: ProductCard.js
export default function ProductCard() { }

// File: NavigationMenu.js
export default function NavigationMenu() { }
```

### ✅ Good: Index Files

```jsx
// File: components/UserCard/index.js
export { default } from './UserCard';
export { default as UserCardSkeleton } from './UserCardSkeleton';

// Usage
import UserCard from './components/UserCard';
```

### ❌ Bad: Mismatched Names

```jsx
// ❌ Bad - File name doesn't match component
// File: user.js
export default function UserProfile() { } // Mismatch!

// ❌ Bad - Wrong case
// File: userprofile.js
export default function UserProfile() { }
```

---

## 🎯 Constant Names

### ✅ Good: UPPER_SNAKE_CASE for Constants

```jsx
// ✅ Good - Constants in UPPER_SNAKE_CASE
const MAX_ITEMS = 10;
const API_BASE_URL = 'https://api.example.com';
const DEFAULT_TIMEOUT = 5000;
const SUPPORTED_LANGUAGES = ['en', 'es', 'fr'];

// ✅ Good - Configuration objects
const API_CONFIG = {
  baseURL: 'https://api.example.com',
  timeout: 5000,
};
```

### ❌ Bad: Wrong Case for Constants

```jsx
// ❌ Bad - Should be uppercase
const maxItems = 10;
const apiBaseUrl = 'https://api.example.com';
```

---

## 🏷️ Prop Names

### ✅ Good: Descriptive, Consistent

```jsx
// ✅ Good - Clear prop names
<UserCard 
  user={userData}
  onFollow={handleFollow}
  showAvatar={true}
  isCompact={false}
/>

// ✅ Good - Boolean props: is*, has*, show*
<Button 
  isDisabled={false}
  isLoading={true}
  hasError={false}
  showIcon={true}
/>
```

### ❌ Bad: Unclear or Inconsistent

```jsx
// ❌ Bad - Unclear
<UserCard data={user} />

// ❌ Bad - Inconsistent boolean naming
<Button disabled={false} loading={true} error={false} />
```

---

## 🎨 CSS Class Names

### ✅ Good: BEM or kebab-case

```jsx
// ✅ Good - BEM methodology
<div className="user-card">
  <div className="user-card__header">
    <img className="user-card__avatar" />
  </div>
  <div className="user-card__body">
    <h3 className="user-card__title" />
  </div>
</div>

// ✅ Good - kebab-case
<div className="user-card">
  <div className="user-card-header">
    <img className="user-card-avatar" />
  </div>
</div>
```

### ❌ Bad: camelCase in CSS

```jsx
// ❌ Bad - camelCase doesn't work well in CSS
<div className="userCard"> // Harder to target in CSS
```

---

## 📦 Import/Export Names

### ✅ Good: Clear and Consistent

```jsx
// ✅ Good - Named exports
export function useCounter() { }
export function useFetch() { }
export const API_URL = 'https://api.example.com';

// ✅ Good - Default export for main component
export default function UserCard() { }

// ✅ Good - Import with clear names
import { useCounter, useFetch } from './hooks';
import UserCard from './components/UserCard';
```

### ❌ Bad: Unclear Imports

```jsx
// ❌ Bad - Unclear what's imported
import * as stuff from './utils';
import { a, b, c } from './helpers';
```

---

## 🎯 Context Names

### ✅ Good: Descriptive with "Context"

```jsx
// ✅ Good - Clear context names
const UserContext = createContext();
const ThemeContext = createContext();
const AuthContext = createContext();

// ✅ Good - Provider names
export function UserProvider() { }
export function ThemeProvider() { }
```

---

## 📚 Quick Reference

| Type | Convention | Example |
|------|-----------|---------|
| Component | PascalCase | `UserProfile` |
| Variable | camelCase | `userName` |
| Function | camelCase, verb | `handleClick` |
| Hook | camelCase, starts with "use" | `useCounter` |
| Constant | UPPER_SNAKE_CASE | `MAX_ITEMS` |
| File | PascalCase (matches component) | `UserProfile.js` |
| CSS Class | kebab-case or BEM | `user-card` or `user-card__header` |
| Boolean | is/has/can/should prefix | `isLoading`, `hasError` |
| Array | Plural noun | `users`, `products` |

---

## ✅ Naming Checklist

Before finalizing a name, ask:

- [ ] Is it clear what this represents?
- [ ] Does it follow the convention for its type?
- [ ] Is it descriptive enough?
- [ ] Would another developer understand it?
- [ ] Is it consistent with the codebase?
- [ ] Is it not too long or too short?

---

## 🎯 Key Takeaways

1. **Components**: PascalCase, descriptive (`UserProfile`)
2. **Variables**: camelCase, descriptive (`userName`)
3. **Functions**: camelCase, verb-based (`handleClick`)
4. **Hooks**: Start with "use" (`useCounter`)
5. **Constants**: UPPER_SNAKE_CASE (`MAX_ITEMS`)
6. **Files**: Match component name (`UserProfile.js`)
7. **Booleans**: Use prefixes (`is`, `has`, `can`)
8. **Arrays**: Use plural nouns (`users`, `products`)

---

**Remember:** Good naming makes code self-documenting and easier to understand!


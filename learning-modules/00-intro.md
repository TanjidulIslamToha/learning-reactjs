# Module 00 · Introduction to React

## 🎯 Learning Objectives
By the end of this module, you will:
- Understand what React is and why it's used
- Know how React differs from vanilla JavaScript
- Set up your development environment
- Create your first React component
- Understand the React component lifecycle at a high level

---

## 📚 What is React?

React is a **JavaScript library** for building user interfaces, created by Facebook (now Meta). Think of it as a tool that helps you build interactive websites more efficiently.

### Why Use React?

**Without React (Vanilla JavaScript):**
```javascript
// You have to manually find elements and update them
const button = document.getElementById('myButton');
const counter = document.getElementById('counter');
let count = 0;

button.addEventListener('click', () => {
  count++;
  counter.textContent = `Count: ${count}`;
});
```

**With React:**
```jsx
// React automatically updates the UI when state changes
function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

### Key Benefits:
1. **Component-Based**: Build reusable pieces of UI
2. **Declarative**: Describe what you want, React figures out how
3. **Efficient Updates**: Only updates what changed (Virtual DOM)
4. **Ecosystem**: Huge community and libraries

---

## 🛠️ Setting Up Your Environment

### Step 1: Install Node.js
1. Visit [nodejs.org](https://nodejs.org/)
2. Download the LTS (Long Term Support) version
3. Install it (includes npm automatically)
4. Verify installation:
   ```bash
   node -v    # Should show version like v20.x.x
   npm -v     # Should show version like 10.x.x
   ```

### Step 2: Install VS Code (Recommended)
1. Download from [code.visualstudio.com](https://code.visualstudio.com/)
2. Install these extensions:
   - **ES7+ React/Redux/React-Native snippets** (dsznajder)
   - **ESLint** (Microsoft)
   - **Prettier** (Prettier)
   - **React Developer Tools** (for browser)

### Step 3: Create Your First React App
```bash
# Navigate to your workspace
cd "C:\Users\AMT\Desktop\React JS\projects"

# Create a new React app
npx create-react-app my-first-app

# Navigate into it
cd my-first-app

# Start the development server
npm start
```

Your browser should open to `http://localhost:3000` with a spinning React logo!

---

## 🧩 Understanding React Components

A **component** is a reusable piece of UI. Think of it like a LEGO block - you can combine many blocks to build something bigger.

### Example: Simple Component

```jsx
// Welcome.js
function Welcome() {
  return <h1>Hello, React Learner!</h1>;
}

export default Welcome;
```

**Breaking it down:**
- `function Welcome()` - This is a function component
- `return` - Returns JSX (looks like HTML but it's JavaScript)
- `export default Welcome` - Makes it available to import elsewhere

### Using the Component

```jsx
// App.js
import Welcome from './Welcome';

function App() {
  return (
    <div>
      <Welcome />
      <Welcome />
      <Welcome />
    </div>
  );
}

export default App;
```

**Result:** Three "Hello, React Learner!" headings appear on the page.

---

## 🎨 JSX Basics (Quick Preview)

JSX lets you write HTML-like syntax in JavaScript:

```jsx
// ✅ This is JSX
const element = <h1>Hello, World!</h1>;

// ❌ This is NOT valid JavaScript (without JSX)
const element = <h1>Hello</h1>; // Syntax error!
```

**Key Rules:**
1. JSX must return a single root element
2. Use `className` instead of `class`
3. Use `{}` to embed JavaScript expressions
4. Self-closing tags need `/` (e.g., `<img />`)

```jsx
function Greeting({ name, age }) {
  return (
    <div className="greeting-card">
      <h2>Hello, {name}!</h2>
      <p>You are {age} years old.</p>
      <p>Next year you'll be {age + 1}.</p>
    </div>
  );
}
```

---

## 🔄 How React Updates the UI

React uses a **Virtual DOM** to efficiently update the real DOM:

1. **Initial Render**: React creates a virtual representation
2. **State Change**: When data changes, React creates a new virtual DOM
3. **Comparison**: React compares old vs new (diffing)
4. **Update**: Only the changed parts update in the real DOM

**Example:**
```jsx
function Counter() {
  const [count, setCount] = useState(0);
  
  // When setCount is called:
  // 1. React creates new virtual DOM
  // 2. Compares with previous
  // 3. Updates only the <p> tag that changed
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>+</button>
    </div>
  );
}
```

---

## 📝 Hands-On Tutorial: Your First Component

### Step 1: Create a Profile Card Component

Create `src/components/ProfileCard.js`:

```jsx
function ProfileCard({ name, role, image }) {
  return (
    <div className="profile-card">
      <img src={image} alt={name} />
      <h3>{name}</h3>
      <p>{role}</p>
    </div>
  );
}

export default ProfileCard;
```

### Step 2: Use It in App.js

```jsx
import ProfileCard from './components/ProfileCard';
import './App.css';

function App() {
  return (
    <div className="App">
      <h1>Team Members</h1>
      <ProfileCard 
        name="Alice" 
        role="Frontend Developer"
        image="https://via.placeholder.com/150"
      />
      <ProfileCard 
        name="Bob" 
        role="Backend Developer"
        image="https://via.placeholder.com/150"
      />
    </div>
  );
}

export default App;
```

### Step 3: Add Basic Styling

Add to `App.css`:
```css
.profile-card {
  border: 2px solid #333;
  border-radius: 8px;
  padding: 20px;
  margin: 10px;
  text-align: center;
  display: inline-block;
}

.profile-card img {
  width: 100px;
  height: 100px;
  border-radius: 50%;
}
```

---

## 🎯 Practice Exercises

### Exercise 1: Personal Info Component
Create a component that displays:
- Your name
- Your favorite programming language
- A fun fact about yourself

**Solution:**
```jsx
function PersonalInfo() {
  return (
    <div>
      <h2>About Me</h2>
      <p><strong>Name:</strong> Your Name</p>
      <p><strong>Favorite Language:</strong> JavaScript</p>
      <p><strong>Fun Fact:</strong> I'm learning React!</p>
    </div>
  );
}
```

### Exercise 2: Product Card
Create a `ProductCard` component that accepts:
- `title` (product name)
- `price` (number)
- `description` (text)

Display them in a nice card layout.

### Exercise 3: Button Component
Create a reusable `Button` component that:
- Accepts `text` and `onClick` props
- Has consistent styling
- Can be used multiple times

---

## ❌ Common Mistakes to Avoid

1. **Forgetting to export/import:**
   ```jsx
   // ❌ Wrong
   function MyComponent() { ... }
   
   // ✅ Right
   function MyComponent() { ... }
   export default MyComponent;
   ```

2. **Using `class` instead of `className`:**
   ```jsx
   // ❌ Wrong
   <div class="container">
   
   // ✅ Right
   <div className="container">
   ```

3. **Not returning JSX:**
   ```jsx
   // ❌ Wrong
   function Component() {
     <div>Hello</div>
   }
   
   // ✅ Right
   function Component() {
     return <div>Hello</div>;
   }
   ```

---

## 🚀 Real-World Application

React is used by:
- **Facebook** - Their entire web interface
- **Instagram** - Web version
- **Netflix** - UI components
- **Airbnb** - Search and booking interfaces
- **WhatsApp Web** - Chat interface

All these use React because it handles complex, interactive UIs efficiently.

---

## 📚 Key Takeaways

1. React is a library for building UIs, not a framework
2. Components are reusable pieces of UI
3. JSX lets you write HTML-like syntax in JavaScript
4. React efficiently updates only what changes
5. Always export components to use them elsewhere

---

## 🔗 Next Steps

- ✅ Complete the practice exercises above
- ✅ Experiment with the ProfileCard component
- ✅ Read through `learning-modules/01-jsx-basics.md` next
- ✅ Update your progress in `docs/react-learning-checklist.md`

---

## 💡 Reflection Questions

Before moving on, ask yourself:
1. Can I explain what React is to someone else?
2. Do I understand the difference between a component and regular HTML?
3. Can I create a simple component from scratch?

If yes to all, you're ready for Module 01! 🎉

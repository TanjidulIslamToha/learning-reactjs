# Module 04 · Event Handling & Forms

## 🎯 Learning Objectives
By the end of this module, you will:
- Handle all types of events (click, change, submit, etc.)
- Build controlled form inputs
- Validate form data
- Handle form submission
- Create reusable form components
- Understand event object and preventDefault

---

## 🖱️ Event Handling Basics

Events are user interactions: clicks, typing, scrolling, etc. React handles events with camelCase handlers.

### Click Events

```jsx
function Button() {
  const handleClick = () => {
    alert('Button clicked!');
  };
  
  return <button onClick={handleClick}>Click me</button>;
}
```

### Inline Handlers

```jsx
function Button() {
  return (
    <button onClick={() => console.log('Clicked!')}>
      Click me
    </button>
  );
}
```

### Passing Arguments

```jsx
function ProductList({ products }) {
  const handleAddToCart = (productId) => {
    console.log(`Adding product ${productId}`);
  };
  
  return (
    <div>
      {products.map(product => (
        <button 
          key={product.id}
          onClick={() => handleAddToCart(product.id)}
        >
          Add {product.name} to Cart
        </button>
      ))}
    </div>
  );
}
```

---

## 📝 Controlled Inputs

**Controlled inputs** are inputs whose value is controlled by React state.

### Basic Controlled Input

```jsx
function NameInput() {
  const [name, setName] = useState('');
  
  return (
    <div>
      <input
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Enter your name"
      />
      <p>Hello, {name}!</p>
    </div>
  );
}
```

**Key Points:**
- `value={name}` - Input displays current state
- `onChange={(e) => setName(e.target.value)}` - Updates state on change
- `e.target.value` - Gets the input's current value

### Multiple Inputs

```jsx
function ContactForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    message: ''
  });
  
  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: value
    }));
  };
  
  return (
    <form>
      <input
        type="text"
        name="name"
        value={formData.name}
        onChange={handleChange}
        placeholder="Name"
      />
      <input
        type="email"
        name="email"
        value={formData.email}
        onChange={handleChange}
        placeholder="Email"
      />
      <textarea
        name="message"
        value={formData.message}
        onChange={handleChange}
        placeholder="Message"
      />
    </form>
  );
}
```

---

## 📋 Form Submission

### Preventing Default Behavior

```jsx
function LoginForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  
  const handleSubmit = (e) => {
    e.preventDefault(); // Prevents page refresh
    console.log('Submitting:', { email, password });
    // Handle login logic here
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email"
      />
      <input
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
        placeholder="Password"
      />
      <button type="submit">Login</button>
    </form>
  );
}
```

### Complete Form Example

```jsx
function RegistrationForm() {
  const [formData, setFormData] = useState({
    username: '',
    email: '',
    password: '',
    confirmPassword: '',
    agreeToTerms: false
  });
  
  const [errors, setErrors] = useState({});
  
  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: type === 'checkbox' ? checked : value
    }));
    // Clear error when user starts typing
    if (errors[name]) {
      setErrors(prev => ({
        ...prev,
        [name]: ''
      }));
    }
  };
  
  const validate = () => {
    const newErrors = {};
    
    if (!formData.username.trim()) {
      newErrors.username = 'Username is required';
    }
    
    if (!formData.email.trim()) {
      newErrors.email = 'Email is required';
    } else if (!/\S+@\S+\.\S+/.test(formData.email)) {
      newErrors.email = 'Email is invalid';
    }
    
    if (!formData.password) {
      newErrors.password = 'Password is required';
    } else if (formData.password.length < 8) {
      newErrors.password = 'Password must be at least 8 characters';
    }
    
    if (formData.password !== formData.confirmPassword) {
      newErrors.confirmPassword = 'Passwords do not match';
    }
    
    if (!formData.agreeToTerms) {
      newErrors.agreeToTerms = 'You must agree to the terms';
    }
    
    return newErrors;
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    const newErrors = validate();
    
    if (Object.keys(newErrors).length > 0) {
      setErrors(newErrors);
      return;
    }
    
    // Form is valid, submit it
    console.log('Form submitted:', formData);
    alert('Registration successful!');
  };
  
  return (
    <form onSubmit={handleSubmit} className="registration-form">
      <div>
        <label>Username</label>
        <input
          type="text"
          name="username"
          value={formData.username}
          onChange={handleChange}
        />
        {errors.username && <span className="error">{errors.username}</span>}
      </div>
      
      <div>
        <label>Email</label>
        <input
          type="email"
          name="email"
          value={formData.email}
          onChange={handleChange}
        />
        {errors.email && <span className="error">{errors.email}</span>}
      </div>
      
      <div>
        <label>Password</label>
        <input
          type="password"
          name="password"
          value={formData.password}
          onChange={handleChange}
        />
        {errors.password && <span className="error">{errors.password}</span>}
      </div>
      
      <div>
        <label>Confirm Password</label>
        <input
          type="password"
          name="confirmPassword"
          value={formData.confirmPassword}
          onChange={handleChange}
        />
        {errors.confirmPassword && (
          <span className="error">{errors.confirmPassword}</span>
        )}
      </div>
      
      <div>
        <label>
          <input
            type="checkbox"
            name="agreeToTerms"
            checked={formData.agreeToTerms}
            onChange={handleChange}
          />
          I agree to the terms and conditions
        </label>
        {errors.agreeToTerms && (
          <span className="error">{errors.agreeToTerms}</span>
        )}
      </div>
      
      <button type="submit">Register</button>
    </form>
  );
}
```

---

## 🎛️ Different Input Types

### Text Input

```jsx
function TextInput() {
  const [text, setText] = useState('');
  
  return (
    <input
      type="text"
      value={text}
      onChange={(e) => setText(e.target.value)}
    />
  );
}
```

### Textarea

```jsx
function TextareaInput() {
  const [message, setMessage] = useState('');
  
  return (
    <textarea
      value={message}
      onChange={(e) => setMessage(e.target.value)}
      rows={5}
      cols={50}
    />
  );
}
```

### Select Dropdown

```jsx
function SelectInput() {
  const [country, setCountry] = useState('');
  
  return (
    <select value={country} onChange={(e) => setCountry(e.target.value)}>
      <option value="">Select a country</option>
      <option value="us">United States</option>
      <option value="uk">United Kingdom</option>
      <option value="ca">Canada</option>
    </select>
  );
}
```

### Checkbox

```jsx
function CheckboxInput() {
  const [isChecked, setIsChecked] = useState(false);
  
  return (
    <label>
      <input
        type="checkbox"
        checked={isChecked}
        onChange={(e) => setIsChecked(e.target.checked)}
      />
      I agree to the terms
    </label>
  );
}
```

### Radio Buttons

```jsx
function RadioInput() {
  const [selected, setSelected] = useState('');
  
  return (
    <div>
      <label>
        <input
          type="radio"
          value="option1"
          checked={selected === 'option1'}
          onChange={(e) => setSelected(e.target.value)}
        />
        Option 1
      </label>
      <label>
        <input
          type="radio"
          value="option2"
          checked={selected === 'option2'}
          onChange={(e) => setSelected(e.target.value)}
        />
        Option 2
      </label>
    </div>
  );
}
```

### Range Slider

```jsx
function RangeInput() {
  const [value, setValue] = useState(50);
  
  return (
    <div>
      <input
        type="range"
        min="0"
        max="100"
        value={value}
        onChange={(e) => setValue(e.target.value)}
      />
      <p>Value: {value}</p>
    </div>
  );
}
```

---

## 🎨 Reusable Form Components

### Input Component

```jsx
function Input({ 
  label, 
  name, 
  type = 'text', 
  value, 
  onChange, 
  error,
  ...props 
}) {
  return (
    <div className="form-group">
      {label && <label htmlFor={name}>{label}</label>}
      <input
        id={name}
        name={name}
        type={type}
        value={value}
        onChange={onChange}
        className={error ? 'error' : ''}
        {...props}
      />
      {error && <span className="error-message">{error}</span>}
    </div>
  );
}
```

### Form Field Component

```jsx
function FormField({ 
  label, 
  name, 
  type, 
  value, 
  onChange, 
  error,
  required = false 
}) {
  return (
    <div className="form-field">
      <label htmlFor={name}>
        {label}
        {required && <span className="required">*</span>}
      </label>
      {type === 'textarea' ? (
        <textarea
          id={name}
          name={name}
          value={value}
          onChange={onChange}
          className={error ? 'error' : ''}
        />
      ) : (
        <input
          id={name}
          name={name}
          type={type}
          value={value}
          onChange={onChange}
          className={error ? 'error' : ''}
        />
      )}
      {error && <span className="error-message">{error}</span>}
    </div>
  );
}
```

### Using Reusable Components

```jsx
function ContactForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    message: ''
  });
  const [errors, setErrors] = useState({});
  
  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    // Validation and submission logic
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <FormField
        label="Name"
        name="name"
        type="text"
        value={formData.name}
        onChange={handleChange}
        error={errors.name}
        required
      />
      <FormField
        label="Email"
        name="email"
        type="email"
        value={formData.email}
        onChange={handleChange}
        error={errors.email}
        required
      />
      <FormField
        label="Message"
        name="message"
        type="textarea"
        value={formData.message}
        onChange={handleChange}
        error={errors.message}
        required
      />
      <button type="submit">Submit</button>
    </form>
  );
}
```

---

## 🎯 Practice Exercises

### Exercise 1: Search Component
Create a search component with:
- Input field
- Search button
- Display search results
- Clear button
- Debounce search (wait 500ms after typing stops)

### Exercise 2: Multi-Step Form
Create a form with multiple steps:
- Step 1: Personal info
- Step 2: Address
- Step 3: Review
- Navigation buttons (Next, Previous, Submit)

### Exercise 3: Dynamic Form Fields
Create a form where users can:
- Add multiple phone numbers
- Remove phone numbers
- Each phone number has type (home, work, mobile)

---

## ❌ Common Mistakes

1. **Not preventing default on form submit:**
   ```jsx
   // ❌ Wrong - Page refreshes
   <form onSubmit={handleSubmit}>
   
   // ✅ Right
   const handleSubmit = (e) => {
     e.preventDefault();
     // Handle submission
   };
   ```

2. **Not using controlled inputs:**
   ```jsx
   // ❌ Wrong - Uncontrolled
   <input type="text" />
   
   // ✅ Right - Controlled
   <input type="text" value={value} onChange={handleChange} />
   ```

3. **Mutating state directly:**
   ```jsx
   // ❌ Wrong
   formData.name = newValue;
   
   // ✅ Right
   setFormData({ ...formData, name: newValue });
   ```

---

## 🚀 Real-World Application

Forms are everywhere:
- **Login/Registration**: Authentication
- **Contact Forms**: Customer inquiries
- **Checkout**: E-commerce purchases
- **Settings**: User preferences
- **Search**: Filtering and queries

---

## 📚 Key Takeaways

1. Use `onChange` for input changes
2. Always use controlled inputs (value + onChange)
3. Prevent default behavior with `e.preventDefault()`
4. Validate form data before submission
5. Create reusable form components
6. Handle different input types appropriately

---

## 🔗 Next Steps

- ✅ Complete all practice exercises
- ✅ Build the registration form
- ✅ Create reusable form components
- ✅ Read `learning-modules/05-useeffect.md` next

---

## 💡 Reflection Questions

1. Can I create a controlled input?
2. Do I know how to handle form submission?
3. Can I validate form data?
4. Do I understand the event object?

If yes to all, you're ready for useEffect! 🎉


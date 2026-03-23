# ⚛️ React.js Development Guide

> **Master Modern React Development with Hooks, Components, and Best Practices**

---

## 📋 **Table of Contents**

1. [React Fundamentals](#react-fundamentals)
2. [Components & Props](#components--props)
3. [State Management](#state-management)
4. [React Hooks](#react-hooks)
5. [Event Handling](#event-handling)
6. [Forms](#forms)
7. [Lists & Keys](#lists--keys)
8. [Conditional Rendering](#conditional-rendering)
9. [React Router](#react-router)
10. [Context API](#context-api)
11. [Performance Optimization](#performance-optimization)
12. [Testing](#testing)

---

## 🎯 **React Fundamentals**

### **What is React?**

React is a JavaScript library for building user interfaces. It's maintained by Meta and a community of developers.

**Key Features:**
- **Component-Based**: Build encapsulated components
- **Declarative**: Design simple views for each state
- **Learn Once, Write Anywhere**: Use on web, mobile, and desktop

### **Setting Up React**

```bash
# Create React App
npx create-react-app my-app
cd my-app
npm start

# Vite (Faster)
npm create vite@latest my-app -- --template react
cd my-app
npm install
npm run dev
```

### **Basic React Component**

```jsx
import React from 'react';

function App() {
    return (
        <div className="app">
            <h1>Hello, React!</h1>
            <p>Welcome to React development</p>
        </div>
    );
}

export default App;
```

### **JSX Syntax**

```jsx
// JSX is a syntax extension for JavaScript
const element = <h1>Hello, World!</h1>;

// Embedding expressions
const name = 'John';
const greeting = <h1>Hello, {name}!</h1>;

// Using attributes
const element = <img src={user.avatarUrl} alt={user.name} />;

// Conditional rendering
const isLoggedIn = true;
const message = isLoggedIn ? <Welcome /> : <Login />;

// Lists
const items = ['Apple', 'Banana', 'Orange'];
const listItems = items.map((item) => <li key={item}>{item}</li>);
```

---

## 🧩 **Components & Props**

### **Functional Components**

```jsx
// Simple component
function Welcome(props) {
    return <h1>Hello, {props.name}</h1>;
}

// Arrow function component
const Welcome = ({ name }) => {
    return <h1>Hello, {name}</h1>;
}

// Component with multiple props
function UserCard({ name, email, role, avatar }) {
    return (
        <div className="user-card">
            <img src={avatar} alt={name} />
            <h2>{name}</h2>
            <p>{email}</p>
            <span className="role">{role}</span>
        </div>
    );
}

// Using component
function App() {
    return (
        <div>
            <Welcome name="John" />
            <UserCard
                name="Jane Smith"
                email="jane@example.com"
                role="Admin"
                avatar="/avatar.jpg"
            />
        </div>
    );
}
```

### **Default Props**

```jsx
function Button({ text, color, onClick }) {
    return (
        <button
            style={{ backgroundColor: color }}
            onClick={onClick}
        >
            {text}
        </button>
    );
}

Button.defaultProps = {
    text: 'Click me',
    color: '#007bff'
};

// Or with ES6 default parameters
function Button({ text = 'Click me', color = '#007bff', onClick }) {
    return (
        <button
            style={{ backgroundColor: color }}
            onClick={onClick}
        >
            {text}
        </button>
    );
}
```

### **Prop Types**

```jsx
import PropTypes from 'prop-types';

function UserCard({ name, email, age, isActive }) {
    return (
        <div className="user-card">
            <h2>{name}</h2>
            <p>{email}</p>
            <p>Age: {age}</p>
            <span>{isActive ? 'Active' : 'Inactive'}</span>
        </div>
    );
}

UserCard.propTypes = {
    name: PropTypes.string.isRequired,
    email: PropTypes.string.isRequired,
    age: PropTypes.number,
    isActive: PropTypes.bool
};

UserCard.defaultProps = {
    age: 0,
    isActive: false
};
```

---

## 📊 **State Management**

### **useState Hook**

```jsx
import { useState } from 'react';

function Counter() {
    const [count, setCount] = useState(0);

    const increment = () => {
        setCount(count + 1);
    };

    const decrement = () => {
        setCount(count - 1);
    };

    const reset = () => {
        setCount(0);
    };

    return (
        <div>
            <h1>Count: {count}</h1>
            <button onClick={increment}>Increment</button>
            <button onClick={decrement}>Decrement</button>
            <button onClick={reset}>Reset</button>
        </div>
    );
}
```

### **Multiple State Variables**

```jsx
function UserForm() {
    const [name, setName] = useState('');
    const [email, setEmail] = useState('');
    const [age, setAge] = useState(0);

    const handleSubmit = (e) => {
        e.preventDefault();
        console.log({ name, email, age });
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
            <button type="submit">Submit</button>
        </form>
    );
}
```

### **Object State**

```jsx
function UserProfile() {
    const [user, setUser] = useState({
        name: '',
        email: '',
        age: 0
    });

    const handleChange = (e) => {
        const { name, value } = e.target;
        setUser(prev => ({
            ...prev,
            [name]: value
        }));
    };

    return (
        <form>
            <input
                name="name"
                value={user.name}
                onChange={handleChange}
                placeholder="Name"
            />
            <input
                name="email"
                value={user.email}
                onChange={handleChange}
                placeholder="Email"
            />
            <input
                name="age"
                type="number"
                value={user.age}
                onChange={handleChange}
                placeholder="Age"
            />
        </form>
    );
}
```

---

## 🪝 **React Hooks**

### **useEffect Hook**

```jsx
import { useState, useEffect } from 'react';

function UserProfile({ userId }) {
    const [user, setUser] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);

    useEffect(() => {
        async function fetchUser() {
            try {
                setLoading(true);
                const response = await fetch(`/api/users/${userId}`);
                const data = await response.json();
                setUser(data);
            } catch (err) {
                setError(err.message);
            } finally {
                setLoading(false);
            }
        }

        fetchUser();
    }, [userId]);

    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;
    if (!user) return <div>No user found</div>;

    return (
        <div>
            <h1>{user.name}</h1>
            <p>{user.email}</p>
        </div>
    );
}
```

### **useContext Hook**

```jsx
import { createContext, useContext, useState } from 'react';

// Create context
const ThemeContext = createContext();

// Provider component
function ThemeProvider({ children }) {
    const [theme, setTheme] = useState('light');

    const toggleTheme = () => {
        setTheme(prev => prev === 'light' ? 'dark' : 'light');
    };

    return (
        <ThemeContext.Provider value={{ theme, toggleTheme }}>
            {children}
        </ThemeContext.Provider>
    );
}

// Custom hook
function useTheme() {
    const context = useContext(ThemeContext);
    if (!context) {
        throw new Error('useTheme must be used within ThemeProvider');
    }
    return context;
}

// Using context
function ThemeButton() {
    const { theme, toggleTheme } = useTheme();

    return (
        <button onClick={toggleTheme}>
            Switch to {theme === 'light' ? 'Dark' : 'Light'} Mode
        </button>
    );
}

// App component
function App() {
    return (
        <ThemeProvider>
            <ThemeButton />
        </ThemeProvider>
    );
}
```

### **useRef Hook**

```jsx
import { useRef, useEffect } from 'react';

function FocusInput() {
    const inputRef = useRef(null);

    useEffect(() => {
        inputRef.current.focus();
    }, []);

    return (
        <div>
            <input ref={inputRef} type="text" placeholder="Auto-focused" />
        </div>
    );
}

function Timer() {
    const [count, setCount] = useState(0);
    const intervalRef = useRef(null);

    useEffect(() => {
        intervalRef.current = setInterval(() => {
            setCount(prev => prev + 1);
        }, 1000);

        return () => {
            clearInterval(intervalRef.current);
        };
    }, []);

    return <div>Count: {count}</div>;
}
```

### **useMemo Hook**

```jsx
import { useState, useMemo } from 'react';

function ExpensiveCalculation({ numbers }) {
    const [filter, setFilter] = useState('');

    const filteredNumbers = useMemo(() => {
        console.log('Calculating filtered numbers...');
        return numbers.filter(num =>
            num.toString().includes(filter)
        );
    }, [numbers, filter]);

    const sum = useMemo(() => {
        console.log('Calculating sum...');
        return filteredNumbers.reduce((acc, num) => acc + num, 0);
    }, [filteredNumbers]);

    return (
        <div>
            <input
                value={filter}
                onChange={(e) => setFilter(e.target.value)}
                placeholder="Filter numbers"
            />
            <p>Sum: {sum}</p>
            <ul>
                {filteredNumbers.map(num => (
                    <li key={num}>{num}</li>
                ))}
            </ul>
        </div>
    );
}
```

### **useCallback Hook**

```jsx
import { useState, useCallback } from 'react';

function ParentComponent() {
    const [count, setCount] = useState(0);

    const handleClick = useCallback(() => {
        console.log('Button clicked');
    }, []);

    return (
        <div>
            <ChildComponent onClick={handleClick} />
            <button onClick={() => setCount(count + 1)}>
                Count: {count}
            </button>
        </div>
    );
}

function ChildComponent({ onClick }) {
    console.log('ChildComponent rendered');
    return <button onClick={onClick}>Click me</button>;
}
```

### **Custom Hooks**

```jsx
// Custom hook for fetching data
function useFetch(url) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);

    useEffect(() => {
        async function fetchData() {
            try {
                setLoading(true);
                const response = await fetch(url);
                const data = await response.json();
                setData(data);
            } catch (err) {
                setError(err.message);
            } finally {
                setLoading(false);
            }
        }

        fetchData();
    }, [url]);

    return { data, loading, error };
}

// Using custom hook
function UserList() {
    const { data: users, loading, error } = useFetch('/api/users');

    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;

    return (
        <ul>
            {users.map(user => (
                <li key={user.id}>{user.name}</li>
            ))}
        </ul>
    );
}

// Custom hook for local storage
function useLocalStorage(key, initialValue) {
    const [storedValue, setStoredValue] = useState(() => {
        try {
            const item = window.localStorage.getItem(key);
            return item ? JSON.parse(item) : initialValue;
        } catch (error) {
            return initialValue;
        }
    });

    const setValue = (value) => {
        try {
            setStoredValue(value);
            window.localStorage.setItem(key, JSON.stringify(value));
        } catch (error) {
            console.error(error);
        }
    };

    return [storedValue, setValue];
}

// Using local storage hook
function ThemeToggle() {
    const [theme, setTheme] = useLocalStorage('theme', 'light');

    const toggleTheme = () => {
        setTheme(theme === 'light' ? 'dark' : 'light');
    };

    return (
        <button onClick={toggleTheme}>
            Current theme: {theme}
        </button>
    );
}
```

---

## 🎯 **Event Handling**

```jsx
function EventHandling() {
    const handleClick = (e) => {
        console.log('Button clicked');
        console.log('Event:', e);
    };

    const handleMouseOver = () => {
        console.log('Mouse over');
    };

    const handleInputChange = (e) => {
        console.log('Input value:', e.target.value);
    };

    const handleSubmit = (e) => {
        e.preventDefault();
        console.log('Form submitted');
    };

    return (
        <div>
            <button onClick={handleClick}>Click me</button>
            <div onMouseOver={handleMouseOver}>Hover me</div>
            <input onChange={handleInputChange} />
            <form onSubmit={handleSubmit}>
                <button type="submit">Submit</button>
            </form>
        </div>
    );
}
```

---

## 📝 **Forms**

```jsx
function ControlledForm() {
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

    const handleSubmit = (e) => {
        e.preventDefault();
        console.log('Form submitted:', formData);
    };

    return (
        <form onSubmit={handleSubmit}>
            <input
                name="name"
                value={formData.name}
                onChange={handleChange}
                placeholder="Name"
            />
            <input
                name="email"
                type="email"
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
            <button type="submit">Submit</button>
        </form>
    );
}
```

---

## 📋 **Lists & Keys**

```jsx
function ListRendering() {
    const items = ['Apple', 'Banana', 'Orange', 'Grape'];

    return (
        <ul>
            {items.map((item, index) => (
                <li key={index}>{item}</li>
            ))}
        </ul>
    );
}

function UserList() {
    const users = [
        { id: 1, name: 'John', email: 'john@example.com' },
        { id: 2, name: 'Jane', email: 'jane@example.com' },
        { id: 3, name: 'Bob', email: 'bob@example.com' }
    ];

    return (
        <ul>
            {users.map(user => (
                <li key={user.id}>
                    <strong>{user.name}</strong> - {user.email}
                </li>
            ))}
        </ul>
    );
}
```

---

## 🔀 **Conditional Rendering**

```jsx
function ConditionalRendering() {
    const [isLoggedIn, setIsLoggedIn] = useState(false);
    const [user, setUser] = useState(null);

    return (
        <div>
            {isLoggedIn ? (
                <WelcomeUser user={user} />
            ) : (
                <LoginForm onLogin={setUser} />
            )}

            {user && <UserProfile user={user} />}

            {user?.role === 'admin' && <AdminPanel />}
        </div>
    );
}
```

---

## 🧭 **React Router**

```jsx
import { BrowserRouter, Routes, Route, Link, useParams } from 'react-router-dom';

function App() {
    return (
        <BrowserRouter>
            <nav>
                <Link to="/">Home</Link>
                <Link to="/about">About</Link>
                <Link to="/users">Users</Link>
            </nav>

            <Routes>
                <Route path="/" element={<Home />} />
                <Route path="/about" element={<About />} />
                <Route path="/users" element={<Users />} />
                <Route path="/users/:id" element={<UserDetail />} />
            </Routes>
        </BrowserRouter>
    );
}

function UserDetail() {
    const { id } = useParams();
    return <div>User ID: {id}</div>;
}
```

---

## ⚡ **Performance Optimization**

```jsx
import { memo, useMemo, useCallback } from 'react';

// Memoized component
const ExpensiveComponent = memo(function ExpensiveComponent({ data }) {
    return <div>{/* Expensive rendering */}</div>;
});

function Parent() {
    const [count, setCount] = useState(0);
    const data = useMemo(() => computeExpensiveData(), []);

    const handleClick = useCallback(() => {
        console.log('Clicked');
    }, []);

    return (
        <div>
            <ExpensiveComponent data={data} />
            <button onClick={() => setCount(count + 1)}>
                Count: {count}
            </button>
        </div>
    );
}
```

---

## 🧪 **Testing**

```jsx
import { render, screen, fireEvent } from '@testing-library/react';
import Counter from './Counter';

test('renders counter with initial value', () => {
    render(<Counter />);
    expect(screen.getByText('Count: 0')).toBeInTheDocument();
});

test('increments count when button is clicked', () => {
    render(<Counter />);
    const button = screen.getByText('Increment');
    fireEvent.click(button);
    expect(screen.getByText('Count: 1')).toBeInTheDocument();
});
```

---

## 📚 **Learning Resources**

### **Documentation**
- [React Documentation](https://react.dev/)
- [React Tutorial](https://react.dev/learn)
- [React Hooks Guide](https://react.dev/reference/react)

### **Courses**
- [React Course](https://www.udemy.com/course/react-the-complete-guide/)
- [Modern React](https://frontendmasters.com/courses/react/)
- [React Patterns](https://kentcdodds.com/workshops/react-performance)

### **Practice**
- [React Examples](https://react.dev/learn)
- [React Patterns](https://reactpatterns.com/)

---

**Ready to master React? Start building components!** 🚀

---

*Last Updated: 2024*
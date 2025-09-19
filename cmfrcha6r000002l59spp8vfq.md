---
title: "React Hooks - Complete Interview Guide"
datePublished: Fri Sep 19 2025 21:21:33 GMT+0000 (Coordinated Universal Time)
cuid: cmfrcha6r000002l59spp8vfq
slug: react-hooks-complete-interview-guide

---

## What is a Hook?

A **hook** is a special function that starts with "use" (like `useState`, `useEffect`) that lets you "hook into" React features.

### Before Hooks:

* Want to store data? → Must use class components
    
* Function components were "dumb" - couldn't remember anything
    

### After Hooks:

* Function components can now remember things and do everything class components could do!
    

---

## What is State?

**State** is a piece of information that changes over time across renders in React.

---

## useState - Component Memory

**What it does:** Enables function components to track state within component instances.

```javascript
const [state, setState] = useState(initialState);
```

**Key Point:** useState = "Component's memory box that triggers re-renders when changed"

**Think of it as:** 📦 A box that remembers and triggers updates

---

## useEffect - Side Effects Manager

**What it does:** Runs code after your component renders to the screen.

### Three Ways to Use:

**1\. Run After Every Render:**

```javascript
useEffect(() => {
  console.log('Component rendered!');
});
```

**2\. Run Once (When Component Appears):**

```javascript
useEffect(() => {
  fetchUserData();
}, []); // Empty array = run once
```

**3\. Run When Specific Values Change:**

```javascript
useEffect(() => {
  document.title = `Count: ${count}`;
}, [count]); // Run when count changes
```

### Cleanup (Prevent Memory Leaks):

```javascript
useEffect(() => {
  const timer = setInterval(() => {
    console.log('Running...');
  }, 1000);
  
  return () => clearInterval(timer); // Stop timer when done
}, []);
```

**Key Point:** useEffect = "Runs side effects after component finishes rendering to screen"

**Think of it as:** ⚡ After-render cleanup crew

---

## useRef - Persistent Reference

**What it does:** Lets you:

* Keep a value between renders (without causing re-renders when it changes)
    
* Directly access DOM elements (like input, div, etc.)
    

```javascript
import { useState, useRef } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);
  const renderCount = useRef(0); 

  renderCount.current = renderCount.current + 1;

  return (
    <div>
      <p>Clicked: {count} times</p>
      <p>Component Rendered: {renderCount.current} times</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

**Key Difference:**

* `count` (useState) → Updates and re-renders component
    
* `renderCount` (useRef) → Updates quietly without re-rendering
    

---

## useMemo - Smart Calculator

**What it does:** Remembers the result of expensive calculations so you don't repeat them unnecessarily.

```javascript
const expensiveValue = useMemo(() => {
  return doExpensiveCalculation();
}, [dependencies]);
```

### When to Use:

* Heavy calculations that slow down your component
    
* Creating objects/arrays that other hooks depend on
    
* Filtering/sorting large lists
    

### Example:

```javascript
function ExpensiveList({ items, filter }) {
  // Without useMemo - filters on every render
  const filteredItems = items.filter(item => item.category === filter);
  
  // With useMemo - only filters when items or filter changes
  const filteredItems = useMemo(() => {
    return items.filter(item => item.category === filter);
  }, [items, filter]);
  
  return <div>{filteredItems.map(...)}</div>;
}
```

**Key Point:** useMemo = "Smart calculator that only recalculates when inputs change"

**Think of it as:** 🧮 Lazy calculator

**Note:** Don't overuse it! Only use for actually expensive operations.

---

## useCallback - Function Recycler

**What it does:** Remembers a function so you don't create a new one on every render.

### Why You Need It:

Functions are recreated on every render, which can cause unnecessary re-renders in child components.

### Without useCallback:

```javascript
function Parent() {
  const [count, setCount] = useState(0);
  
  // New function created every render!
  const handleClick = () => {
    console.log('Clicked!');
  };
  
  return <Child onClick={handleClick} />; // Child re-renders every time
}
```

### With useCallback:

```javascript
function Parent() {
  const [count, setCount] = useState(0);
  
  // Same function reference every render
  const handleClick = useCallback(() => {
    console.log('Clicked!');
  }, []); // No dependencies = same function always
  
  return <Child onClick={handleClick} />; // Child doesn't re-render unnecessarily
}
```

**Key Point:** useCallback = "Function photocopier that returns same copy until dependencies change"

**Think of it as:** 📋 Function recycler

---

## useReducer - State Controller

**What it does:** Like `useState` but for complex state with multiple related values or complicated update logic.

### Benefits:

* One place for all state update logic
    
* Impossible to have inconsistent state
    
* Easier testing - just test the reducer function
    
* Clearer intent - action types describe what's happening
    

### Example:

```javascript
function todoReducer(todos, action) {
  switch (action.type) {
    case 'add':
      return [...todos, { id: Date.now(), text: action.text, done: false }];
    case 'toggle':
      return todos.map(todo => 
        todo.id === action.id ? { ...todo, done: !todo.done } : todo
      );
    case 'delete':
      return todos.filter(todo => todo.id !== action.id);
    default:
      return todos;
  }
}

function TodoApp() {
  const [todos, dispatch] = useReducer(todoReducer, []);
  
  const addTodo = (text) => dispatch({ type: 'add', text });
  const toggleTodo = (id) => dispatch({ type: 'toggle', id });
  const deleteTodo = (id) => dispatch({ type: 'delete', id });
  
  return (
    <div>
      {todos.map(todo => 
        <TodoItem key={todo.id} todo={todo} onToggle={toggleTodo} onDelete={deleteTodo} />
      )}
    </div>
  );
}
```

### Why useReducer Instead of useState?

**useState Approach (Messy):**

```javascript
const addItem = (item) => {
  setLoading(true);
  setItems(prev => [...prev, item]);
  setTotal(prev => prev + item.price);
  setItemCount(prev => prev + 1);
  if (item.price > 100) {
    setDiscount(10);
  }
  setLoading(false);
};
```

**useReducer Approach (Clean):**

```javascript
const addItem = (item) => {
  dispatch({ type: 'add_item', payload: item });
  // Reducer handles ALL the complex logic in one place!
};
```

**Key Point:** useReducer = "State manager with actions - like useState with superpowers"

**Think of it as:** 🎮 State controller with action buttons

---

## useContext - Data Broadcaster

**What it does:** Shares data between components without passing props down through every level.

### The Problem (Prop Drilling):

```javascript
function App() {
  const user = { name: 'John', theme: 'dark' };
  return <Header user={user} />;
}

function Header({ user }) {
  return <Navigation user={user} />; // Just passing it down
}

function Navigation({ user }) {
  return <UserMenu user={user} />; // Still passing it down
}

function UserMenu({ user }) {
  return <div>Welcome {user.name}</div>; // Finally using it!
}
```

### The Solution - Context:

**1\. Create Context:**

```javascript
const UserContext = createContext();
```

**2\. Provide Data at Top Level:**

```javascript
function App() {
  const user = { name: 'John', theme: 'dark' };
  
  return (
    <UserContext.Provider value={user}>
      <Header />
    </UserContext.Provider>
  );
}
```

**3\. Use Data Anywhere Below:**

```javascript
function UserMenu() {
  const user = useContext(UserContext); // Get data directly!
  return <div>Welcome {user.name}</div>;
}

function Header() {
  return <Navigation />; // No props needed!
}

function Navigation() {
  return <UserMenu />; // No props needed!
}
```

### Real Example - Theme System:

```javascript
// 1. Create context
const ThemeContext = createContext();

// 2. Provider component
function App() {
  const [theme, setTheme] = useState('light');
  
  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      <Header />
      <MainContent />
      <Footer />
    </ThemeContext.Provider>
  );
}

// 3. Use anywhere
function Header() {
  const { theme, setTheme } = useContext(ThemeContext);
  
  return (
    <header style={{ backgroundColor: theme === 'dark' ? '#333' : '#fff' }}>
      <button onClick={() => setTheme(theme === 'dark' ? 'light' : 'dark')}>
        Toggle Theme
      </button>
    </header>
  );
}

function MainContent() {
  const { theme } = useContext(ThemeContext);
  
  return (
    <main style={{ color: theme === 'dark' ? '#fff' : '#000' }}>
      Content here
    </main>
  );
}
```

### When to Use:

* Global data (user info, theme, language)
    
* Deeply nested components need the same data
    
* Avoid prop drilling
    

### When NOT to Use:

* Local component state
    
* Performance critical data that changes frequently
    
* Simple parent-child communication
    

**Key Point:** useContext = "Wireless data sharing that skips prop drilling completely"

**Think of it as:** 📡 Data broadcaster to all subscribers

---

## React Hooks - Memory Tricks 🧠

### One-Line Descriptions:

* **useState** = "Component's memory box that triggers re-renders when changed"
    
* **useEffect** = "Runs side effects after component finishes rendering to screen"
    
* **useMemo** = "Smart calculator that only recalculates when inputs change"
    
* **useCallback** = "Function photocopier that returns same copy until dependencies change"
    
* **useReducer** = "State manager with actions - like useState with superpowers"
    
* **useContext** = "Wireless data sharing that skips prop drilling completely"
    

### Visual Memory Aids:

* **useState** → 📦 A box that remembers and triggers updates
    
* **useEffect** → ⚡ After-render cleanup crew
    
* **useMemo** → 🧮 Lazy calculator
    
* **useCallback** → 📋 Function recycler
    
* **useReducer** → 🎮 State controller with action buttons
    
* **useContext** → 📡 Data broadcaster to all subscribers
    

### Ultra-Short Version:

* **useState** → Remembers values
    
* **useEffect** → Runs after render
    
* **useMemo** → Caches calculations
    
* **useCallback** → Caches functions
    
* **useReducer** → Manages complex state
    
* **useContext** → Accesses shared data
    

---

Note: This writing is keeping notes for personal use, and GPT is used to rewrite it.
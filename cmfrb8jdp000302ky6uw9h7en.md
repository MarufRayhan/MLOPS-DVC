---
title: "Higher Order Components (HOCs) in React: A Quick Guide"
datePublished: Fri Sep 19 2025 20:46:46 GMT+0000 (Coordinated Universal Time)
cuid: cmfrb8jdp000302ky6uw9h7en
slug: higher-order-components-hocs-in-react-a-quick-guide

---

## What is an HOC?

A Higher Order Component is a function that takes a component and returns a new enhanced component. Think of it as a wrapper that adds extra features to your existing components.

```javascript
const withSomething = (Component) => {
  return (props) => {
    // Add extra logic here
    return <Component {...props} />;
  };
};
```

## Simple Example: Adding Loading State

**Before (Plain Component):**

```javascript
const UserCard = ({ name, email }) => {
  return (
    <div>
      <h2>{name}</h2>
      <p>{email}</p>
    </div>
  );
};
```

Creating an HOC:

```javascript
const withLoading = (Component) => {
  return (props) => {
    const [loading, setLoading] = useState(true);
    
    useEffect(() => {
      setTimeout(() => setLoading(false), 2000);
    }, []);
    
    if (loading) return <div>Loading...</div>;
    
    return <Component {...props} />;
  };
};
```

After (Enhanced Component):

```javascript
const UserCardWithLoading = withLoading(UserCard);

// Use it like any component
<UserCardWithLoading name="John" email="john@email.com" />
```

Real-World Example: Authentication

```javascript
const withAuth = (Component) => {
  return (props) => {
    const [isAuthenticated, setIsAuthenticated] = useState(false);
    
    useEffect(() => {
      const token = localStorage.getItem('token');
      setIsAuthenticated(!!token);
    }, []);
    
    if (!isAuthenticated) {
      return <div>Please login to continue</div>;
    }
    
    return <Component {...props} />;
  };
};

// Protect any component
const Dashboard = () => <div>Secret Dashboard</div>;
const ProtectedDashboard = withAuth(Dashboard);
```

## Why Use HOCs?

**Reusability:** Write once, use everywhere. The same `withAuth` can protect multiple components.

**Separation of Concerns:** Keep components focused. `Dashboard` handles dashboard logic, `withAuth` handles authentication.

## Key Rules to Remember

### 1\. Always Pass Props Through

```javascript
// ✅ Good - preserves all props
return <Component {...props} />;

// ❌ Bad - loses props
return <Component />;
```

### 2\. Name Convention

Always prefix with "with": `withAuth`, `withRouter`, `withTheme`

3\. Create Outside Render

```javascript
// ✅ Good
const Enhanced = withAuth(MyComponent);
function App() {
  return <Enhanced />;
}

// ❌ Bad - creates new component every render
function App() {
  const Enhanced = withAuth(MyComponent); // Don't do this!
  return <Enhanced />;
}
```

## HOCs vs Modern Alternatives

**HOCs** are great when you need to:

* Wrap components with additional JSX
    
* Conditionally render different components
    
* Work with older codebases
    

**Custom Hooks** are better when you:

* Only need to share logic (not JSX)
    
* Want more flexibility
    
* Are building new features
    
    ```javascript
    // Modern Hook approach
    const useAuth = () => {
      const [isAuthenticated, setIsAuthenticated] = useState(false);
      // auth logic
      return isAuthenticated;
    };
    
    function Dashboard() {
      const isAuthenticated = useAuth();
      if (!isAuthenticated) return <div>Please login</div>;
      return <div>Dashboard</div>;
    }
    ```
    
    ## Common Interview Questions
    
    **Q: What happens to the original component?** A: Nothing! HOCs don't modify the original component. They create a new one that renders the original.
    
    **Q: Can I use multiple HOCs?** A: Yes! You can compose them: `withA(withB(withC(Component)))`
    
    **Q: What's the {...props} for?** A: It passes all props through to the wrapped component, ensuring it receives everything it needs.
    
    ## Quick Tip for Interviews
    
    When explaining HOCs, use this simple analogy:
    
    * **Component** = A gift (toy)
        
    * **HOC** = Gift wrapping
        
    * **Enhanced Component** = Wrapped gift (same toy, now with wrapping)
        
    
    The wrapping doesn't change the toy - it just adds something extra around it!
    

*Note: This writing is for personal use, and GPT is used for its enhancement.*
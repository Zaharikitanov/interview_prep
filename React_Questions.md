# React Interview Questions

[← Back to Main Appendix](README.md#appendix)

---

## Table of Contents

- [Virtual DOM](#virtual-dom)
- [React Hooks - useMemo vs useCallback](#react-hooks---usememo-vs-usecallback)
- [useEffect Cleanup](#useeffect-cleanup)
- [Custom Hooks](#custom-hooks)
- [Redux vs React Query](#redux-vs-react-query)
- [Performance Optimization](#performance-optimization)

---

## Virtual DOM

**Q:** What is the Virtual DOM and why does React use it?

**A:**

**Definition:** A lightweight JavaScript representation of the actual DOM.

**Why React uses it:**

- Direct DOM manipulation is expensive (slow)
- Virtual DOM enables efficient updates
- React can batch updates and minimize reflows/repaints

**How it works:**

1. **State changes** → new Virtual DOM tree created
2. **Diffing** → React compares new Virtual DOM with previous snapshot
3. **Reconciliation** → Calculate minimal set of changes needed
4. **Patch** → Update only changed parts in real DOM

**Process Example:**

```javascript
// User clicks a button, state changes
setState({ count: count + 1 });

// Virtual DOM before: <div>Count: 0</div>
// Virtual DOM after:  <div>Count: 1</div>
// Diff: Only text node changed
// Real DOM update: Only update the text node (not entire div)
```

**Benefits:**

- **Performance** - Fewer, batched DOM operations
- **Predictability** - Declarative updates (you say what UI should look like)
- **Cross-platform** - Same API can target different renderers (React Native, React VR)

**Key Concepts:**

- **Reconciliation** - Process of diffing two Virtual DOM trees
- **Keys** - Help React identify which items changed/added/removed in lists
- **Fiber** - React's reconciliation engine (can pause/resume work)

**Common misconceptions:**

- Virtual DOM is not always faster than direct DOM manipulation
- For simple apps, Virtual DOM adds overhead
- Benefit shows in complex apps with frequent updates

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## React Hooks - useMemo vs useCallback

**Q:** What's the difference between useMemo and useCallback?

**A:**

**useMemo** → memoizes a **value** (result of computation)
**useCallback** → memoizes a **function** (function reference)

Both prevent unnecessary re-computations and help optimize performance.

**useMemo Example:**

```javascript
const expensiveResult = useMemo(() => {
  // Expensive computation
  return data.reduce((acc, item) => acc + item.value, 0);
}, [data]); // Only recompute when 'data' changes
```

**useCallback Example:**

```javascript
const handleClick = useCallback(() => {
  console.log("clicked", userId);
}, [userId]); // Only recreate function when 'userId' changes
```

**When to use useMemo:**

- Expensive calculations (filtering, sorting large arrays)
- Derived state that's computationally intensive
- Preventing child re-renders when passing computed values as props

**When to use useCallback:**

- Passing callbacks to child components that use `React.memo`
- Dependencies in other hooks (useEffect, useMemo)
- Event handlers passed to optimized child components

**Practical Example:**

```javascript
function ParentComponent() {
  const [count, setCount] = useState(0);
  const [text, setText] = useState("");

  // Without useCallback - new function on every render
  const handleClick = () => {
    console.log("clicked");
  };

  // With useCallback - same function reference
  const handleClickMemo = useCallback(() => {
    console.log("clicked");
  }, []); // Empty deps = never changes

  return (
    <>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      {/* ChildComponent re-renders on every keystroke without useCallback */}
      <ChildComponent onClick={handleClickMemo} />
    </>
  );
}

const ChildComponent = React.memo(({ onClick }) => {
  console.log("Child rendered");
  return <button onClick={onClick}>Click me</button>;
});
```

**Relationship:**

```javascript
// useCallback returns memoized function
const fn = useCallback(() => doSomething(), [dep]);

// Is equivalent to:
const fn = useMemo(() => () => doSomething(), [dep]);
```

**When NOT to use:**

- Premature optimization (profile first!)
- Simple computations (overhead not worth it)
- No re-render issues or performance problems

**Red flags:**

- Overusing memoization everywhere (adds complexity)
- Wrong dependencies (stale closures, infinite loops)
- Not understanding when child components actually re-render

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## useEffect Cleanup

**Q:** Why do we need cleanup functions in useEffect?

**A:**

**Purpose:** Prevent memory leaks, side effects, and unexpected behavior.

**When cleanup runs:**

- Before the effect runs again (when dependencies change)
- When component unmounts

**Common use cases:**

**1. Event Listeners:**

```javascript
useEffect(() => {
  const handleScroll = () => {
    console.log("scrolling");
  };

  window.addEventListener("scroll", handleScroll);

  // Cleanup
  return () => {
    window.removeEventListener("scroll", handleScroll);
  };
}, []); // Empty deps = only run once
```

**2. Timers:**

```javascript
useEffect(() => {
  const timer = setTimeout(() => {
    console.log("Delayed action");
  }, 1000);

  // Cleanup
  return () => {
    clearTimeout(timer);
  };
}, []);
```

**3. Subscriptions:**

```javascript
useEffect(() => {
  const subscription = observable.subscribe((data) => {
    setData(data);
  });

  // Cleanup
  return () => {
    subscription.unsubscribe();
  };
}, []);
```

**4. API Calls (AbortController):**

```javascript
useEffect(() => {
  const controller = new AbortController();

  fetch("/api/data", { signal: controller.signal })
    .then((res) => res.json())
    .then((data) => setData(data))
    .catch((err) => {
      if (err.name === "AbortError") {
        console.log("Fetch aborted");
      }
    });

  // Cleanup
  return () => {
    controller.abort();
  };
}, []);
```

**5. WebSocket Connections:**

```javascript
useEffect(() => {
  const ws = new WebSocket("ws://localhost:8080");

  ws.onmessage = (event) => {
    setMessages((prev) => [...prev, event.data]);
  };

  // Cleanup
  return () => {
    ws.close();
  };
}, []);
```

**What happens without cleanup:**

```javascript
// BAD - no cleanup
useEffect(() => {
  window.addEventListener("resize", handleResize);
}, [handleResize]); // Adds new listener on every handleResize change!

// Result:
// - Multiple listeners pile up
// - All fire on resize event
// - Memory leak
// - Performance degradation
```

**Red flags:**

- Setting state after component unmounts
- Not cleaning up subscriptions/listeners
- Memory leaks from uncanceled requests
- Multiple event listeners accumulating

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Custom Hooks

**Q:** What are custom hooks and when would you create one?

**A:**

**Definition:** Functions that start with "use" and can call other hooks. Extract and reuse stateful logic across components.

**When to create:**

1. **Reusing stateful logic** across multiple components
2. **Sharing complex logic** without prop drilling
3. **Abstracting component logic** for better readability
4. **Encapsulating side effects** and state management

**Common Use Cases:**

**1. Data Fetching:**

```javascript
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const controller = new AbortController();

    setLoading(true);
    fetch(url, { signal: controller.signal })
      .then((res) => res.json())
      .then((data) => {
        setData(data);
        setLoading(false);
      })
      .catch((err) => {
        if (err.name !== "AbortError") {
          setError(err);
          setLoading(false);
        }
      });

    return () => controller.abort();
  }, [url]);

  return { data, loading, error };
}

// Usage
function UserProfile({ userId }) {
  const { data: user, loading, error } = useFetch(`/api/users/${userId}`);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  return <div>{user.name}</div>;
}
```

**2. Form Handling:**

```javascript
function useForm(initialValues) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});

  const handleChange = (e) => {
    const { name, value } = e.target;
    setValues((prev) => ({ ...prev, [name]: value }));
  };

  const handleSubmit = (callback) => (e) => {
    e.preventDefault();
    // Validate, then call callback
    if (Object.keys(errors).length === 0) {
      callback(values);
    }
  };

  return { values, errors, handleChange, handleSubmit };
}

// Usage
function LoginForm() {
  const { values, handleChange, handleSubmit } = useForm({
    email: "",
    password: "",
  });

  const onSubmit = (data) => {
    console.log("Submitting:", data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input name="email" value={values.email} onChange={handleChange} />
      <input name="password" value={values.password} onChange={handleChange} />
      <button type="submit">Login</button>
    </form>
  );
}
```

**3. Browser APIs:**

```javascript
function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(error);
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

// Usage
function Settings() {
  const [theme, setTheme] = useLocalStorage("theme", "light");

  return (
    <button onClick={() => setTheme(theme === "light" ? "dark" : "light")}>
      Toggle Theme (Current: {theme})
    </button>
  );
}
```

**4. Window Size / Media Queries:**

```javascript
function useWindowSize() {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight,
  });

  useEffect(() => {
    const handleResize = () => {
      setSize({
        width: window.innerWidth,
        height: window.innerHeight,
      });
    };

    window.addEventListener("resize", handleResize);
    return () => window.removeEventListener("resize", handleResize);
  }, []);

  return size;
}
```

**5. Debounce:**

```javascript
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => clearTimeout(handler);
  }, [value, delay]);

  return debouncedValue;
}

// Usage
function SearchInput() {
  const [searchTerm, setSearchTerm] = useState("");
  const debouncedSearchTerm = useDebounce(searchTerm, 500);

  useEffect(() => {
    if (debouncedSearchTerm) {
      // Perform search
      console.log("Searching for:", debouncedSearchTerm);
    }
  }, [debouncedSearchTerm]);

  return (
    <input value={searchTerm} onChange={(e) => setSearchTerm(e.target.value)} />
  );
}
```

**Rules for Custom Hooks:**

1. **Must start with "use" prefix** (React enforces this)
2. **Can call other hooks inside** (useState, useEffect, other custom hooks)
3. **Follow all hooks rules** (top level, React functions only)
4. **Return values/functions** that components need
5. **Not tied to a specific component** (reusable logic)

**Benefits:**

- **DRY** (Don't Repeat Yourself)
- **Easier testing** (test hooks independently with `@testing-library/react-hooks`)
- **Better code organization** (separate concerns)
- **Share logic** without render props or HOCs
- **Composable** (hooks can use other hooks)

**Red flags:**

- Not following the "use" prefix convention
- Side effects without proper cleanup
- Creating hooks for non-reusable logic
- Too many responsibilities in one hook
- Not understanding hook dependency arrays

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Redux vs React Query

**Q:** When would you use Redux and when React Query?

**A:**

**Redux** → **Client state management**
**React Query** → **Server state management**

They solve different problems and are often used together.

**Redux (Client State):**

Use for:

- **UI state** (modals, tabs, sidebars open/closed)
- **Auth state** (user info, tokens, permissions)
- **Form state** (complex multi-step forms)
- **Filters & preferences** (search filters, sorting, pagination UI state)
- **Theme & settings** (dark mode, language)
- **Global state** needed by many components

**React Query (Server State):**

Use for:

- **Data fetching** from APIs
- **Caching** server responses
- **Background refetching** (keep data fresh)
- **Automatic retries** on failure
- **Optimistic updates**
- **Pagination & infinite scroll**
- **Request deduplication**

**Key Differences:**

| Feature            | Redux           | React Query  |
| ------------------ | --------------- | ------------ |
| **Purpose**        | Client state    | Server state |
| **Data source**    | In-memory       | API/Server   |
| **Caching**        | Manual          | Automatic    |
| **Refetching**     | Manual          | Automatic    |
| **Loading states** | Manual          | Built-in     |
| **Boilerplate**    | More            | Less         |
| **Stale data**     | Manual handling | Automatic    |

**Example - Bad (Redux for everything):**

```javascript
// DON'T: Storing API data in Redux
const fetchUsers = () => async (dispatch) => {
  dispatch({ type: "FETCH_USERS_REQUEST" });
  try {
    const response = await fetch("/api/users");
    const data = await response.json();
    dispatch({ type: "FETCH_USERS_SUCCESS", payload: data });
  } catch (error) {
    dispatch({ type: "FETCH_USERS_ERROR", payload: error });
  }
};
```

**Example - Good (React Query for API data):**

```javascript
// DO: Use React Query for API data
function useUsers() {
  return useQuery({
    queryKey: ["users"],
    queryFn: () => fetch("/api/users").then((res) => res.json()),
    staleTime: 5 * 60 * 1000, // 5 minutes
    refetchOnWindowFocus: true, // Auto-refetch when user returns
  });
}

function UsersList() {
  const { data, isLoading, error } = useUsers();

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  return (
    <ul>
      {data.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

**Common Interview Follow-ups:**

**Q:** Where would you store logged-in user info?

**A:** Depends on use case:

- **Authentication token** → React Query (from `/api/me` endpoint)
- **User permissions/roles** → React Query (fetched from server)
- **Current auth state** (logged in/out) → Redux or Context (UI state)
- **User preferences** (theme, language) → Redux or localStorage

**Q:** Would you store API data in Redux? Why / why not?

**A:** **Generally no:**

- Redux doesn't handle caching, refetching, stale data
- Requires manual loading/error states
- Increases boilerplate significantly
- React Query handles this better

**Use Redux for API data only if:**

- Need complex client-side transformations
- Sharing same data across many unrelated features
- Need to replay actions for debugging (Redux DevTools)

**Using Both Together:**

```javascript
// Redux: UI state
const uiSlice = createSlice({
  name: "ui",
  initialState: { theme: "light", sidebarOpen: true },
  reducers: {
    toggleTheme: (state) => {
      state.theme = state.theme === "light" ? "dark" : "light";
    },
  },
});

// React Query: Server data
function useProjects() {
  return useQuery({
    queryKey: ["projects"],
    queryFn: fetchProjects,
  });
}

function Dashboard() {
  const theme = useSelector((state) => state.ui.theme);
  const { data: projects } = useProjects();

  return (
    <div className={theme}>
      <ProjectsList projects={projects} />
    </div>
  );
}
```

**Red flags:**

- "React Query replaces Redux" (they serve different purposes)
- Using Redux for everything including API data
- Not understanding the difference between client and server state
- Manually managing cache invalidation
- Duplicating server data in multiple state management solutions

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Performance Optimization

**Q:** How would you optimize a slow React component?

**A:**

**Step-by-step approach:**

**1. Profile First (Don't guess!)**

```javascript
// Use React DevTools Profiler
// Record interaction → Identify slow components
// Look for:
// - Components rendering too often
// - Components taking long to render
// - Unnecessary renders
```

**2. Prevent Unnecessary Re-renders:**

**React.memo (for class components: PureComponent):**

```javascript
// Memoize component - only re-render if props change
const ExpensiveComponent = React.memo(({ data }) => {
  return <div>{/* expensive rendering */}</div>;
});

// With custom comparison
const MyComponent = React.memo(
  ({ user }) => <div>{user.name}</div>,
  (prevProps, nextProps) => {
    // Return true if props are equal (skip render)
    return prevProps.user.id === nextProps.user.id;
  },
);
```

**useMemo - Memoize expensive computations:**

```javascript
function DataTable({ data }) {
  // Bad: filters on every render (even when data unchanged)
  const filteredData = data.filter((item) => item.active);

  // Good: only recompute when data changes
  const filteredData = useMemo(
    () => data.filter((item) => item.active),
    [data],
  );

  return <Table data={filteredData} />;
}
```

**useCallback - Memoize functions:**

```javascript
function Parent() {
  const [count, setCount] = useState(0);

  // Bad: new function on every render
  const handleClick = () => {
    console.log("clicked");
  };

  // Good: same function reference
  const handleClick = useCallback(() => {
    console.log("clicked");
  }, []);

  return <Child onClick={handleClick} />;
}
```

**3. Virtualize Long Lists:**

```javascript
import { FixedSizeList } from "react-window";

function VirtualizedList({ items }) {
  const Row = ({ index, style }) => (
    <div style={style}>{items[index].name}</div>
  );

  return (
    <FixedSizeList
      height={600}
      width="100%"
      itemCount={items.length}
      itemSize={50}
    >
      {Row}
    </FixedSizeList>
  );
}
```

**4. Code Splitting / Lazy Loading:**

```javascript
import { lazy, Suspense } from "react";

// Lazy load heavy components
const HeavyComponent = lazy(() => import("./HeavyComponent"));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <HeavyComponent />
    </Suspense>
  );
}

// Route-based code splitting
const Dashboard = lazy(() => import("./Dashboard"));
const Settings = lazy(() => import("./Settings"));
```

**5. Avoid Inline Function Definitions:**

```javascript
// Bad: creates new function on every render
<button onClick={() => handleDelete(id)}>Delete</button>

// Better options:

// Option 1: useCallback
const handleClick = useCallback(() => handleDelete(id), [id]);
<button onClick={handleClick}>Delete</button>

// Option 2: Event delegation (for lists)
<ul onClick={(e) => {
  if (e.target.matches('.delete-btn')) {
    handleDelete(e.target.dataset.id);
  }
}}>
  <li><button className="delete-btn" data-id={id}>Delete</button></li>
</ul>
```

**6. Avoid Inline Object/Array Creation:**

```javascript
// Bad: new object on every render
<Component style={{ margin: 10 }} />
<Component data={[1, 2, 3]} />

// Good: define outside or use useMemo
const style = { margin: 10 };
<Component style={style} />

// Or
const style = useMemo(() => ({ margin: spacing }), [spacing]);
```

**7. Debounce Expensive Operations:**

```javascript
function Search() {
  const [term, setTerm] = useState("");
  const debouncedTerm = useDebounce(term, 300);

  useEffect(() => {
    // Only search after user stops typing
    if (debouncedTerm) {
      performSearch(debouncedTerm);
    }
  }, [debouncedTerm]);

  return <input onChange={(e) => setTerm(e.target.value)} />;
}
```

**8. Use Keys Correctly:**

```javascript
// Bad: index as key (breaks when order changes)
{
  items.map((item, index) => <Item key={index} data={item} />);
}

// Good: stable unique identifier
{
  items.map((item) => <Item key={item.id} data={item} />);
}
```

**9. Optimize Context:**

```javascript
// Bad: entire context re-renders all consumers
const AppContext = React.createContext();

// Better: Split contexts by update frequency
const UserContext = React.createContext(); // Changes rarely
const ThemeContext = React.createContext(); // Changes occasionally
const UIContext = React.createContext(); // Changes frequently
```

**10. Web Workers for Heavy Computation:**

```javascript
// Move CPU-intensive work off main thread
const worker = new Worker("worker.js");

function HeavyComponent({ data }) {
  const [result, setResult] = useState(null);

  useEffect(() => {
    worker.postMessage(data);
    worker.onmessage = (e) => setResult(e.data);
  }, [data]);

  return <div>{result}</div>;
}
```

**Common Mistakes:**

- **Premature optimization** - profile before optimizing
- **Over-memoizing** - adds complexity, may hurt performance
- **Wrong dependencies** in useMemo/useCallback
- **Not understanding** when components actually re-render
- **Optimizing the wrong thing** - focus on actual bottlenecks

**Profiling Tools:**

- React DevTools Profiler
- Chrome DevTools Performance tab
- Lighthouse
- Web Vitals (CLS, LCP, FID)

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

[← Back to Main Appendix](README.md#appendix)

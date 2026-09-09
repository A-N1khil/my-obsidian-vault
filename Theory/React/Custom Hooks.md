Suppose several components need to know whether the browser is online. You could write:
```tsx
const [isOnline, setIsOnline] =
  useState(navigator.onLine);

useEffect(() => {
  function handleOnline() {
    setIsOnline(true);
  }

  function handleOffline() {
    setIsOnline(false);
  }

  window.addEventListener("online", handleOnline);
  window.addEventListener("offline", handleOffline);

  return () => {
    window.removeEventListener("online", handleOnline);
    window.removeEventListener("offline", handleOffline);
  };
}, []);
```

Works. But now three components need it. You don't want to copy all that logic three times. Extract it:
```tsx
function useOnlineStatus() {
  const [isOnline, setIsOnline] =
    useState(navigator.onLine);

  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }

    function handleOffline() {
      setIsOnline(false);
    }

    window.addEventListener("online", handleOnline);
    window.addEventListener("offline", handleOffline);

    return () => {
      window.removeEventListener(
        "online",
        handleOnline
      );

      window.removeEventListener(
        "offline",
        handleOffline
      );
    };
  }, []);

  return isOnline;
}
```

Now
```tsx
function Header() {
  const isOnline = useOnlineStatus();

  return (
    <span>
      {isOnline ? "Online" : "Offline"}
    </span>
  );
}
```

That's a **custom hook**.

## Why Is It Called a Hook?

React hooks are functions such as:
```tsx
useState()
useEffect()
useRef()
```

A custom hook is simply a function that can compose other hooks:
```tsx
function useSomething() {
  const [state, setState] = useState(...);

  useEffect(...);

  const ref = useRef(...);

  return ...;
}
```

Conventionally, its name begins with `use`. For example:
```tsx
useApplications()
useOnlineStatus()
useDebounce()
useLocalStorage()
useAuth()
```

The `use` prefix also allows React tooling/lint rules to recognize it as a hook.

## Custom Hooks Share Logic, Not State

This distinction is **very important**. Suppose:
```tsx
function useCounter() {
  const [count, setCount] = useState(0);

  return {
    count,
    increment: () => setCount(c => c + 1),
  };
}
```

Now:
```tsx
function ComponentA() {
  const counter = useCounter();
}

function ComponentB() {
  const counter = useCounter();
}
```

These don't share the same `count`. You effectively get:
```text
ComponentA
    │
    └── useCounter()
            ↓
         state A


ComponentB
    │
    └── useCounter()
            ↓
         state B
```

The **logic is shared**. The **[[State and Data Flow|state]] is independent**.
## Custom Hook With Parameters

Hooks can receive arguments like normal functions. For example:
```tsx
function useDocumentTitle(title: string) {
  useEffect(() => {
    document.title = title;
  }, [title]);
}
```

Usage:
```tsx
function ApplicationsPage() {
  useDocumentTitle("Applications | ApplyFlow");

  return ...
}
```

Or they can use [[Components, JSX, and TSX#Props|props]]:
```tsx
function ApplicationPage({
  application,
}: Props) {
  useDocumentTitle(
    `${application.company} | ApplyFlow`
  );

  return ...
}
```

Now the synchronization logic lives in one reusable place.

## Custom Hooks Can Return Data and Functions

For example:
```tsx
function useToggle(initialValue = false) {
  const [value, setValue] =
    useState(initialValue);

  function toggle() {
    setValue(value => !value);
  }

  return {
    value,
    toggle,
  };
}
```

Usage:
```tsx
function ApplicationCard() {
  const {
    value: expanded,
    toggle,
  } = useToggle();

  return (
    <>
      <button onClick={toggle}>
        Details
      </button>

      {expanded && (
        <ApplicationDetails />
      )}
    </>
  );
}
```

This gives you a reusable behavioral API:
```
useToggle()
   │
   ├── value
   └── toggle()
```

## Practical Hook — `useLocalStorage`

Here's a more useful example.
Suppose ApplyFlow remembers the user's preferred view:
```tsx
type ViewMode = "grid" | "list";
```

We could build:
```tsx
function useLocalStorage<T>(
  key: string,
  initialValue: T
) {
  const [value, setValue] = useState<T>(() => {
    const stored = localStorage.getItem(key);

    return stored
      ? JSON.parse(stored)
      : initialValue;
  });

  useEffect(() => {
    localStorage.setItem(
      key,
      JSON.stringify(value)
    );
  }, [key, value]);

  return [value, setValue] as const;
}
```

Then:
```tsx
const [viewMode, setViewMode] =
  useLocalStorage<ViewMode>(
    "application-view",
    "grid"
  );
```

Usage feels almost like `useState`, but your hook handles persistence.
```tsx
setViewMode("list");
```

# Hooks and their design rules
## Hooks only belong in React Functions

Hooks should be called from **React components** or **custom hooks**
```tsx
/* components/application-card.tsx */
function ApplicationCard() {
  const [status, setStatus] = useState(...);
}

/* hooks/useApplications.tsx */
function useApplications() {
  const [applications, setApplications] =
    useState(...);
}
```

Not arbitrary utility functions:
```tsx
function calculatePrice() {
  const [price, setPrice] = useState(0); // ❌
}
```

## Hooks should be declared at the top level
Don't:
```tsx
if (loggedIn) {
  const [user, setUser] = useState(...); // ❌
}

for (...) {
  useEffect(...); // ❌
}

function handleClick() {
  const ref = useRef(null); // ❌
}
```

Instead:
```tsx
const [user, setUser] = useState(...);

if (loggedIn) {
  // use user
}
```

###  Why Does Hook Order Matter?

Consider:
```tsx
function Component() {
  const [name, setName] = useState("");
  const [age, setAge] = useState(0);
  const ref = useRef(null);
}
```

Conceptually React associates hook state by call order:
```
Component instance

Hook #1 → name state
Hook #2 → age state
Hook #3 → ref
```

If you conditionally call one:
```tsx
if (something) {
  useState(...);
}
```

The ordering could become:
```
Render #1

Hook #1 → name
Hook #2 → conditional state
Hook #3 → age


Render #2

Hook #1 → name
Hook #2 → age   ← 💥 expected something else
```

That's why hook calls need stable ordering between renders.
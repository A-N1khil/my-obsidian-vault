We already know that [[State and Data Flow#State preserves your data|State preserves your data]]. But changing state also causes React to re-render the component. What if we need to preserve data between renders but do not want a change to re-render the entire component? Enter `useRef`

```tsx
const countRef = useRef(0);
```

Unlike [[State Design with useState|useState]] `useRef` does not expose a setter function and a variable. It is an object stored exterior memory of a component function
```json
{
	current: 0
}
```
And it is read as `countRef.current`. It can be modified directly without a setter function.

## Ref survives the render
```tsx
function Counter() {
  const countRef = useRef(0);

  function handleClick() {
    countRef.current++;
    console.log(countRef.current);
  }

  return <button onClick={handleClick}>Click</button>;
}

/* console */
// 1
// 2
// 3
```

## Ref does not update the component
```tsx
function Counter() {
  const countRef = useRef(0);

  function handleClick() {
    countRef.current++;
  }

  return (
    <button onClick={handleClick}>
      {countRef.current} {/* Button always shows 1 */}
    </button>
  );
}
```

The Button will always show `1` because updating the ref does not re-render the UI

## Refs are mutable

We usually treat [[State and Data Flow#States must be treated as immutable|states as immutable]]. Updating a state needs to be created as a new object.
```tsx
// ❌
application.status = "INTERVIEW";

// ✅
setApplication(app => ({
  ...app,
  status: "INTERVIEW",
}));
```

However, refs don't need to be immutable. By design, refs can be mutable and can be updated directly. Thus, refs act as mutable storage which are also preserved by React.
,
> [!INFO] React Ref vs Normal Variable
> A normal variable and a ref are both mutable and do not re-render the UI. But normal variables are not preserved across renders, whereas refs are.

## Use for Refs 
### Scrolling
```tsx
function ApplicationsPage() {
  const formRef =
    useRef<HTMLDivElement>(null);

  function scrollToForm() {
    formRef.current?.scrollIntoView({
      behavior: "smooth",
    });
  }

  return (
    <>
      <button onClick={scrollToForm}>
        Add Application
      </button>

      <div ref={formRef}>
        <ApplicationForm />
      </div>
    </>
  );
}
```

### Timers

Suppose you need to retain a timer ID:
```tsx
const timerRef =
  useRef<ReturnType<typeof setTimeout> | null>(null);
```

Then:
```tsx
function startTimer() {
  timerRef.current = setTimeout(() => {
    console.log("Done");
  }, 1000);
}
```

Later:
```tsx
function cancelTimer() {
  if (timerRef.current) {
    clearTimeout(timerRef.current);
  }
}
```

The timer ID:
- needs to survive renders
- isn't something you display
Perfect ref candidate.

### Previous Values With Refs

Refs can also remember previous values.
```tsx
function ApplicationCard({
  application,
}: Props) {
  const previousStatus =
    useRef(application.status);

  useEffect(() => {
    console.log(
      `${previousStatus.current} → ${application.status}`
    );

    previousStatus.current =
      application.status;
  }, [application.status]);

  return ...
}
```

Suppose:
```
APPLIED
   ↓
INTERVIEW
   ↓
OFFER
```

The ref lets you retain the previous status without introducing extra rendered state.

# Custom Hooks

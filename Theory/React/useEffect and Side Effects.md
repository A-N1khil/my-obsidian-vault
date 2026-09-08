## Side Effects
Assume the following component
```tsx
function ApplicationCard({ application }: Props) {
  return (
    <div>
      <h2>{application.company}</h2>
      <p>{application.status}</p>
    </div>
  );
}
```

[[Rendering]] is mostly related to the UI and how the UI changes with changes to the [[State and Data Flow|states]]. But, in a real application, things like `applications` need to interact with external changes as well, such as DB retrieval. These interactions are called **side effects**
Some examples
For example:

```tsx
localStorage.setItem("theme", theme);
document.title = "ApplyFlow";
const timer = setInterval(...);
```

## `useEffect`
> `useEffect` lets you synchronize a React component with something outside React.

```tsx
import { useEffect } from "react";

useEffect(() => {
	// side effects
}, [dependencies]);
```

Example
```tsx
function ApplicationPage() {
  const [status, setStatus] = useState("APPLIED");

  useEffect(() => {
    document.title = `Applications - ${status}`;
  }, [status]);

  return ...
}
```

```mermaid
---
config:
 look: handDrawn
---
```
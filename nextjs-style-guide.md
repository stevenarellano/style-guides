# React/Next Frontend Guide

## Core Principles

-   **Treat UI as data** — minimize local state; avoid `useState` unless reactive/local.
-   **State**: Prefer state machines/reducer over multiple `useState`.
-   **Component abstraction**: Extract components when logic or if/else grows.
-   **Side effects**: Use explicit logic, minimize `useEffect` dependencies.
-   **Timing**: `setTimeout` is flaky; comment why if used.
-   **Goal**: Brevity, clarity, type safety, consistency.

---

## File & Code Structure

-   **<200 lines/target, <300/hard limit**; split if too large.
-   **Compact vertical space** (few/no blank lines).
-   **No comments** (only TODO or rare JSX structure).
-   **Self-documenting**: Clear names, TypeScript, focused functions/components.

```tsx
// BAD
function Component() {
	const [x, setX] = useState(0);

	const doIt = () => {
		setX(x + 1);
	};

	return (
		<div>
			<button onClick={doIt}>+</button>
		</div>
	);
}

// GOOD
function Component() {
	const [x, setX] = useState(0);
	const doIt = () => setX(x + 1);
	return (
		<div>
			<button onClick={doIt}>+</button>
		</div>
	);
}
```

**Spacing Rules**

-   0 blank lines between imports
-   1 blank after imports before code
-   0 inside functions (except before complex return)
-   1 between components

---

## Strong Typing

Always use explicit types and interfaces. Prefer named type aliases for re-use.

```tsx
type User = { id: string; email: string; name: string };
interface Props {
	user: User;
	onUpdate: (user: User) => void;
}
```

Co-locate shared types in `types.ts` and import as needed.

Prefer `interface` for objects, `type` for unions/aliases.

---

## Component Patterns

-   **Props**: Use interfaces, destructure in signature.
-   **Hooks at top**, return JSX at bottom.
-   **Early return** for conditional rendering (avoid nested ternaries).
-   **No in-JSX object/array literals;** compute outside or with `useMemo`.
-   **Complex state**: Use reducer if >5 related fields, else separate primitives with `useState`.
-   **Custom hook**: Move logic out if component grows long.

---

## Naming

-   **Components**: PascalCase, descriptive/specific (`UserCard`, not `Card`)
-   **Hooks**: camelCase, start with `use`, describe intent (`useUser`, `useIsMobile`)
-   **Vars/Fns**: camelCase; booleans as `isX`, arrays plural
-   **Constants**: ALL_CAPS for hard constants, otherwise camelCase

---

## Imports

Order:

1. React/Next
2. Third-party
3. Internal components
4. Hooks
5. Types
6. Utils/helpers

Use type-only imports when possible.

---

## Styling

-   Use `cn()` for class composition, avoid huge `className` strings.
-   Use CVA (`cva`) for variants.

---

## Error Handling

Use early returns, type-safe error state (discriminated unions).

---

## Utilities & Types

Centralize shared types in `types.ts`. Use utility types (`Pick`, `Omit`, `Partial`) as needed.

Use type guards for safe narrowing.

---

## Examples

```tsx
// Concise UserProfile example
import { useState, useEffect } from 'react';
type User = { id: string; name: string; email: string };
interface UserProfileProps {
	userId: string;
}
export function UserProfile({ userId }: UserProfileProps) {
	const [user, setUser] = useState<User | null>(null);
	const [loading, setLoading] = useState(false);
	const [error, setError] = useState<string | null>(null);
	const fetchUser = async () => {
		setLoading(true);
		setError(null);
		try {
			const r = await fetch(`/api/users/${userId}`);
			setUser(await r.json());
		} catch (e) {
			setError(e instanceof Error ? e.message : 'Unknown error');
		} finally {
			setLoading(false);
		}
	};
	useEffect(() => {
		fetchUser();
	}, [userId]);
	if (loading) return <div>Loading...</div>;
	if (error) return <div>Error: {error}</div>;
	if (!user) return null;
	return (
		<div>
			<h1>{user.name}</h1>
			<p>{user.email}</p>
		</div>
	);
}
```

---

## File Organization

Keep related code together (component, hooks, types, utils by feature).

---

## Performance

-   Use `useMemo` for expensive calculations.
-   Use context to avoid prop drilling.

---

## Testing

Write pure, testable utility functions and rendering logic.

---

## Checklist

-   [ ] <200 lines (or split)
-   [ ] No explanatory comments
-   [ ] All types explicit
-   [ ] Props/interface defined
-   [ ] Early returns
-   [ ] Import order correct
-   [ ] Good names
-   [ ] handleX/onX pattern for handlers
-   [ ] Hooks extracted if comp. is long
-   [ ] No inline object/array in JSX

---

**Summary:**  
Write concise, typed, readable code. Favor simple patterns. Components and files should be quick to review.  
Every file must be understandable in ~30 seconds!

---

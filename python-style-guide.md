# Backend Style Guide

## Core Principles

1. **Brevity**: Short files, minimal vertical space
2. **Clarity**: Code is self-documenting, no comments
3. **Type Safety**: Strong typing everywhere
4. **Invariants**: Fail fast with assertions

---

## File Structure

### Maximum File Length

-   **Target**: <200 lines per file
-   **Hard limit**: <300 lines
-   **Action**: If longer, split into smaller modules

### Vertical Spacing

```python
# BAD: Too much whitespace
def foo():
    x = 1

    y = 2

    return x + y


# GOOD: Compact, readable
def foo():
    x = 1
    y = 2
    return x + y
```

### No Blank Lines

-   Between function definitions: **1 blank line** (Python standard)
-   Inside functions: **0 blank lines** (keep compact)
-   Between imports and code: **1 blank line**
-   Between logical sections: **Use a single line if absolutely necessary**

---

## No Comments

Code should be self-explanatory through:

-   Clear function names
-   Descriptive variable names
-   Type hints
-   Small, focused functions

### BAD (Comments explaining what)

```python
# Get the user from the database
user = await db.get_user(user_id)

# Check if user exists
if not user:
    # Raise 404 error
    raise HTTPException(status_code=404)
```

### GOOD (Self-documenting)

```python
user = await db.get_user(user_id)
if not user:
    raise HTTPException(status_code=404, detail="User not found")
```

### Exception: Type Hints as Documentation

```python
async def get_user_with_tenant(
    user_id: UUID,
    tenant_id: UUID
) -> tuple[User, Tenant]:
    """Returns user and their tenant."""
    user = await db.users.get(user_id)
    tenant = await db.tenants.get(tenant_id)
    assert user and tenant, "User or tenant not found"
    return user, tenant
```

---

## Strong Typing

### Always Use Type Hints

```python
# BAD: No types
async def process_request(data, user):
    result = await do_something(data)
    return result

# GOOD: Fully typed
async def process_request(
    data: Dict[str, Any],
    user: User
) -> ProcessResult:
    result = await do_something(data)
    return result
```

### Type Aliases for Complex Types

```python
# BAD: Repeated complex types
def foo() -> tuple[str, str, UUID]:
    ...

def bar() -> tuple[str, str, UUID]:
    ...

# GOOD: Named type alias
AuthData = tuple[str, str, UUID]  # (user_id, email, tenant_id)

def foo() -> AuthData:
    ...

def bar() -> AuthData:
    ...
```

### Use Pydantic Models

```python
# BAD: Dict with uncertain structure
def create_user(data: Dict[str, Any]) -> User:
    name = data.get("name")  # Might be None!
    email = data["email"]     # Might KeyError!
    ...

# GOOD: Validated Pydantic model
class CreateUserRequest(BaseModel):
    name: str
    email: str
    role: Optional[str] = None

def create_user(data: CreateUserRequest) -> User:
    ...  # All fields guaranteed to exist and be correct type
```

---

## Invariants & Assertions

### Fail Fast

```python
# BAD: Silent failures or late errors
def process_data(user_id, tenant_id):
    user = get_user(user_id)
    if user:
        tenant = get_tenant(tenant_id)
        if tenant:
            if user.tenant_id == tenant_id:
                return do_work(user, tenant)
    return None  # What went wrong?

# GOOD: Explicit invariants
def process_data(user_id: UUID, tenant_id: UUID) -> Result:
    user = get_user(user_id)
    assert user, f"User {user_id} not found"
    tenant = get_tenant(tenant_id)
    assert tenant, f"Tenant {tenant_id} not found"
    assert user.tenant_id == tenant_id, "User does not belong to tenant"
    return do_work(user, tenant)
```

### Check Preconditions

```python
# Use assertions for internal invariants
def divide(a: float, b: float) -> float:
    assert b != 0, "Division by zero"
    return a / b

# Use HTTPException for user-facing errors
async def get_api_key(key: str) -> ApiKey:
    assert key.startswith("herd_"), "Invalid key format"
    api_key = await db.api_keys.get(key)
    if not api_key:
        raise HTTPException(status_code=401, detail="Invalid API key")
    return api_key
```

---

## Code Organization

### Import Order

```python
# 1. Standard library
from typing import Any, Dict, Optional
from uuid import UUID

# 2. Third-party
from fastapi import HTTPException, Request
from pydantic import BaseModel

# 3. Local
from .db import orm
from .utils import generate_id
```

### One Blank Line Between Imports and Code

```python
from typing import Dict
from fastapi import HTTPException

async def foo():
    ...
```

### Function Order

1. Type definitions / constants
2. Helper functions
3. Main functions
4. Endpoints (if applicable)

---

## Formatting

### Line Length

-   **Target**: 88 characters (Black default)
-   **Hard limit**: 100 characters

### Function Signatures

```python
# BAD: Too long, hard to read
def create_response(request: Request, registry: Registry, auth_data: Tuple[str, str, UUID]) -> Dict[str, Any]:
    ...

# GOOD: Multi-line when needed
def create_response(
    request: Request,
    registry: Registry,
    auth_data: Tuple[str, str, UUID]
) -> Dict[str, Any]:
    ...
```

### Dictionary/List Formatting

```python
# Single line if short
data = {"model": "gpt-4", "temp": 0.7}

# Multi-line if long, but compact
data = {
    "model": "gpt-4",
    "temperature": 0.7,
    "max_tokens": 1000,
}
```

---

## Error Handling

### Specific Exception Types

```python
# BAD: Generic exception
except Exception:
    return None

# GOOD: Specific exceptions
except ValueError as e:
    raise HTTPException(status_code=400, detail=str(e))
except KeyError:
    raise HTTPException(status_code=404, detail="Resource not found")
```

### Early Returns

```python
# BAD: Nested conditions
def process(user, data):
    if user:
        if data:
            if validate(data):
                return do_work(user, data)
    return None

# GOOD: Early returns
def process(user: User, data: Dict) -> Result:
    if not user:
        raise ValueError("User required")
    if not data:
        raise ValueError("Data required")
    if not validate(data):
        raise ValueError("Invalid data")
    return do_work(user, data)
```

---

## Naming Conventions

### Functions

-   **Verbs**: `get_user`, `create_api_key`, `validate_token`
-   **Boolean returns**: `is_valid`, `has_access`, `can_create`

### Variables

-   **Descriptive**: `user_email` not `e`, `tenant_id` not `tid`
-   **Plurals for collections**: `users`, `api_keys`, `results`

### Constants

```python
# ALL_CAPS for true constants
MAX_RETRIES = 3
DEFAULT_TIMEOUT = 30

# Type aliases in PascalCase
AuthData = tuple[str, str, UUID]
```

---

## Examples

### Before (Verbose)

```python
"""
Authentication utilities for the API.
This module handles user authentication via JWT and API keys.
"""
from typing import Tuple
from uuid import UUID


async def get_current_user_from_api_key(request: Request) -> Tuple[str, str, UUID]:
    """
    FastAPI dependency to extract and validate API key.

    Args:
        request: The FastAPI request object

    Returns:
        Tuple containing (user_id, user_email, tenant_id)

    Raises:
        HTTPException: If API key is invalid or missing
    """
    # Get the authorization header from the request
    auth_header = request.headers.get("authorization")

    # Check if header exists and has correct format
    if not auth_header or not auth_header.startswith("Bearer "):
        raise HTTPException(
            status_code=401,
            detail="Missing or invalid authorization header"
        )

    # Extract the actual key (remove "Bearer " prefix)
    api_key = auth_header[7:]

    # Validate the key using the cache
    result = await api_key_cache.validate_api_key(api_key)

    # Return the user info if valid
    if not result:
        raise HTTPException(status_code=401, detail="Invalid API key")

    return result
```

### After (Concise)

```python
from typing import Tuple
from uuid import UUID
from fastapi import HTTPException, Request

AuthData = tuple[str, str, UUID]

async def get_current_user_from_api_key(request: Request) -> AuthData:
    auth_header = request.headers.get("authorization")
    if not auth_header or not auth_header.startswith("Bearer "):
        raise HTTPException(status_code=401, detail="Missing auth header")
    api_key = auth_header[7:]
    result = await api_key_cache.validate_api_key(api_key)
    if not result:
        raise HTTPException(status_code=401, detail="Invalid API key")
    return result
```

**Reduction: 40 lines → 11 lines** (72% smaller!)

---

## Enforcement

### Use Black Formatter

```bash
black --line-length 88 src/
```

### Use MyPy for Type Checking

```bash
mypy --strict src/
```

### Use Ruff for Linting

```bash
ruff check src/
```

---

## Checklist for New Code

-   [ ] File is <200 lines
-   [ ] No comments (except docstrings for public APIs)
-   [ ] All functions have type hints
-   [ ] Invariants use `assert`
-   [ ] Early returns instead of nesting
-   [ ] Single blank line between functions
-   [ ] No unnecessary blank lines
-   [ ] Import order: stdlib → third-party → local
-   [ ] Clear, descriptive names
-   [ ] Pydantic models for request/response

---

## Summary

**Write code that:**

-   ✅ Reads like prose
-   ✅ Fits on one screen
-   ✅ Catches errors early
-   ✅ Types guarantee correctness
-   ❌ Needs comments to understand
-   ❌ Requires scrolling to comprehend
-   ❌ Fails silently

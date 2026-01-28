---
name: js-style-guide
description: |
  JavaScript code style guidelines. Apply these rules when generating, editing,
  or reviewing JavaScript code. Triggers: "javascript", "js", "genera código js",
  "crea función", "escribe javascript", any JS/TS file creation or modification.
---

# JavaScript Style Guide

Apply these rules to ALL JavaScript code generated or modified.

## Core Rules

### No Semicolons
```javascript
// ✗ Bad
const name = "John";

// ✓ Good
const name = "John"
```

### Comments in English
```javascript
// ✗ Bad
// Obtener el usuario actual

// ✓ Good
// Get current user
```

### No jQuery
Use vanilla JS or modern frameworks. Never use jQuery.

```javascript
// ✗ Bad
$(".btn").click(fn)

// ✓ Good
document.querySelector(".btn").addEventListener("click", fn)
```

### Arrow Functions
Prefer arrow functions. Parentheses only when multiple parameters.

```javascript
// ✗ Bad
function double(n) { return n * 2 }
array.map((x) => x * 2)

// ✓ Good
const double = n => n * 2
array.map(x => x * 2)
array.reduce((acc, item) => acc + item, 0)
```

## Formatting

| Rule | Value |
|------|-------|
| Indentation | Tabs |
| Quotes | Double (`"`) |
| Trailing commas | No |

```javascript
// ✓ Correct formatting
const user = {
	name: "John",
	age: 30
}

const colors = [
	"red",
	"blue",
	"green"
]
```

## Variables

- Use `let` by default
- Use `const` only for true constants (config values, magic numbers)
- Use `UPPER_SNAKE_CASE` for constants, `camelCase` for everything else

```javascript
const MAX_RETRIES = 3
const API_URL = "https://api.example.com"

let userName = "John"
let isLoading = false
```

## Modern JS Features

Always use the latest JavaScript features.

### ES Modules
```javascript
// ✓ Good
import { getData } from "./api.js"
export const helper = () => {}

// ✗ Bad
const { getData } = require("./api")
module.exports = { helper }
```

### Async/Await
```javascript
// ✓ Good
const fetchUser = async id => {
	const response = await fetch(`/users/${id}`)
	return response.json()
}

// ✗ Bad
const fetchUser = id => {
	return fetch(`/users/${id}`)
		.then(response => response.json())
}
```

### Template Literals
Use for string interpolation.

```javascript
// ✓ Good
const message = `Hello, ${name}!`

// ✗ Bad
const message = "Hello, " + name + "!"
```

### Optional Chaining & Nullish Coalescing
```javascript
const city = user?.address?.city ?? "Unknown"
```

### Destructuring
Use when it improves readability.

```javascript
// ✓ Good - improves clarity
const { name, email } = user
const [first, second] = items

// ✓ Also good - simple access is fine
const name = user.name
```

## Quick Reference

```javascript
import { api } from "./services.js"

const MAX_ITEMS = 100

let items = []
let isLoading = false

// Fetch all items from API
const fetchItems = async () => {
	isLoading = true
	const response = await api.get("/items")
	items = response.data ?? []
	isLoading = false
}

// Filter items by category
const filterByCategory = category => {
	return items.filter(item => item.category === category)
}

// Process multiple items
const processItems = (items, callback) => {
	items.forEach(item => callback(item))
}
```

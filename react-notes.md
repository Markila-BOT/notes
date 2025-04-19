# React Notes & Interview Questions 📚

## Introduction

React is a popular JavaScript library for building user interfaces, particularly single-page applications. It's used by many large companies including Facebook, Instagram, Netflix, and Airbnb.

## Table of Contents

1. [Core Concepts](#core-concepts)
2. [Components & Props](#components--props)
3. [State & Lifecycle](#state--lifecycle)
4. [Hooks](#hooks)
5. [Project Setup](#project-setup)
6. [Interview Questions](#interview-questions)
7. [Resources](#resources)

## Core Concepts

- Virtual DOM
- JSX
  ```jsx
  function AboutPage() {
    return (
      <>
        <h1>About</h1>
        <p>
          Hello there.
          <br />
          How do you do?
        </p>
      </>
    );
  }
  ```
- Components
- Props
- State
- Lifecycle Methods

## Components & Props

### Functional Components

```jsx
function MyButton() {
  function handleClick() {
    alert("You clicked me!");
  }

  return <button onClick={handleClick}>Click me</button>;
}
```

### Class Components

```jsx
class Greeting extends Component {
  render() {
    return <h1>Hello, {this.props.name}!</h1>;
  }
}
```

### Props Handling

```jsx
function MyButton({ count, onClick }) {
  return <button onClick={onClick}>Clicked {count} times</button>;
}
```

## State & Lifecycle

### Using State

```jsx
import { useState } from "react";

function MyComponent() {
  const [count, setCount] = useState(0);
  // ...
}
```

### Effect Hook

```jsx
function SearchResults({ query }) {
  const [results, setResults] = useState([]);

  useEffect(() => {
    // Fetch data here
    // Don't forget cleanup!
    return () => {
      // Cleanup code
    };
  }, [query]);
}
```

## Hooks

- useState - State management
- useEffect - Side effects
- useContext - Context consumption
- useRef - DOM references
- useCallback - Memoized callbacks
- useMemo - Memoized values
- useImperativeHandle - Customizing ref handle
- Custom Hooks - Reusable stateful logic

## Project Setup

### Modern Setup (Recommended)

```bash
# Using Vite (Recommended)
npm create vite@latest my-app -- --template react

# Install dependencies
cd my-app
npm install
```

### Basic Dependencies

```bash
npm install react react-dom
```

### Development Tools

```bash
# Install React DevTools
npm install -g react-devtools
```

## Interview Questions

For a comprehensive list of React interview questions and answers, check out:
[React Interview Questions Repository](https://github.com/sudheerj/reactjs-interview-questions)

Key topics covered in the interview questions:

- React Fundamentals
- Components
- State Management
- Props & State
- Hooks
- Performance Optimization
- Best Practices
- Common Patterns
- Error Handling
- Testing

## Resources

- [Official React Documentation](https://react.dev)
- [React Router Documentation](https://reactrouter.com)
- [Redux Documentation](https://redux.js.org)
- [Testing Library Documentation](https://testing-library.com/docs/react-testing-library/intro)

---

_This document is regularly updated with the latest React best practices and interview questions._

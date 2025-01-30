### **React Class Components: Lifecycle Methods**

When working with class components in React, we don't have `useEffect()` like in functional components. Instead, React provides **lifecycle methods** to handle different stages of a component’s life.

Here are the three most important lifecycle methods:

---

### **1. `componentDidMount()`**

🔹 Called **once** when the component is mounted (inserted into the DOM).  
🔹 Used for fetching data, setting up subscriptions, or performing side effects.  
🔹 Equivalent to:

```jsx
useEffect(() => {
  // Effect logic
}, []); // Runs once when mounted
```

```jsx
componentDidMount() {
  console.log('UserFinder Mounted!');
  // Example: Fetch data
  this.setState({ filteredUsers: DUMMY_USERS });
}
```

---

### **2. `componentDidUpdate(prevProps, prevState)`**

🔹 Called **after every update** (whenever props or state changes).  
🔹 Used to respond to state/prop changes (e.g., fetching new data when search term updates).  
🔹 Equivalent to:

```jsx
useEffect(() => {
  // Effect logic
}, [dependency]); // Runs when `dependency` changes
```

```jsx
componentDidUpdate(prevProps, prevState) {
  if (prevState.searchTerm !== this.state.searchTerm) {
    this.setState({
      filteredUsers: DUMMY_USERS.filter((user) =>
        user.name.toLowerCase().includes(this.state.searchTerm.toLowerCase())
      ),
    });
  }
}
```

---

### **3. `componentWillUnmount()`**

🔹 Called **right before** the component is removed from the DOM.  
🔹 Used for cleanup (removing event listeners, canceling network requests, etc.).  
🔹 Equivalent to the cleanup function in `useEffect()`:

```jsx
useEffect(() => {
  return () => {
    // Cleanup logic
  };
}, []);
```

```jsx
componentWillUnmount() {
  console.log('UserFinder Unmounted!');
}
```

---

## **Transforming `UserFinder.js` to a Class-Based Component**

### **Before (Functional Component)**

This version uses `useState` and `useEffect()` to update the filtered users list.

```jsx
import { Fragment, useState, useEffect } from "react";
import Users from "./Users";
import classes from "./UserFinder.module.css";

const DUMMY_USERS = [
  { id: "u1", name: "Max" },
  { id: "u2", name: "Manuel" },
  { id: "u3", name: "Julie" }
];

const UserFinder = () => {
  const [filteredUsers, setFilteredUsers] = useState(DUMMY_USERS);
  const [searchTerm, setSearchTerm] = useState("");

  useEffect(() => {
    setFilteredUsers(
      DUMMY_USERS.filter((user) =>
        user.name.toLowerCase().includes(searchTerm.toLowerCase())
      )
    );
  }, [searchTerm]);

  const searchChangeHandler = (event) => {
    setSearchTerm(event.target.value);
  };

  return (
    <Fragment>
      <div className={classes.finder}>
        <input type="search" onChange={searchChangeHandler} />
      </div>
      <Users users={filteredUsers} />
    </Fragment>
  );
};

export default UserFinder;
```

---

### **After (Class-Based Component)**

```jsx
class UserFinder extends Component {
  constructor() {
    this.state = {
      filteredUsers: DUMMY_USERS,
      searchTerm: " "
    };
  }
  searchChangeHandler(event) {
    this.setState({
      searchTerm: event.target.value
    });
  }

  render() {
    return (
      <Fragment>
        <div className={classes.finder}>
          <input type="search" onChange={this.searchChangeHandler.bind(this)} />
        </div>
        <Users users={this.state.filteredUsers} />
      </Fragment>
    );
  }
}
```

This version has all the necessary changes except the useEffect bit. To implement that, we utilize the

componentDidUpdate() {} function.

But this creates a infinite loop because we set state, and re-render. So we need to know if the search term changed

```jsx

componentDidUpdate(prevProps, prevState) {
    if (prevProps.searchTerm !== this.state.searchTerm) {
      this.setState({
        filteredUsers: DUMMY_USERS.filter((user) =>
          user.name.includes(this.state.searchTerm)
        )
      });
    }
  }

```

This version replaces `useState` with `this.state` and `useEffect()` with lifecycle methods.

```jsx
import { Component, Fragment } from "react";
import Users from "./Users";
import classes from "./UserFinder.module.css";

const DUMMY_USERS = [
  { id: "u1", name: "Max" },
  { id: "u2", name: "Manuel" },
  { id: "u3", name: "Julie" }
];

class UserFinder extends Component {
  constructor() {
    super();
    this.state = {
      filteredUsers: DUMMY_USERS,
      searchTerm: ""
    };
  }

  componentDidMount() {
    console.log("UserFinder Mounted!");
    this.setState({ filteredUsers: DUMMY_USERS });
  }

  componentDidUpdate(prevProps, prevState) {
    if (prevState.searchTerm !== this.state.searchTerm) {
      this.setState({
        filteredUsers: DUMMY_USERS.filter((user) =>
          user.name.toLowerCase().includes(this.state.searchTerm.toLowerCase())
        )
      });
    }
  }

  componentWillUnmount() {
    console.log("UserFinder Unmounted!");
  }

  searchChangeHandler = (event) => {
    this.setState({ searchTerm: event.target.value });
  };

  render() {
    return (
      <Fragment>
        <div className={classes.finder}>
          <input type="search" onChange={this.searchChangeHandler} />
        </div>
        <Users users={this.state.filteredUsers} />
      </Fragment>
    );
  }
}

export default UserFinder;
```

---

### **Key Differences Between Functional and Class-Based Versions**

| Feature                   | Functional Component (`useEffect`)        | Class Component (`Lifecycle Methods`)                             |
| ------------------------- | ----------------------------------------- | ----------------------------------------------------------------- |
| **State Declaration**     | `useState(initialValue)`                  | `this.state = {}` in `constructor()`                              |
| **Updating State**        | `setState(newValue)`                      | `this.setState({ key: value })`                                   |
| **Initial Fetching**      | `useEffect(() => {...}, [])`              | `componentDidMount()`                                             |
| **Reactivity to Changes** | `useEffect(() => {...}, [dependency])`    | `componentDidUpdate(prevProps, prevState)`                        |
| **Cleanup Logic**         | `return () => {...}` inside `useEffect()` | `componentWillUnmount()`                                          |
| **Method Binding**        | Not needed (auto-bound)                   | Needs `this.method = this.method.bind(this)` (or arrow functions) |

---

### **Final Thoughts**

✅ **Class components require more boilerplate** compared to functional components with hooks.  
✅ **Lifecycle methods provide better control** but are less intuitive than `useEffect()`.  
✅ **React prefers functional components** because they are more readable, concise, and easier to manage with hooks.

Even though class components are **less common** in modern React, they are still used in legacy codebases. Understanding them helps in debugging older applications and preparing for React job interviews. 🚀

### **Implementing Context in `UserFinder` (Class vs Functional Components)**  

Using **React Context** helps manage global state across components without prop drilling. The key difference between **functional** and **class-based** components when using context is **how you consume the context**.

---

## **Setting Up Context (users-context.js)**  
Let's assume we created a new **Context Provider** inside `store/users-context.js`:

```jsx
import React, { Component, createContext } from "react";

const UsersContext = createContext({
  users: [],
});

export class UsersContextProvider extends Component {
  state = {
    users: [
      { id: "u1", name: "Max" },
      { id: "u2", name: "Manuel" },
      { id: "u3", name: "Julie" }
    ],
  };

  render() {
    return (
      <UsersContext.Provider value={{ users: this.state.users }}>
        {this.props.children}
      </UsersContext.Provider>
    );
  }
}

export default UsersContext;
```

---

## **Using Context in `UserFinder` (Class Component)**
In a **class-based component**, we can use **two approaches**:

### **Option 1: Using `contextType` (Recommended for Class Components)**
The easiest way is by setting a **static property** inside the class:

```jsx
import React, { Component, Fragment } from "react";
import Users from "./Users";
import classes from "./UserFinder.module.css";
import UsersContext from "../store/users-context";

class UserFinder extends Component {
  static contextType = UsersContext; // Attach context

  constructor() {
    super();
    this.state = {
      filteredUsers: [],
      searchTerm: ""
    };
  }

  componentDidMount() {
    this.setState({ filteredUsers: this.context.users }); // Fetch users from context
  }

  componentDidUpdate(prevProps, prevState) {
    if (prevState.searchTerm !== this.state.searchTerm) {
      this.setState({
        filteredUsers: this.context.users.filter((user) =>
          user.name.toLowerCase().includes(this.state.searchTerm.toLowerCase())
        ),
      });
    }
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

### **Why Use `static contextType = UsersContext;`?**
- This allows **`this.context`** to access the context **without needing a wrapper component**.  
- Best for **single context consumption** inside a class-based component.  
- **Downside:** Can only consume **one** context at a time.

---

### **Option 2: Using `Context.Consumer` (For Multiple Contexts)**
Alternatively, we can use **`UsersContext.Consumer`** inside the `render()` method:

```jsx
render() {
  return (
    <UsersContext.Consumer>
      {(context) => (
        <Fragment>
          <div className={classes.finder}>
            <input type="search" onChange={this.searchChangeHandler} />
          </div>
          <Users users={context.users} />
        </Fragment>
      )}
    </UsersContext.Consumer>
  );
}
```

### **Why Use `<UsersContext.Consumer>`?**
- Allows **multiple contexts** inside a single component.  
- More **flexible**, but introduces **nested JSX**, which can be **harder to read**.  

---

## **Using Context in `UserFinder` (Functional Component)**
For **functional components**, we use `useContext()` instead:

```jsx
import React, { useState, useEffect, useContext, Fragment } from "react";
import Users from "./Users";
import classes from "./UserFinder.module.css";
import UsersContext from "../store/users-context";

const UserFinder = () => {
  const usersCtx = useContext(UsersContext);
  const [filteredUsers, setFilteredUsers] = useState(usersCtx.users);
  const [searchTerm, setSearchTerm] = useState("");

  useEffect(() => {
    setFilteredUsers(usersCtx.users);
  }, [usersCtx.users]); // Re-run if context changes

  useEffect(() => {
    setFilteredUsers(
      usersCtx.users.filter((user) =>
        user.name.toLowerCase().includes(searchTerm.toLowerCase())
      )
    );
  }, [searchTerm, usersCtx.users]); // Re-run when searchTerm or users list changes

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

## **Comparison: Functional vs Class Components Using Context**
| Feature                 | Class Component (`contextType`) | Functional Component (`useContext()`) |
|-------------------------|--------------------------------|--------------------------------|
| **Ease of Use**         | Simple but limited to one context | More flexible, supports multiple contexts |
| **Readability**         | More boilerplate (`this.context`) | Cleaner syntax (`useContext()`) |
| **Performance**         | Can trigger unnecessary re-renders | More efficient, updates when context changes |
| **Multiple Contexts**   | Not supported (`contextType` supports only one) | Supported (`useContext()` multiple times) |

---

## **Final Takeaways**
✅ **Class Components:**
- Use **`static contextType = UsersContext;`** when consuming **one** context.
- Use **`UsersContext.Consumer`** when dealing with **multiple contexts**.

✅ **Functional Components:**
- Always use **`useContext(UsersContext)`** for a cleaner and more readable approach.
- Works well with multiple contexts without adding nesting.

🚀 **React favors functional components with hooks, so `useContext()` is the modern approach.**
### **Converting Functional Components to Class-Based Components**

Before React 16.8, **class-based components** were required for managing **state** and **side effects**. With the introduction of **React Hooks**, functional components could now handle these concerns.

However, class components **cannot use hooks**, so in this section, we’ll convert a **functional component into a class-based component** to understand how state was traditionally managed.

---

### **Functional Component (Before Conversion)**

Here's the `User` component as a **functional component** using React Hooks:

```jsx
import classes from "./User.module.css";

const User = (props) => {
  return <li className={classes.user}>{props.name}</li>;
};

export default User;
```
---



### **Next: Converting to a Class-Based Component**

We'll now transform this into a **class component**

```jsx
import {Component} from 'react';
import classes from "./User.module.css";

class User extends Component{
  render() {
    return <li className={classes.user}>{this.props.name}</li>;
  }
}

export default User;
```


### **Next: Converting Users.js to a Class-Based Component**

## Functional Approach: 

```jsx
import { useState } from 'react';
import User from './User';

import classes from './Users.module.css';

const DUMMY_USERS = [
  { id: 'u1', name: 'Max' },
  { id: 'u2', name: 'Manuel' },
  { id: 'u3', name: 'Julie' },
];

const Users = () => {
  const [showUsers, setShowUsers] = useState(true);

  const toggleUsersHandler = () => {
    setShowUsers((curState) => !curState);
  };

  const usersList = (
    <ul>
      {DUMMY_USERS.map((user) => (
        <User key={user.id} name={user.name} />
      ))}
    </ul>
  );

  return (
    <div className={classes.users}>
      <button onClick={toggleUsersHandler}>
        {showUsers ? 'Hide' : 'Show'} Users
      </button>
      {showUsers && usersList}
    </div>
  );
};

export default Users;
```

### Class Based Conversion: 

This is how state is defined in class based component:
```jsx
constructor(){
    this.state = {
        showUsers: false,
        more: 'TEST',
    }
  }
```
Heads up! State is always one object. It's not optional. 
Group all the state into one object 

No matter if the state are related or not. You can have multiple states inside but it must all be inside one obeject 
### **Converting a Functional Component to a Class-Based Component in React**  

Before React Hooks (`useState`, `useEffect`, etc.), **class-based components** were the only way to manage **state** and **lifecycle methods** in React. Converting a functional component to a class-based component helps us understand React's evolution and nuances like `this`, `super`, `bind`, and `setState()`.  

---

## **Functional Component (Before Conversion)**  
This is a **functional component** that uses `useState` to toggle the visibility of a list of users:  

```jsx
import { useState } from 'react';
import User from './User';
import classes from './Users.module.css';

const DUMMY_USERS = [
  { id: 'u1', name: 'Max' },
  { id: 'u2', name: 'Manuel' },
  { id: 'u3', name: 'Julie' },
];

const Users = () => {
  const [showUsers, setShowUsers] = useState(true);

  const toggleUsersHandler = () => {
    setShowUsers((curState) => !curState);
  };

  const usersList = (
    <ul>
      {DUMMY_USERS.map((user) => (
        <User key={user.id} name={user.name} />
      ))}
    </ul>
  );

  return (
    <div className={classes.users}>
      <button onClick={toggleUsersHandler}>
        {showUsers ? 'Hide' : 'Show'} Users
      </button>
      {showUsers && usersList}
    </div>
  );
};

export default Users;
```

### **Key Features of the Functional Component**  
✅ Uses the `useState` hook to manage state.  
✅ `toggleUsersHandler` updates `showUsers`.  
✅ Renders a list of users dynamically with `.map()`.  
✅ Uses **conditional rendering** (`{showUsers && usersList}`).  

---

## **Class-Based Conversion**  

### **Step 1: Define a Class Component**  
A class component extends `Component` from React.  

```jsx
import { Component } from 'react';
import User from './User';
import classes from './Users.module.css';

const DUMMY_USERS = [
  { id: 'u1', name: 'Max' },
  { id: 'u2', name: 'Manuel' },
  { id: 'u3', name: 'Julie' },
];

class Users extends Component {
  constructor() {
    super(); // Calls the parent class (Component) constructor
    this.state = {
      showUsers: true
    };
  }

  render() {
    return <div>Users Component</div>;
  }
}

export default Users;
```
### **Breakdown:**  
- `class Users extends Component {}` → Defines a class-based component.  
- `constructor()` → Initializes the component’s state.  
- `super();` → Calls the parent constructor (`Component`). **Must be called before using `this` in the constructor.**  
- `this.state = {}` → State must be defined inside an object. Unlike hooks, state is always an **object** in class components.  

---

### **Step 2: Adding State and Handling Updates**  

```jsx
class Users extends Component {
  constructor() {
    super();
    this.state = {
      showUsers: true
    };

    this.toggleUsersHandler = this.toggleUsersHandler.bind(this);
  }

  toggleUsersHandler() {
    this.setState((prevState) => {
      return { showUsers: !prevState.showUsers };
    });
  }

  render() {
    return (
      <div className={classes.users}>
        <button onClick={this.toggleUsersHandler}>
          {this.state.showUsers ? 'Hide' : 'Show'} Users
        </button>
      </div>
    );
  }
}
```

### **Key Differences from the Functional Approach**  
🟢 `this.state` → State is stored as an **object**.  
🟢 `this.setState()` → Unlike hooks (`useState`), updates must be done via `setState()`, which merges new state into the existing object.  
🟢 **Binding `this` in the constructor**  
   - `this.toggleUsersHandler = this.toggleUsersHandler.bind(this);`  
   - In JavaScript, class methods are **not automatically bound** to the class instance. If we don’t bind `this`, `toggleUsersHandler` will be `undefined` when called from `onClick`.  
   - Alternative: Use an **arrow function** (`toggleUsersHandler = () => {}`) to auto-bind `this` without manually calling `.bind()`.  

---

### **Step 3: Adding the User List and Handling Re-Rendering**  

```jsx
class Users extends Component {
  constructor() {
    super();
    this.state = {
      showUsers: true
    };

    this.toggleUsersHandler = this.toggleUsersHandler.bind(this);
  }

  toggleUsersHandler() {
    this.setState((prevState) => ({
      showUsers: !prevState.showUsers
    }));
  }

  render() {
    const usersList = (
      <ul>
        {DUMMY_USERS.map((user) => (
          <User key={user.id} name={user.name} />
        ))}
      </ul>
    );

    return (
      <div className={classes.users}>
        <button onClick={this.toggleUsersHandler}>
          {this.state.showUsers ? 'Hide' : 'Show'} Users
        </button>
        {this.state.showUsers && usersList}
      </div>
    );
  }
}

export default Users;
```

### **Key Takeaways from the Final Class Component:**  
✅ **State Management:** Uses `this.state` and updates via `this.setState()`.  
✅ **Binding Methods:** Functions need explicit binding (`this.toggleUsersHandler.bind(this)`) unless using arrow functions.  
✅ **Render Method:** Unlike functional components, **class components require a `render()` method** to return JSX.  
✅ **Conditional Rendering:** `{this.state.showUsers && usersList}` follows the same logic as functional components.  

---

## **Final Thoughts: Functional vs. Class Components**  

| Feature                | Functional Components (`useState`) | Class Components (`this.state`) |
|------------------------|--------------------------------|--------------------------------|
| **State Declaration**  | `useState(initialValue)`      | `this.state = {}` in `constructor()` |
| **Updating State**     | `setState(newValue)`         | `this.setState({ key: value })` |
| **State Merging**      | Replaces state unless function is used | Merges new state into existing state |
| **Binding Methods**    | Not needed                    | Must manually bind (`this.method.bind(this)`) |
| **Lifecycle Methods**  | `useEffect()`                 | `componentDidMount()`, `componentDidUpdate()`, `componentWillUnmount()` |
| **Readability**        | More concise & readable      | More boilerplate (e.g., `constructor()`, `bind()`, `render()`) |

### **Which Should You Use?**
- ✅ **Use Functional Components** whenever possible (React Hooks allow them to handle all state & side effects).  
- 🟠 **Use Class Components** only when working with legacy codebases or if required by a specific use case.  

By understanding class components, you gain insight into React’s evolution and why **functional components with hooks** have become the preferred approach! 🚀
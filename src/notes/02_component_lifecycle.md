Intro to Component LifeCycle. These are all lifecycle methods and by far the most important or common ones

componenedDidMount():

- called when component is mounted or evaluated and rendered to the DOM
- equivalent to `useEffect (()={}, [])`

componentDidUpdate():

- called once the component was updated. Like when a state changed and component re-rendered
- equivalent to useEffect with some dependencies
  `useEffect(()=>{}, [someValue])`
- whenever the dependencies change, useEffect function re-execute. Dependencies are re-evaluated.

componentWillUnmount():

- called right before component is removed from the DOM
- equivalent to useEffect's cleanup function:
  ```jsx
  useEffect(
      ()=>{
          return () => {
              ...
          }
      },
      []
  )
  ```

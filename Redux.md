![schematic representation of a component file structure in a typical React application](https://www.freecodecamp.org/news/content/images/2022/08/Group-49.png)

### Disadvantages of Prop Drilling

- Unnecessary bloating of Components
- Unnecessary re-rendering

  ![Rerendering Issue](https://www.freecodecamp.org/news/content/images/2022/08/Group-52-1.png)

## Why Redux?

- To Overcome `prop drilling`:

  we can create a global state and put it in a store. Whichever component requires any state from that store can easily get it by subscribing to the store.

## Redux workflow

![workflow](https://dotnettrickscloud.blob.core.windows.net/img/react/how-redux-works.png)

## Resources:

- https://bretcameron.medium.com/a-beginners-guide-to-redux-with-react-50309ae09a14

- https://www.freecodecamp.org/news/how-to-build-a-redux-powered-react-app/

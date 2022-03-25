[![Version](https://img.shields.io/npm/v/miniplex)](https://www.npmjs.com/package/miniplex-react)
[![Downloads](https://img.shields.io/npm/dt/miniplex-react.svg)](https://www.npmjs.com/package/miniplex-react)
[![Bundle Size](https://img.shields.io/bundlephobia/min/miniplex-react?label=bundle%20size)](https://bundlephobia.com/result?p=miniplex-react)

# miniplex-react

_TODO_

## Usage with React

**🚨 Warning: the React glue provided by this package is still incomplete and should be considered unstable. (It works, but there will be breaking changes!)**

Even though Miniplex can be used without React (it is entirely framework agnostic), it does ship with some useful React glue, available in the `miniplex/react` module.

```ts
import { createECS } from "miniplex/react"
```

This will create an object containing a newly created Miniplex world as well as a collection of useful React components and hooks. It is recommended that you invoke this function from a module in your application that exports the generated object, and then have the rest of your project import that module.

```ts
export default createECS()
```

### world

`createECS` returns a `world` property containing the actual ECS world. You can interact with it like you would usually do to imperatively create, modify and destroy entities (see the chapters above.)

### useArchetype

The `useArchetype` hook lets you get the entities of the specified archetype (similar to the `world.get` above) from within a React component. More importantly, this hook will make the component _re-render_ every time entities are added to or removed from the archetype. This is useful for implementing systems as React components, or writing React components that render entities:

```ts
const MovementSystem = () => {
  const { entities } = useArchetype(movingEntities)

  useFrame(() => {
    for (const { position, velocity } of entities) {
      position.x += velocity.x
      position.y += velocity.y
      position.z += velocity.z
    }
  })

  return null
}
```

`createECS` also provides `Entity` and `Component` React components that you can use to declaratively create (or add components to) entities:

```jsx
const Car = () => (
  <Entity>
    <Component name="position" data="{ x: 0, y: 0, z: 0 }" />
    <Component name="position" data="{ x: 10, y: 0, z: 0 }" />
    <Component name="sprite" data="/images/car.png" />
  <Entity>
)
```

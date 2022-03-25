[![Version](https://img.shields.io/npm/v/miniplex)](https://www.npmjs.com/package/miniplex-react)
[![Downloads](https://img.shields.io/npm/dt/miniplex-react.svg)](https://www.npmjs.com/package/miniplex-react)
[![Bundle Size](https://img.shields.io/bundlephobia/min/miniplex-react?label=bundle%20size)](https://bundlephobia.com/result?p=miniplex-react)

# miniplex-react

### React glue for [miniplex], the gentle game entity manager.

**🚨 Warning: the React glue provided by this package is still incomplete and should be considered unstable. (It works, but there will be breaking changes!)**

The main entry point for this library is the `createECS` function, which will create a miniplex world alongside a collection of useful hooks and React components that will interact with it.

```ts
import { createECS } from "miniplex-react"
```

It is recommended that you invoke this function from a module in your application that exports the generated object, and then have the rest of your project import that module, similar to how you would provide a global store:

```js
/* state.js */
export const ECS = createECS()
```

**TypeScript note:** it is recommended that you define a type that describes the structure of your entities, and pass that to the `createECS` function. This will make sure that any and all interactions with the ECS world and the provided hooks and components have full type checking/hinting/autocomplete support:

```ts
/* state.ts */
import { createECS } from "miniplex-react"

type Entity = {
  position: { x: number; y: number; z: number }
  velocity?: { x: number; y: number; z: number }
  health?: number
} & IEntity

export const ECS = createECS<Entity>()
```

### world

`createECS` returns a `world` property containing the actual ECS world. You can interact with it like you would usually do to imperatively create, modify and destroy entities:

```ts
const entity = ECS.world.createEntity({ position: { x: 0, y: 0 } })
```

### useArchetype

The `useArchetype` hook lets you fetch and subscribe to a specific set of entities by way of an archetype query; it's just like using `world.archetype(query)`, but will reactively re-render your React component whenever an entity is added to or removed from the archetype:

```tsx
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

### `<Entity>`

_TODO_

### `<Component>`

_TODO_

### `<Entities>`

_TODO_

### `<Collection>`

_TODO_

[miniplex]: https://github.com/hmans/miniplex

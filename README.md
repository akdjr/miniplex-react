[![Version](https://img.shields.io/npm/v/miniplex)](https://www.npmjs.com/package/miniplex-react)
[![Downloads](https://img.shields.io/npm/dt/miniplex-react.svg)](https://www.npmjs.com/package/miniplex-react)
[![Bundle Size](https://img.shields.io/bundlephobia/min/miniplex-react?label=bundle%20size)](https://bundlephobia.com/result?p=miniplex-react)

# miniplex-react

### React glue for [miniplex], the gentle game entity manager.

**🚨 Warning: the React glue provided by this package is still incomplete and should be considered unstable. (It works, but there will be breaking changes!)**

## Usage

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

For more details on how to interact with the ECS world, please refer to the [miniplex] documentation.

### `<Entity>` and `<Component>`

As a first step, let's add a single entity to your React application. We use `<Entity>` to declare the entity, and `<Component>` to add components to it.

```tsx
import { ECS } from "./state"

const Player = () => (
  <ECS.Entity>
    <ECS.Component name="position" data={{ x: 0, y: 0, z: 0 }} />
    <ECS.Component name="health" data={100} />
  </ECS.Entity>
)
```

This will, once mounted, create a single entity in your ECS world, and add the `position` and `health` components to it. Once unmounted, it will also automatically destroy the entity.

### Storing objects in components

If your components are designed to store rich objects, and these can be expressed as React components providing Refs, you can pass a single React child to `<Component>`, and its Ref value will automatically be picked up. For example, let's imagine a react-three-fiber based game that allows entities to have a scene object:

```tsx
import { ECS } from "./state"

const Player = () => (
  <ECS.Entity>
    <ECS.Component name="position" data={{ x: 0, y: 0, z: 0 }} />
    <ECS.Component name="health" data={100} />
    <ECS.Component name="three">
      <mesh>
        <sphereGeometry />
        <meshStandardMaterial color="hotpink" />
      </mesh>
    </ECS.Component>
  </ECS.Entity>
)
```

Now the player's `three` component will be set to a reference to the Three.js scene object created by the `<mesh>` element.

### Enhancing existing entities

`<Entity>` can also represent _previously created_ entities, which can be used to enhance them with additional components. This is tremendously useful if your entities are created somewhere else, but at render time, you still need to enhance them with additional components. For example:

```tsx
import { ECS } from "./state"

const Game = () => {
  const [player] = useState(() =>
    ECS.world.createEntity({
      position: { x: 0, y: 0, z: 0 },
      health: 100
    })
  )

  return (
    <>
      {/* All sorts of stuff */}
      <RenderPlayer player={player} />
      {/* More stuff */}
    </>
  )
}

const RenderPlayer = ({ player }) => (
  <ECS.Entity entity={player}>
    <ECS.Component name="three">
      <mesh>
        <sphereGeometry />
        <meshStandardMaterial color="hotpink" />
      </mesh>
    </ECS.Component>
  </ECS.Entity>
)
```

When `<Entity>` is used to represent and enhance an existing entity, the entity will _not_ be destroyed once the component is unmounted.

### `<Collection>`

_TODO_

### useArchetype

The `useArchetype` hook lets you fetch and subscribe to a specific set of entities by way of an archetype query; it's just like using `world.archetype(query)`, but will reactively re-render your React component whenever an entity is added to or removed from the archetype:

### `<Entities>`

_TODO_

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

[miniplex]: https://github.com/hmans/miniplex

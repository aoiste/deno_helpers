# deno_helpers
Some useful functions for deno

## AsyncSingleton

This function is like using dependency injection, when you call a function, it wait until all dependencies ready, after that, all request will return the same instance

```ts
import { asyncSingleton } from "https://deno.land/x/deno_helpers/asyncSingleton.ts"

const [getServiceA, getServiceCurrentA] = asyncSingleton(async () => {
    const config = await getConfig();

    return new SomeService(config)
})

const [getServiceB, getServiceCurrentB] = asyncSingleton(async () => {
    const serviceA = await getServiceA();

    const someData = await serviceA.getDataForB();

    return new SomeServiceB(someData)
})

// If I call service a
getServiceB().then(console.log)     // pending
getServiceB().then(console.log)     // pending

// now inject the config for a
setConfig({ ... })

// the pending task will get their results

// Now call it again
getServiceB().then(console.log)     // Log the result immediately
```

## Delayed

It's a bit like AsyncSingleton, but you can update the value with a set method

```ts
import { delayed } from "https://deno.land/x/deno_helpers/delayed.ts"

const [getConfig, setConfig, getConfigCurrent] = delayed<ConfigType>()

getConfig().then(console.log)   // pending until setConfig is called

setConfig({ ... })

getConfig().then(console.log)   // later calls will return current value immediately
```
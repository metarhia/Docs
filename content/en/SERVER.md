## 🧩 Application server features

[👉 Back to contents](/) | [🚀 Getting started](/content/en/START.md) | [🥞 Application server layers](/content/en/LAYERS.md)

## Multithreading

Metarhia is a first application server for node.js with native worker threads support for scaling and concurrency domain logic execution.
See configuration `application/config/server.js`

```js
({
  balancer: 8000,        // server will create separate thread balancing
  ports: [8001, 8002],   // server will create separate thread for each port
  ...  
  workers: {
    pool: 2,             // number of threads for domain logic execution
    wait: 2000,          // timeout to get idle thread from the pool
    timeout: 5000,       // timeout to execute procedure it thread
  },
});
```

To run procedure in a separate thread use `application.invoke` as in example:

```js
const result = await application.invoke({
  method: 'lib.invoke1.method1',
  args: { key: 'value' },
  exclusive: true,
});
console.log({ result });
```

## Context isolation

Metarhia provides isolation between users in terms of both state and control flow, and further isolates individual user requests by preventing the mutation of connection states from within the domain logic and API endpoints. The application server employs multiple techniques for code execution isolation: including V8 sandboxing, closures, and worker threads. The primary objectives of this isolation are to enhance security and prevent race conditions. Additionally, isolation helps to safeguard the code through both reference pollution prevention and prototype pollution prevention.

## Logging

- Use `Console` interface from application code, e.g. `console.log('Hello');`
  See methods in node.js documentation: https://nodejs.org/api/console.html
- Log files buffering (lazy write), fetch buffers by timer and by buffer size limit
- Log rotarion: keep logs N days, than delete automaticaly

## Serve static

Just pup all files for browser-side application here: `application/static`

## Scheduling

You can schedule a task (function execution) at specific intervals or certain times leke here:

```js
const res = await application.scheduler.add({
  name: 'name',
  every: 'Sep 10th 10s',
  args: { i: 2 },
  run: 'lib.task1.f1',
});
console.log('Add task', res);
// Output: Add task 2023-10-19-id-0
```

File `application/lib/task1/f1.js` will contain async function like this:

```js
async () => {
  console.log('Task started');
  await metarhia.metautil.delay(1000);
  console.log('Task finished');
};
```

## Testing

Metarhia uses node.js native test runner to execute tests. You can add tests for `application/domain/chat.js` in `application/domain/chat.test.js`:

```
({
  name: 'Chat test',

  async run(t) {
    await t.test('Get room with domain.chat.getRoom', async () => {
      const name = 'example1';
      const room = await domain.chat.getRoom(name);
      node.assert(room);
    });

    await t.test('Send message with domain.chat.send', async () => {
      const result = domain.chat.send('example1', 'Hello there');
      node.assert.strictEqual(result, undefined);
    });
  },
});
```

## Resources

Just pup all files here: `application/resources`. You can get Buffer with file content from code:
```js
const file = application.resources.get(`/fileName.exe`);
```
    
## Start

You can create `start` hook by putting `start.js` file to `application/domain`, `application/db`, or `application/lib`:

```js
async () => {
  console.log('Code to be executed after start');
};
```

## Graceful shutdown

Application server will stop after:
- Signals: `SIGINT` and `SIGTERM`
- Keyboard: `Ctrl + C`

Before shutdown all `stop` hooks will be executed. You can place `spot.js` files to `application/domain`, `application/db`, or `application/lib` with async function inside like here:

```js
async () => {
  console.log('Code to be executed before stop');
};
```

[👉 Back to contents](/) | [🚀 Getting started](/content/en/START.md) | [🥞 Application server layers](/content/en/LAYERS.md)

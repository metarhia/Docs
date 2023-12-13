## 🧩 Application server features

[👉 Back to contents](/) | [🚀 Getting started](/content/en/START.md) | [🥞 Application server layers](/content/en/LAYERS.md) | [🗃️ Data modeling, storage, and access](/content/en/DATA.md)

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

Invocation of procedures has additional option to control — `exclusive` flag. Exclusivity means whether to capture a whole worker thread for execution or a procedure might be processed asynchronously beside the other non-exclusive procedures and tasks at the same thread. That way gives you flexibility in launching practical tasks logic to balance between more stable runtime for critical logic and handling higher workload of small async tasks (evenly spread among threads).

## Context isolation

Metarhia provides isolation between users in terms of both state and control flow, and further isolates individual user requests by preventing the mutation of connection states from within the domain logic and API endpoints. The application server employs multiple techniques for code execution isolation: including V8 sandboxing, closures, and worker threads. The primary objectives of this isolation are to enhance security and prevent race conditions. Additionally, isolation helps to safeguard the code through both reference pollution prevention and prototype pollution prevention.

## Logging

- Use `Console` interface from application code, e.g. `console.log('Hello');`
  See methods in node.js documentation: https://nodejs.org/api/console.html
- Log files buffering (lazy write), fetch buffers by timer and by buffer size limit
- Log rotation: keep logs N days, then delete automatically

## Serve static

Just put all static files for browser-side application here: `application/static` and server will automatically load files to cache on start except large files (see configs for more details).

Custom error pages: `static/.XXX.html` where `XXX` is error code, for example: `static/.404.html`. You can put different error page templates in different subfolders, for example: `static/.500.html` and `static/profile/.500.html`, nested folders can override templates.

Virtual path: put template to `static/NAME/.virtual.html` where `NAME` is directory name or even nested path, for example: `static/article/.virtual.html` will be served for example getting `/article/programming/javascript`, except truly existing files.

## Scheduling

You can schedule a task (function execution) at specific intervals or certain times like here:

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

Task files are JSON files (with name format `YYYY-MM-DD-id-N.json`) placed in `application/tasks`.
These files will be created and deleted automatically. On the application server start all files will be loaded into a special thread to be executed.

Task file fields:

- `id: string` - task unique identifier (example: `"2021-07-22-id-0"`)
- `name: string` - not unique task name
- `every: string` - (example: `Jul 22th 100s`), see format below
- `args: object` - task arguments (example: `{"i":2}`)
- `run: string` - function name to run (example: `lib.task1.f1`)

Task file example:

```js
{
  "id": "2021-07-22-id-0",
  "name": "name",
  "every": "Jul 22th 100s",
  "args": { "i": 2 },
  "run": "lib.task1.f1"
}
```

Examples for `every` field syntax:

- `Apr 1st` - once at `00:00` 1st of April
- `15th 100s` - every 100 seconds each 15th
- `Sun 17:` - every Sunday at `17:00`
- `20th 17:15` - every 20th of any month at `17:15`
- `Apr Sun :30` - at HH:30 every hour on Sunday of April
- `2nd :30` - every 2nd of any month at HH:30 every hour
- `Sun 3rd 00:` - every Sunday if this day will be 3rd of month at `00:00`
- `30m 17s` - interval 30 minutes and 17 seconds
- `Sun 5h 30m` - every Sunday with interval 5 hours 30 minutes
- `2021-07-20` - certain date
- `2021-07-20 17:30` - certain date and time

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

Just put all files here: `application/resources`. You can get Buffer with file content from code:
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

Before shutdown all `stop` hooks will be executed. You can place `stop.js` files to `application/domain`, `application/db`, or `application/lib` with async function inside like here:

```js
async () => {
  console.log('Code to be executed before stop');
};
```

[👉 Back to contents](/) | [🚀 Getting started](/content/en/START.md) | [🥞 Application server layers](/content/en/LAYERS.md) | [🗃️ Data modeling, storage, and access](/content/en/DATA.md)

# 🥞 Application server layers

[👉 Back to contents](/) | [🚀 Getting started](/content/en/START.md) | [🧩 Application server features](/content/en/SERVER.md)

## Network

protocol layer: metacom

## API

Let's introduce two basic concepts:
- `endpoint` - a single API method or RPC procedure to be invoked from browser-side app or third-party apps. Endpoint has a contract or signature and a name.
- `unit` - group of endpoints (an interface). Unit has a name, and may have a version (e.g. `chat.1`, `chat.2`, `auth`).

To create API endpoint put file `getCity.js` to folder `application/api/geo` with following source:

```js
async ({ cityId }) => {
  if (cityId !== 1) return new Error('Not found');
  return { name: 'Rome', area: 1285, region: 'Lazio' };
};
```

- Now you can start the server: `node server.js`
- Open the browser and DevTools (F12)
- On the `Console` tab and write: `await api.geo.getCity({ cityId: 1 });`
- You will get: `metacom.js:18 Uncaught Error: Forbidden`
- You need either to deploy a database for the auth subsystem or remove restrictions to access this method
- Add `access: 'public'` so endpoint will look like this:

```js
({
  access: 'public',

  async method({ cityId }) {
    if (cityId !== 1) return new Error('Not found');
    return { name: 'Rome', area: 1285, region: 'Lazio' };
  },
});
```

- Call again: `await api.example.getCity({ cityId: 1 });`
- You will get: `{ name: 'Rome', area: 1285, region: 'Lazio' }`
- Now let's add a contract schema (add `parameters` and `returns` keys):

```js
({
  access: 'public',

  parameters: {
    cityId: 'number',
  },

  async method({ cityId }) {
    if (cityId !== 1) return new Error('Not found');
    return { name: 'Rome', area: 1285, region: 'Lazio' };
  },

  returns: {
    name: 'string',
    area: 'number',
    region: 'string'
  },
});
```

- Let's try to call: `await api.example.getCity({ cityId: '1' });`
- You will get an error, because `cityId` is a `string`
- Try to return an object with the wrong structure from an endpoint

Let's look at a more complex handler: `application/api/geo/citiesByCountry.js`

```js
async ({ countryId }) => {
  const fields = ['cityId', 'name'];
  const where = { countryId };
  const data = await domain.db.select('City', fields, where);
  return { result: 'success', data };
};
```

## Domain logic

There is a special place for domain logic and application state: `application/domain`. You can group code in modules (files) and folders. For example put following to `chat.js` in mentioned folder:

```js
({
  rooms: new Map(),

  getRoom(name) {
    let room = domain.chat.rooms.get(name);
    if (room) return room;
    room = new Set();
    domain.chat.rooms.set(name, room);
    return room;
  },

  dropRoom(name) {
    domain.chat.rooms.delete(name);
  },

  send(name, message) {
    const room = domain.chat.rooms.get(name);
    if (!room) throw new Error(`Room ${name} is not found`);
    for (const client of room) {
      client.emit('chat/message', { room: name, message });
    }
  },
});
```

## Libraries

Auxiliary code that is not related to subject domain, but we don't want to create or import separete dependencies for it, can be placed in: `application/lib`. For example:

```js
({
  UNITS: ['', ' Kb', ' Mb', ' Gb', ' Tb', ' Pb', ' Eb', ' Zb', ' Yb'],

  bytesToSize(bytes) {
    if (bytes === 0) return '0';
    const exp = Math.floor(Math.log(bytes) / Math.log(1000));
    const size = bytes / 1000 ** exp;
    const short = Math.round(size, 2);
    const unit = this.UNITS[exp];
    return short + unit;
  },
});
```

## Dependencies

You can access node.js internal modules and third-party depende with namespaces:
- `node.`, e.g. `node.fs.readFile(filePath, callback);`
- `npm.`, e.g. `const client = npm.redis.createClient();`
- `metarhia.`, e.g. `const metacom = metarhia.metacom.Metacom.create('http://127.0.0.1:8001/api');`

To add new dependency just use `npm install` command e.g. `npm i ws` and `ws` will be available as `npm.ws` after next (re)start.

## Data access

## Bus

Mapping remote services to namespaces of your application is easy, just put file `.service.js` in `application/bus/worldTime`:

```js
({
  url: 'http://worldtimeapi.org/api'
});
```

and add file `currentTime.js` near it:

```js
({
  method: {
    get: 'timezone',
    path: ['area', 'location'],
  },
});
```

Now you can call `bus.worldTime.currentTime` from anywhere in the application:

```js
try {
  const time = await bus.worldTime.currentTime({
    area: 'Europe',
    location: 'Rome',
  });
  console.log(`${time.timezone} - ${time.datetime}`);
} catch {
  console.log('Can not access time server');
}
```

[👉 Back to contents](/) | [🚀 Getting started](/content/en/START.md) | [🧩 Application server features](/content/en/SERVER.md)

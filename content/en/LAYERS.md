# 🥞 Application server layers

[👉 Back to contents](/) | [🚀 Getting started](/content/en/START.md) | [🧩 Application server features](/content/en/SERVER.md)

Metarhia features an auto-loader for its codebase. Upon application start, it automatically loads all layers of code and dependencies, forming namespaces that are accessible from the application code. After loading is complete, it triggers `start` hooks. If files change on the disk, the application server will reload the new version on the fly without stopping the server. No connections will be broken, and no API calls will be terminated.

## API

Let's introduce two basic concepts:
- `endpoint` - a single API method or RPC procedure to be invoked from browser-side app or third-party apps. Endpoint has a contract or signature and a name.
- `unit` - group of endpoints (an interface). Unit has a name, and may have a version (e.g. `chat.1`, `chat.2`, `auth`).

In order to provide the best developer experience for rapid API development, Metarhia offers auto-routing for API requests and webhooks. There's no need to manually add routes; all calls made over supported protocols (HTTP, HTTPS, WS, WSS) will be automatically directed to `endpoints` based on file system paths. The format of request and response payloads is defined by the [Metacom protocol](https://github.com/metarhia/Contracts/blob/master/doc/Metacom.md) specification and implemented in the npm package [Metacom](https://www.npmjs.com/package/metacom). Metarhia supports automatic request concurrency control, including request execution timeouts and an execution queue with both timeout and queue size limitations. API calls can have contracts (schemas) for automatic input and output data validation. The application server provides isolation for code execution; for more details, see [isolation](https://github.com/metarhia/Docs/blob/main/content/en/SERVER.md#context-isolation). The application server also supports various API styles: RPC over AJAX, RPC over Websocket, REST, and webhooks.

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

## Network

Metarhia abstracts away the network protocol layer from the developer on both the client and server sides. You can invoke server methods as if they are simple functions in your client-side (or browser) application.

The server spawns separate threads for:

- The load balancer (always HTTP). However, you can disable the built-in load balancer in the configuration. The balancer redirects incoming traffic to one of the open ports using a round-robin algorithm for simple scaling. Alternatively an external balancer can be used
- Each port (HTTP, HTTPS)

Metarhia provides promise-based abstraction for RPC calls implemented in [metacom](https://github.com/metarhia/metacom).

```js
const metacom = Metacom.create('https://domainname.com:8001');
await metacom.load('auth', 'chat');
const { auth, chat } = metacom.api;

chat.on('message', (event) => {
  console.log(event.message);
});

await auth.signIn({ login: 'marcus', password: 'marcus' });
await chat.subscribe({ room: 'Room1' });
await chat.send({ room: 'Room1', message: 'Hello' });
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

## Data access

Let's look at a more complex endpoint: `application/api/geo/citiesByCountry.js`

```js
async ({ countryId }) => {
  const fields = ['cityId', 'name'];
  const where = { countryId };
  const data = await db.pg.select('City', fields, where);
  return { result: 'success', data };
};
```

We need Postgres server running and dependencies installed: `npm i pg metasql`.
To initialize connection to the database add following code to file `application/db/pg/start.js`:

```js
async () => {
  const options = { ...config.database, console };
  db.pg = new metarhia.metasql.Database(options);
};
```

Also we need configuration in `application/config/database.js`:

```js
({
  host: process.env.DB_HOST || '127.0.0.1',
  port: 5432,
  database: 'application',
  user: 'marcus',
  password: 'marcus',
});
```

Then need to have a tables in database. To generate it just add following schemas.

Schema `application/schemas/Country.js`:

```js
({
  Entity: {},

  name: { type: 'string', unique: true },
});
```

Schema `application/schemas/City.js`:

```js
({
  Entity: {},

  name: { type: 'string', unique: true },
  country: 'Country',
});
```

Add database schemas configuration `application/schemas/.database.js`:

```
({
  name: 'example',
  version: 1,
  driver: 'pg',
});
```

Then generate SQL script by: `npx metasql c`

After that we will get `application/schemas/database.sql` with two tables:

```sql
CREATE TABLE "Country" (
  "countryId" bigint generated always as identity,
  "name" varchar NOT NULL
);

ALTER TABLE "Country" ADD CONSTRAINT "pkCountry" PRIMARY KEY ("countryId");
CREATE UNIQUE INDEX "akCountryName" ON "Country" ("name");

CREATE TABLE "City" (
  "cityId" bigint generated always as identity,
  "name" varchar NOT NULL,
  "countryId" bigint NOT NULL
);

ALTER TABLE "City" ADD CONSTRAINT "pkCity" PRIMARY KEY ("cityId");
CREATE UNIQUE INDEX "akCityName" ON "City" ("name");
ALTER TABLE "City" ADD CONSTRAINT "fkCityCountry" FOREIGN KEY ("countryId") REFERENCES "Country" ("countryId");
```

To put those tables in postgre server first run two SQL commands (you may use `psql` or any other tool to do that):
- `CREATE USER marcus WITH PASSWORD 'marcus';`
- `CREATE DATABASE application OWNER marcus;`

Then execute `database.sql` with `psql`:

```
PGPASSWORD=marcus psql -d application -f application/schemas/database.sql -U marcus
```

Then we need a few records in each table:

```sql
INSERT INTO "Country" ("name") VALUES
  ('Italy'),
  ('Cuba');

INSERT INTO "City" ("name", "countryId") VALUES
  ('Rome', 1),
  ('Havana', 2),
  ('Livorno', 1);
```

[👉 Back to contents](/) | [🚀 Getting started](/content/en/START.md) | [🧩 Application server features](/content/en/SERVER.md)

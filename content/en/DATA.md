# 🗃️ Data modeling, storage, and access

[👉 Back to contents](/) | [🚀 Getting started](/content/en/START.md) | [🥞 Application server layers](/content/en/LAYERS.md) | [🧩 Application server features](/content/en/SERVER.md)

## PostgreSQL

Install `npm install pg` and see docs: https://node-postgres.com/

Put connection options to `application/config/database.js`, for example:

```js
({
  host: process.env.DB_HOST || '127.0.0.1',
  port: 5432,
  database: 'application',
  user: 'marcus',
  password: 'marcus',
});
```

Example with `Client` class:

```js
const client = new npm.pg.Client(config.database);
await client.connect();
const res = await client.query('SELECT * from CITIES where ID = $1', [25]);
console.log(res.rows[0]);
await client.end();
```

Example with `Pool` class:

You can initialize connection on start from `application/db/geo/start.js`:

```js
async () => {
  db.geo.pg = new npm.pg.Pool(config.database);
};
```

After that you can use Pool from anywhere like:

```js
const res = await db.geo.pg.query('SELECT * from CITIES where ID = $1', [25]);
console.log(res.rows[0]);
```

## Redis

Install `npm install redis` and see docs: https://www.npmjs.com/package/redis

Initialize connection from `application/db/redis/start.js`:

```js
async () => {
  db.redis.client = await npm.redis.createClient().connect();
};
```

After that use `db.redis.client` from anywhere:

```js
await db.redis.client.set('key', 'value');
const value = await db.redis.client.get('key');
```

## Metasql query builder

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

## Metasql template sql

```js
const query = db.pg.sql`
  SELECT * FROM "City"
  WHERE "cityId" < ${5} AND "name" <> 'La Haye-en-Touraine'
  ORDER BY name
  LIMIT 3
`;
```

See more examples: https://github.com/metarhia/metasql/blob/master/test/sql.js

## Knex query builder

Put `start` hook to `application/db/pg/start.js`:

```js
async () => {
  db.pg = npm.knex({
    client: 'pg',
    connection: config.database
  });
};
```

See docs here: https://knexjs.org/

## Metaschema domain model

Directory with database schema should contain following files:

| File              | Description       |
| ----------------- | ----------------- |
| `.database.js`    | Schema parameters |
| `.types.js`       | Custom types      |
| `<EntityName>.js` | Entity definition |
|                   |                   |

Schema parameters `.database.js`

```js
({
  name: 'example',
  description: 'Example database schema',
  version: 3,
  driver: 'pg',

  authors: [
    { name: 'Timur Shemsedinov', email: 'timur.shemsedinov@gmail.com' },
  ],

  extensions: [
    'hstore',
    'postgis',
    'postgis_topology',
    'pg_trgm',
  ],

  connection: {
    host: '127.0.0.1',
    port: 5432,
    database: 'application',
    user: 'postgres',
    password: 'postgres',
  },
});
```

Custom types `.types.js`

```js
({
  point: 'geometry(Point, 4326)',
});
```

Entity definition `<EntityName>.js`

Where `<EntityName>` is a name of certain Entity (Class) of subject domain. For example, `City.js` definition may looks like:

```js
({
  Registry: { realm: 'application', storage: 'append' },

  country: 'Country',
  name: { type: 'string', unique: true },
  location: { type: 'point', required: false },
  population: { type: 'number', default: 0 },

  area: { // group of fields
    total: '?number', // nullable field (shorthand for required: false)
    water: '?number',
  },
});
```

Here is `Registry` is a metadata record. Other allowed kinkds:
- `Dictionary` - lookup table, have own id for primary key;
- `Registry` - registry, uses global identifier for primary key;
- `Entity` - entity, has own id for primary key;
- `Journal` - access logs and other logs, have own id for primary key);
- `Details` - details for registry or entity table, have own id for primary key;
- `Relation` - detailes attached to the intersection of multiple entities;
- `View` - named query database allow to select from as from regular table;
- `Struct` - structure (in-memory, API contract or db complex data type);
- `Scalar` - scalar value;
- `Form` - user interface form;
- `Projection` - schema projection;

Realm:
- `global` - universal and globally used data;
- `application` - application specific data;
- `local` - data stored on this certain server;

Storage: `enum: ['persistent', 'append', 'view', 'memory'] }`.

Define custom primary key:

```js
({
  company: 'Company',
  city: 'City',

  companyCity: { primary: ['Company', 'City'] },
});
// This will generate pkCompanyCity
```

Many-to-many relation (Company-to-City):

```js
// File: Company.js
({
  name: 'string',
  cities: { many: 'City' },
});
// This will generate CompanyCities cross-reference table
// with structure: { company, city }
```

Unique composite index:

```js
({
  country: 'Country',
  name: 'string',

  nameByCountry: { unique: ['country', 'name'] },
});
```

Not unique composite index:

```js
({
  street: 'string',
  building: 'string',
  apartment: 'string',

  natural: { index: ['street', 'building', 'apartment'] },
});
```

Custom indexes (gin, gist):

```js
({
  name: 'string',
  location: 'point',

  akName: { index: 'gin (name gin_trgm_ops)' },
  akLocation: { index: 'gist (location)' },
});
```

## Metasql SQL DDL generation

Put schemas to `application/schemas` and run `npx metasql c` after that you will find SQL DDL in `application/schemas/database.sql` and TypeScript typings in `application/schemas/database.d.ts`.

## Metasql SQL Migrations

To be implemented in 3-6 months.

[👉 Back to contents](/) | [🚀 Getting started](/content/en/START.md) | [🥞 Application server layers](/content/en/LAYERS.md) | [🧩 Application server features](/content/en/SERVER.md)

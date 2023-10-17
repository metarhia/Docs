# 🥞 Application server layers

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

application state and procedures

## Libraries

and utilities

## Dependencies

node internal and npm libs

## Data access

## Bus

mapping remote services

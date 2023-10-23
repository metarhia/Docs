# 🚀 Getting started

[👉 Back to contents](/) | [🥞 Application server layers](/content/en/LAYERS.md) | [🗃️ Data modeling, storage, and access](/content/en/DATA.md) | [🧩 Application server features](/content/en/SERVER.md)

## Quick start

- We recommend Linux for development and deployment but MacOS, FreeBSD and Windows are also supported
- We need Node.js 18.x or 20.x
- How to create new repository
  - You can fork and clone [metarhia/Template](https://github.com/metarhia/Template) to your machine
  - Alternatively you can fork and clone [metarhia/Example](https://github.com/metarhia/Example)
  - Also you can create new repo and add initial files and folder manually
- Use `npm i` to install dependencies in development environment or `npm i --omit=dev` for production
- Start the server: `node server.js`
- Stop the server: `Ctrl + C`

## Application template

Metarhia application example: [metarhia/Template](https://github.com/metarhia/Example) has following structure:

```
📁 Example
├── 📁 application
│   ├── 📁 api                      # API folder
│   │   └── 📁 chat                 # API unit (interface or group of endpoints)
│   │       ├── 📄 send.js          # API endpoint
│   │       ├── 📄 subscribe.js
│   │       └── 📄 unsubscribe.js
│   ├── 📁 bus                      # BUS: APIs mapped to application mamespaces
│   ├── 📁 cert                     # SSL certificates
│   ├── 📁 config                   # Configuration files
│   ├── 📁 db                       # Database connections initialization
│   ├── 📁 domain                   # Subject domain code
│   ├── 📁 lib                      # Common code not related to the subject domain
│   ├── 📁 resources                # Resources: files to be automaticaly loaded into memory
│   ├── 📁 schemas                  # Domain model schemas (for database, validation, gui generation etc.)
│   └── 📁 static                   # Browser application (static files accessible from browser)
├── 📁 types                        # Typings in .d.ts format
├── 📄 package.json                 # Package file
├── 📄 server.js                    # Application server entry point
└── 📄 tsconfig.json                # TypeScript configuration
```

## Application configuration

Config files are JavaScript files containing object expression `({ });`. We may have different versions for each config file to be loaded depending on server environment mode: `MODE` variable. For example: `config/log.js` and `config/log.test.js`. First file will be loaded by default, second one will be loaded only if `MODE=test`.

Content of `application/config` directory:

| File           | Description |
| -------------- | ----------- |
| 📄 database.js | Database connection parameters |
| 📄 log.js      | Logger options |
| 📄 log.test.js | Logger options for test mode |
| 📄 scale.js    | Scaling config |
| 📄 server.js   | Server config: ports, timeouts, threads, etc. |
| 📄 session.js  | Session token and state management config |

You can place additional config files like this `example.js`:

```js
({
  field: 'value',
});
```

File `config/log.js`:

```js
({
  keepDays: 100,                                         // delete logs after N days
  writeInterval: 3000,                                   // maximum interval between flush to disk
  writeBuffer: 64 * 1024,                                // maximum buffer size before flush to disk
  toFile: ['error', 'warn', 'info', 'debug', 'log'],     // methods that output to a file
  toStdout: ['error', 'warn', 'info', 'debug', 'log'],   // methods that output to a terminal
});
```

File `config/server.js`:

```js
({
  host: '0.0.0.0',     // Host ip address or `0.0.0.0` for all network interfaces
  balancer: 8000,      // Balancer port (remove this field to disable balancing)
  protocol: 'http',    // http or https (use http for ws and https for wss)
  ports: [8001, 8002], // server ports
  nagle: false,        // eneble Nagle's algorithm (immediately after the socket is established)
  timeouts: {
    bind: 2000,        // Try to bind ports
    start: 30000,      // Application server start timeout
    stop: 5000,        // Stop timeout
    request: 5000,     // API timeout
    watch: 1000,       // Watch event aggregation timeout
    test: 60000,       // Test runner timeout
  },
  queue: {
    concurrency: 1000, // Maximum API concurrency
    size: 2000,        // API queue size
    timeout: 3000,     // queue timeout
  },
  scheduler: {
    concurrency: 10,   // Task scheduler concurrency
    size: 2000,        // Scheduler queue size
    timeout: 3000,     // Task execution timeout
  },
  workers: {
    pool: 0,           // Workers pool size
    wait: 2000,        // Await for worker idle timeout
    timeout: 5000,     // Worker task execution timeout
  },
  cors: {
    origin: '*',       // CORS headers
  },
});
```

File `config/sessions.js`:

```js
({
  sid: 'token',
  characters: 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789',
  length: 64,
  secret: 'secret',
  regenerate: 60 * 60 * 1000,
  expire: 2 * 60 * 60 * 1000,
  persistent: true,
  limits: {
    ip: 20,
    user: 5,
  },
});
```

[👉 Back to contents](/) | [🥞 Application server layers](/content/en/LAYERS.md) | [🗃️ Data modeling, storage, and access](/content/en/DATA.md) | [🧩 Application server features](/content/en/SERVER.md)

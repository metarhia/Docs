## Metarhia tech stack documentation

Metarhia is the **first** technology stack for Node.js scaled with **threads**, offering ultra-lightweight workload **isolation**.
It is optimized for **high-intensive** data exchange, rapid development, **clean architecture**, and easily understandable domain-specific code.
Metarhia provides an all-in-one solution for a **reliable** and **efficient backend**. It supports network communication with web and mobile clients, offers a protocol-agnostic **API**, run-time type validation, real-time and in-memory data processing, and both **stateful** and **stateless** services.

**Weaknesses**: not suitable as content publishing platforms, including blogs and SEO-focused websites, as well as server-side rendering and serving static content.

**Strengths**: enhanced security and architecture suitable for enterprise-level applications, long-lived WebSocket connections to minimize latency and handshake overhead, and no third-party dependencies.

- [🚀 Getting started](/content/en/START.md)
  - [Quick start and configuration](/content/en/START.md#quick-start)
  - [Application template](/content/en/START.md#application-template)
  - [Application configuration](/content/en/START.md#application-configuration)
- [🥞 Application server layers](/content/en/LAYERS.md)
  - [API layer: units and endpoints](/content/en/LAYERS.md#api)
  - [Network protocol layer: metacom](/content/en/LAYERS.md#network)
  - [Domain logic layer: state and procedures](/content/en/LAYERS.md#domain-logic)
  - [Libraries: utilities](/content/en/LAYERS.md#libraries)
  - [Dependencies: node internal and npm libs](/content/en/LAYERS.md#dependencies)
  - [Bus: mapping remote services](/content/en/LAYERS.md#bus)
  - [Data access leyer](/content/en/LAYERS.md#data-access)
- [🗃️ Data modeling, storage, and access](/content/en/DATA.md)
  - [PostgreSQL driver](/content/en/DATA.md#postgresql)
  - [Redis usage](/content/en/DATA.md#redis)
  - [Metasql query builder](/content/en/DATA.md#metasql-query-builder)
  - [Metasql template sql](/content/en/DATA.md#metasql-template-sql)
  - [Knex query builder](/content/en/DATA.md#knex-query-builder)
  - [Metaschema domain model](/content/en/DATA.md#metaschema-domain-model)
  - [Metasql SQL DDL generation](/content/en/DATA.md#metasql-ddl)
  - [Metasql SQL Migrations](/content/en/DATA.md#metasql-migrations)
- [🧩 Application server features](/content/en/SERVER.md)
  - [Multithreading](/content/en/SERVER.md#multithreading)
  - [Context isolation](/content/en/SERVER.md#context-isolation)
  - [Logging](/content/en/SERVER.md#logging)
  - [Serve static files: application for browser-side](/content/en/SERVER.md#serve-static)
  - [Scheduling](/content/en/SERVER.md#scheduling)
  - [Testing](/content/en/SERVER.md#testing)
  - [Resources](/content/en/SERVER.md#resources)
  - [Start](/content/en/SERVER.md#start)
  - [Graceful shutdown](/content/en/SERVER.md#graceful-shutdown)

## Short feature list

- **API auto-routing** calls to `endpoint` for rapid API development (no need to add routes manually)
- **API concurrency**: request execution timeout and execution queue with both timeout and size limitations
- **Schemas** for API contract, data structures validation, and domain models
- **Application server** supports different API styles: RPC over AJAX and over Websocket, REST, and web hooks
- **Multiple protocols** support: HTTP, HTTPS, WS, WSS
- **Auto loader** with `start` hooks, namespace generation for code and dependencies
- **Live reload** of code through filesystem watch
- **Graceful shutdown** with `stop` hooks
- **Minimal dependencies** and reduced code size
- **Layered architecture**: api, domain logic, data access layer, and system code layer (hidden)
- **Code sandboxing** for enhanced security and execution context isolation
- **Code protection**: reference pollution prevention, prototype pollution prevention
- **Multi-threading** for CPU utilization and execution isolation
- **Load balancing** for simple scaling with redirection to multiple ports
- **Caching**: in-memory caching for APIs and static files
- **Configuration**: environment-specific application settings
- **Database access** layer compatible with PostgreSQL with SQL-injection protection
- **Persistent sessions** with authentication, groups, and anonymous sessions
- **Buffered logging** (lazy write) with log rotation (keep logs N days) and console interface
- **Testing**: integrated node.js native test runner and table-test support
- **Inter-process** communication and shared memory used for state management
- **File utilities**: upload, download, support for partial content and streaming
- **Task Management**: scheduled task execution at specific intervals or certain times

## TODO list

Those features will be implemented in nearest future (3-6 months):

- Server health monitoring
- Database migrations
- State synchronization mechanism with transactions and subscription
- Multi-tenancy support

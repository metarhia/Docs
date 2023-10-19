## Metarhia tech stack documentation

Metarhia is the **first** technology stack for Node.js scaled with **threads**, offering ultra-lightweight workload **isolation**.
It is optimized for **high-intensive** data exchange, rapid development, **clean architecture**, and easily understandable domain-specific code.
Metarhia provides an all-in-one solution for a **reliable** and **efficient backend**. It supports network communication with web and mobile clients, offers a protocol-agnostic **API**, run-time type validation, real-time and in-memory data processing, and both **stateful** and **stateless** services.

**Weaknesses**: not suitable as content publishing platforms, including blogs and SEO-focused websites, as well as server-side rendering and serving static content.

**Strengths**: enhanced security and architecture suitable for enterprise-level applications, long-lived WebSocket connections to minimize latency and handshake overhead, and no third-party dependencies.

- [🚀 Getting started](/content/en/START.md)
  - [Quick start and configuration](/content/en/START.md#quick-start)
  - [Application template](/content/en/START.md#application-template)
- [🥞 Application server layers](/content/en/LAYERS.md)
  - [API layer: units and endpoints](/content/en/LAYERS.md#api)
  - [Network protocol layer: metacom](/content/en/LAYERS.md#network)
  - [Domain logic layer: state and procedures](/content/en/LAYERS.md#domain-logic)
  - [Libraries: utilities](/content/en/LAYERS.md#libraries)
  - [Dependencies: node internal and npm libs](/content/en/LAYERS.md#dependencies)
  - [Bus: mapping remote services](/content/en/LAYERS.md#bus)
  - [Data access leyer](/content/en/LAYERS.md#data-access)
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

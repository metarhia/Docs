# 🌐 Global storage

> Know `localStorage`? Meet `globalstorage` — the distributed database and application publishing platform.

These technologies together form the infrastructure for local-first applications.
- [metarhia/globalstorage](https://github.com/metarhia/globalstorage)

<img src="https://github.com/user-attachments/assets/dc76989e-9ddb-481a-9055-cee52157e5b8" width="600" />

## 🚀 PWA (Progressive Web App)

Web apps with UX close to native: offline mode, installation, fast loading. PWAs help when connectivity is poor or networks are slow by combining strengths of web and native platforms.

- [Web App Manifest on MDN](https://developer.mozilla.org/en-US/docs/Web/Manifest)
- [Service Worker on MDN](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)
- [Code examples](https://github.com/HowProgrammingWorks/PWA)

## 🔀 Sync Engine

## CRDT (Conflict-Free Replicated Data Types)

Data structures that enable automatic conflict resolution in distributed systems. CRDTs simplify synchronization and concurrent editing, enabling offline-first applications without data loss or conflicts.

- [Code examples](https://github.com/HowProgrammingWorks/CRDT)
- [CRDT: G-Counter, PN-Counter, Δ-G-Counter, State-based, Operation-based, Delta-based for JavaScript](https://youtu.be/uOmRdXlVkYM)
- [CRDT: множества G-set, 2P-set, LWW-set, OR-set, PN-set на JavaScript](https://youtu.be/7HVvvtQdkRg)

## ↔️ CAS Containers (Compare-And-Swap)

An atomic concurrency mechanism that stores records protected by hashes or versions. CAS containers help avoid race conditions and concurrent modification conflicts, ensuring data consistency and enabling optimistic concurrency control.

- [Code examples](https://github.com/HowProgrammingWorks/CAS)
- [CAS: Compare and Swap контейнеры значений и записей со сравнением по версии и хешу](https://youtu.be/_S8zcKaj7Fk)

## 🗃️ IndexedDB (browser built-in database)

Client-side database API for transactional storage of structured data in browsers. IndexedDB provides offline persistence, local querying, caching, and support for building b-tree indexes.

- [IndexedDB on MDN](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API)
- [Code examples](https://github.com/HowProgrammingWorks/indexedDB)
- [metarhia/indexeddb-client](https://github.com/metarhia/indexeddb-client)

## 📥 OPFS (Origin Private File System)

Secure, high-performance file system accessible only by web applications within their origin. OPFS is suited for large file storage and high-speed file operations in the browser.

- [OPFS on MDN](https://developer.mozilla.org/en-US/docs/Web/API/File_System_API/Origin_private_file_system)
- [Code examples](https://github.com/HowProgrammingWorks/OPFS)

## 🧊 Blockchain (without mining)

Distributed ledger providing immutable history and strong integrity guarantees. Used for decentralized databases where immutability, transparency, and trust are required.

- [Code examples](https://github.com/HowProgrammingWorks/Blockchain)
- [Blockchain и распределенная база данных Globalstorage, CRDT, синхронизация и разрешение конфликтов](https://youtu.be/7Scep7CXKoI)

## 🤖 Bot Engine

## 🏃 JavaScript Smart Contracts

Business logic executed in JavaScript within decentralized environments. Solves automation and trust issues related to data changes, ensures automatic enforcement of agreements, and secure code execution.

## 🔌 WebSocket

Protocol for real-time, two-way data exchange over a single TCP connection. WebSocket reduces latency and enables interactive near-real-time applications.

- [WebSocket on MDN](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)
- [Code examples](https://github.com/HowProgrammingWorks/WebsocketChat)
- [metarhia/metacom](https://github.com/metarhia/metacom)
- [Websocket Server на Node.js](https://youtu.be/Sf7ln3n16ws)
- [Websocket ревью реализации на JavaScript](https://youtu.be/j1PEkJtIca0)

## 💬 WebRTC (Web Real-Time Communication)

Protocol for real-time streaming of multimedia and peer-to-peer data exchange. WebRTC Solves issues of direct real-time communication, low latency, and decentralization without intermediary servers.

- [WebRTC on MDN](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API)
- [Code examples](https://github.com/HowProgrammingWorks/WebRTC)

## 🤝 Metaschema

Declarative schema language for modeling, validation, and data synchronization. Metaschema addresses data inconsistency, schema evolution and migration, and simplifies metadata definition.

- [metarhia/metaschema](https://github.com/metarhia/metaschema)

## 🧰 Metarhia tools

- [metarhia/impress](https://github.com/metarhia/impress)
- [metarhia/metautil](https://github.com/metarhia/metautil)
- [HowProgrammingWorks/UUID](https://github.com/HowProgrammingWorks/UUID)

## Related videos

- [Local-first для фронтенда: CRDT, PWA, OPFS, Blockchain прямо в браузере](https://www.youtube.com/live/cSWOOVh7xkU)
- [Local-first приложение: PWA + CRDT + OPFS + Node.js + Websocket + Service Worker](https://youtu.be/jHgprxfOgBY)
- [PWA: Progressive Web App — Websocket, local-first, offline-first установка JavaScript приложение](https://youtu.be/w0CDo7u2L0E)
- [Local-first подход, CRDT и фронтенд — синхронизация в распределенных системах — Metarhia Weekly](https://youtu.be/XNUNxghX9dg)
- [Презентация техстека Metarhia](https://youtu.be/PHyl4b8Fj5A)

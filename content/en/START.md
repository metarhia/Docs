# 🚀 Getting started

[👉 Back to contents](/) | [🥞 Application server layers](/content/en/LAYERS.md) | [🧩 Application server features](/content/en/SERVER.md)

## Quick start

- We recommend Linux for development and deploy but MacOS, FreeBSD and Windows are also supported
- We need node.js 18.x or 20.x
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

[👉 Back to contents](/) | [🥞 Application server layers](/content/en/LAYERS.md) | [🧩 Application server features](/content/en/SERVER.md)

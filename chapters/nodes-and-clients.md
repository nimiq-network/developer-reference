---
category: "Higher level concepts"
---

# Node and Client Types

There are two types of clients in the Nimiq Network: NodeJS Clients and Browser Clients. Both types use the same isomorphic JavaScript code base and can run any of the three node types: Full, Light and Nano. Some combination of Node/Client types will, in the long run, be more common that others (i.e Browser Clients will mainly be light/nano nodes, and they won’t necessarily participate as miners).

## Client Types

Depending on the platform (Browser/NodeJS) used to run the Client, it will work as a Browser Client or a NodeJS Client. Another main difference between the two of them is that Browser Clients store the Blockchain data in [IndexedDB](https://developers.google.com/web/ilt/pwa/working-with-indexeddb#what_is_indexeddb) (which has a limited capacity of ~50MB) whereas NodeJS Clients rely on [LevelDB](https://github.com/google/leveldb) (with no size constraint). To maintain a common codebase through Client Types we developed [JungleDB](https://github.com/nimiq-network/jungle-db).

### NodeJS Client

NodeJS Clients are meant to run on servers and, thanks to their virtually unlimited storage capacity and publicly routable IP address, act as backbone of the network. They communicate with each other via [WebSockets](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket), and they act as entry point and signaling server for Browser Clients to establish browser-to-browser WebRTC connections.

Browser APIs are restricted to secure origins. This means that NodeJS Clients need to provide an encrypted connection for Browser Clients to connect to them. This requires a domain and an SSL certificate.

### Browser Client

Browser Clients are built upon browser engines and therefore they are completely installation-free. To connect to the network, they establish a WebSocket connection to at least one NodeJS Client. Once they have established their first connection, they start to establish browser-to-browser connections using the NodeJS Client as signaling server. Browser Clients can also act as signaling server for further browser-to-browser connections.

By using [Babel](https://babeljs.io/) older browser versions are supported, WebRTC functionality is a requirement so only browsers that support WebRTC can connect to the network.

An easy way to understand the different Clients/Nodes Types is to have a look at [the most simple web application on top of the Nimiq Blockchain](https://demo.nimiq.com/).

## Node Types

Distinct Node types are used for different usecases and they differentiate from one another in the amount of data they need to download in order to join and synchronize with the network, which directly affects the time it takes each Node type to establish consensus and be able to process and validate transactions.

### Full Nodes

Nodes of this type download the full blockchain thus requiring more storage. We expect this node type to be used solely with NodeJS Clients although theoretically it could run also in a browser.

### Light Nodes

This type of nodes securely sync to almost full consensus by downloading just about 100 MB. To do that they use a LightChain which is initialized by using NiPoPoWs instead of the full blockchain history, but after initialization, it behaves as a regular full blockchain.

### Nano Nodes

The preferred Node type for the Browser Clients since they require less data to be downloaded (~ 1MB) in order to establish consensus.

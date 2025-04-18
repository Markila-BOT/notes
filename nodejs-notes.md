# Node.js Notes 📝

## Table of Contents

- [Introduction](#introduction)
- [Core Concepts](#core-concepts)
- [Event-Driven Programming](#event-driven-programming)
- [Event Loop](#event-loop)
- [Modules](#modules)
- [File System](#file-system)
- [Streams](#streams)
- [Buffer](#buffer)
- [HTTP Server](#http-server)
- [Child Processes](#child-processes)
- [Best Practices](#best-practices)
- [Common Interview Questions](#common-interview-questions)

## Introduction 🚀

Node.js is an open-source, cross-platform JavaScript runtime environment that executes code outside of a web browser. It's built on Chrome's V8 JavaScript engine and uses an event-driven, non-blocking I/O model that makes it lightweight and efficient.

### Key Features

- **Asynchronous & Non-Blocking**: Handles multiple concurrent connections efficiently
- **V8 Engine**: Powered by Google's V8 for high-performance JavaScript execution
- **Libuv Library**: Ensures consistent performance across platforms
- **NPM**: Vast package ecosystem for module management
- **Full-Stack JavaScript**: Unified server and client-side code

## Core Concepts 🧠

### Event-Driven Architecture

Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient. The event loop is the heart of Node.js's asynchronous programming.

```javascript
const EventEmitter = require("events");

class MyEmitter extends EventEmitter {}

const myEmitter = new MyEmitter();

myEmitter.on("event", () => {
  console.log("An event occurred!");
});

myEmitter.emit("event");
```

### Non-Blocking I/O

Node.js uses a single-threaded event loop model, but it can handle multiple concurrent operations through non-blocking I/O operations.

```javascript
const fs = require("fs");

// Non-blocking file read
fs.readFile("file.txt", "utf8", (err, data) => {
  if (err) throw err;
  console.log(data);
});

console.log("Reading file...");
```

## Event-Driven Programming 🎯

Event-driven programming is a fundamental paradigm in Node.js that enables asynchronous, non-blocking operations. It's based on the concept of events and event handlers.

### Event Emitter Pattern

The EventEmitter class is the foundation of Node.js's event-driven architecture. It allows objects to emit named events that cause functions (listeners) to be called.

```javascript
const EventEmitter = require("events");

// Create a custom event emitter
class MyEmitter extends EventEmitter {}

const myEmitter = new MyEmitter();

// Register event listeners
myEmitter.on("event", () => {
  console.log("Event occurred!");
});

// Emit events
myEmitter.emit("event");
```

### Event Types

1. **System Events**: Built-in events from Node.js core modules

   - `fs`: 'open', 'close', 'data'
   - `http`: 'request', 'response'
   - `stream`: 'data', 'end', 'error'

2. **Custom Events**: User-defined events

   ```javascript
   myEmitter.on("userCreated", (user) => {
     console.log(`New user created: ${user.name}`);
   });

   myEmitter.emit("userCreated", { name: "John" });
   ```

### Event Handling Best Practices

1. **Error Events**: Always handle error events

   ```javascript
   myEmitter.on("error", (err) => {
     console.error("Error occurred:", err);
   });
   ```

2. **Once Listeners**: Use `once()` for one-time events

   ```javascript
   myEmitter.once("connection", () => {
     console.log("Connection established");
   });
   ```

3. **Remove Listeners**: Clean up listeners when no longer needed
   ```javascript
   const listener = () => console.log("Event occurred");
   myEmitter.on("event", listener);
   myEmitter.removeListener("event", listener);
   ```

### Event Loop Integration

The event loop processes events in the following order:

1. **Timers**: Execute scheduled callbacks
2. **I/O Events**: Process I/O callbacks
3. **Idle/Prepare**: Internal operations
4. **Poll**: Check for new I/O events
5. **Check**: Execute setImmediate callbacks
6. **Close Callbacks**: Handle close events

```javascript
// Example of event loop phases
setTimeout(() => {
  console.log("Timer callback");
}, 0);

setImmediate(() => {
  console.log("Immediate callback");
});

process.nextTick(() => {
  console.log("Next tick callback");
});
```

### Event-Driven Architecture Benefits

1. **Scalability**: Handles multiple concurrent operations efficiently
2. **Responsiveness**: Non-blocking operations keep the application responsive
3. **Modularity**: Events provide loose coupling between components
4. **Extensibility**: Easy to add new event handlers without modifying existing code

### Common Event Patterns

1. **Pub/Sub Pattern**: Publish events that multiple subscribers can listen to

   ```javascript
   // Publisher
   myEmitter.emit("data", { value: 42 });

   // Subscribers
   myEmitter.on("data", (data) => {
     console.log("Subscriber 1:", data);
   });

   myEmitter.on("data", (data) => {
     console.log("Subscriber 2:", data);
   });
   ```

2. **Observer Pattern**: Objects maintain a list of dependents

   ```javascript
   class Subject extends EventEmitter {
     constructor() {
       super();
       this.state = null;
     }

     setState(newState) {
       this.state = newState;
       this.emit("stateChange", newState);
     }
   }

   const subject = new Subject();
   subject.on("stateChange", (state) => {
     console.log("State changed:", state);
   });
   ```

3. **Event Delegation**: Handle events at a higher level

   ```javascript
   class EventManager extends EventEmitter {
     handleUserAction(action) {
       this.emit("userAction", action);
     }
   }

   const manager = new EventManager();
   manager.on("userAction", (action) => {
     console.log("User performed:", action);
   });
   ```

## Event Loop 🔄

The event loop is what allows Node.js to perform non-blocking I/O operations despite being single-threaded. It's responsible for executing the code, collecting and processing events, and executing queued sub-tasks.

### Phases of Event Loop

1. **Timers**: Executes callbacks scheduled by setTimeout() and setInterval()
2. **Pending Callbacks**: Executes I/O callbacks deferred to the next loop iteration
3. **Idle, Prepare**: Used internally
4. **Poll**: Retrieve new I/O events; execute I/O related callbacks
5. **Check**: Execute setImmediate() callbacks
6. **Close Callbacks**: Execute socket.on('close', ...) callbacks

## Modules 📦

Node.js has a simple module loading system. Each file is treated as a separate module.

### CommonJS Modules

```javascript
// math.js
const add = (a, b) => a + b;
const subtract = (a, b) => a - b;

module.exports = {
  add,
  subtract,
};

// app.js
const math = require("./math");
console.log(math.add(5, 3)); // 8
```

### ES Modules (ESM)

```javascript
// math.mjs
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;

// app.mjs
import { add, subtract } from "./math.mjs";
console.log(add(5, 3)); // 8
```

## File System 📁

The `fs` module provides an API for interacting with the file system.

### Reading Files

```javascript
const fs = require("fs");

// Asynchronous
fs.readFile("file.txt", "utf8", (err, data) => {
  if (err) throw err;
  console.log(data);
});

// Synchronous
const data = fs.readFileSync("file.txt", "utf8");
console.log(data);
```

### Writing Files

```javascript
const fs = require("fs");

// Asynchronous
fs.writeFile("file.txt", "Hello World!", (err) => {
  if (err) throw err;
  console.log("File written successfully");
});

// Synchronous
fs.writeFileSync("file.txt", "Hello World!");
```

## Streams 🌊

Streams are objects that let you read data from a source or write data to a destination in a continuous fashion.

### Types of Streams

1. **Readable**: Streams from which data can be read
2. **Writable**: Streams to which data can be written
3. **Duplex**: Streams that are both Readable and Writable
4. **Transform**: Duplex streams that can modify or transform the data

```javascript
const fs = require("fs");

// Create readable stream
const readableStream = fs.createReadStream("input.txt");

// Create writable stream
const writableStream = fs.createWriteStream("output.txt");

// Pipe the streams
readableStream.pipe(writableStream);
```

## Buffer 🧱

The Buffer class is a global class that can be used to handle binary data directly.

```javascript
// Create a buffer
const buf = Buffer.from("Hello World");

// Convert to string
console.log(buf.toString()); // Hello World

// Get buffer length
console.log(buf.length); // 11
```

## HTTP Server 🌐

Node.js can be used to create HTTP servers.

```javascript
const http = require("http");

const server = http.createServer((req, res) => {
  res.writeHead(200, { "Content-Type": "text/plain" });
  res.end("Hello World\n");
});

server.listen(3000, () => {
  console.log("Server running at http://localhost:3000/");
});
```

## Child Processes 👶

Node.js can create child processes using the `child_process` module.

```javascript
const { spawn } = require("child_process");

const ls = spawn("ls", ["-lh", "/usr"]);

ls.stdout.on("data", (data) => {
  console.log(`stdout: ${data}`);
});

ls.stderr.on("data", (data) => {
  console.error(`stderr: ${data}`);
});

ls.on("close", (code) => {
  console.log(`child process exited with code ${code}`);
});
```

## Best Practices 🏆

1. **Error Handling**: Always handle errors in callbacks
2. **Use Async/Await**: Prefer async/await over callbacks
3. **Environment Variables**: Use environment variables for configuration
4. **Logging**: Implement proper logging
5. **Security**: Follow security best practices
6. **Code Organization**: Follow modular architecture
7. **Testing**: Write unit and integration tests
8. **Performance**: Monitor and optimize performance

## Common Interview Questions ❓

1. What is Node.js and how does it work?
2. Explain the event loop in Node.js
3. What are streams in Node.js?
4. How does error handling work in Node.js?
5. What is the difference between process.nextTick() and setImmediate()?
6. Explain the concept of middleware in Express.js
7. How do you handle file uploads in Node.js?
8. What is clustering in Node.js?
9. How do you debug Node.js applications?
10. What are the best practices for Node.js security?

## Resources 📚

- [Node.js Official Documentation](https://nodejs.org/en/docs/)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [Express.js Documentation](https://expressjs.com/)
- [Node.js Design Patterns](https://www.nodejsdesignpatterns.com/)

---

_Last updated: [Current Date]_

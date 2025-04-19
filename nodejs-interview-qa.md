# Node.js Interview Questions and Answers 🎯

## Table of Contents 📑

1. [Fundamentals](#fundamentals)
2. [Event Loop & Asynchronous Programming](#event-loop--asynchronous-programming)
3. [Modules & Package Management](#modules--package-management)
4. [Security](#security)
5. [Performance & Optimization](#performance--optimization)
6. [Design Patterns & Architecture](#design-patterns--architecture)
7. [Error Handling & Debugging](#error-handling--debugging)
8. [Real-world Scenarios](#real-world-scenarios)

## Fundamentals

### Q1: What is Node.js and how is it different from browser JavaScript?

**Answer:** Node.js is an open-source, cross-platform JavaScript runtime environment that executes JavaScript code outside a web browser. Key differences include:

- **Global Object**: `window` in browsers vs `global` in Node.js
- **APIs**: Browser has DOM/Web APIs vs Node.js has system-level APIs
- **Module System**: Node.js uses CommonJS/ES modules vs browser's ES modules
- **Threading**: Node.js has a single-threaded event loop with worker threads
- **File System Access**: Direct file system access in Node.js

### Q2: Explain the Node.js event loop

**Answer:** The event loop is the core mechanism that allows Node.js to perform non-blocking I/O operations despite JavaScript being single-threaded. It works in phases:

1. **Timers**: Execute `setTimeout`/`setInterval` callbacks
2. **Pending callbacks**: Execute I/O callbacks
3. **Idle, prepare**: Internal use
4. **Poll**: Retrieve new I/O events
5. **Check**: Execute `setImmediate` callbacks
6. **Close callbacks**: Execute close event callbacks

```javascript
console.log("1");
setTimeout(() => console.log("2"), 0);
Promise.resolve().then(() => console.log("3"));
console.log("4");

// Output: 1, 4, 3, 2
```

### Q3: What are Streams in Node.js?

**Answer:** Streams are objects that let you read data from a source or write data to a destination continuously. Types:

- **Readable**: For reading (e.g., `fs.createReadStream`)
- **Writable**: For writing (e.g., `fs.createWriteStream`)
- **Duplex**: Both readable and writable (e.g., net.Socket)
- **Transform**: Duplex streams that can modify data (e.g., zlib.createGzip)

```javascript
const fs = require("fs");
const readStream = fs.createReadStream("input.txt");
const writeStream = fs.createWriteStream("output.txt");
readStream.pipe(writeStream);
```

## Event Loop & Asynchronous Programming

### Q4: What is the difference between `process.nextTick()` and `setImmediate()`?

**Answer:**

- `process.nextTick()`: Executes in the current iteration of the event loop, before the next phase
- `setImmediate()`: Executes in the check phase of the next iteration of the event loop

```javascript
setImmediate(() => console.log("setImmediate"));
process.nextTick(() => console.log("nextTick"));
// Output: nextTick, setImmediate
```

### Q5: How do Promises compare to callbacks?

**Answer:** Promises provide better error handling and chaining compared to callbacks:

```javascript
// Callback approach
fs.readFile("file.txt", (err, data) => {
  if (err) {
    console.error(err);
    return;
  }
  console.log(data);
});

// Promise approach
const util = require("util");
const readFile = util.promisify(fs.readFile);
readFile("file.txt")
  .then((data) => console.log(data))
  .catch((err) => console.error(err));

// Async/await approach
async function readFileAsync() {
  try {
    const data = await readFile("file.txt");
    console.log(data);
  } catch (err) {
    console.error(err);
  }
}
```

## Modules & Package Management

### Q6: What are the different types of modules in Node.js?

**Answer:** Node.js has three types of modules:

1. **Core Modules**: Built-in modules (e.g., `fs`, `http`, `path`)
2. **Local Modules**: Created locally for your application
3. **Third-party Modules**: From npm registry

```javascript
// Core module
const fs = require("fs");

// Local module
const myModule = require("./myModule");

// Third-party module
const express = require("express");
```

### Q7: What is the difference between `package.json` and `package-lock.json`?

**Answer:**

- `package.json`: Defines project metadata and dependencies with version ranges
- `package-lock.json`: Locks exact versions of all dependencies and their dependencies

## Security

### Q8: How do you handle security in Node.js applications?

**Answer:** Key security practices include:

1. **Input Validation**

```javascript
const validator = require("validator");
app.post("/api/user", (req, res) => {
  if (!validator.isEmail(req.body.email)) {
    return res.status(400).send("Invalid email");
  }
});
```

2. **Authentication & Authorization**
3. **CORS Configuration**
4. **Security Headers**
5. **Rate Limiting**
6. **Dependencies Security**

## Performance & Optimization

### Q9: How can you improve the performance of a Node.js application?

**Answer:** Key optimization strategies:

1. **Caching**

```javascript
const cache = new Map();
function getData(key) {
  if (cache.has(key)) {
    return cache.get(key);
  }
  const data = // expensive operation
    cache.set(key, data);
  return data;
}
```

2. **Load Balancing**
3. **Memory Leaks Prevention**
4. **Clustering**
5. **Proper Error Handling**

## Design Patterns & Architecture

### Q10: Explain the Observer pattern in Node.js

**Answer:** The Observer pattern is implemented through EventEmitter:

```javascript
const EventEmitter = require("events");

class JobQueue extends EventEmitter {
  constructor() {
    super();
    this.queue = [];
  }

  addJob(job) {
    this.queue.push(job);
    this.emit("jobAdded", job);
  }
}

const jobQueue = new JobQueue();
jobQueue.on("jobAdded", (job) => {
  console.log(`New job added: ${job}`);
});
```

## Error Handling & Debugging

### Q11: What are the best practices for error handling in Node.js?

**Answer:**

1. **Use try-catch for synchronous code**

```javascript
try {
  const data = JSON.parse(invalidJson);
} catch (err) {
  console.error("Error parsing JSON:", err);
}
```

2. **Use Promise catch for async code**
3. **Create custom error classes**
4. **Use error middleware (in Express)**
5. **Implement global error handlers**

## Real-world Scenarios

### Q12: How would you implement rate limiting in a Node.js API?

**Answer:** Using a package like `express-rate-limit`:

```javascript
const rateLimit = require("express-rate-limit");

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests per windowMs
});

app.use(limiter);
```

### Q13: How do you handle file uploads in Node.js?

**Answer:** Using `multer` middleware:

```javascript
const multer = require("multer");
const upload = multer({ dest: "uploads/" });

app.post("/upload", upload.single("file"), (req, res) => {
  console.log(req.file);
  res.send("File uploaded successfully");
});
```

---

## Additional Resources 📚

- [Node.js Official Documentation](https://nodejs.org/docs)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [Node.js Design Patterns Book](https://www.packtpub.com/product/node-js-design-patterns-third-edition/9781839214110)

---

_Last updated: [Current Date]_

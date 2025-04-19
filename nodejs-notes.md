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
- [Microservices with Node.js](#microservices-with-nodejs)

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

## Microservices with Node.js 🔧

Microservices architecture is a design approach where an application is built as a collection of small, independent services that communicate through well-defined APIs. Node.js is particularly well-suited for building microservices due to its event-driven, non-blocking nature.

### Key Concepts of Microservices 🎯

1. **Service Independence**

   - Each service runs independently
   - Services can be deployed, scaled, and updated separately
   - Individual service failures don't cascade to other services

2. **Communication Patterns**

   ```javascript
   // REST API Example
   const express = require("express");
   const app = express();

   app.get("/api/users", async (req, res) => {
     try {
       const users = await userService.getUsers();
       res.json(users);
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   });

   // Message Queue Example (using RabbitMQ)
   const amqp = require("amqplib");

   async function publishEvent(event) {
     const connection = await amqp.connect("amqp://localhost");
     const channel = await connection.createChannel();
     const queue = "user_events";

     channel.assertQueue(queue, { durable: true });
     channel.sendToQueue(queue, Buffer.from(JSON.stringify(event)));
   }
   ```

3. **Data Management**
   - Each service manages its own database
   - Database per service pattern
   - Event sourcing and CQRS patterns

### Synchronous vs Asynchronous Communication 🔄

1. **Synchronous (Request/Response)**

   ```javascript
   // REST API Example (Synchronous)
   const express = require("express");
   const axios = require("axios");
   const app = express();

   // Direct service-to-service call
   app.get("/order-details/:id", async (req, res) => {
     try {
       // Synchronous call to other services
       const order = await axios.get(
         `http://order-service/orders/${req.params.id}`
       );
       const payment = await axios.get(
         `http://payment-service/payments/${order.data.paymentId}`
       );
       const user = await axios.get(
         `http://user-service/users/${order.data.userId}`
       );

       res.json({
         order: order.data,
         payment: payment.data,
         user: user.data,
       });
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   });
   ```

   **Advantages:**

   - Immediate response
   - Easier to implement and debug
   - Straightforward error handling

   **Disadvantages:**

   - Higher coupling between services
   - Potential performance bottlenecks
   - Chain of service failures
   - Limited scalability

2. **Asynchronous (Event-Driven)**

   ```javascript
   // Event-Based Example (Asynchronous)
   const amqp = require("amqplib");

   // Publisher (Event Producer)
   async function publishOrderCreated(order) {
     const connection = await amqp.connect("amqp://localhost");
     const channel = await connection.createChannel();
     const exchange = "order_events";

     await channel.assertExchange(exchange, "topic", { durable: true });
     channel.publish(
       exchange,
       "order.created",
       Buffer.from(JSON.stringify(order))
     );
   }
   ```

   **Event Bus Pattern** 🚌

   ```javascript
   // Event Bus Implementation
   class EventBus {
     constructor() {
       this.subscribers = new Map();
     }

     // Subscribe to events
     subscribe(event, callback) {
       if (!this.subscribers.has(event)) {
         this.subscribers.set(event, []);
       }
       this.subscribers.get(event).push(callback);
     }

     // Publish events
     publish(event, data) {
       if (this.subscribers.has(event)) {
         this.subscribers.get(event).forEach((callback) => {
           // Execute callback asynchronously
           setImmediate(() => callback(data));
         });
       }
     }
   }

   // Usage Example
   const eventBus = new EventBus();

   // Order Service
   class OrderService {
     constructor(eventBus) {
       this.eventBus = eventBus;
     }

     async createOrder(orderData) {
       // Process order
       const order = await this.processOrder(orderData);

       // Publish event
       this.eventBus.publish("order.created", order);
       return order;
     }
   }

   // Payment Service
   class PaymentService {
     constructor(eventBus) {
       this.eventBus = eventBus;

       // Subscribe to order events
       this.eventBus.subscribe("order.created", this.processPayment.bind(this));
     }

     async processPayment(order) {
       try {
         const payment = await this.createPayment(order);
         this.eventBus.publish("payment.completed", {
           orderId: order.id,
           paymentId: payment.id,
           status: "success",
         });
       } catch (error) {
         this.eventBus.publish("payment.failed", {
           orderId: order.id,
           error: error.message,
         });
       }
     }
   }

   // Notification Service
   class NotificationService {
     constructor(eventBus) {
       this.eventBus = eventBus;

       // Subscribe to multiple events
       this.eventBus.subscribe(
         "payment.completed",
         this.sendConfirmation.bind(this)
       );
       this.eventBus.subscribe(
         "payment.failed",
         this.sendFailureNotice.bind(this)
       );
     }

     async sendConfirmation(paymentData) {
       // Send success notification
       await this.sendEmail({
         type: "ORDER_CONFIRMATION",
         orderId: paymentData.orderId,
       });
     }

     async sendFailureNotice(paymentData) {
       // Send failure notification
       await this.sendEmail({
         type: "PAYMENT_FAILED",
         orderId: paymentData.orderId,
         error: paymentData.error,
       });
     }
   }

   // Initialize services
   const eventBus = new EventBus();
   const orderService = new OrderService(eventBus);
   const paymentService = new PaymentService(eventBus);
   const notificationService = new NotificationService(eventBus);
   ```

   **Event Bus Benefits:**

   - Decoupled Communication: Services don't need to know about each other
   - Scalability: Easy to add new subscribers without modifying publishers
   - Reliability: Events can be persisted and replayed if needed
   - Flexibility: Easy to add new event types and handlers

   **Event Bus Considerations:**

   - Event Schema Management: Maintain consistent event formats
   - Event Versioning: Handle changes in event structure
   - Error Handling: Manage failed event processing
   - Event Order: Consider event ordering requirements
   - Monitoring: Track event flow and processing

   ```javascript
   // Subscriber (Event Consumer)
   async function subscribeToOrderEvents() {
   // ... existing code ...
   ```

3. **Hybrid Approach**

   ```javascript
   // Hybrid Communication Example
   const express = require("express");
   const amqp = require("amqplib");
   const app = express();

   // Synchronous endpoint for critical operations
   app.post("/orders", async (req, res) => {
     try {
       // Sync validation
       const validationResult = await axios.post(
         "http://validation-service/validate",
         req.body
       );
       if (!validationResult.data.valid) {
         return res.status(400).json({ error: "Invalid order" });
       }

       // Async order processing
       await publishOrderCreated(req.body);
       res.status(202).json({ message: "Order is being processed" });
     } catch (error) {
       res.status(500).json({ error: error.message });
     }
   });

   // Background event processing
   async function processOrderAsync(order) {
     try {
       // Async steps that don't need immediate response
       await updateInventory(order);
       await notifyShipping(order);
       await sendCustomerNotification(order);
     } catch (error) {
       await publishOrderError(order, error);
     }
   }
   ```

   **Best Practices:**

   - Use synchronous communication for:
     - User-facing operations requiring immediate response
     - Critical business operations
     - Data validation
   - Use asynchronous communication for:
     - Background tasks
     - Cross-service updates
     - Notifications
     - Long-running operations

### Essential Components 🏗️

1. **API Gateway**

   ```javascript
   // API Gateway using Express
   const express = require("express");
   const proxy = require("express-http-proxy");

   const app = express();

   app.use("/users", proxy("http://user-service:3001"));
   app.use("/orders", proxy("http://order-service:3002"));
   app.use("/payments", proxy("http://payment-service:3003"));
   ```

2. **Service Discovery**

   ```javascript
   // Service Registration with Consul
   const Consul = require("consul");

   const consul = new Consul({
     host: "localhost",
     port: 8500,
   });

   consul.agent.service.register({
     name: "user-service",
     address: "localhost",
     port: 3001,
     check: {
       http: "http://localhost:3001/health",
       interval: "10s",
     },
   });
   ```

3. **Circuit Breaker**

   ```javascript
   // Circuit Breaker Implementation
   const CircuitBreaker = require("opossum");

   const breaker = new CircuitBreaker(
     async function () {
       const response = await fetch("http://api.example.com/data");
       return response.json();
     },
     {
       timeout: 3000,
       errorThresholdPercentage: 50,
       resetTimeout: 30000,
     }
   );

   breaker.fallback(() => ({ error: "Service unavailable" }));
   ```

### Best Practices 📚

1. **Monitoring and Logging**

   ```javascript
   // Centralized Logging with Winston
   const winston = require("winston");

   const logger = winston.createLogger({
     level: "info",
     format: winston.format.json(),
     defaultMeta: { service: "user-service" },
     transports: [
       new winston.transports.File({ filename: "error.log", level: "error" }),
       new winston.transports.File({ filename: "combined.log" }),
     ],
   });
   ```

2. **Health Checks**

   ```javascript
   // Health Check Endpoint
   app.get("/health", (req, res) => {
     const healthcheck = {
       uptime: process.uptime(),
       message: "OK",
       timestamp: Date.now(),
     };
     res.send(healthcheck);
   });
   ```

3. **Container Orchestration**

   ```yaml
   # Docker Compose Example
   version: "3"
   services:
     user-service:
       build: ./user-service
       ports:
         - "3001:3001"
       environment:
         - DB_HOST=mongodb
         - RABBITMQ_HOST=rabbitmq

     order-service:
       build: ./order-service
       ports:
         - "3002:3002"
       environment:
         - DB_HOST=mongodb
         - RABBITMQ_HOST=rabbitmq
   ```

### Testing Strategies 🧪

1. **Unit Testing**

   ```javascript
   // Jest Test Example
   describe("User Service", () => {
     it("should create a new user", async () => {
       const user = await userService.create({
         name: "John Doe",
         email: "john@example.com",
       });
       expect(user).toHaveProperty("id");
       expect(user.name).toBe("John Doe");
     });
   });
   ```

2. **Integration Testing**

   ```javascript
   // Supertest Example
   const request = require("supertest");
   const app = require("../app");

   describe("User API", () => {
     it("should return users list", async () => {
       const response = await request(app).get("/api/users").expect(200);
       expect(Array.isArray(response.body)).toBeTruthy();
     });
   });
   ```

### Security Considerations 🔒

1. **Authentication & Authorization**

   ```javascript
   // JWT Authentication Middleware
   const jwt = require("jsonwebtoken");

   const authMiddleware = (req, res, next) => {
     const token = req.headers.authorization?.split(" ")[1];
     if (!token) {
       return res.status(401).json({ error: "No token provided" });
     }

     try {
       const decoded = jwt.verify(token, process.env.JWT_SECRET);
       req.user = decoded;
       next();
     } catch (error) {
       res.status(401).json({ error: "Invalid token" });
     }
   };
   ```

2. **Rate Limiting**

   ```javascript
   // Rate Limiting with Express
   const rateLimit = require("express-rate-limit");

   const limiter = rateLimit({
     windowMs: 15 * 60 * 1000, // 15 minutes
     max: 100, // limit each IP to 100 requests per windowMs
   });

   app.use(limiter);
   ```

### Deployment and Scaling 🚀

1. **Containerization**

   ```dockerfile
   # Dockerfile Example
   FROM node:16-alpine
   WORKDIR /app
   COPY package*.json ./
   RUN npm install
   COPY . .
   EXPOSE 3000
   CMD ["npm", "start"]
   ```

2. **Load Balancing**

   ```javascript
   // Load Balancer Configuration (nginx)
   const nginx = `
   upstream microservices {
     server service1:3000;
     server service2:3000;
     server service3:3000;
   }
   
   server {
     listen 80;
     location / {
       proxy_pass http://microservices;
     }
   }
   `;
   ```

### Monitoring and Observability 📊

1. **Metrics Collection**

   ```javascript
   // Prometheus Metrics
   const prometheus = require("prom-client");
   const counter = new prometheus.Counter({
     name: "http_requests_total",
     help: "Total HTTP requests",
     labelNames: ["method", "path", "status"],
   });

   app.use((req, res, next) => {
     res.on("finish", () => {
       counter.inc({
         method: req.method,
         path: req.path,
         status: res.statusCode,
       });
     });
     next();
   });
   ```

2. **Distributed Tracing**

   ```javascript
   // OpenTelemetry Implementation
   const { trace } = require("@opentelemetry/api");
   const tracer = trace.getTracer("user-service");

   async function handleRequest(req, res) {
     const span = tracer.startSpan("process-request");
     try {
       // Process request
       span.end();
     } catch (error) {
       span.recordException(error);
       span.end();
     }
   }
   ```

## Resources 📚

- [Node.js Official Documentation](https://nodejs.org/en/docs/)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [Express.js Documentation](https://expressjs.com/)
- [Node.js Design Patterns](https://www.nodejsdesignpatterns.com/)

---

_Last updated: [Current Date]_

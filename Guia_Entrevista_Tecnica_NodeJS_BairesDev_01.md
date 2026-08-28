# Guía intensiva de preparación — Entrevista técnica Node.js
## BairesDev · Miércoles 2 de septiembre de 2026

> **Objetivo:** llegar a la entrevista no solamente sabiendo "usar Node.js", sino pudiendo **explicar qué ocurre internamente, justificar decisiones de arquitectura y resolver ejercicios en vivo**.

La información pública de BairesDev indica que su entrevista técnica es principalmente una conversación sobre conocimientos teóricos de la tecnología, aunque puede incluir un ejercicio práctico. Su propia guía de contratación de Node.js destaca especialmente arquitectura event-driven, operaciones asíncronas, modularidad, Express, testing y seguridad. Además, una experiencia publicada por un candidato para Senior Node.js menciona preguntas sobre **streams, JavaScript fundamentals y ejercicios de programación**. Por eso esta guía prioriza esos temas. 

---

# 1. Prioridad absoluta: lo que debes DOMINAR

## Nivel A — "Si me preguntan esto, tengo que responder sin dudar"

### 1.1 Event Loop y modelo de concurrencia

Este es probablemente el tema **#1**.

Debes poder explicar:

- Node.js ejecuta JavaScript principalmente en un hilo.
- Qué significa realmente "single-threaded".
- Event Loop.
- Call Stack.
- Callback Queue / task queues.
- Microtasks.
- Promises.
- `process.nextTick()`.
- `setTimeout()`.
- `setImmediate()`.
- I/O callbacks.
- Qué operaciones pueden usar el Worker Pool.
- Diferencia entre concurrencia y paralelismo.
- Por qué Node.js puede manejar muchas conexiones simultáneas.
- Qué significa bloquear el Event Loop.
- Por qué un `for` enorme, un cálculo CPU-intensive o ciertas operaciones síncronas pueden degradar todo el servidor.

**Pregunta típica:**

> "Node.js es single-threaded. ¿Cómo puede atender miles de requests simultáneamente?"

Debes poder responderla con un diagrama mental:

```text
Request
   ↓
Event Loop
   ↓
¿Operación rápida?
   ├── Sí → ejecuta callback
   │
   └── I/O / operación delegable
          ↓
      OS / Worker Pool
          ↓
      callback
          ↓
      Event Loop
```

### Práctica obligatoria

Crea pequeños programas que permitan predecir el orden:

```js
console.log('A');

setTimeout(() => console.log('B'), 0);

Promise.resolve().then(() => console.log('C'));

process.nextTick(() => console.log('D'));

console.log('E');
```

Antes de ejecutarlo, escribe el resultado que esperas y explica **por qué**.

Haz al menos 10 variaciones.

**Recurso principal:**
Node.js — Don't Block the Event Loop or the Worker Pool:
https://nodejs.org/en/learn/asynchronous-work/dont-block-the-event-loop

---

# 2. Asynchronous JavaScript

Debes dominar:

- Callbacks.
- Callback hell.
- Promises.
- Promise chaining.
- `async/await`.
- `try/catch` con async.
- Propagación de errores.
- `Promise.all()`.
- `Promise.allSettled()`.
- `Promise.race()`.
- `Promise.any()`.
- Concurrencia controlada.
- Diferencia entre ejecutar operaciones secuencialmente y concurrentemente.

## Ejercicio obligatorio

Explica la diferencia:

```js
const a = await getUser();
const b = await getOrders();
const c = await getNotifications();
```

vs.

```js
const [a, b, c] = await Promise.all([
  getUser(),
  getOrders(),
  getNotifications()
]);
```

Y responde:

> ¿Cuándo NO usarías `Promise.all()`?

Debes mencionar escenarios como:

- Dependencias entre operaciones.
- Necesidad de continuar aunque alguna falle.
- Rate limits.
- Cantidades enormes de operaciones.
- Recursos limitados.

---

# 3. Node.js Modules

Domina perfectamente:

## CommonJS

```js
const express = require('express');

module.exports = myFunction;
```

## ES Modules

```js
import express from 'express';

export default myFunction;
```

Debes conocer:

- `require()`
- `module.exports`
- `exports`
- `import`
- `export`
- `export default`
- `package.json`
- `"type": "module"`
- `.cjs`
- `.mjs`
- resolución de módulos
- dependencias locales
- `node_modules`
- `npm`

### Pregunta clásica

> ¿Cuál es la diferencia entre `exports.foo = foo` y `module.exports = foo`?

Debes saber explicar que `exports` inicialmente referencia al objeto `module.exports`, pero reasignar `exports` rompe esa referencia.

---

# 4. npm y package.json

Debes poder explicar:

- `dependencies`
- `devDependencies`
- `peerDependencies`
- `optionalDependencies`
- `package-lock.json`
- Semantic Versioning
- `^`
- `~`
- versiones exactas
- `npm install`
- `npm ci`
- `npm update`
- scripts
- `npx`
- dependencias transitivas

### Pregunta importante

> ¿Cuál es la diferencia entre `npm install` y `npm ci`?

Debes saber explicar por qué `npm ci` es especialmente apropiado para CI/CD y builds reproducibles.

---

# 5. Streams — MUY IMPORTANTE

Este tema merece especial atención porque existe evidencia pública de que en una entrevista anterior de Senior Node.js en BairesDev preguntaron específicamente por streams y por sus modos `paused` y `flowing`.

Debes dominar:

- ¿Qué es un Stream?
- Readable
- Writable
- Duplex
- Transform
- `pipe()`
- backpressure
- flowing mode
- paused mode
- `data`
- `readable`
- `end`
- `error`
- `finish`

### Ejercicio obligatorio

Implementa un programa que:

1. Lea un archivo grande.
2. Lo procese usando streams.
3. Lo transforme.
4. Escriba el resultado.
5. Evite cargar todo el archivo en memoria.

Luego explica:

> ¿Por qué sería mala idea hacer `fs.readFile()` con un archivo de 10 GB?

Debes relacionarlo con:

- memoria
- throughput
- backpressure
- Event Loop
- escalabilidad

---

# 6. Buffers

Debes conocer:

```js
Buffer.from()
Buffer.alloc()
Buffer.concat()
buffer.toString()
```

Y entender:

- qué representa un Buffer;
- por qué existe en Node.js;
- relación con datos binarios;
- archivos;
- TCP;
- streams;
- encoding.

Pregunta:

> ¿Por qué Node.js necesita Buffer si JavaScript trabaja principalmente con strings y objetos?

---

# 7. HTTP y APIs

Debes dominar el módulo HTTP nativo:

```js
const http = require('node:http');
```

Conceptualmente:

- request
- response
- headers
- status codes
- methods
- body
- keep-alive
- HTTP/1.1
- HTTPS
- JSON
- streaming HTTP

También debes poder explicar cómo construirías una API REST.

---

# 8. Express.js

Aunque la entrevista sea sobre Node.js, es muy razonable que aparezca Express.

BairesDev incluye Express entre las áreas que utiliza para evaluar desarrolladores Node.js.

Debes dominar:

- application
- router
- middleware
- route handlers
- error middleware
- `req`
- `res`
- `next`
- parámetros
- query params
- body
- headers
- status codes

### Debes saber escribir de memoria algo parecido a:

```js
app.use(express.json());

app.get('/users/:id', async (req, res, next) => {
  try {
    const user = await userService.findById(req.params.id);

    if (!user) {
      return res.status(404).json({
        message: 'User not found'
      });
    }

    res.json(user);
  } catch (error) {
    next(error);
  }
});
```

Y explicar qué ocurre en cada línea.

---

# 9. Middleware

Debes poder explicar:

> ¿Qué es middleware y cómo funciona?

Ejemplo:

```js
app.use(authMiddleware);
app.use(loggingMiddleware);
app.use('/users', usersRouter);
```

Entender:

```text
Request
   ↓
Logger
   ↓
Authentication
   ↓
Validation
   ↓
Controller
   ↓
Response
```

Y el papel de:

```js
next();
```

También debes saber qué ocurre si un middleware:

- llama `next()`;
- no llama `next()`;
- lanza un error;
- llama `next(error)`.

---

# 10. Error handling

Debes dominar:

- synchronous errors
- asynchronous errors
- `try/catch`
- rejected Promises
- error middleware
- custom Error classes
- HTTP errors
- logging
- errores operacionales vs errores de programación

Pregunta:

> ¿Cómo diseñarías el manejo global de errores de una API Express?

Debes hablar de:

```text
Controller
    ↓
Service
    ↓
Error
    ↓
Global error handler
    ↓
Log + HTTP response
```

Y nunca devolver:

```js
res.status(500).json({
  error: error.stack
});
```

en producción.

---

# 11. Testing

BairesDev menciona explícitamente testing como área relevante al evaluar desarrolladores Node.js.

Debes conocer al menos:

- unit tests
- integration tests
- end-to-end tests
- mocking
- stubbing
- spies
- fixtures
- test isolation
- coverage

Conocer uno de estos ecosistemas es suficiente para poder conversar con solvencia:

- Jest
- Node.js Test Runner
- Mocha

## Ejercicio

Crea un pequeño servicio:

```text
UserService
 ├── createUser()
 ├── findUser()
 └── deleteUser()
```

Y escribe:

- tests unitarios;
- mocks del repositorio;
- casos exitosos;
- casos de error;
- edge cases.

---

# 12. Seguridad en Node.js

Debes dominar al menos conceptualmente:

## Authentication

- sessions
- JWT
- access token
- refresh token

## Authorization

- roles
- permissions
- RBAC

## Vulnerabilidades

- SQL Injection
- NoSQL Injection
- XSS
- CSRF
- SSRF
- prototype pollution
- command injection
- path traversal
- insecure dependencies

## Buenas prácticas

- validar input;
- sanitizar;
- usar HTTPS;
- gestionar secrets correctamente;
- no almacenar passwords en texto plano;
- hashing con algoritmos apropiados;
- rate limiting;
- CORS correctamente configurado;
- headers de seguridad;
- actualizar dependencias.

Pregunta típica:

> "¿Cómo asegurarías una API Node.js?"

Debes poder responder de forma estructurada.

---

# 13. Performance

Debes saber identificar:

### Problemas típicos

- Event Loop bloqueado.
- Queries lentas.
- N+1 queries.
- Memory leaks.
- demasiadas llamadas externas.
- exceso de serialización/deserialización.
- uso incorrecto de streams.
- falta de caching.
- operaciones CPU-intensive.

### Herramientas/conceptos

- profiling
- CPU profiling
- memory profiling
- heap snapshots
- caching
- connection pooling
- pagination
- compression
- load balancing

Pregunta:

> "Una API Node.js comenzó a responder lentamente. ¿Cómo investigarías el problema?"

No respondas simplemente "aumento servidores".

Debes explicar un proceso:

```text
Measure
  ↓
Observe
  ↓
Identify bottleneck
  ↓
Profile
  ↓
Fix
  ↓
Load test
  ↓
Measure again
```

---

# 14. Worker Threads, Cluster y Child Processes

Debes saber distinguir:

## worker_threads

Para trabajo CPU-intensive dentro de un proceso Node.js.

Ejemplos:

- procesamiento de imágenes;
- cálculos;
- parsing pesado;
- criptografía computacional.

## cluster

Permite ejecutar múltiples procesos Node.js.

## child_process

Permite ejecutar procesos externos.

Debes poder responder:

> "Tengo un algoritmo que consume 5 segundos de CPU. ¿Lo ejecutarías directamente en el request handler?"

Respuesta: normalmente no.

---

# 15. EventEmitter

Domina:

```js
const emitter = new EventEmitter();

emitter.on('user.created', handler);

emitter.emit('user.created', user);
```

Debes conocer:

- `on`
- `once`
- `emit`
- `off`
- listeners
- error events
- memory leaks por listeners

Pregunta:

> ¿Qué diferencia existe entre EventEmitter y Promise?

No son mecanismos equivalentes:

- Promise representa el resultado de una operación asíncrona.
- EventEmitter representa un flujo de eventos que puede emitir múltiples eventos.

---

# 16. File System

Debes conocer:

```js
fs.readFile()
fs.writeFile()
fs.appendFile()
fs.unlink()
fs.rename()
fs.stat()
```

Y sus equivalentes:

```js
fs/promises
```

También:

- sync vs async;
- streams;
- paths;
- permisos;
- errores;
- manejo de archivos grandes.

Evita responder que las versiones síncronas son "malas". Son útiles en determinados contextos, pero pueden bloquear el Event Loop si se usan en servidores durante requests.

---

# 17. JavaScript que debes dominar aunque la entrevista diga "Node.js"

Esta parte es CRÍTICA.

Una entrevista Node.js también puede convertirse rápidamente en una entrevista JavaScript.

Debes dominar:

### Variables

- `var`
- `let`
- `const`
- scope
- hoisting
- temporal dead zone

### Functions

- function declaration
- function expression
- arrow functions
- callbacks
- higher-order functions

### Closures

Debes poder explicar y escribir un closure.

### Objects

- references
- shallow copy
- deep copy
- destructuring
- spread
- rest
- optional chaining
- nullish coalescing

### Arrays

Domina:

```js
map
filter
reduce
find
findIndex
some
every
sort
includes
flat
flatMap
```

### Prototypes

No necesitas ser experto en internals de V8, pero debes entender:

- prototype chain
- inheritance
- classes
- `this`

### Async JavaScript

- Promise
- async/await
- microtasks
- event loop

---

# 18. Algoritmos y estructuras de datos

No asumiría que la entrevista será exclusivamente Node.js.

Debes estar cómodo resolviendo problemas como:

- encontrar máximo/mínimo;
- eliminar duplicados;
- contar frecuencias;
- invertir strings;
- palindrome;
- two sum;
- ordenar;
- buscar;
- recorrer arrays;
- transformar objetos;
- agrupar elementos;
- detectar duplicados.

Y conocer:

- Big O;
- arrays;
- objects/maps;
- sets;
- stacks;
- queues;
- linked lists;
- trees a nivel conceptual.

## Ejercicios

Empieza con problemas Easy de:

- arrays
- strings
- hash maps
- sorting
- two pointers

No pierdas tiempo intentando dominar algoritmos avanzados si tu base de Node.js todavía tiene huecos.

---

# 19. Arquitectura de una aplicación Node.js

Para un perfil Senior, debes poder hablar de estructura.

Por ejemplo:

```text
src/
├── controllers/
├── services/
├── repositories/
├── routes/
├── middlewares/
├── validators/
├── models/
├── config/
├── utils/
└── app.js
```

Pero lo importante no es memorizar carpetas.

Debes explicar:

```text
HTTP
 ↓
Controller
 ↓
Service / Use Case
 ↓
Repository
 ↓
Database
```

Y saber justificar:

- separación de responsabilidades;
- dependency injection;
- modularidad;
- testability;
- desacoplamiento.

---

# 20. Bases de datos desde Node.js

Debes poder hablar de:

- connection pooling;
- transactions;
- prepared statements;
- ORM vs query builder vs SQL;
- pagination;
- indexes;
- N+1;
- timeouts;
- retries;
- connection management.

No hace falta estudiar una base de datos completa para esta entrevista si el foco confirmado es Node.js, pero sí debes poder explicar cómo Node interactúa con una DB.

---

# 21. Graceful Shutdown

Tema muy bueno para diferenciar a un candidato senior.

Debes conocer:

```js
process.on('SIGTERM', async () => {
  // stop accepting requests
  // finish ongoing work
  // close DB connections
  // close queues
  // exit
});
```

Pregunta:

> ¿Qué sucede si Kubernetes mata tu contenedor?

Debes hablar de:

- SIGTERM;
- dejar de aceptar tráfico;
- terminar requests existentes;
- cerrar conexiones;
- timeout;
- SIGKILL;
- readiness/liveness.

---

# 22. Logging y observabilidad

Debes conocer:

- structured logging;
- log levels;
- correlation/request ID;
- metrics;
- traces;
- latency;
- error rate;
- throughput;
- health checks.

Ejemplo conceptual:

```json
{
  "level": "error",
  "requestId": "abc123",
  "route": "/users",
  "statusCode": 500,
  "durationMs": 420,
  "error": "Database timeout"
}
```

---

# 23. Preguntas que debes ser capaz de responder oralmente

Practica estas **sin mirar apuntes**:

1. ¿Qué es Node.js?
2. ¿Por qué Node.js es eficiente para I/O?
3. ¿Qué es el Event Loop?
4. ¿Node.js es realmente single-threaded?
5. ¿Qué es el Worker Pool?
6. ¿Qué bloquea el Event Loop?
7. ¿Qué diferencia hay entre `process.nextTick`, Promise y `setImmediate`?
8. ¿Qué diferencia hay entre callback, Promise y async/await?
9. ¿Qué diferencia hay entre `Promise.all` y `Promise.allSettled`?
10. ¿Qué es un Stream?
11. ¿Qué es backpressure?
12. ¿Paused mode vs flowing mode?
13. ¿Qué es Buffer?
14. ¿CommonJS vs ES Modules?
15. ¿`exports` vs `module.exports`?
16. ¿Qué hace package.json?
17. ¿`npm install` vs `npm ci`?
18. ¿Qué es middleware?
19. ¿Cómo funciona `next()` en Express?
20. ¿Cómo manejas errores en Express?
21. ¿Cómo diseñarías una API REST?
22. ¿Cómo autenticarías una API?
23. ¿JWT vs session?
24. ¿Cómo protegerías una API Node?
25. ¿Cómo detectarías un memory leak?
26. ¿Cómo investigarías una API lenta?
27. ¿Cuándo usarías worker_threads?
28. ¿Cuándo usarías cluster?
29. ¿Qué es EventEmitter?
30. ¿Qué diferencia hay entre EventEmitter y Promise?
31. ¿Cómo harías graceful shutdown?
32. ¿Cómo testearías un servicio Node?
33. ¿Unit vs integration vs E2E?
34. ¿Qué es dependency injection?
35. ¿Cómo estructurarías una aplicación Node grande?

---

# 24. Ejercicios prácticos que debes hacer

## Ejercicio 1 — API REST

Construye:

```text
GET    /users
GET    /users/:id
POST   /users
PATCH  /users/:id
DELETE /users/:id
```

Incluye:

- Express;
- validation;
- middleware;
- error handling;
- logging;
- tests.

---

## Ejercicio 2 — Rate limiter

Implementa un middleware:

```text
100 requests / minute / IP
```

Después responde:

- ¿funcionaría con múltiples instancias?
- ¿qué problema aparece?
- ¿cómo lo solucionarías con Redis?

---

## Ejercicio 3 — Concurrencia

Supón:

```js
getUser()
getOrders()
getNotifications()
```

Implementa:

1. versión secuencial;
2. versión concurrente;
3. versión con límite de concurrencia;
4. manejo parcial de errores.

---

## Ejercicio 4 — Stream

Procesa un CSV de varios GB sin cargarlo completo en memoria.

Pipeline:

```text
File
 ↓
Readable Stream
 ↓
Parser
 ↓
Transform
 ↓
Writable Stream
```

---

## Ejercicio 5 — Worker Thread

Implementa:

```text
POST /calculate
```

que recibe un número y calcula algo CPU-intensive.

Primero hazlo bloqueando el Event Loop.

Después mueve el cálculo a `worker_threads`.

Mide la diferencia.

---

## Ejercicio 6 — Event Loop

Crea 20 ejercicios de predicción del output.

Ejemplo:

```js
console.log('1');

setTimeout(() => console.log('2'), 0);

Promise.resolve().then(() => console.log('3'));

process.nextTick(() => console.log('4'));

console.log('5');
```

No ejecutes el código hasta haber explicado tu predicción.

---

# 25. Sitios para practicar

## 1. HackerRank — prioridad ALTA

Especialmente interesante porque ofrece actualmente un **mock interview específico para Backend Developer con Node.js**, con ejercicios de Node.js, JavaScript y Express.

https://www.hackerrank.com/mock-interviews/back-end-developer-node/node

Úsalo para practicar bajo presión y con tiempo limitado.

También:

https://www.hackerrank.com/skills-directory/nodejs_basic

---

## 2. Codewars — prioridad ALTA

Excelente para velocidad con JavaScript.

Tiene una colección específica de Node.js que incluye ejercicios como:

- Node.js Intro
- Password Hashes
- Node.js Async I/O

https://www.codewars.com/collections/node-js

Mi recomendación:

- 7 kyu primero;
- luego 6 kyu;
- evita perder tiempo en problemas demasiado matemáticos.

---

## 3. Exercism — prioridad MEDIA/ALTA

Actualmente ofrece un track de JavaScript con numerosos ejercicios, conceptos y análisis automático.

https://exercism.org/tracks/javascript

Es excelente para reforzar:

- closures;
- arrays;
- objects;
- functions;
- modules;
- testing;
- clean JavaScript.

---

## 4. Node.js Documentation — prioridad MÁXIMA

No estudies Node.js únicamente mediante tutoriales.

La documentación oficial actual cubre EventEmitter, streams, HTTP, filesystem, modules, worker threads, cluster, test runner, crypto, performance, etc.

https://nodejs.org/api/

---

# 26. Cómo practicar de aquí al miércoles

Tienes poco tiempo, por lo que **no recomiendo intentar estudiar todo Node.js**.

## Día 1 — JavaScript + Event Loop

### Mañana

- closures
- scope
- `this`
- prototypes
- destructuring
- array methods
- Promises
- async/await

### Tarde

- Event Loop
- microtasks
- timers
- `nextTick`
- `setImmediate`
- Worker Pool

### Noche

20 ejercicios de output prediction.

---

# Día 2 — Node Core

Estudia:

- modules;
- npm;
- package.json;
- EventEmitter;
- Buffer;
- streams;
- filesystem;
- HTTP.

### Prioridad especial

**Streams + Event Loop.**

---

# Día 3 — Backend

Estudia:

- Express;
- middleware;
- routing;
- validation;
- error handling;
- REST;
- authentication;
- authorization;
- security;
- testing.

Construye una API pequeña.

---

# Día 4 — Senior Level

Repasa:

- performance;
- memory leaks;
- worker_threads;
- graceful shutdown;
- logging;
- observability;
- scalability;
- caching;
- DB connections;
- concurrency.

Después haz una entrevista simulada.

---

# Día 5 — Simulación

Haz una sesión de 60–90 minutos.

### Bloque 1 — Teoría

20 preguntas rápidas.

### Bloque 2 — Código

2 ejercicios.

### Bloque 3 — System thinking

Problema:

> "Diseña una API Node.js que soporte 10.000 requests por segundo."

Debes hablar de:

```text
Load Balancer
     ↓
Node instances
     ↓
Cache
     ↓
Database
     ↓
Queue
```

Y explicar:

- dónde escala;
- dónde puede fallar;
- qué monitorizar;
- cómo evitar bloquear el Event Loop.

---

# 27. La técnica que más te recomiendo

No estudies únicamente leyendo.

Para cada tema utiliza este ciclo:

```text
1. Estudiar
      ↓
2. Explicarlo sin mirar
      ↓
3. Implementarlo
      ↓
4. Romperlo intencionalmente
      ↓
5. Explicar por qué falló
      ↓
6. Volver a implementarlo
```

Por ejemplo:

### Event Loop

No basta con saber:

> "Node.js usa un Event Loop."

Debes ser capaz de decir:

> "El código JavaScript se ejecuta en el Event Loop, mientras ciertas operaciones I/O pueden ser gestionadas por el sistema operativo o el Worker Pool. Cuando completan, sus callbacks quedan disponibles para ser procesados posteriormente. Por eso Node.js puede manejar muchas operaciones concurrentes sin crear un thread por request. El problema aparece cuando ejecutamos trabajo CPU-intensive o código síncrono costoso en el hilo principal, porque bloqueamos el procesamiento de otros callbacks."

Ese nivel de explicación es el objetivo.

---

# 28. Mi ranking de importancia para TU entrevista

| Tema | Prioridad |
|---|---:|
| Event Loop | ⭐⭐⭐⭐⭐ |
| Async/Await + Promises | ⭐⭐⭐⭐⭐ |
| JavaScript fundamentals | ⭐⭐⭐⭐⭐ |
| Streams + Backpressure | ⭐⭐⭐⭐⭐ |
| Express + Middleware | ⭐⭐⭐⭐⭐ |
| Error Handling | ⭐⭐⭐⭐⭐ |
| HTTP / REST APIs | ⭐⭐⭐⭐⭐ |
| Modules | ⭐⭐⭐⭐ |
| npm / package.json | ⭐⭐⭐⭐ |
| Testing | ⭐⭐⭐⭐ |
| Security | ⭐⭐⭐⭐ |
| Performance | ⭐⭐⭐⭐ |
| Buffers | ⭐⭐⭐⭐ |
| EventEmitter | ⭐⭐⭐⭐ |
| Worker Threads | ⭐⭐⭐ |
| Graceful Shutdown | ⭐⭐⭐ |
| Child Processes | ⭐⭐⭐ |
| Cluster | ⭐⭐⭐ |
| Observability | ⭐⭐⭐ |
| Algorithms / Big O | ⭐⭐⭐⭐ |

---

# 29. Lo que NO priorizaría ahora

Con la entrevista tan próxima, no dedicaría horas a:

- C++ addons;
- Node-API;
- WASI;
- FFI;
- HTTP/2 internals profundos;
- V8 internals avanzados;
- libuv internals a nivel de implementación;
- escribir un framework desde cero;
- memorizar APIs poco utilizadas.

Es mejor dominar **15 conceptos fundamentales profundamente** que conocer superficialmente 50 APIs.

---

# 30. Checklist final

Antes de entrar a la entrevista deberías poder marcar "sí" en todo esto:

### JavaScript

- [ ] Puedo explicar closures.
- [ ] Puedo explicar scope y hoisting.
- [ ] Domino Promises.
- [ ] Domino async/await.
- [ ] Entiendo microtasks.
- [ ] Domino map/filter/reduce/find/some/every.
- [ ] Entiendo referencias vs valores.
- [ ] Entiendo `this`.
- [ ] Puedo resolver ejercicios de arrays/objects.

### Node.js

- [ ] Puedo explicar Event Loop.
- [ ] Puedo explicar Worker Pool.
- [ ] Sé qué bloquea Node.
- [ ] Domino EventEmitter.
- [ ] Domino Streams.
- [ ] Entiendo backpressure.
- [ ] Domino Buffer.
- [ ] Domino fs.
- [ ] Domino HTTP.
- [ ] Domino CommonJS/ESM.
- [ ] Domino npm/package.json.

### Backend

- [ ] Puedo construir una API REST.
- [ ] Entiendo middleware.
- [ ] Sé manejar errores.
- [ ] Sé validar inputs.
- [ ] Sé autenticar una API.
- [ ] Conozco JWT.
- [ ] Conozco amenazas comunes.
- [ ] Sé diseñar tests.

### Senior

- [ ] Sé investigar problemas de performance.
- [ ] Sé detectar Event Loop blocking.
- [ ] Sé explicar memory leaks.
- [ ] Sé cuándo usar worker_threads.
- [ ] Sé explicar graceful shutdown.
- [ ] Sé hablar de scalability.
- [ ] Sé explicar caching.
- [ ] Sé explicar connection pooling.
- [ ] Sé hablar de observability.

---

# 31. Fuentes principales

- BairesDev — Node.js interview questions:
  https://www.bairesdev.com/blog/node-js-interview-questions/

- BairesDev — Node.js developer hiring guide:
  https://www.bairesdev.com/technologies/nodejs/hire-developers/

- BairesDev — Technical Interview FAQ:
  https://applicants.bairesdev.com/faq

- Node.js official documentation:
  https://nodejs.org/api/

- Node.js — Don't Block the Event Loop:
  https://nodejs.org/en/learn/asynchronous-work/dont-block-the-event-loop

- HackerRank — Node.js Backend Mock Interview:
  https://www.hackerrank.com/mock-interviews/back-end-developer-node/node

- HackerRank — Node.js skills:
  https://www.hackerrank.com/skills-directory/nodejs_basic

- Codewars — Node.js collection:
  https://www.codewars.com/collections/node-js

- Exercism — JavaScript track:
  https://exercism.org/tracks/javascript

---

## Conclusión

Para esta entrevista, mi estrategia sería:

**JavaScript → Event Loop → Async → Streams → Express → Error Handling → HTTP/REST → Testing → Security → Performance → Senior topics.**

Y algo muy importante: **no memorices respuestas**. Practica explicando cada concepto con tus propias palabras y acompáñalo con un ejemplo de código.

Si puedes explicar **por qué** Node.js funciona como funciona, no solamente **cómo** escribir una API, estarás mucho mejor preparado para una entrevista técnica de nivel senior.

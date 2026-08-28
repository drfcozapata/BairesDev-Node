# 🚀 GUÍA INTEGRAL DE PREPARACIÓN — ENTREVISTA TÉCNICA SENIOR BACKEND NODE.JS
**Empresa:** BairesDev  
**Fecha de la Entrevista:** Miércoles, 2 de septiembre de 2026  
**Perfil:** Senior Backend Node.js Developer  
**Objetivo:** Dominar los internals del runtime de Node.js, justificar decisiones de arquitectura backend, responder preguntas conceptuales de alta profundidad y resolver live coding / system design bajo la evaluación del Top 1% de BairesDev.

---

## 📋 ÍNDICE DE CONTENIDOS
1. [Perfil de Evaluación en BairesDev](#1-perfil-de-evaluación-en-bairesdev)
2. [Bloque 1: Runtime de Node.js & Event Loop (Nivel Experto)](#bloque-1-runtime-de-nodejs--event-loop-nivel-experto)
3. [Bloque 2: Asincronía, Modularidad y Manejo de Errores](#bloque-2-asincronía-modularidad-y-manejo-de-errores)
4. [Bloque 3: Streams, Buffers e I/O de Alto Rendimiento](#bloque-3-streams-buffers-e-io-de-alto-rendimiento)
5. [Bloque 4: Express.js, Arquitectura Backend y Bases de Datos](#bloque-4-expressjs-arquitectura-backend-y-bases-de-datos)
6. [Bloque 5: Seguridad, Performance, Memory Leaks y Observabilidad](#bloque-5-seguridad-performance-memory-leaks-y-observabilidad)
7. [Bloque 6: Fundamentos de JavaScript para Entrevistas Backend](#bloque-6-fundamentos-de-javascript-para-entrevistas-backend)
8. [Bloque 7: Ejercicios Prácticos, Live Coding y System Design](#bloque-7-ejercicios-prácticos-live-coding-y-system-design)
9. [Bloque 8: Preguntas de Respuesta Oral Inmediata](#bloque-8-preguntas-de-respuesta-oral-inmediata)
10. [Plan de Estudio Intensivo (Día a Día) y Checklist Final](#plan-de-estudio-intensivo-día-a-día-y-checklist-final)

---

## 1. PERFIL DE EVALUACIÓN EN BAIRESDEV

### ¿Qué busca BairesDev según sus especificaciones oficiales?
BairesDev evalúa a sus candidatos Senior mediante un proceso riguroso centrado en:
- **Core Runtime & Frameworks:** Conocimiento profundo de Node.js LTS, TypeScript, Express, Fastify o NestJS.
- **Arquitectura Event-Driven y Concurrencia:** Dominio del Event Loop, gestión de *backpressure*, microservicios y escalabilidad I/O.
- **Seguridad e Identidad:** Auth (JWT/OAuth2), gestión segura de secretos, sanitización (Zod/Joi), protección contra inyecciones y headers de seguridad (`helmet`).
- **Observabilidad, Diagnostics y Performance:** Profiling de CPU/Memoria, tuning de concurrencia, PM2, `cluster`, `worker_threads`, análisis de Heap Snapshots y manejo de memory leaks.
- **Testing y Calidad de Código:** Tests unitarios, de integración y de contratos utilizando Jest, Supertest, Vitest o Mocha.

---

## BLOQUE 1: RUNTIME DE NODE.JS & EVENT LOOP (NIVEL EXPERTO)

### 1.1 Las 6 Fases del Event Loop en libuv
Node.js delega el Event Loop a la librería **libuv**. Cada iteración ("tick") recorre exactamente estas fases en orden:

```text
       ┌───────────────────────────┐
    ┌─>│           timers          │
    │  └─────────────┬─────────────┘
    │  ┌─────────────┴─────────────┐
    │  │     pending callbacks     │
    │  └─────────────┬─────────────┘
    │  ┌─────────────┴─────────────┐
    │  │       idle, prepare       │
    │  └─────────────┬─────────────┘      ┌────────────────┐
    │  ┌─────────────┴─────────────┐      │ connections,   │
    │  │           poll            │<─────┤ data, etc.     │
    │  └─────────────┬─────────────┘      └────────────────┘
    │  ┌─────────────┴─────────────┐
    │  │           check           │
    │  └─────────────┬─────────────┘
    │  ┌─────────────┴─────────────┐
    └──│      close callbacks      │
       └───────────────────────────┘
```

1. **Timers:** Ejecuta callbacks programados por `setTimeout()` y `setInterval()`.
2. **Pending Callbacks:** Ejecuta callbacks I/O pospuestos de la iteración anterior (ej. errores de socket tipo `ECONNREFUSED`).
3. **Idle, Prepare:** Uso interno de libuv.
4. **Poll:** Recupera nuevos eventos I/O y ejecuta callbacks asociados. Si no hay timers activos, el Event Loop se bloqueará aquí esperando I/O.
5. **Check:** Ejecuta exclusivamente callbacks de `setImmediate()`.
6. **Close Callbacks:** Ejecuta callbacks de eventos de cierre (ej. `socket.on('close', ...)`).

---

### 1.2 Prioridad de Microtasks vs Macrotasks
Dentro de cada fase, JavaScript procesa las colas de **Microtasks** antes de continuar con la siguiente fase o task.

- **Microtasks (Prioridad Máxima):**
  1. `process.nextTick()` (se ejecuta INMEDIATAMENTE al finalizar la operación actual, antes de cualquier otra microtarea).
  2. `Promise.then()` / `catch()` / `finally()` y `queueMicrotask()`.
- **Macrotasks:** `setTimeout`, `setInterval`, `setImmediate`, I/O Callbacks.

#### Ejercicio de Predicción de Output (Clásico de Entrevista):
```javascript
console.log('1');

setTimeout(() => console.log('2'), 0);

setImmediate(() => console.log('3'));

Promise.resolve().then(() => console.log('4'));

process.nextTick(() => console.log('5'));

console.log('6');
```
**Resultado correcto:** `1 -> 6 -> 5 -> 4 -> 2 -> 3` (Nota: En algunos entornos, el orden entre `setTimeout(0)` y `setImmediate` puede variar en el tick inicial según el delay de inicialización, pero la secuencia de microtareas `5 -> 4` es **absolutamente determinista**).

---

### 1.3 Libuv y el Worker Thread Pool
Node.js ejecuta el código JavaScript en un solo hilo (Single Thread). Sin embargo, libuv mantiene un **Thread Pool por defecto (4 hilos, ampliable con `UV_THREADPOOL_SIZE`)**.

- **Operaciones Asíncronas del Sistema Operativo (NO usan Thread Pool):** Red (sockets TCP/UDP, HTTP). El SO notifica mediante `epoll` (Linux), `kqueue` (macOS) o `IOCP` (Windows).
- **Operaciones delegadas al Thread Pool de libuv:**
  - Sistema de archivos (`fs.*` asíncrono).
  - Criptografía (`crypto.pbkdf2`, `crypto.randomBytes`).
  - Compresión (`zlib`).
  - Consultas DNS (`dns.lookup`).

---

### 1.4 Escalabilidad CPU-Bound: Cluster vs Worker Threads vs Child Process
| Característica | `cluster` | `worker_threads` | `child_process` |
|---|---|---|---|
| **Mecanismo** | Múltiples procesos Node separables. | Múltiples hilos dentro del mismo proceso. | Procesos independientes del sistema. |
| **Memoria** | Memoria aislada (Master-Worker). | Memoria compartida (`SharedArrayBuffer`). | Memoria totalmente aislada. |
| **Caso de Uso** | Aprovechar núcleos de CPU para servidor web. | Tareas intensivas de CPU (cálculos, imágenes). | Ejecutar comandos del SO o scripts externos. |
| **Overhead** | Medio (copia de memoria por proceso). | Bajo (hilos livianos). | Alto. |

---

## BLOQUE 2: ASINCRONÍA, MODULARIDAD Y MANEJO DE ERRORES

### 2.1 Concurrencia Controlada con Promesas
Diferencias críticas entre los combinadores de Promesas:
- `Promise.all([p1, p2])`: Falla rápido (*short-circuits*) al primer rechazo.
- `Promise.allSettled([p1, p2])`: Espera a que todas finalicen (resueltas o rechazadas). Devuelve objetos `{ status, value/reason }`.
- `Promise.race([p1, p2])`: Se resuelve o rechaza con el resultado de la primera promesas que finalice.
- `Promise.any([p1, p2])`: Se resuelve con la primera promesa exitosa. Falla solo si TODAS son rechazadas (`AggregateError`).

#### Cuándo NO usar `Promise.all()`:
1. Cuando existan dependencias secuenciales entre llamadas.
2. Cuando el número de promesas sea masivo (provocando agotamiento de sockets o *rate limits* en APIs).
3. Cuando se requiera tolerancia a fallos parciales sin abortar todo el lote.

---

### 2.2 CommonJS (CJS) vs ES Modules (ESM)
- **CommonJS:** Cargado sincrónico (`require()`). Mutabilidad de `module.exports`. Ámbito global incluye `__dirname` y `__filename`.
- **ES Modules:** Cargado asincrónico y estático (`import`). Asignaciones inmutables (*live bindings*). No existen `__dirname` ni `__filename` (se derivan con `import.meta.url`). Soporta *Top-level await*.

#### Diferencia entre `exports` y `module.exports`:
```javascript
// Correcto: añade propiedades al objeto exportado
exports.foo = function() {}; 

// ¡ERROR! Reasigna la variable local 'exports' rompiendo la referencia a module.exports
exports = function() {}; 

// Correcto: Exporta la función/clase directamente
module.exports = function() {};
```

---

### 2.3 Manejo Robusto de Errores en APIs
1. **Errores Operacionales:** Situaciones esperadas (404 Not Found, 400 Bad Request, DB Connection Timeout). Deben manejarse limpiamente.
2. **Errores de Programación:** Bugs (TypeError, Null pointer). Requieren reiniciar el proceso de forma segura para evitar estados corruptos.

#### Global Middleware de Errores en Express (4 parámetros obligatorios):
```javascript
// Debe ser el ÚLTIMO middleware en app.js
app.use((err, req, res, next) => {
  logger.error(err.stack);
  
  const status = err.statusCode || 500;
  const message = err.isOperational ? err.message : 'Internal Server Error';

  res.status(status).json({
    success: false,
    error: message,
    ...(process.env.NODE_ENV === 'development' && { stack: err.stack })
  });
});
```

---

## BLOQUE 3: STREAMS, BUFFERS E I/O DE ALTO RENDIMIENTO

### 3.1 Streams y los 4 Tipos Básicos
Los Streams procesan datos fragmento por fragmento (*chunks*) sin cargarlos completamente en memoria RAM.
1. **Readable:** Fuente de datos (`fs.createReadStream`, `req`).
2. **Writable:** Destino de datos (`fs.createWriteStream`, `res`).
3. **Duplex:** Lectura y Escritura independiente (`net.Socket`).
4. **Transform:** Stream Dúplex que modifica/transforma los datos mientras lee/escribe (`zlib.createGzip`, `crypto.createCipher`).

---

### 3.2 Backpressure: El Gran Tema de Seniority
El **Backpressure** ocurre cuando un Stream Readable emite datos a una velocidad superior a la capacidad de procesamiento del Stream Writable de destino.

- Si el buffer interno del Writable supera el `highWaterMark`, `writable.write()` devuelve `false`.
- La función `stream.pipe()` o el helper `stream.promises.pipeline()` gestionan la pausa (`readable.pause()`) y reanudación (`readable.resume()`) de forma automática evitando el agotamiento de memoria.

#### Caso Práctico: Procesamiento de CSV de 10 GB sin rebasar memoria RAM
```javascript
import { pipeline } from 'node:stream/promises';
import fs from 'node:fs';
import { transformCSVStream } from './transformer.js';

async function processHugeFile() {
  try {
    await pipeline(
      fs.createReadStream('input_10gb.csv'),
      transformCSVStream(),
      fs.createWriteStream('output_processed.json')
    );
    console.log('Procesamiento masivo completado exitosamente.');
  } catch (err) {
    console.error('Fallo en el pipeline de streams:', err);
  }
}
```

---

### 3.3 Buffers y Memoria V8
Un `Buffer` representa una secuencia de bytes de tamaño fijo asignada **fuera del Heap de V8** (memoria unmanaged).
- `Buffer.alloc(size)`: Inicializa memoria limpia (llena de ceros).
- `Buffer.allocUnsafe(size)`: Asignación rápida sin limpiar memoria previa (¡riesgo de seguridad si no se sobreescribe inmediatamente!).
- `Buffer.from(arrayOrString)`: Crea un buffer a partir de datos existentes.

---

## BLOQUE 4: EXPRESS.JS, ARQUITECTURA BACKEND Y BASES DE DATOS

### 4.1 Ciclo de Vida y Middlewares en Express
Un Middleware en Express es una función con acceso al objeto de solicitud (`req`), respuesta (`res`), y la función `next()`.

```text
Request ──> Middleware 1 (Auth) ──> Middleware 2 (Validation) ──> Controller ──> Response
```
- Llamar a `next()` pasa el control al siguiente middleware.
- Llamar a `next(error)` salta directamente al Middleware Global de Errores.
- No llamar a `next()` ni enviar respuesta deja la solicitud colgada indefinidamente.

---

### 4.2 Arquitectura Capada (Clean Backend Architecture)
```text
src/
├── controllers/    # Entrada HTTP, extracción de req.params/body, envío de res
├── services/       # Lógica de negocio pura (reutilizable e independiente de HTTP)
├── repositories/   # Acceso a base de datos (queries, ORM models)
├── middlewares/    # Auth, validación con Zod/Joi, rate limiting, logging
├── errors/         # Custom AppErrors (OperationalError, NotFoundError)
└── config/         # Configuración y variables de entorno validadas
```

---

### 4.3 Integración Eficiente con Bases de Datos desde Node
- **Connection Pooling:** Mantener un pool de conexiones abiertas reutilizables en lugar de abrir/cerrar conexiones por cada request HTTP.
- **Problema N+1 Query:** Ocurre al iterar un listado de N elementos y hacer 1 consulta individual a la DB por cada elemento en lugar de hacer un `JOIN` o un `WHERE IN (...)`.
- **Transacciones y Isolation:** Uso de transacciones ACID cuando múltiples operaciones de escrituras dependan entre sí.

---

## BLOQUE 5: SEGURIDAD, PERFORMANCE, MEMORY LEAKS Y OBSERVABILIDAD

### 5.1 Fugas de Memoria (Memory Leaks) en Node.js
Causas comunes de Memory Leaks en producción:
1. **EventEmitters huérfanos:** Agregar listeners con `.on()` sin removerlos con `.removeListener()` o `.off()`.
2. **Cachés globales sin límites:** Guardar objetos en mapas JavaScript globales sin TTL ni algoritmos de desalojo (LRU).
3. **Closures que retienen referencias:** Funciones internas que retienen ámbitos de memoria grandes involuntariamente.

#### Diagnóstico y Debugging:
- Ejecutar el proceso con la bandera `--inspect` y conectar **Chrome DevTools**.
- Generar y comparar dos **Heap Snapshots** (uno de línea base y otro tras la carga de trabajo) para identificar objetos retenidos.
- Herramientas recomendadas: `Clinic.js Doctor`, `Clinic.js Bubbleprof`, `0x`.

---

### 5.2 Checklist de Seguridad Backend en Node.js
- **Helmet:** Configuración de cabeceras HTTP de seguridad (`X-Frame-Options`, `X-Content-Type-Options`, `Content-Security-Policy`).
- **Autenticación JWT:** Utilizar firmas robustas (RS256/HS256), tiempos de expiración cortos y almacenamiento seguro (cookies `HttpOnly; Secure; SameSite=Strict` para web).
- **Protección contra Inyecciones:** Queries parametrizadas/prepared statements (evitar concatenación directa de cadenas).
- **Prototype Pollution:** Sanitizar o congelar (`Object.freeze`) objetos modificados dinámicamente.
- **Rate Limiting:** Prevenir ataques de denegación de servicio (DoS/Brute Force) mediante `express-rate-limit` respaldado por Redis.

---

### 5.3 Graceful Shutdown (Cierre Elegante)
Evita la pérdida de transacciones e inconsistencia de datos cuando Kubernetes/Docker envía un `SIGTERM`.

```javascript
const server = app.listen(PORT, () => console.log(`Server en puerto ${PORT}`));

async function shutdown(signal) {
  console.log(`Recibida señal ${signal}. Iniciando Graceful Shutdown...`);
  
  // 1. Dejar de aceptar nuevas conexiones HTTP
  server.close(async () => {
    console.log('Servidor HTTP cerrado.');
    
    try {
      // 2. Cerrar pools de conexiones a DB y colas
      await db.disconnect();
      await redisClient.quit();
      console.log('Conexiones a recursos cerradas limpiamente.');
      process.exit(0);
    } catch (err) {
      console.error('Error durante el cierre de recursos:', err);
      process.exit(1);
    }
  });

  // 3. Forzar el cierre si las solicitudes pendientes tardan demasiado
  setTimeout(() => {
    console.error('Cierre forzado por Timeout de Graceful Shutdown');
    process.exit(1);
  }, 10000);
}

process.on('SIGTERM', () => shutdown('SIGTERM'));
process.on('SIGINT', () => shutdown('SIGINT'));
```

---

## BLOQUE 6: FUNDAMENTOS DE JAVASCRIPT PARA ENTREVISTAS BACKEND

### 6.1 `var` vs `let` vs `const`
- `var`: Scope de función, hoisting con inicialización `undefined`, permite redeclaración.
- `let` / `const`: Scope de bloque (`{}`), hoisting en la **Temporal Dead Zone (TDZ)** (lanza `ReferenceError` si se accede antes de la declaración). `const` exige asignación inicial e inmutabilidad de la binding (no del contenido si es objeto/array).

---

### 6.2 Closures
Un **Closure** es una función que retiene acceso a las variables de su ámbito léxico externo incluso después de que la función externa haya finalizado su ejecución.

```javascript
function createCounter() {
  let count = 0; // Variable privada encerrada en el closure
  return {
    increment: () => ++count,
    getValue: () => count
  };
}

const counter = createCounter();
console.log(counter.increment()); // 1
console.log(counter.getValue());  // 1
```

---

### 6.3 Binding de `this` y Arrow Functions
- **Funciones Tradicionales:** `this` se determina en tiempo de **ejecución** según cómo sea llamada la función (invocación directa, método de objeto, `call`, `apply`, `bind`).
- **Arrow Functions:** `this` se determina sintácticamente en tiempo de **compilación** (toman el `this` de su ámbito léxico circundante) y **no pueden** ser invocadas con `new` ni rebindeadas con `bind()`.

---

## BLOQUE 7: EJERCICIOS PRÁCTICOS, LIVE CODING Y SYSTEM DESIGN

### Ejercicio 1: Implementar un Custom EventEmitter desde cero
```javascript
class CustomEventEmitter {
  constructor() {
    this.events = {};
  }

  on(event, listener) {
    if (!this.events[event]) {
      this.events[event] = [];
    }
    this.events[event].push(listener);
    return this;
  }

  emit(event, ...args) {
    if (!this.events[event]) return false;
    this.events[event].forEach(listener => listener(...args));
    return true;
  }

  off(event, listenerToRemove) {
    if (!this.events[event]) return this;
    this.events[event] = this.events[event].filter(
      listener => listener !== listenerToRemove
    );
    return this;
  }

  once(event, listener) {
    const wrapper = (...args) => {
      listener(...args);
      this.off(event, wrapper);
    };
    return this.on(event, wrapper);
  }
}
```

---

### Ejercicio 2: Limitador de Concurrencia para Promesas
```javascript
async function mapConcurrent(items, limit, asyncFn) {
  const results = [];
  const executing = new Set();

  for (const item of items) {
    const p = Promise.resolve().then(() => asyncFn(item));
    results.push(p);
    executing.add(p);

    const clean = () => executing.delete(p);
    p.then(clean, clean);

    if (executing.size >= limit) {
      await Promise.race(executing);
    }
  }

  return Promise.all(results);
}
```

---

### Ejercicio 3: System Design Mock — Procesador de Carga Masiva (10k req/sec)
**Problema:** Diseñar una API backend en Node.js para recibir peticiones masivas y procesarlas de forma asíncrona sin caer.

**Solución Arquitectónica:**
1. **Load Balancer (NGINX / AWS ALB):** Distribución de tráfico round-robin.
2. **Cluster / Múltiples Instancias Node:** N procesos escuchando en la misma máquina o escalar horizontalmente con contenedores en AWS ECS/EKS.
3. **Ingestión Liviana:** La API Node **NO** procesa la tarea pesada directamente; valida los datos y publica el mensaje en una cola como **Redis (BullMQ)** o **RabbitMQ / AWS SQS**.
4. **Worker Processes:** Procesos Node de fondo (*Worker Threads* o procesos independientes) consumen de la cola a su propia tasa de procesamiento segura.
5. **Caching:** Uso de **Redis** para lecturas de estado rápido.

---

## BLOQUE 8: PREGUNTAS DE RESPUESTA ORAL INMEDIATA

1. **¿Qué bloquea el Event Loop en Node.js?**  
   *Respuesta:* Operaciones sincrónicas intensivas de CPU (ej. bucles masivos, ordenamientos complejos, JSON.parse de payloads gigantescos, llamadas `fs.*Sync`).
2. **¿Cuál es la diferencia entre `process.nextTick` y `setImmediate`?**  
   *Respuesta:* `process.nextTick` ejecuta su callback inmediatamente al terminar la operación actual (fase de microtareas), mientras que `setImmediate` se ejecuta en la fase *Check* del Event Loop.
3. **¿Por qué Node.js requiere el objeto `Buffer`?**  
   *Respuesta:* Porque JavaScript históricamente solo manejaba cadenas de texto. `Buffer` permite manipular Streams y streams de datos binarios directamente desde la memoria fuera del heap de V8 (ej. TCP, archivos, imágenes).
4. **¿Cuál es la diferencia entre `npm install` y `npm ci`?**  
   *Respuesta:* `npm install` instala dependencias y puede actualizar `package-lock.json` basándose en los rangos de semver. `npm ci` descarta `node_modules`, instala estrictamente lo especificado en `package-lock.json` y falla si hay discrepancias, siendo ideal para pipelines de CI/CD.
5. **¿Qué sucede con las unhandled promise rejections en Node.js moderno?**  
   *Respuesta:* En versiones modernas de Node.js, una promesa rechazada no capturada finaliza el proceso de Node.js con un código de salida distinto de cero si no hay un listener registrado en `process.on('unhandledRejection')`.

---

## PLAN DE ESTUDIO INTENSIVO (DÍA A DÍA) Y CHECKLIST FINAL

| Día | Tema Clave | Tareas Prácticas |
|---|---|---|
| **Día 1 (Jue 27)** | Event Loop & Microtasks | Hacer 10 ejercicios de predicción de output en consola. |
| **Día 2 (Vie 28)** | Streams, Buffers & File System | Implementar un pipeline de transformación de archivo CSV con `stream.pipeline()`. |
| **Día 3 (Sáb 29)** | Performance, Memory Leaks & Clinic.js | Ejecutar script con memory leak simulado y capturar Heap Snapshot con Chrome DevTools. |
| **Día 4 (Dom 30)** | Seguridad, Middlewares & Express | Configurar Rate Limiter con Redis y escribir un Global Error Middleware. |
| **Día 5 (Lun 31)** | Live Coding & Algoritmos | Implementar `CustomEventEmitter` y limitador de concurrencia a mano en BFE.dev / Codewars. |
| **Día 6 (Mar 1)** | Entrevista Simulada & Repaso Oral | Practicar en voz alta las respuestas del Bloque 8 sin mirar apuntes. Descansar temprano. |

### Checklist de Preparación Senior
- [ ] Explicar fluidamente las 6 fases del Event Loop y el orden de microtareas.
- [ ] Diferenciar la responsabilidad de libuv vs código JS de usuario.
- [ ] Explicar y resolver problemas de Backpressure en Streams.
- [ ] Diseñar el esquema de un Graceful Shutdown completo para Docker/K8s.
- [ ] Identificar fugas de memoria mediante Heap Snapshots.
- [ ] Explicar `this`, Closures, TDZ y prototipos sin vacilar.
- [ ] Justificar la arquitectura en capas y separación de responsabilidades en APIs REST.

---
*¡Mucho éxito Francisco en tu entrevista el próximo miércoles 2 de septiembre! Estás listo para demostrar tu seniority.*

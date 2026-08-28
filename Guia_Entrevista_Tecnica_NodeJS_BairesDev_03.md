# Guía de Preparación: Entrevista Técnica Node.js (BairesDev)

Candidato: Francisco Zapata  
Enfoque: Backend / Node.js Core & Ecosystem

## 1. El "Corazón" de Node.js (Lo que siempre preguntan)

### Arquitectura Interna

- Event Loop a fondo: No basta con decir que es "no bloqueante". Debes conocer las fases: _Timers, Pending Callbacks, Idle/Prepare, Poll, Check (setImmediate) y Close Callbacks_.
- Libuv & Thread Pool: Explica cómo Node maneja operaciones pesadas (I/O de archivos, DNS, Criptografía) usando el Thread Pool interno, mientras el hilo principal sigue libre.
- Microtasks vs Macrotasks: Diferencia de prioridad entre `process.nextTick()`, `Promise.then()` y `setTimeout`.

### Streams & Buffers (Esencial para Seniority)

- Buffers: Cómo Node maneja datos binarios fuera del motor V8.
- Streams: Por qué usar `pipe()`. Diferencia entre Readable, Writable, Duplex y Transform. Prepárate para explicar cómo manejar el _Backpressure_ (cuando el origen envía datos más rápido de lo que el destino puede procesarlos).

### Escalabilidad y Procesos

- Cluster Module vs Worker Threads: Cuándo usar cada uno. (Cluster para paralelizar el servidor en múltiples núcleos; Worker Threads para tareas intensivas de CPU como procesamiento de imágenes o grandes cálculos).
- Child Processes: Diferencia entre `spawn`, `exec`, `execFile` y `fork`.

## 2. Patrones y Mejores Prácticas

- Error Handling: Por qué nunca debemos dejar que un error llegue a un `uncaughtException`. Manejo de errores en flujos asíncronos y flujos de Streams.
- Event Emitters: Entender el patrón Observador dentro de Node y los posibles _Memory Leaks_ si no se remueven los listeners adecuadamente.
- Security: Implementación de JWT, protección contra ataques de fuerza bruta, seguridad en dependencias (`npm audit`) y el uso de `helmet`.

## 3. Entorno y Herramientas

- Performance Profiling: Cómo detectar un "Event Loop lag" o una fuga de memoria (Heap snapshots).
- Testing: Dominio de Jest o Mocha/Chai. Enfoque en Mocking de servicios externos y bases de datos.
- Package Management: Diferencia entre `npm` y `pnpm` o `yarn` (especialmente el manejo de archivos lock).

## 4. Recursos para Practicar (Específicos Node)

### Práctica de Código

- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices): Es la "biblia" de Node. Lee especialmente la sección de Error Handling y Seguridad.
- [Node.js Interview Questions (RisingStack)](https://blog.risingstack.com/node-js-interview-questions-and-answers-2017/): Aunque el post tiene tiempo, las preguntas sobre el Event Loop siguen siendo estándar de oro.

### Ejercicios de Lógica en Node

- [LeetCode (JS/TS track)](https://leetcode.com/): Enfócate en problemas que requieran manipulación de grandes conjuntos de datos o concurrencia.
- [NodeSchool.io](https://nodeschool.io/): Haz los talleres de "Stream-adventure" y "Learn-you-node". Son excelentes para recordar la sintaxis de bajo nivel.
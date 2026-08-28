# Guía de preparación — Entrevista técnica Node.js (BairesDev)
**Fecha de la entrevista:** miércoles 2 de septiembre de 2026 · **Formato:** conversación técnica con un ingeniero

## Qué esperar de esta entrevista

Revisando experiencias reales de candidatos de BairesDev en roles de Node.js, un patrón se repite: **suele ser más conceptual que de "live coding" puro**. Preguntan cosas como palabras reservadas del lenguaje, tipos primitivos, "¿cómo debuggearías una app con memory leak?" o "¿cómo arquitecturarías el procesamiento de un CSV grande?". Es decir, evalúan que entiendas el *por qué* detrás de las cosas, no solo que sepas la sintaxis. Algunas entrevistas sí incluyen un ejercicio corto de código o pseudocódigo en vivo, así que conviene estar listo para ambos escenarios.

Con tu experiencia (NestJS, Laravel, AWS, y el backend Node que armaste para la prueba de Geest) ya tienes bases sólidas. Esta guía prioriza los temas donde a los devs senior se les suele "trabar" en preguntas de profundidad — el event loop, streams, memory leaks, seguridad — más que los básicos que ya dominas.

---

## 1. El runtime de Node.js (lo más preguntado, domínalo a fondo)

- **Event loop**: sus fases en orden — *timers → pending callbacks → idle/prepare → poll → check → close callbacks*. Qué corre en cada una.
- **libuv** y el thread pool: qué operaciones son realmente asíncronas a nivel de SO (red) vs cuáles usan el thread pool (fs, crypto, DNS lookup).
- **Microtasks vs macrotasks**: orden de ejecución entre `process.nextTick`, `Promise.then`, `setTimeout(fn, 0)` y `setImmediate`. Te van a pedir que expliques o predigas el output de un snippet con estos cuatro mezclados — practica hasta que te salga sin dudar.
- Por qué Node es single-threaded para el código JS pero no bloqueante para I/O, y cómo eso lo hace escalar bien para cargas I/O-bound pero mal para CPU-bound.
- Cómo Node escala en CPU-bound: `cluster` vs `worker_threads` — diferencias, cuándo usar cada uno.

## 2. Asincronía y manejo de errores

- Callbacks con la convención *error-first*, Promises, `async/await`.
- `Promise.all` vs `allSettled` vs `race` vs `any` — diferencia práctica y cuándo usar cada uno.
- Unhandled promise rejections: qué pasa si no las capturas, `process.on('unhandledRejection')`.
- Manejo de errores en código async: por qué un `try/catch` no captura errores de un callback fuera de esa cadena.
- Clases de error personalizadas y una estrategia centralizada de manejo de errores (útil mencionar tu experiencia con middlewares de error en Express/Nest).

## 3. Sistema de módulos: CommonJS vs ESM

- `require`/`module.exports` vs `import`/`export`.
- Module caching: por qué un módulo solo se ejecuta una vez aunque se importe varias veces.
- Diferencias prácticas al migrar de CJS a ESM (`__dirname` no existe en ESM, `import.meta.url`, extensiones de archivo obligatorias).

## 4. Streams y Buffers

- Los 4 tipos: `Readable`, `Writable`, `Duplex`, `Transform`.
- **Backpressure**: qué es, por qué existe, cómo `pipe()` la maneja automáticamente.
- Caso clásico de entrevista: "¿cómo procesarías un archivo de 5GB sin quedarte sin memoria?" → streams en vez de cargarlo entero con `fs.readFile`.
- Qué es un `Buffer` y por qué existe (datos binarios, fuera del heap de V8).

## 5. Módulos core que suelen salir en preguntas

- `events` (`EventEmitter`) — que puedas implementar uno básico desde cero es un ejercicio clásico.
- `fs` (sync vs async vs streams), `path`, `http`/`https`, `crypto`, `os`, `child_process`.

## 6. Memoria, performance y escalabilidad

- **Memory leaks**: causas típicas (closures que retienen referencias, listeners de `EventEmitter` no removidos, caches sin límite de tamaño, variables globales).
- Cómo detectarlos: `--inspect` + Chrome DevTools, heap snapshots, `clinic.js`, `0x`.
- `cluster` para aprovechar varios cores, balanceo de carga, PM2 en producción.
- Perfilado básico: identificar si un problema es CPU-bound o I/O-bound.

## 7. APIs REST (Express y equivalentes)

- Pipeline de middlewares, orden de ejecución, middleware de manejo de errores (4 parámetros: `err, req, res, next`).
- Diseño de rutas REST, códigos de estado HTTP correctos, versionado de API.
- Dado tu background con NestJS, puedes mencionar la diferencia de enfoque (arquitectura opinionada + DI vs Express minimalista) si surge la comparación — es un buen punto a tu favor.

## 8. Testing

- Jest/Mocha/Vitest: unit tests vs integration tests.
- `supertest` para testear endpoints HTTP.
- Mocking de dependencias externas (DB, servicios de terceros).

## 9. Seguridad (tema que se pregunta seguido y se olvida repasar)

- Inyección (SQL/NoSQL), *prototype pollution*, ReDoS.
- Validación de entradas (Joi, Zod), `helmet`, rate limiting.
- Manejo seguro de secrets/variables de entorno, `npm audit`.
- JWT: expiración, refresh tokens, dónde NO guardar el token (localStorage vs cookie httpOnly).

## 10. Bases de datos desde Node

- Connection pooling y por qué importa.
- Transacciones, problema N+1 con ORMs.
- Sequelize/TypeORM/Prisma — trade-offs generales (ya los conoces desde tu stack).

## 11. Preguntas de arquitectura/diseño frecuentes

Practica explicar en voz alta (2-3 minutos, sin mirar notas) tu respuesta a:
- "Diseña una API REST escalable para X" — toca caching (Redis), balanceo de carga, rate limiting.
- "¿Cómo debuggearías un memory leak en producción?"
- "¿Cómo procesarías un CSV/archivo grande sin tumbar el servidor?"
- "Monolito vs microservicios: cuándo usar cada uno."

## 12. Trivia de JavaScript que también preguntan

- `var` vs `let` vs `const` (scope, hoisting, TDZ).
- `==` vs `===`, coerción de tipos.
- Closures — que puedas explicarlo con un ejemplo propio, no solo la definición.
- `this` binding (arrow functions vs functions normales).
- Prototypal inheritance vs clases ES6 (azúcar sintáctico sobre prototipos).
- Tipos primitivos de JS (los 7: string, number, bigint, boolean, undefined, symbol, null).

---

## Recursos para practicar

**Documentación y teoría**
- [Node.js Guides oficiales](https://nodejs.org/en/docs/guides) — sección de Event Loop es lectura obligatoria.
- *Node.js Design Patterns* (Mario Casciaro) — el libro de referencia para preguntas de profundidad tipo BairesDev.
- [roadmap.sh/nodejs](https://roadmap.sh/nodejs) — checklist visual de todos los temas, bueno para detectar huecos rápido.
- Charla "What the heck is the event loop anyway?" (Philip Roberts, YouTube) — la explicación visual clásica, ideal para repasar en 25 min.

**Ejercicios prácticos (hands-on)**
- [Learn You The Node.js](https://github.com/workshopper/learnyounode) (NodeSchool) — ejercicios de terminal sobre streams, fs, módulos core. Muy útil para afianzar lo básico rápido.
- [BFE.dev](https://bfe.dev/) — retos de implementar cosas como `debounce`, `EventEmitter`, tu propio `Promise`, `throttle` desde cero. Justo el tipo de pregunta que les gusta a entrevistadores backend.
- [Exercism — Node.js track](https://exercism.org/tracks/javascript) — ejercicios con feedback, buenos para practicar async patterns.
- Clinic.js ([clinicjs.org](https://clinicjs.org/)) — instálalo y practica diagnosticar un memory leak real en un script pequeño; te da una anécdota concreta para contar en la entrevista.

**Algoritmos y estructuras de datos** (por si la entrevista incluye una parte de coding)
- [LeetCode](https://leetcode.com/) — filtra por "Easy/Medium" y JavaScript.
- [Codewars](https://www.codewars.com/) — katas cortas, buenas para calentar antes de la entrevista misma.

**Mini-proyecto de repaso integrador** (opcional, si te queda tiempo)
Arma en un día una API REST pequeña con Express: CRUD + auth JWT + rate limiting + un endpoint que procese un archivo con streams + tests con supertest. Te obliga a tocar la mitad de los temas de esta guía en pocas horas.

---

## Plan sugerido para los próximos 6 días

| Día | Foco |
|---|---|
| Jue 27 | Event loop, microtasks/macrotasks, asincronía (secciones 1-2) |
| Vie 28 | Streams, buffers, módulos core, EventEmitter (secciones 4-5) |
| Sáb 29 | Memory leaks, performance, cluster/worker_threads (sección 6) — practica con Clinic.js |
| Dom 30 | Seguridad + bases de datos (secciones 9-10) |
| Lun 31 | Preguntas de arquitectura/diseño en voz alta + trivia de JS (secciones 11-12) |
| Mar 1 | Repaso general, katas rápidas en Codewars/BFE.dev, descansa temprano |


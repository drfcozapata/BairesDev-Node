# 🚀 Preparación — Entrevista Técnica Senior Backend Node.js (BairesDev)

Material de estudio integral para la entrevista técnica de **Node.js** en **BairesDev**.

| Dato | Detalle |
| --- | --- |
| **Perfil** | Senior Backend Node.js Developer |
| **Empresa** | BairesDev |
| **Fecha objetivo** | Miércoles, 2 de septiembre de 2026 |
| **Candidato** | Francisco Zapata |
| **Enfoque** | Backend / Node.js Core & Ecosystem |

> **Objetivo:** llegar a la entrevista no solo sabiendo "usar Node.js", sino pudiendo
> explicar qué ocurre internamente, justificar decisiones de arquitectura y resolver
> ejercicios en vivo (live coding / system design).

---

## 📚 Cómo usar este repositorio

El contenido está dividido en dos grupos complementarios:

1. **Guías de entrevista** — lectura orientada a la conversación técnica, con
   preguntas frecuentes, respuestas modelo y ejercicios cortos.
2. **Bloques temáticos (`Node_Bloque_XX.md`)** — teoría detallada por área, con
   ejemplos de código (ES2020+), diagramas Mermaid y preguntas de práctica.

**Ruta recomendada:** empieza por `Guia_Preparacion_NodeJS_BairesDev.md` (índice
integral) y `Node_Bloque_00.md` (plan de estudio), luego profundiza bloque por bloque
siguiendo el [cronograma](#-plan-de-estudio).

---

## 🗂️ Índice de documentos

### Guías de entrevista (visión general y conceptual)

| Archivo | Contenido |
| --- | --- |
| [`Guia_Preparacion_NodeJS_BairesDev.md`](./Guia_Preparacion_NodeJS_BairesDev.md) | Guía integral: perfil de evaluación, 8 bloques, live coding, preguntas de respuesta inmediata y checklist final. |
| [`Guia_NodeJS_BairesDev.md`](./Guia_NodeJS_BairesDev.md) | Especificación de entregables y bloques temáticos con teoría y ejemplos. |
| [`Guia_Entrevista_Tecnica_NodeJS_BairesDev_01.md`](./Guia_Entrevista_Tecnica_NodeJS_BairesDev_01.md) | Guía intensiva: event loop, concurrencia, streams, seguridad (la más profunda). |
| [`Guia_Entrevista_Tecnica_NodeJS_BairesDev_02.md`](./Guia_Entrevista_Tecnica_NodeJS_BairesDev_02.md) | Runtime de Node.js, asincronía y manejo de errores. |
| [`Guia_Entrevista_Tecnica_NodeJS_BairesDev_03.md`](./Guia_Entrevista_Tecnica_NodeJS_BairesDev_03.md) | El "corazón" de Node.js, streams/buffers y escalabilidad. |
| [`Guia_Entrevista_Tecnica_NodeJS_BairesDev_04.md`](./Guia_Entrevista_Tecnica_NodeJS_BairesDev_04.md) | Núcleo de Node.js: event loop, datos/recursos y patrones de diseño. |

### Bloques temáticos (teoría + ejercicios)

| Archivo | Bloque |
| --- | --- |
| [`Node_Bloque_00.md`](./Node_Bloque_00.md) | Plan de estudio y cronograma (7 y 14 días) |
| [`Node_Bloque_01.md`](./Node_Bloque_01.md) | Bloque 1 — JavaScript Avanzado (ES2020+) |
| [`Node_Bloque_02.md`](./Node_Bloque_02.md) | Bloque 2 — Fundamentos de Node.js (Event Loop, streams, cluster) |
| [`Node_Bloque_03.md`](./Node_Bloque_03.md) | Bloque 3 — Express.js y desarrollo de APIs |
| [`Node_Bloque_04.md`](./Node_Bloque_04.md) | Bloque 4 — PostgreSQL y bases de datos relacionales |
| [`Node_Bloque_05.md`](./Node_Bloque_05.md) | Bloque 5 — Redis y caché en memoria |
| [`Node_Bloque_06.md`](./Node_Bloque_06.md) | Bloque 6 — Arquitectura de microservicios |
| [`Node_Bloque_07.md`](./Node_Bloque_07.md) | Bloque 7 — Contenedores, Kubernetes y nube (AWS/GCP) |
| [`Node_Bloque_08.md`](./Node_Bloque_08.md) | Bloque 8 — Pruebas, seguridad y buenas prácticas |

---

## 🧠 Temas prioritarios (domina sin dudar)

1. **Event Loop y modelo de concurrencia** — fases, libuv, thread pool, microtasks vs macrotasks (`process.nextTick` vs `Promise.then` vs `setTimeout` vs `setImmediate`).
2. **Asincronía y manejo de errores** — callbacks error-first, Promises, `async/await`, `Promise.all/allSettled/race/any`, `unhandledRejection`.
3. **Streams, Buffers y backpressure** — `pipe()`, Readable/Writable/Duplex/Transform.
4. **Escalabilidad** — `cluster` vs `worker_threads`, `spawn`/`exec`/`fork`.
5. **Seguridad y performance** — OWASP Top 10, memory leaks, observabilidad.

---

## 🗓️ Plan de estudio

Basado en [`Node_Bloque_00.md`](./Node_Bloque_00.md), con dos horizontes: **7 días intensivos** o **14 días detallados**.

| Bloque | Prioridad | Dificultad | Horas |
| --- | --- | --- | --- |
| 1. JavaScript Avanzado | Alta | Media | 6 h |
| 2. Node.js Core (Event Loop) | Alta | Alta | 6 h |
| 3. Express.js (APIs) | Alta | Media | 6 h |
| 4. PostgreSQL (SQL, ACID) | Alta | Alta | 6 h |
| 5. Redis & Caché | Media | Media | 5 h |
| 6. Microservicios / Arquitectura | Alta | Alta | 8 h |
| 7. Docker / Kubernetes / Cloud | Media | Alta | 7 h |
| 8. Testing y Seguridad | Alta | Alta | 7 h |

---

## ✅ Checklist final (antes de la entrevista)

- [ ] Explicar el Event Loop y predecir el output de snippets con micro/macrotasks.
- [ ] Diferenciar `cluster` de `worker_threads` y cuándo usar cada uno.
- [ ] Explicar streams y backpressure con un ejemplo de archivo/CSV grande.
- [ ] Diseñar una API Express con middleware de errores y validación.
- [ ] Resolver una transacción ACID y explicar niveles de aislamiento en PostgreSQL.
- [ ] Proponer una estrategia de caché con Redis (cache-aside, TTL).
- [ ] Describir una arquitectura de microservicios (API Gateway, Circuit Breaker, event-driven).
- [ ] Dockerizar una app Node y describir despliegue en K8s / nube.
- [ ] Aplicar OWASP, JWT/OAuth y testing (Jest/Mocha) en un ejercicio.

---

## 📝 Notas

- Todo el material está en **Markdown**; los diagramas usan **Mermaid** (ver con
  cualquier visor compatible: GitHub, VS Code, Obsidian).
- Los ejemplos de código asumen **Node.js 18+** y sintaxis **ES2020+**.

> Material de preparación personal. No constituye documentación oficial de BairesDev.

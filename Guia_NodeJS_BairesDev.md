**Entregables:** Se generarán los siguientes archivos en formato Markdown (.md), cada uno correspondiente a un bloque temático y al plan de estudio:

- **Bloque1_JSAvanzado.md:** JavaScript avanzado (ES2020+) – concepto y sintaxis moderna en Node.js.  
- **Bloque2_NodeCore.md:** Fundamentos de Node.js – event loop, módulos nativos, concurrencia.  
- **Bloque3_Express.md:** Desarrollo de APIs con Express – routing, middleware y buenas prácticas.  
- **Bloque4_PostgreSQL.md:** Bases de datos relacionales con PostgreSQL – SQL, transacciones, conexión desde Node.  
- **Bloque5_Redis.md:** Almacenamiento en caché con Redis – estructuras de datos y patrones de caching.  
- **Bloque6_Microservicios.md:** Arquitectura y microservicios – diseño escalable, patrones y comunicación entre servicios.  
- **Bloque7_DevOps.md:** Contenedores y nube – Docker, Kubernetes y despliegue en AWS/GCP.  
- **Bloque8_SeguridadPruebas.md:** Pruebas y seguridad – testing en Node.js, OWASP y mejores prácticas de seguridad.  
- **PlanEstudio.md:** Cronograma de estudio y comparación de temas (plan a 7 y 14 días, tabla de prioridades y dificultades).

Cada archivo incluirá: resumen ejecutivo, teoría detallada, ejemplos de código Node.js (ES2020+), preguntas de entrevista con respuestas modelo, ejercicios prácticos con soluciones, fuentes clave (preferiblemente oficiales y en español) y estimación de tiempo por subtema. Se usarán diagramas mermaid donde sea útil (p. ej. arquitectura, flujogramas, timeline).  

```markdown
# Bloque 1: JavaScript Avanzado (ES2020+)

## Resumen Ejecutivo
JavaScript es el lenguaje base de Node.js y conocer sus características modernas es esencial para un *Senior Backend Node.js*. JS es un lenguaje prototípico, dinámico y de tipado flexible. En este bloque se repasan novedades de ES2020+ (p. ej. *optional chaining*, *nullish coalescing*), sintaxis avanzada (promesas, *async/await*, arrow functions, destructuring) y conceptos clave (alcance de variables, closures). Todo ello permite escribir código backend más limpio, eficiente y fácil de mantener. Además, se introduce cómo Node.js aprovecha el **event loop** de JavaScript para manejar I/O no bloqueante.

## Material Teórico Detallado
- **Sintaxis moderna:** Uso de `let`/`const` (scope de bloque, inmutabilidad de `const` vs `var` hoisting). Ejemplo: 
  ```js
  const PI = 3.1415;
  let contador = 0;
  if (true) {
    let x = 5;
    // x solo existe aquí
  }
  ```
- **Funciones flecha y `this`:** Las *arrow functions* (`() => {}`) ofrecen sintaxis concisa y enlazan `this` léxico. Son útiles para callbacks:
  ```js
  const nums = [1,2,3];
  const cuadrados = nums.map(n => n*n);
  ```
- **Clases y prototipos:** Aunque JS es prototípico, ES6 introdujo `class` como azúcar sintáctico. Las clases permiten herencia y métodos:
  ```js
  class Animal {
    constructor(nombre) { this.nombre = nombre; }
    hablar() { console.log(`${this.nombre} hace ruido.`); }
  }
  class Perro extends Animal {
    hablar() { console.log(`${this.nombre} dice ¡guau!`); }
  }
  ```
- **Desestructuración:** Extrae valores de arrays/objetos en variables:
  ```js
  const [a,b] = [10,20]; // a=10, b=20
  const {id, name} = {id:1, name:'Ana'};
  ```
- **Parámetros rest/spread:** Permiten manejar listas de argumentos o expandir arrays/objetos:
  ```js
  function sumar(...numeros) { return numeros.reduce((a,b)=>a+b); }
  const arr2 = [...arr1, 4,5];
  ```
- **Template literals:** Cadenas con expresiones embebidas y multilínea:
  ```js
  const user = {name:'Carlos', age:30};
  console.log(`Usuario: ${user.name}, edad ${user.age}`);
  ```
- **Promesas y async/await:** Modelo de asincronía moderno. 
  - *Promesas* representan resultados futuros, se encadenan con `.then()` y manejan errores con `.catch()`. 
  - `async function` y `await` permiten código asíncrono con estilo sincrónico. Por ejemplo:
    ```js
    async function obtenerDatos() {
      try {
        const res = await fetch('https://api.example.com/data');
        const data = await res.json();
        return data;
      } catch(err) {
        console.error('Error:', err);
      }
    }
    ```
- **Funciones de Primera Clase y Closures:** En JS, las funciones son objetos de primera clase (pueden pasarse como argumentos, retornarse, almacenarse). Un *closure* es cuando una función interna mantiene acceso a variables de su función contenedora:
  ```js
  function contadorPrueba(inicio) {
    let cuenta = inicio;
    return () => ++cuenta;  // este inner function cierra sobre 'cuenta'
  }
  const c = contadorPrueba(10);
  console.log(c()); // 11
  ```
- **Módulos:** Node.js soporta CommonJS (`require`) y desde ES2020 permite módulos ESM (`import/export`). Con ESM, se usan `export` en archivos y `import x from 'archivo'`. Ejemplo:
  ```js
  // utils.js
  export function saludar() { console.log('¡Hola!'); }

  // index.js
  import { saludar } from './utils.js';
  saludar();
  ```
- **Iteradores y Generators (avanzado opcional):** Objetos que definen *Symbol.iterator* para iterar (p. ej. `for...of`). Los *generators* (`function*`) crean iteradores personalizados.  

## Ejemplos Prácticos (Node.js ES2020+)
```js
// Ejemplo: Funciones flecha, destructuración y async/await
const fs = require('fs').promises;

async function procesarArchivo(path) {
  try {
    // Leer archivo de forma asíncrona
    const contenido = await fs.readFile(path, 'utf8');
    // Desestructurar palabra más larga
    const palabras = contenido.split(/\s+/);
    const larga = palabras.reduce((a,b) => a.length>b.length ? a:b, "");
    // Arrow function para filtrar
    const largas = palabras.filter(p => p.length > 5);
    return { masLarga: larga, lista: largas };
  } catch(err) {
    throw new Error('Fallo lectura: ' + err);
  }
}

procesarArchivo('archivo.txt')
  .then(resultado => console.log(resultado))
  .catch(err => console.error(err));
```
Este fragmento muestra `async/await`, manejo de promesas (`then/catch`), funciones flecha y destructuring.

```js
// Ejemplo: Closure para encapsular estado privado
function crearContador() {
  let cuenta = 0;
  return function() {
    cuenta++;
    return cuenta;
  };
}
const contar = crearContador();
console.log(contar()); // 1
console.log(contar()); // 2
```
Aquí `crearContador` devuelve una función que cierra sobre la variable `cuenta`, demostrando un closure.

## Preguntas Comunes de Entrevista
- **¿En qué se diferencian `var`, `let` y `const`?**  
  `var` tiene scope de función y hoisting, mientras `let`/`const` son de bloque. `const` define constantes (no reasignables). Ejemplo: `const x = 5; x = 6; // Error`.
- **¿Qué es un closure en JavaScript?**  
  Un closure es una función que recuerda el entorno donde fue creada. Permite que una función interna acceda a variables externas incluso tras finalizar su llamada. (Ver ejemplo `contador` arriba).
- **Explica la asincronía en JS y el event loop.**  
  JavaScript es de un solo hilo pero no bloqueante: I/O asíncrono se maneja con callbacks o promesas, y el *event loop* coordina la ejecución cuando llegan datos. Node.js utiliza este modelo para concurrencia.
- **¿Qué es *async/await*?**  
  Sintaxis azúcar para manejar promesas: una función `async` retorna una promesa, y dentro `await` “espera” la resolución de una promesa (no detiene todo el programa, sólo la ejecución local).  
- **¿Qué son los módulos en Node y cómo importas uno?**  
  Node usa módulos CommonJS (`module.exports`/`require`). A partir de ES2020 puede usar módulos ESM (`export`/`import`). Ejemplo: `const fs = require('fs');` o con ESM: `import fs from 'fs';`.

## Ejercicios Prácticos
1. **Suma asíncrona:** Escribe una función `sumarAsync(numeros)` que reciba un array y devuelva una promesa que resuelva la suma de los números tras 1 segundo. Utiliza `async/await` y `setTimeout`. *Solución:*  
   ```js
   function sumarAsync(nums) {
     return new Promise((res) => {
       setTimeout(() => {
         const suma = nums.reduce((a,b) => a+b, 0);
         res(suma);
       }, 1000);
     });
   }

   (async () => {
     const total = await sumarAsync([1,2,3,4]);
     console.log(total); // 10
   })();
   ```
2. **Clausuras y contenedores:** Crea una función `filtrarPor(n, lista)` que retorna otra función. La función interna debe tomar un array de números y devolver sólo aquellos mayores que `n`, usando cierre. *Solución:*
   ```js
   function filtrarPor(n) {
     return function(lista) {
       return lista.filter(x => x > n);
     };
   }
   const mayorQue10 = filtrarPor(10);
   console.log(mayorQue10([5,12,3,18])); // [12,18]
   ```
3. **Desestructuración en Node:** Dado un objeto `{a:1, b:2, c:3}`, crea variables `a` y `c` usando destructuración en una sola línea. *Solución:*  
   ```js
   const obj = {a:1, b:2, c:3};
   const {a, c} = obj;
   console.log(a, c); // 1 3
   ```

## Fuentes Clave y Lecturas
- *Mozilla MDN (ES)*: guía de JavaScript (lenguaje, clases, prototipos).  
- *javascript.info*: artículo sobre **Event Loop** (explicación en español).  
- *Node.js Docs*: apartado *The Event Loop* (modelo de concurrencia en Node).  
- *OWL (Odin) Labs / Ejemplos:* repaso de ES6+ (en inglés) y blogs de comunidad (v.g. Platzi, TutorialesPoint).
- *TC39/ECMAScript*: especificaciones oficiales de ECMAScript para detalles sintácticos.

## Tiempo de Estudio Estimado
- *ES6/ES2020 sintaxis avanzada:* 2 horas (arrow, destructuring, clases, módulos).  
- *Asincronía en JS:* 2 horas (promesas, async/await, callbacks, event loop).  
- *Closures y scope:* 1 hora (concepto y ejemplos).  
- *Práctica de código:* 2 horas (resolver ejercicios y revisar soluciones).  

```markdown
# Bloque 2: Fundamentos de Node.js

## Resumen Ejecutivo
Node.js es un entorno de ejecución de JavaScript en el servidor, basado en el motor V8 de Chrome. Su modelo clave es el **event loop** de un solo hilo, que permite I/O asíncrono y alto rendimiento. En este bloque se estudian sus componentes fundamentales: el loop de eventos, el sistema de módulos nativos, manejo de archivos y streams, concurrencia (libuv, *threads*, *clusters*) y herramientas de perfilado. Dominar estos conceptos es crucial para entender el comportamiento de una app Node en producción y evitar bloqueos de CPU.

## Material Teórico Detallado
- **Modelo de Concurrencia en Node:** Node usa un hilo principal con un *event loop*. Operaciones de I/O se delegan al kernel (mediante libuv) y se notifican cuando terminan. Esto permite atender miles de conexiones sin bloquear el hilo de JS.  
- **Event Loop (Loop de eventos):** Constituido por *fases* (timers, pending callbacks, poll, check, close callbacks). Un ejemplo simplificado está en la doc oficial. Proceso: Node ejecuta el script inicial, luego entra en un ciclo infinito manejando callbacks y timers.  
- **Timers y Async Hooks:** Node proporciona *setTimeout*, *setImmediate*, *process.nextTick* para programar tareas. `process.nextTick` se ejecuta antes de volver al loop; `setImmediate` se ejecuta en la fase *check*. Saber diferenciar evita bloqueos inesperados.  
- **Módulos Nativos de Node:** Node incluye módulos internos (e.g., `fs`, `path`, `http`, `events`) que ofrecen funcionalidades de bajo nivel. Se importan con `const fs = require('fs');`. Ej.: `fs.readFileSync` vs *async* `fs.readFile`. Dominar estos módulos es básico (Documentación oficial: Node API).  
- **Streams y Buffers:** Para I/O eficiente, Node utiliza *streams* (lectura/escritura por trozos) y *buffers* (binarios en memoria). Ejemplo: `fs.createReadStream('file.txt').pipe(process.stdout)`. Los streams tienen métodos `.on('data')`, `.on('end')`. Permiten manejar grandes archivos sin cargar todo en memoria.  
- **Cluster y Multithreading:** Aunque JS es single-threaded, Node permite crear procesos hijos (*cluster*) para aprovechar múltiples núcleos. `cluster.fork()` clona el proceso. Además, Node 10+ ofrece *Worker Threads* para tareas CPU-intensivas (hilos reales dentro del proceso).  
- **Árbol de procesos y gestión de excepciones:** `process` es un objeto global con info del proceso (env vars, PID, etc.). Importante manejar eventos: `'uncaughtException'`, `'unhandledRejection'` para no colapsar el servidor ante errores.  
- **Garbage Collector y V8:** Node usa el GC del motor V8. Comprender cómo monitorizar memoria (`--inspect`, `heap snapshot`) ayuda a prevenir fugas. Node.js 18+ incluye inspector y herramientas de rendimiento integradas.  

## Ejemplos Prácticos (Node.js)
```js
// Ejemplo: Event Loop, Timers vs setImmediate
console.log('Inicio');
setTimeout(() => console.log('Timeout 0'), 0);
setImmediate(() => console.log('setImmediate'));
process.nextTick(() => console.log('nextTick'));
console.log('Fin');
// Posibles salidas: 'Inicio', 'Fin', 'nextTick', 'Timeout 0', 'setImmediate'.
// nextTick siempre antes de timers y immediates.
```
```js
// Ejemplo: Uso de streams para copiar un archivo
const fs = require('fs');
const readStream = fs.createReadStream('origen.txt');
const writeStream = fs.createWriteStream('destino.txt');
readStream.on('data', chunk => writeStream.write(chunk));
readStream.on('end', () => console.log('Copia completa'));
```
```js
// Ejemplo: Cluster para múltiples procesos
const cluster = require('cluster');
const http = require('http');
const numCPUs = require('os').cpus().length;
if (cluster.isMaster) {
  console.log(`Maestro ${process.pid} arrancando`);
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }
  cluster.on('exit', (worker, code, signal) => {
    console.log(`Worker ${worker.process.pid} murió`);
  });
} else {
  // Cada worker crea un servidor HTTP sencillo
  http.createServer((req, res) => {
    res.writeHead(200);
    res.end(`Hola mundo desde worker ${process.pid}`);
  }).listen(8000);
  console.log(`Worker ${process.pid} iniciado`);
}
```

## Preguntas Comunes de Entrevista
- **¿Qué es el event loop de Node.js?**  
  Es un ciclo continuo que permite la ejecución de callbacks asíncronos. Node delega I/O al kernel y, cuando la operación termina, el callback se encola en el loop. Así, aunque JS es single-thread, puede manejar concurrencia.
- **¿Cómo evita Node bloquear el hilo principal en operaciones de I/O?**  
  Todas las operaciones de I/O (lectura/escritura de archivos, red) se realizan de forma **no bloqueante**. Bajo el capó, libuv usa hilos del sistema o mecanismos eficientes del kernel. Los datos se reciben mediante callbacks o promesas cuando estén listos.  
- **¿Qué son Streams en Node? ¿Cuándo usarlos?**  
  Los streams permiten procesar datos en trozos. Existen *Readable*, *Writable*, *Duplex* y *Transform*. Son útiles para leer/escribir archivos grandes o transferir datos (p.ej., en respuestas HTTP) sin cargar todo en memoria.
- **¿Cómo levantar múltiples instancias de una app Node?**  
  Usando el módulo `cluster` o herramientas externas (PM2, etc.). El módulo `cluster` permite forkar procesos workers que comparten el mismo puerto, equilibrando carga entre núcleos (multicore).  
- **¿Qué sucede si olvido manejar un error en callback o promesa?**  
  En Node, un error no atrapado puede desacelerar o terminar el proceso. Es crítico usar `try/catch` en funciones `async` o `.catch()` en promesas, y escuchar eventos `'uncaughtException'` para evitar crashes no deseados.

## Ejercicios Prácticos
1. **Temporizadores y eventos:** Escribe un script Node que imprima "Tick" cada 1 segundo (usa `setInterval`) y "Tock" cada 2 segundos. Detenlo después de 5 “Tick”. *Solución:*  
   ```js
   let ticks = 0;
   const interval = setInterval(() => {
     console.log('Tick');
     ticks++;
     if (ticks === 5) clearInterval(interval);
   }, 1000);
   setInterval(() => console.log('Tock'), 2000);
   ```
2. **File I/O síncrono vs asíncrono:** Comparar tiempos: lee 10MB de un archivo grande usando `fs.readFileSync` y luego con `fs.readFile` asíncrono. *(Manual):* Se notará que la versión síncrona bloquea el evento, mientras que la asíncrona permite otras operaciones durante la lectura.
3. **Streams en acción:** Implementa una copia de archivo usando *pipes*:  
   ```js
   const fs = require('fs');
   fs.createReadStream('source.bin').pipe(fs.createWriteStream('dest.bin'))
     .on('finish', () => console.log('Copiado con pipe listo'));
   ```

## Fuentes Clave y Lecturas
- *Node.js Documentation:* guía sobre *The Node.js Event Loop* y API de *fs*, *stream*, *cluster*.  
- *Node.js GitHub Wiki:* varias entradas (por ejemplo sobre *cluster*).  
- *Artículos de la comunidad:* explicaciones de event loop en español (e.g. javascript.info) y blogs de Node.  
- *V8 & libuv:* documentación oficial del motor V8 y libuv (in glosario/architecture docs) para entender recolección de basura y sistema de hilos.

## Tiempo de Estudio Estimado
- *Event Loop y asincronía en Node:* 2 horas (leer docs oficiales, hacer ejercicios).  
- *Streams y Buffer:* 1.5 horas (ejemplos de copy, network).  
- *Clusters y multithreading:* 1 hora (experimentos con `cluster`).  
- *Práctica de I/O:* 1 hora (comparativa sync vs async, uso de streams).  

```markdown
# Bloque 3: Express.js y Desarrollo de APIs

## Resumen Ejecutivo
Express.js es el framework web más popular para Node.js. Es minimalista y flexible, diseñado para construir servidores HTTP y APIs REST. En este bloque se cubrirán conceptos de routing (rutas), middleware, manejo de solicitudes/respuestas, y buenas prácticas al estructurar una app Express. Se abordarán temas como autenticación, manejo de errores y escalabilidad de aplicaciones Express. Dominar Express es clave para desarrollar servicios backend eficientes y escalables.

## Material Teórico Detallado
- **¿Qué es Express?**  
  Framework web minimalista para Node.js que provee un conjunto robusto de características para aplicaciones web y móviles. Proporciona capas de *routing* y *middleware*, manteniendo la simplicidad de Node.js nativo (HTTP).  
- **Rutas HTTP:**  
  Se definen con `app.get`, `app.post`, etc. Cada ruta especifica un *handler* que recibe objetos `req` (request) y `res` (response). Ejemplo:  
  ```js
  app.get('/usuario/:id', (req, res) => {
    const id = req.params.id;
    // Buscar usuario...
    res.json({id, name: 'Ana'});
  });
  ```
- **Middleware:**  
  Funciones que procesan `req`/`res` antes de llegar a la ruta final. Útiles para autenticación, logging, parsing de JSON (`express.json()`), manejo de CORS, etc. Se aplican globalmente (`app.use()`) o por ruta. Ejemplo común:  
  ```js
  app.use(express.json()); // parsea JSON en el body
  app.use((req, res, next) => {
    console.log(`${req.method} ${req.url}`);
    next();
  });
  ```
- **Organización de rutas:**  
  En apps grandes, se suelen separar en *routers*. Ejemplo:  
  ```js
  const usuariosRouter = require('./routes/usuarios');
  app.use('/usuarios', usuariosRouter);
  ```
  Donde en `routes/usuarios.js` se define un `express.Router()` con rutas relacionadas. Esto facilita mantenimiento y escalabilidad.  
- **Manejo de errores:**  
  Express permite definir middleware de error (cuatro parámetros). Ejemplo:  
  ```js
  app.use((err, req, res, next) => {
    console.error(err.stack);
    res.status(500).json({ error: 'Algo falló' });
  });
  ```
- **Integración con bases de datos y ORM:**  
  En ruta típica se conecta a BD (p.ej. MongoDB o PostgreSQL). A menudo se usan ORM/ODM (Sequelize, TypeORM) para mapear datos. No es específico de Express, pero sí típico ver `async/await` en handlers para esperar consultas.
- **Seguridad básica en Express:**  
  Uso de `helmet` (cabezeras seguras), `cors` (control de orígenes), gestión de cookies y sesiones (p.ej. `express-session` con Redis Store). Validar entradas (evitar inyección), establecer HTTPS en producción.  
- **Escalabilidad y rendimiento:**  
  Express es liviano, pero para alto tráfico se suele combinar con *clusters* de Node (ver Bloque 2) y balanceadores. Uso de proxy inverso (NGINX) frecuente. El middleware se debe ordenar adecuadamente (por ejemplo, parse JSON antes de rutas).
- **Otros frameworks comparados:**  
  Nest.js (opinionado, MVC, basado en Express); Koa (middleware más minimal, basado en promesas). Conocerlos es útil, pero Express sigue siendo la base más requerida.

## Ejemplos Prácticos (Express)
```js
// Ejemplo: Servidor básico con rutas y middleware
const express = require('express');
const app = express();
const port = 3000;

// Middleware global: parsea JSON
app.use(express.json());

// Rutas
app.get('/', (req, res) => {
  res.send('¡Hola mundo Express!');
});

app.post('/echo', (req, res) => {
  // Devuelve el body recibido
  res.json({ recibido: req.body });
});

// Router para usuarios
const usuarios = express.Router();
usuarios.get('/', async (req, res) => {
  // Simular DB
  const data = [{id:1,name:'Ana'}, {id:2,name:'Luis'}];
  res.json(data);
});
usuarios.get('/:id', (req, res) => {
  const id = Number(req.params.id);
  if (id === 1) res.json({id:1,name:'Ana'});
  else res.status(404).json({error:'No encontrado'});
});
app.use('/usuarios', usuarios);

// Manejo de errores
app.use((err, req, res, next) => {
  console.error(err);
  res.status(500).json({error: 'Error interno'});
});

app.listen(port, () => {
  console.log(`Servidor escuchando en http://localhost:${port}`);
});
```
Este código ilustra rutas GET/POST, middleware de parsing y de logging, un *router* modular, y middleware de error.

## Preguntas Comunes de Entrevista
- **¿Cómo define rutas y middleware en Express?**  
  Rutas con `app.METHOD(path, handler)`. Middleware con `app.use(func)`. Los routers (`express.Router()`) permiten agrupar rutas por funcionalidad.  
- **¿Qué es un middleware de error en Express?**  
  Función con firma `(err, req, res, next)`. Se ejecuta al llamar `next(err)`. Sirve para centralizar el manejo de excepciones y enviar respuesta amigable.  
- **¿Cómo manejarías autenticación en Express?**  
  Ejemplos: JWT con middleware (`express-jwt` o personalizado), sesiones con `express-session`, o Passport.js para estrategias (local, OAuth). El middleware autentica antes de las rutas protegidas.  
- **¿Por qué Express es “unopinionated” (sin opinión)?**  
  Porque sólo provee lo básico (rutas y middleware) y deja al desarrollador decidir estructura, ORM, capa de servicio, etc. Esto da flexibilidad pero exige buenas prácticas de arquitectura.  
- **¿Qué problemas comunes hay al usar Express en producción?**  
  Falta de límites de carga, no usar HTTPS, no manejar tiempo de respuesta largo (p. ej. olvidarse de `timeout`), no sanitizar entradas (riesgo de inyección). Se recomienda usar reverse proxy, rate limiting y validadores (Joi, express-validator).

## Ejercicios Prácticos
1. **API REST básica:** Crea una API que maneje un recurso `libros` con endpoints `GET /libros`, `POST /libros`, `GET /libros/:id`, `PUT /libros/:id`, `DELETE /libros/:id`. Simula una base de datos en memoria (array). *Solución:* Crear un array global y actualizarlo en cada ruta, cuidando respuestas 404.  
2. **Middleware personalizado:** Implementa un middleware que registre el tiempo de respuesta de cada petición. Debe imprimir la ruta y tiempo en ms al terminar. *Pista:* usar `res.on('finish', ...)`.  
   ```js
   app.use((req, res, next) => {
     const inicio = Date.now();
     res.on('finish', () => {
       const delta = Date.now() - inicio;
       console.log(`${req.method} ${req.originalUrl} - ${delta}ms`);
     });
     next();
   });
   ```
3. **Validación de datos:** Instala `express-validator` y crea un endpoint `POST /login` que valide que el body tiene un email y password no vacío. Responde con error 400 si faltan. *Pista:* ver `body('email').isEmail()`.  

## Fuentes Clave y Lecturas
- *Express.js Official Docs:* guía de inicio y API (rutas, middleware, error handling).  
- *MDN Express Tutorial:* introducción a Node/Express (en inglés).  
- *Blogs en español:* tutoriales como [Fazt](https://faztweb.com) o [Platzi](https://platzi.com) sobre Express.  
- *Seminal:* *REST API Design*, principios de diseño (ciertas guías REST pueden ser útiles).

## Tiempo de Estudio Estimado
- *Core de Express (rutas/middleware):* 2 horas (doc oficial y ejemplos).  
- *Seguridad en Express:* 1 hora (aprender `helmet`, `cors`, sanitización).  
- *Estructura de proyectos:* 1 hora (ejemplo de organizador de routers, servicios).  
- *Ejercicios prácticos:* 2 horas (implementación de API y middlewares).  

```markdown
# Bloque 4: PostgreSQL y Bases de Datos Relacionales

## Resumen Ejecutivo
En backend, el manejo de datos es crítico. Para un puesto senior se espera dominio de bases de datos SQL como PostgreSQL. Este bloque cubre conceptos de bases de datos relacionales (modelo entidad-relación, normalización), SQL básico (SELECT, JOIN, transacciones) y transacciones ACID (Atomicidad, Consistencia, Aislamiento, Durabilidad). También se incluye la conexión desde Node.js (p.ej. usando `pg` o un ORM) y técnicas de optimización (índices, queries preparadas). Entender ACID es esencial para garantizar integridad de datos en operaciones concurrentes.

## Material Teórico Detallado
- **Bases de Datos Relacionales:** Repositorios estructurados de datos (tablas, filas/columnas) que siguen el modelo relacional. Soportan SQL (Structured Query Language) para consultas. PostgreSQL es un RDBMS robusto, open source.  
- **SQL Básico:**  
  - `SELECT`: Extraer datos. Ej: `SELECT nombre, edad FROM usuarios WHERE activo = true;`.  
  - `INSERT`, `UPDATE`, `DELETE`: modificar datos.  
  - `JOIN`: combinar tablas. Ej: `SELECT p.nombre, c.nombre FROM productos p JOIN categorias c ON p.cat_id = c.id;`.  
  - *Subconsultas y agregaciones:* Uso de `GROUP BY`, `HAVING`, funciones agregadas (`COUNT`, `SUM`, etc.).  
- **Índices:** Estructuras que aceleran las búsquedas. PostgreSQL usa B-tree por defecto. Crear índices en columnas buscadas frecuentemente mejora desempeño (`CREATE INDEX idx_nombre ON usuarios(nombre);`).  
- **Transacciones y ACID:**  
  Una **transacción** agrupa operaciones en un bloque “todo o nada”. Las propiedades ACID aseguran integridad. Por ejemplo, en una transferencia bancaria, se debe realizar el débito y el abono juntos. Las cuatro garantías: atomicidad (aplicar todas o ninguna), consistencia (pasar de estado válido a válido), aislamiento (transacciones concurrentes no interfieren) y durabilidad (una vez `COMMIT`, cambios persistentes).  
- **Control de concurrencia:** PostgreSQL ofrece diferentes niveles de aislamiento (READ COMMITTED por defecto). Familiarizarse con *bloqueos* y *deadlocks*, y saber cuándo usar *SELECT FOR UPDATE* para bloquear filas.  
- **ORMs y Conexión en Node:** 
  - Conector nativo (`pg`): permite consultas SQL directas. Ej: `const res = await client.query('SELECT * FROM usuarios');`.  
  - ORMs (Sequelize, TypeORM): abstracción orientada a objetos. Útiles en proyectos grandes pero es bueno conocer el SQL subyacente.  
  - Pool de conexiones: crear un *pool* para reutilizar conexiones y evitar sobrecargar la BD. Ejemplo con `pg.Pool`.  
- **Normalización:** Dividir datos en tablas separadas para evitar redundancia (1NF, 2NF, 3NF). Entender claves primarias/foráneas. Esto optimiza integridad y consultas.  
- **Backups y migraciones:** conocimiento básico de herramientas (`pg_dump`, migraciones con *migrations* en ORMs).  
- **SQL No Relacional vs SQL:** Reconocer cuándo usar NoSQL (Mongo, Redis) vs SQL. Si se necesita integridad ACID es mejor SQL, si se prioriza velocidad simple puede usar NoSQL.

## Ejemplos Prácticos (Node.js & SQL)
```js
// Ejemplo: Conexión a PostgreSQL con node-postgres (pg)
const { Client } = require('pg');
async function ejemploDB() {
  const client = new Client({ connectionString: 'postgres://user:pass@localhost:5432/miapp' });
  await client.connect();
  try {
    await client.query('BEGIN');
    const res1 = await client.query('SELECT saldo FROM cuentas WHERE id = $1 FOR UPDATE', [1]);
    const res2 = await client.query('SELECT saldo FROM cuentas WHERE id = $1 FOR UPDATE', [2]);
    // Simular transferencia
    const monto = 100;
    await client.query('UPDATE cuentas SET saldo = saldo - $1 WHERE id = $2', [monto, 1]);
    await client.query('UPDATE cuentas SET saldo = saldo + $1 WHERE id = $2', [monto, 2]);
    await client.query('COMMIT');
    console.log('Transferencia completada');
  } catch(e) {
    await client.query('ROLLBACK');
    console.error('Error en transacción:', e);
  } finally {
    await client.end();
  }
}
ejemploDB();
```
Este código muestra una transacción ACID en Node.js: se bloquean las filas con `FOR UPDATE`, se actualizan saldos y se confirma (`COMMIT`). Ante error se deshacen (`ROLLBACK`).

```js
// Ejemplo: Consultas preparadas y pool
const { Pool } = require('pg');
const pool = new Pool({ /* config */ });
async function obtenerUsuariosActivos() {
  const { rows } = await pool.query('SELECT * FROM usuarios WHERE activo = $1', [true]);
  return rows;
}
```
Uso de pool para optimizar conexiones, y consultas parametrizadas para evitar SQL injection.

## Preguntas Comunes de Entrevista
- **¿Qué son las propiedades ACID?**  
  Acrónimo de Atomicidad, Consistencia, Aislamiento, Durabilidad. Garantizan transacciones seguras (p.ej. en pagos o inventarios críticos).
- **¿Cómo manejarías un fallo a mitad de una operación en la base de datos?**  
  Usar transacciones: ante cualquier error ejecutar `ROLLBACK` para regresar al estado previo. Así se evita estado parcial corrupto.
- **¿Cuándo usarías índices en SQL?**  
  Para acelerar consultas frecuentes en columnas específicas (WHERE, JOIN). Ejemplo: índice único en campo `email` garantiza búsquedas rápidas y unicidad. Atentos a *downsides*: índices ocupan espacio y enlentecen *INSERT*.
- **¿Qué es normalización (3NF)?**  
  Es separar datos en tablas independientes para eliminar redundancia (p.ej. almacenar dirección en tabla aparte). Se aplica hasta la 3ª forma normal: cada dato no clave depende solo de la clave primaria. Mejora integridad.
- **¿Cuál es la diferencia entre DELETE y TRUNCATE?**  
  `DELETE` remueve filas (puede usar WHERE, es transaccional). `TRUNCATE` borra todas las filas más rápido, pero no activa triggers y se comporta diferente (puede hacer COMMIT implícito en algunos DB).
- **¿SQL o NoSQL?**  
  SQL (PostgreSQL) aporta integridad ACID y es ideal para datos estructurados. NoSQL (p.ej. Redis, Mongo) es más flexible y escala horizontal, útil para caching o datos semiestructurados.

## Ejercicios Prácticos
1. **Consulta JOIN:** Dadas dos tablas, `clientes(id,nombre)` y `pedidos(id, cliente_id, total)`, escribe una consulta que muestre nombre de cliente y suma de sus pedidos totales. *Solución:*  
   ```sql
   SELECT c.nombre, SUM(p.total) AS total_pedidos
   FROM clientes c
   JOIN pedidos p ON c.id = p.cliente_id
   GROUP BY c.nombre;
   ```
2. **Transacción bancaria:** Simula en SQL (Node o psql) una transferencia: restar monto a una cuenta y sumar a otra dentro de una transacción. Si se trata de transferir de una misma cuenta, realiza rollback. *Pista:* Ver ejemplo de código arriba con `BEGIN ... COMMIT`.
3. **Índices y rendimiento:** Crea una tabla grande (p.ej. 1M filas), mide el tiempo de una consulta sin índice y luego añade índice en la columna usada en `WHERE`. *(Manual):* se evidencia mejora drástica tras índice.

## Fuentes Clave y Lecturas
- *PostgreSQL Official Tutorial:* Introducción a SQL en PostgreSQL (site oficial).  
- *Guía de Transacciones y ACID:* explicación en español de las propiedades ACID.  
- *Documentación de pg (node-postgres):* ejemplos de uso de *Pool* y transacciones.  
- *Libro "PostgreSQL: Up and Running"* u otro recurso avanzado (incluyendo hints de performance).  

## Tiempo de Estudio Estimado
- *SQL y consultas básicas:* 2 horas (SELECT, JOIN, GROUP BY).  
- *Índices y optimización:* 1 hora (experimentar con index).  
- *Transacciones ACID:* 1 hora (leer teoría y practicar BEGIN/COMMIT/ROLLBACK).  
- *Integración con Node:* 1 hora (configurar pg en Node, probar consultas).  
- *Ejercicios prácticos:* 2 horas.  

```markdown
# Bloque 5: Redis y Caché en Memoria

## Resumen Ejecutivo
Redis es un almacén de datos en memoria (*in-memory data store*) de código abierto, optimizado para operaciones de lectura/escritura veloces. Se utiliza comúnmente como sistema de caché para reducir la carga de bases de datos y acelerar aplicaciones. Este bloque aborda los conceptos de caché (almacenar temporalmente datos frecuentemente accedidos), estructuras de datos de Redis (strings, hashes, listas, sets, sorted sets, pubs/subs) y patrones de uso (caché al margen, write-back, pub/sub). También se muestra cómo integrar Redis con Node.js usando `node-redis`. Con Redis se consigue baja latencia en escenarios como caching de consultas SQL frecuentes o sesiones de usuario.

## Material Teórico Detallado
- **Caché y Memoria:** El *cache* consiste en almacenar datos de acceso frecuente en memoria rápida para reducir latencia. Por ejemplo, almacenar resultados de una consulta de base de datos. Redis, por residir en RAM, es ideal para ello.  
- **¿Qué es Redis?**  
  Es un *almacén de estructuras de datos en memoria*. Soporta tipos como cadenas, hashes (mapas key-value), listas, conjuntos (`Set`), conjuntos ordenados (`Sorted Set`), geoespacial y Pub/Sub (publish/subscribe). Ejemplos de comandos: `SET key valor`, `GET key`, `HSET hash campo valor`, `LPUSH lista valor`, `ZRANGE sortedSet 0 -1 WITHSCORES`.  
- **Estrategias de Caching:**  
  - *Cache Aside (al margen)*: La aplicación primero consulta Redis; si no existe la clave, lee de la DB, guarda en cache y retorna. Válida cuando “fallo de cache” (miss) es tolerable.  
  - *Write-Through y Write-Behind:* En *write-through*, los cambios primero van a cache (y sincronizan a DB). En *write-behind* (write-back), se escribe en cache y se persiste después en DB, mejora escritura pero complejo de manejar.  
  - *Invalidate/Expiration:* Configurar TTL (time-to-live) en claves para invalidar datos obsoletos. Redis permite expiraciones automáticas (`EXPIRE`).  
- **Publicación/Suscripción:** Redis tiene un sistema pub/sub para mensajería. Un cliente se suscribe a un canal (`SUBSCRIBE canal`), otro publica (`PUBLISH canal mensaje`). Útil para notificaciones en aplicaciones distribuidas.  
- **Respaldo y Persistencia:** Aunque Redis es en memoria, soporta opcionalmente *RDB snapshots* y *AOF logs* para persistencia. Importante configurar según caso: en caché simple puede no necesitar persistir (datos reconstruibles).  
- **Escalabilidad:** Redis puede *sharding* de datos (clustered Redis) y *replicación* maestro-esclavo. En producción, a menudo se usa Redis Enterprise u otra nube.  
- **Casos de Uso Común:**  
  - Caché de consultas pesadas (ej. respuestas JSON de API).  
  - Almacenamiento de sesiones (p.ej. en apps Express con `connect-redis`).  
  - Contadores y ranking (Sorted Sets en sistemas de gamificación).  
  - Colas simples (listas de Redis) o sistemas de pub/sub para eventos.

## Ejemplos Prácticos (Node.js con Redis)
```js
// Ejemplo: Conexión a Redis y operaciones básicas con node-redis
const redis = require('redis');
async function ejemploRedis() {
  const client = redis.createClient({ url: 'redis://localhost:6379' });
  client.on('error', err => console.error('Redis Error', err));
  await client.connect();

  // SET y GET (tipo string)
  await client.set('clave', 'valor');
  const valor = await client.get('clave');
  console.log('Valor:', valor);

  // Hashes
  await client.hSet('user:100', { nombre: 'Maria', edad: '30' });
  const nombre = await client.hGet('user:100', 'nombre');
  console.log('Nombre de user:100 =', nombre);

  // Listas
  await client.rPush('tareas', 'tarea1');
  await client.rPush('tareas', 'tarea2');
  const tareas = await client.lRange('tareas', 0, -1);
  console.log('Tareas:', tareas);

  // Expiración
  await client.set('session:abc', 'data', { EX: 3600 }); // expira en 1h

  // Pub/Sub (en diferentes conexiones):
  //   const sub = client.duplicate(); await sub.connect();
  //   sub.subscribe('canalNoticias', msg => console.log('Noticias:', msg));
  //   client.publish('canalNoticias', '¡Hola suscriptores!');
  
  await client.quit();
}
ejemploRedis();
```
Este script muestra operaciones clave en Redis: cadenas, hashes, listas y establecimiento de TTL. También indica cómo usar pub/sub (comentado) con `subscribe` y `publish`.

## Preguntas Comunes de Entrevista
- **¿Por qué usar Redis en lugar de una base de datos SQL para caché?**  
  Redis almacena datos en memoria y opera en milisegundos, ideal para lecturas rápidas. Las bases SQL son más lentas al disco. Redis además ofrece estructuras versátiles (listas, hashes) y expiración integrada.
- **¿Qué tipo de datos soporta Redis?**  
  Strings, Hashes (mapas), Lists, Sets, Sorted Sets (zset), bitmaps, geoespaciales, HyperLogLog, Streams. Ej: listas para colas, sorted sets para leaderboards.
- **Explica el patrón Cache Aside.**  
  La aplicación consulta el cache primero. Si el dato no existe (cache miss), lee de la DB, actualiza el cache, y retorna el valor. Así, el cache “se va poblando” con datos solicitados.
- **¿Cómo se gestionan las caídas en Redis?**  
  Usar replicación y persistencia. Por ejemplo, habilitar *replica* (sentinel o cluster) para alta disponibilidad. Hacer snapshots periódicas o AOF para no perder datos importantes. En caché pura a veces se puede simplemente limpiar y reconstruir (aceptando *cache miss*).
- **¿Redis es transaccional?**  
  No en el sentido ACID completo, pero ofrece *MULTI/EXEC* para agrupar comandos en bloque atómico (ejecuta en serie). Esto asegura que un lote de comandos se ejecute sin interrupción entre ellos.
- **¿Qué diferencia hay entre `SET key value` y `HSET`?**  
  `SET` almacena un string (único campo). `HSET` almacena un hash, es un mapa de campos->valores dentro de una clave, útil para representar objetos.

## Ejercicios Prácticos
1. **Cache simple:** Implementa en Node un caché con Redis: consulta una API externa (p.ej. JSON placeholder) y almacena la respuesta en Redis con TTL de 60 seg. Si en una petición posterior existe en cache, retorna desde allí. Esto simula cache aside. *Pista:* usa `client.get`; si hay valor, parsea JSON; si no, haz `fetch` y `client.set` con EX.
2. **Pub/Sub Demo:** Crea dos scripts Node: uno se suscribe a un canal (`canal:chat`) y otro publica mensajes en ese canal cada 2 segundos. Observa cómo llegan los mensajes en el subscriber. *Pista:* usa `client.subscribe` y `client.publish`.
3. **Lista de trabajo:** Usa Redis para simular una cola: clientes encolar tareas con `LPUSH queue dato`, y un *worker* que haga `BRPOP queue` y procese. Implementar en Node un simple *producer-consumer*.

## Fuentes Clave y Lecturas
- *Redis Documentation (ES):* sección de caché en el sitio oficial.  
- *Tutoriales en español:* blog de Redis en Apidog explicando concepto.  
- *node-redis Docs:* ejemplos de uso de Redis en Node.js.  
- *Redis Patterns:* guía de patrones de caché (Patrones en RedisLabs, aunque en inglés).

## Tiempo de Estudio Estimado
- *Conceptos de Caché:* 1 hora (leer teoría y patrones).  
- *Redis básico:* 1 hora (comandos básicos: SET/GET, hashes).  
- *Estructuras avanzadas:* 1 hora (listas, sorted sets, pub/sub).  
- *Integración con Node:* 1 hora (práctica con node-redis).  
- *Ejercicios prácticos:* 2 horas.  

```markdown
# Bloque 6: Arquitectura de Microservicios

## Resumen Ejecutivo
La arquitectura de microservicios divide una aplicación en servicios pequeños e independientes que se comunican entre sí. Node.js, con su modelo ligero y orientado a eventos, encaja bien en esta arquitectura. Este bloque cubre los principios de diseño (servicios autónomos, despliegue independiente), patrones comunes (API Gateway, Circuit Breaker, Eventos), y comunicación (REST, gRPC, colas de mensajes). Se discutirán ventajas (escalabilidad, despliegue ágil) y retos (complejidad operativa, testing distribuido). Incluir diagramas mermaid ayudará a visualizar flujos y componentes.

## Material Teórico Detallado
- **Definición:** Los microservicios son servicios pequeños que realizan funciones específicas. Cada servicio es desplegable y escalable por separado. Node.js se ajusta bien debido a su **bajo consumo de memoria** y rápido arranque.  
- **Ventajas clave:** Scalabilidad horizontal (fácil duplicar servicios), despliegue independiente, tecnología por servicio, tolerancia a fallos (un fallo afecta sólo a un servicio), ciclo de desarrollo más rápido por equipo. Según [HDWebsoft], Node.js junto con microservicios ofrece *“rendimiento orientado a eventos… arquitectura modular e independientemente desplegable”*.
- **Patrones de diseño:**  
  - *API Gateway:* Punto único de entrada que enruta peticiones al servicio apropiado y puede manejar autenticación/compressión.  
  - *Circuit Breaker:* Patrón de resiliencia para evitar cascadas de fallos; corta llamadas a un servicio con errores repetidos y permite recuperación. Herramientas: *Hystrix*, *Openssl-circuit-breaker*.  
  - *Service Discovery:* Registro de servicios (p.ej. Consul, etcd) para encontrar dinámicamente servicios en arquitectura cambiante.  
  - *Event-driven:* Uso de mensajería asíncrona (RabbitMQ, Kafka) para desacoplar servicios. Se generan eventos en vez de invocaciones directas.
  - *CQRS + Event Sourcing:* Separación de comandos/consultas y almacenamiento de eventos (avanzado).
- **Comunicación entre servicios:**  
  - *REST/HTTP:* Protocolo común para APIs. Node.js con Express facilita exponer endpoints.  
  - *gRPC:* RPC eficiente usando Protobuf, soportado en Node. Útil para baja latencia entre servicios.  
  - *Mensajería:* Brokers (RabbitMQ, Kafka, o Redis pub/sub) para comunicación asíncrona. Node.js es adecuado para suscribirse/publicar eventos debido a su ciclo de eventos.
- **Desafíos:**  
  - *Operacional:* Más servicios = más despliegues, logs, monitoreo. Se requieren prácticas DevOps robustas (CI/CD, containerización).  
  - *Pruebas:* Testear microservicios distribuidos es complejo (contratos API, entornos simulados). Se aplican *contract tests* (Pact) y testing de integración.  
  - *Consistencia:* Transacciones distribuidas son difíciles (se usan sagas o compensaciones en vez de ACID global).
- **Node.js específico:**  
  - Varios servicios pueden compartir tecnologías (JS en frontend/backend).  
  - Node habilita *full-stack JavaScript*, compartiendo validadores o modelos (TypeScript).  
  - Ventajas de Node citadas en [38†L563-L572]: non-blocking I/O, motor V8 rápido, arranque ligero, ecosistema npm, etc.

## Ejemplos Prácticos y Diagramas
```mermaid
flowchart LR
  A[Cliente] -- Request --> B[API Gateway]
  B --> C{Usuarios Service}
  B --> D{Pagos Service}
  C --> E[(DB Usuarios)]
  D --> F[(DB Pagos)]
  C -->|Eventos| G[Cola de RabbitMQ]
  D -->|Eventos| G
  G --> H[Notificaciones Service]
```
*Diagrama:* Cliente envía petición al API Gateway, que enruta a servicios de Usuarios o Pagos. Los servicios usan sus propias bases de datos. A su vez, publican eventos en cola para el servicio de Notificaciones.

```js
// Ejemplo esquemático: envío de evento tras crear recurso
// Servicio de Usuarios (Node.js)
const express = require('express'), kafka = require('kafkajs');
const app = express();
const { Kafka } = kafka;
const producer = new Kafka({ brokers: ['kafka:9092'] }).producer();

app.post('/usuarios', async (req, res) => {
  const user = req.body; // validar...
  // Guardar usuario en DB (simulado)
  const newUser = { id: 123, ...user };
  // Publicar evento
  await producer.connect();
  await producer.send({
    topic: 'usuariosCreado',
    messages: [{ key: 'user', value: JSON.stringify(newUser) }]
  });
  await producer.disconnect();
  res.status(201).json(newUser);
});
app.listen(4000);
```
En este snippet, al crear un usuario se publica un evento en Kafka (`usuariosCreado`). Otros servicios pueden suscribirse a este evento para acciones como envío de email de bienvenida.

## Preguntas Comunes de Entrevista
- **¿Qué es un microservicio?**  
  Servicio autónomo que realiza una función específica. Los servicios se comunican por APIs/colas. Los microservicios no comparten código o base de datos por defecto.  
- **Ventajas vs Monolito:**  
  *Ventajas:* despliegue independiente, escalabilidad por componente, aislamiento de fallos. *Desventajas:* complejidad de operaciones, dificultad en tests integrados, latencia inter-servicio.  
- **¿Cómo manejarías transacciones distribuidas?**  
  Usualmente se evitan y se usan sagas. Un ejemplo es la **SAGA**: divide la transacción global en pasos locales con posibles compensaciones. P.ej., reserva en un servicio y en caso de fallo ejecutar lógica inversa.  
- **¿Qué es un API Gateway?**  
  Punto de entrada unificado. Se encarga de enrutamiento, autenticación y agregación de llamadas. Oculta la complejidad de múltiples servicios al cliente.  
- **¿Cómo escalarías microservicios con Node?**  
  *Horizontalmente*: múltiples réplicas detrás de balanceador (p.ej. K8s). Emplear *auto-scaling* según CPU o colas.  
- **Patrones de resiliencia:**  
  Circuit Breaker (p.ej. *Hystrix*, *Opossum* en Node) para evitar fallos en cascada. Bulkhead (aislamiento de recursos). Rate Limiting (evitar sobrecarga).

## Ejercicios Prácticos
1. **Diseño de Arquitectura:** Dado un sistema de pedidos online (usuarios, inventario, pagos, envíos), dibuja un esquema de microservicios con comunicación por APIs/colas. Señala dónde iría un API Gateway, colas de mensajes, bases de datos separadas, y cómo se realizaría el checkout.  
2. **Implementación simple:** Crea dos pequeños servicios Node: *A* expone `GET /data` que devuelve un JSON, *B* expone `GET /external` que llama internamente a *A* (usando `fetch` o Axios). Usa `Promise`/`async` para la llamada y maneja error si *A* no responde (por ejemplo, con try/catch). Es un mini-ejemplo de dependencia entre microservicios.  
3. **Prueba de Contrato:** Usa la librería [Pact](https://docs.pact.io) (en Node) para generar un contrato de consumidor/servicio entre dos servicios ficticios (se puede simular con un simple JSON). Esto ilustra cómo documentar APIs de microservicios.

## Fuentes Clave y Lecturas
- *HDWebsoft Blog:* "Microservicios con Node.js..." (guía en español).  
- *Microsoft Patterns & Practices:* Architectural guides (en inglés) sobre microservicios.  
- *Crockford (seminal papers):* [Design Principles of RESTful Services].  
- *Mermaid.js:* para diagramas de arquitectura (como el anterior).

## Tiempo de Estudio Estimado
- *Conceptos de microservicios:* 2 horas (artículos generales y la fuente).  
- *Patrones de diseño:* 2 horas (Circuit Breaker, API Gateway, eventos).  
- *Comunicación entre servicios:* 1.5 horas (REST vs gRPC vs mensajería).  
- *Diagramas y planificación:* 1 hora (dibujar flujos mermaid).  
- *Ejercicios prácticos:* 2 horas.

```markdown
# Bloque 7: Contenedores, Kubernetes y Nube

## Resumen Ejecutivo
Para un Senior Backend es esencial saber desplegar aplicaciones a escala. Este bloque abarca **Docker** (crear imágenes y contenedores) y **Kubernetes** (orquestación de contenedores). Se muestra cómo dockerizar una aplicación Node.js, configurar `Dockerfile` y `docker-compose`. Luego se explican los componentes básicos de K8s: *pods, deployments, services, configmaps*, con ejemplos en YAML. Finalmente, se revisa despliegue en la nube (AWS/GCP): p.ej. Amazon ECS/EKS o Google Cloud Run. Las buenas prácticas incluyen desplegar múltiples réplicas, usar variables de entorno seguras y monitoreo. Se incluirán diagramas de flujo de despliegue y ejemplos de comandos CLI.

## Material Teórico Detallado
- **Docker:** Tecnología de contenedores que empaqueta la aplicación y sus dependencias en una imagen ligera.  
  - *Dockerfile:* especifica la imagen base (ej. `node:18-alpine`), copia código y corre comandos. Ejemplo:
    ```dockerfile
    FROM node:18-alpine
    WORKDIR /app
    COPY package*.json ./
    RUN npm install --production
    COPY . .
    CMD ["node", "index.js"]
    ```
  - *Construir y ejecutar:* `docker build -t miapp .` y `docker run -p 3000:3000 miapp`. El contenedor lleva el entorno Node completo.
  - *docker-compose:* Permite definir varios servicios (app, base de datos, redis) en un archivo YAML. Facilita desarrollo local.
- **Kubernetes:** Sistema de orquestación de contenedores.  
  - *Pod:* unidad mínima, agrupa uno o varios contenedores (normalmente uno).  
  - *Deployment:* mantiene un conjunto de pods replicados. Define cuántas réplicas.  
  - *Service:* expone los pods (p.ej. tipo ClusterIP o LoadBalancer para acceso externo).  
  - *ConfigMap/Secret:* almacenar configuración/externalizar credenciales.  
  - *Ejemplo simple:* Desplegar un *deployment* de la app Node:  
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata: { name: miapp-deploy }
    spec:
      replicas: 3
      selector: { matchLabels: { app: miapp } }
      template:
        metadata: { labels: { app: miapp } }
        spec:
          containers:
          - name: miapp
            image: miapp:latest
            ports: [ { containerPort: 3000 } ]
    ```
  - *Comandos útiles:* `kubectl apply -f despliegue.yaml`, `kubectl get pods`, `kubectl logs pod-name`.
- **Nube (AWS/GCP):**  
  - *AWS:*  
    - *ECS/EKS:* ECS maneja contenedores (vía Fargate o EC2), EKS es Kubernetes administrado.  
    - *Lambda:* para Node.js sin servidor, aunque menos usado en APIs REST complejas (se explicaría brevemente).  
  - *GCP:*  
    - *Cloud Run:* despliegue serverless de contenedores Docker.  
    - *GKE:* Kubernetes gestionado similar a EKS.  
  - *Despliegue CI/CD:* integrarlo con pipelines (GitHub Actions, AWS CodeDeploy), automatización.  
- **Buenas prácticas:**  
  - Mantener imágenes pequeñas (usar base *alpine*, `.dockerignore`).  
  - Usar healthchecks (`HEALTHCHECK` en Docker o probes en K8s) para detectar caídas.  
  - No correr contenedores como root, establecer *user* en Dockerfile.  
  - Escalamiento automático (HPA en K8s) basado en métricas (CPU, cola de mensajes).
- **Monitoring y Logging:**  
  - *Logs:* Redirigir stdout/stderr, usar volúmenes o soluciones ELK/CloudWatch.  
  - *Métricas:* Prometheus + Grafana, o servicios en nube (CloudWatch, Stackdriver).  
  - *Alertas:* Configurar alertas para **Downtime** o uso elevado.

## Ejemplos Prácticos (YAML y CLI)
```yaml
# docker-compose.yml: app Node + base de datos (Postgres) + Redis
version: '3.8'
services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=postgres://user:pass@db:5432/miapp
      - REDIS_URL=redis://cache:6379
    depends_on:
      - db
      - cache
  db:
    image: postgres:15-alpine
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
    volumes:
      - db_data:/var/lib/postgresql/data
  cache:
    image: redis:latest

volumes:
  db_data:
```
Con `docker-compose up`, se crean 3 contenedores: la app Node, un Postgres y un Redis, enlazados en red interna.

```bash
# Kubernetes: crear Deployment y Service
kubectl apply -f miapp-deployment.yaml
kubectl apply -f miapp-service.yaml
# Por ejemplo, Service YAML:
# apiVersion: v1
# kind: Service
# metadata: { name: miapp-service }
# spec:
#   type: LoadBalancer
#   selector: { app: miapp }
#   ports: [ { port: 80, targetPort: 3000 } ]
```
La Service de tipo LoadBalancer en AWS/GCP expondrá la app al exterior. En minikube usaríamos NodePort.

```mermaid
gantt
    title Plan de Despliegue de App Node
    dateFormat  YYYY-MM-DD
    section Desarrollo
    Código Lista :a1, 2025-06-01, 5d
    section CI/CD
    Docker Build    :a2, 2025-06-07, 2d
    Test en Container :after a2, 2d
    section Despliegue
    Despliegue K8s :2025-06-10, 3d
    Validación en Cloud : 1d
```
*Diagrama de Gantt:* ejemplo de timeline para preparar la aplicación, construir la imagen y desplegar en Kubernetes.

## Preguntas Comunes de Entrevista
- **¿Cuál es la diferencia entre contenedor y máquina virtual?**  
  Un contenedor comparte el kernel del host y aísla procesos. Es más ligero y arranca rápido comparado con una VM completa.  
- **¿Qué es un Pod en Kubernetes?**  
  Unidad básica que ejecuta uno o más contenedores (normalmente uno) en K8s. Un Pod tiene IP propia. Los Pods son efímeros y se administran mediante Deployments.  
- **¿Cómo escalarías un servicio en Docker?**  
  Con `docker-compose scale` (v2 de compose) o mejor aún orquestadores: Docker Swarm o Kubernetes, configurando múltiples réplicas del contenedor.  
- **¿Qué es un Ingress?**  
  Recurso de K8s para reglas de enrutamiento HTTP. Permite exponer múltiples servicios detrás de una sola IP o dominio, definiendo reglas (hosts, paths).  
- **¿Por qué usar variables de entorno en Docker/K8s?**  
  Para configurar la aplicación en tiempo de despliegue (credenciales, endpoints) sin modificar la imagen. Las *Secrets* de K8s almacenan info sensible.  
- **Despliegue en AWS:**  
  AWS Fargate (serverless con ECS), ECS clásico en EC2, EKS (Kubernetes). *¿Serverless vs contenedores?* Los lambdas son buenos para funciones cortas; para apps con estado o escuchas, se prefiere contenedores.  

## Ejercicios Prácticos
1. **Dockerizar Aplicación Node:** Escribe un `Dockerfile` para una app Node (usa `node:18`). Construye la imagen y verifica que corra con `docker run` (mostrar logs).  
2. **Múltiples contenedores:** Crea un `docker-compose.yml` para tu app Node + PostgreSQL + Redis. Haz que Node se conecte usando hostnames de servicios. Levanta el stack y verifica comunicación (p.ej. crear un registro en Postgres usando la app).  
3. **Despliegue en Minikube:** Inicializa Minikube local. Aplica un Deployment (2 réplicas) y un Service NodePort. Accede a la app via `minikube service`. Establece escalado automático básico: `kubectl autoscale deployment miapp-deploy --cpu-percent=70 --min=2 --max=5`.

## Fuentes Clave y Lecturas
- *Docker Docs:* Guía de Primeros Pasos (Dockerfile, Compose).  
- *Kubernetes Documentation:* Tutorial de *gaston deploy your first app*.  
- *AWS Official:* Guías de ECS/EKS y Lambda para Node.js.  
- *Google Cloud Run Docs:* Despliegue de contenedores Node.js (en inglés).  
- *Mermaid Gantt/Tikz:* documentación de diagramas de Gantt para cronogramas.

## Tiempo de Estudio Estimado
- *Docker básico:* 1 hora (Dockerfile, comandos build/run).  
- *docker-compose:* 0.5 horas (montar varios contenedores).  
- *Kubernetes Core:* 2 horas (pods, deployments, services con YAML).  
- *Despliegue Cloud:* 1 hora (leer guías específicas AWS/GCP).  
- *Ejercicios prácticos:* 2 horas.

```markdown
# Bloque 8: Pruebas, Seguridad y Buenas Prácticas

## Resumen Ejecutivo
Un Senior Backend debe asegurar calidad y seguridad del código. Este bloque cubre *testing* (pruebas unitarias, integración y end-to-end en Node.js con Jest o Mocha), además de seguridad (proteger API, OWASP Top 10 en Node). Se verán técnicas como validación de datos (e.g. Joi, express-validator), manejo de autenticación/autorization (JWT, OAuth), cifrado de datos sensibles y mitigación de ataques comunes (inyección, XSS, CSRF). También se destacan prácticas de logging y trazabilidad. El objetivo es implementar código confiable y seguro desde la base.

## Material Teórico Detallado
- **Testing en Node.js:**  
  - *Unit tests:* Probar funciones individuales. Herramientas populares: Jest (incluye aserciones y mocks), Mocha + Chai/Sinon. Ejemplo de test con Jest:
    ```js
    // sum.js
    function sum(a, b) { return a + b; }
    module.exports = sum;

    // sum.test.js
    const sum = require('./sum');
    test('suma 1 + 2 es 3', () => {
      expect(sum(1,2)).toBe(3);
    });
    ```
  - *Integración:* Probar endpoints HTTP de Express (p.ej. con Supertest), simulando peticiones.  
  - *Cobertura:* Medir qué tanto del código fue probado. Se busca >80%. Herramientas como `jest --coverage` generan reportes.
- **Validación de datos:**  
  Antes de procesar datos de usuarios, siempre validar. Ej: Joi o `express-validator` para comprobar formato de cuerpo JSON. Previene errores y ataques de inyección.  
- **Autenticación y Autorización:**  
  - *JWT:* Token-based (ej. sign con secret, validar en cada petición).  
  - *OAuth2:* Para integrarse con terceros (p.ej. login con Google).  
  - *Session-based:* Menos común en APIs REST, pero válido para apps web tradicionales (almacenar sesiones en Redis).  
- **OWASP Top 10 (Node.js):** Principales riesgos (Inyección de código/SQ, Data exposure, autenticación rota). Ejemplos prácticos:
  - **Inyección (SQL/NoSQL):** Siempre usar consultas parametrizadas (ver Bloque 4) o escaped queries. Evitar concatenar strings en queries.  
  - **Cross-Site Scripting (XSS):** Aunque en backend es menor, cuidar de datos que podrían ir al frontend (limpiar entrada).  
  - **Cross-Site Request Forgery (CSRF):** No aplica en APIs puras si no usan cookies de sesión. Si usas cookies, implementar tokens CSRF.  
  - **Exposición de datos sensibles:** Nunca incluir contraseñas en respuestas JSON. Encriptar contraseñas (bcrypt). Usar HTTPS (certificados TLS, van implicados en cloud).
- **Cifrado y hashing:**  
  - *Hashing:* Para contraseñas (bcrypt, argon2). Nunca almacenarlas en claro.  
  - *Cifrado simétrico/ASimétrico:* Para datos sensibles o JWT (JWT se firma con HMAC o RSA).  
- **Logging y Monitoreo:**  
  - Registrar eventos relevantes (fallos de login, errores de servidor) usando *winston* o similar.  
  - No loguear datos sensibles (p.ej. contraseñas).  
  - Monitoreo de salud: endpoints de */healthz* y métricas (Prometheus).  
- **Manejo de Errores:**  
  - Errores esperados vs excepciones. Siempre manejar excepciones en *async/await*.  
  - Enviar respuestas HTTP adecuadas (p.ej. 400 Bad Request en validación fallida, 401 sin auth, 500 en error del servidor).  

## Ejemplos Prácticos (Pruebas y Seguridad)
```js
// Ejemplo: Test de API con Supertest y Jest
const request = require('supertest');
const app = require('./app'); // Express app
describe('GET /status', () => {
  it('debe devolver 200 OK', async () => {
    const res = await request(app).get('/status');
    expect(res.statusCode).toBe(200);
    expect(res.body).toEqual({ status: 'ok' });
  });
});
```
```js
// Ejemplo: Hashing de contraseñas con bcrypt
const bcrypt = require('bcrypt');
async function registrarUsuario(usuario) {
  const hash = await bcrypt.hash(usuario.password, 10);
  // Guardar `usuario.email` y `hash` en DB...
}
async function login(email, pass) {
  // Recuperar usuario de DB con email
  const isMatch = await bcrypt.compare(pass, usuarioFromDB.hash);
  if(!isMatch) throw new Error('Credenciales inválidas');
}
```

## Preguntas Comunes de Entrevista
- **¿Por qué son importantes las pruebas unitarias?**  
  Ayudan a detectar errores temprano, documentan el comportamiento y facilitan refactorizaciones. Permiten *confirmar* que cada unidad de código funciona según lo esperado.  
- **¿Qué es OWASP y por qué es relevante en Node.js?**  
  OWASP es un proyecto que lista las vulnerabilidades más críticas en aplicaciones web. En Node, sigue aplicando: p.ej. inyección (SQL/NoSQL), exponer API inseguras, etc. Usar sus recomendaciones evita brechas de seguridad.  
- **¿Cómo manejarías la autenticación en un API REST?**  
  Común: tokens JWT. Usuario hace login, recibe JWT firmado. En cada petición protegida envía el JWT (p.ej. en header `Authorization: Bearer <token>`). El servidor valida el token. JWT transporta claims (roles/usuario) seguros.  
- **¿Qué es CSRF y cómo prevenirlo?**  
  CSRF es un ataque donde un usuario autenticado ejecuta acciones involuntariamente. En APIs REST sin cookies no se aplica; si usas cookies, debes agregar tokens CSRF o sameSite cookies.  
- **¿Para qué sirve OWASP Cheat Sheet de Node.js?**  
  Es una guía con buenas prácticas específicas de Node.js (uso de helmet, validaciones, manejo de sesiones seguras, etc.). Ayuda a desarrollar apps Node seguras.  

## Ejercicios Prácticos
1. **Prueba Unitaria:** Crea un archivo de prueba para una función que calcule factorial. Verifica correctitud para varios valores. Usa Jest.  
   ```js
   // factorial.js
   function factorial(n) { return n <= 1 ? 1 : n * factorial(n-1); }
   module.exports = factorial;
   // factorial.test.js (usar Jest)...
   ```
2. **API con autenticación JWT:** En un proyecto Express, crea ruta `POST /login` que recibe usuario/clave falsos, si coinciden retorna un JWT firmado (usar `jsonwebtoken`). Luego protege otra ruta `GET /perfil` que sólo responde si el JWT es válido (middleware que verifica token).  
3. **Escáner de vulnerabilidades:** Instala `npm audit` en un proyecto. Identifica vulnerabilidades comunes (e.g. versiones inseguras de paquetes), actualiza o parchea. Repite hasta tener lista limpia.  
4. **Implementar Helmet:** Añade `app.use(require('helmet')())` en Express y verifica que las cabeceras HTTP cambian (p.ej. `X-Content-Type-Options: nosniff`).  

## Fuentes Clave y Lecturas
- *OWASP Node.js CheatSheet:* mejores prácticas de seguridad específicas (en inglés).  
- *OWASP Top 10:* describe amenazas (eng) aplicables a cualquier stack.  
- *Documentación de Jest:* testing en Node.js.  
- *Express-validator / Joi docs:* validación de datos.  
- *Artículos de seguridad en Node:* blogs (Node.js Security Handbook, etc.).

## Tiempo de Estudio Estimado
- *Pruebas unitarias e integradas:* 2 horas (aprender Jest/Supertest).  
- *Seguridad básica:* 2 horas (OWASP top 10, middleware de seguridad).  
- *Validación y autenticación:* 1 hora (JWT, bcrypt, validadores).  
- *Ejercicios prácticos:* 2 horas.  

```markdown
# Plan de Estudio y Cronograma

Este plan flexible contempla dos horizontes: 7 días intensivos o 14 días detallados, para cubrir los 8 bloques temáticos con prioridad, dificultad y horas estimadas. 

## Tabla de Temas vs Prioridad/Dificultad/Horas

| Bloque                          | Prioridad | Dificultad | Horas Totales Estimadas |
|---------------------------------|-----------|------------|-------------------------|
| 1. JavaScript Avanzado          | Alta      | Media      | 6 h                    |
| 2. Node.js Core (Event Loop)    | Alta      | Alta       | 6 h                    |
| 3. Express.js (APIs)            | Alta      | Media      | 6 h                    |
| 4. PostgreSQL (SQL, ACID)       | Alta      | Alta       | 6 h                    |
| 5. Redis & Caché                | Media     | Media      | 5 h                    |
| 6. Microservicios/Arquitectura  | Alta      | Alta       | 8 h                    |
| 7. Docker/Kubernetes/Cloud      | Media     | Alta       | 7 h                    |
| 8. Testing y Seguridad         | Alta      | Alta       | 7 h                    |

- **Prioridad:** Basada en relevancia común en entrevistas y rol *Senior Back-End*.  
- **Dificultad:** Estimada según complejidad del tema.  
- **Horas Totales:** Suma de subtemas y práctica por bloque.

## Calendario 7 Días
```mermaid
gantt
    title Plan de 7 D\u00edas
    dateFormat  YYYY-MM-DD
    section D\u00eda 1
    JS Avanzado (Bloque 1)        :a1, 2026-08-28, 6h
    Node Core (Bloque 2)          :after a1, 6h
    section D\u00eda 2
    Express.js (Bloque 3)         : 2026-08-29, 6h
    DB PostgreSQL (Bloque 4)      : same as 3, 6h
    section D\u00eda 3
    Redis (Bloque 5)              : 2026-08-30, 5h
    Microservicios (Bloque 6)     : after Redis, 4h
    section D\u00eda 4
    Microservicios (continuaci\u00f3n) : 2026-08-31, 4h
    Docker/K8s/Cloud (Bloque 7)    : same, 3h
    section D\u00eda 5
    Docker/K8s/Cloud (continuaci\u00f3n) : 2026-09-01, 4h
    Testing/Security (Bloque 8)     : same, 3h
    section D\u00eda 6
    Testing/Security (continuaci\u00f3n) : 2026-09-02, 4h
    Repaso & Ejercicios         : 2h
    section D\u00eda 7
    Simulacro Entrevista (preguntas) : 2026-09-03, 4h
    Revisi\u00f3n de puntos d\u00e9biles  : 4h
```
*(Diagrama Gantt ejemplo en formato mermaid)*

## Calendario 14 Días (flexible)
```mermaid
gantt
    title Plan de 14 D\u00edas
    dateFormat  YYYY-MM-DD
    section Sem. 1
    JS Avanzado         :a1, 2026-08-28, 3d
    Node Core           :4d
    Express.js          :3d
    section Sem. 2
    PostgreSQL          :2d
    Redis               :2d
    Microservicios      :4d
    Docker/Kubernetes   :3d
    Testing/Security    :3d
    Repaso & Mock entrev :2d
```
*(Timelapse semanal para más detalle)*

**Nota:** Este cronograma puede adaptarse según conocimientos previos. Por ejemplo, si ya dominas JavaScript, dedicar menos tiempo al Bloque 1 y más a temas avanzados. Es recomendable intercalar teoría con práctica diaria.

## Comparación por Prioridad y Horas
Por prioridad, enfocar primero bloques (1,2,3,4,6,8) que tienen *Alta relevancia*. Los bloques 5 y 7 quedan *medios*, pero no son triviales. El plan asegura cubrir los temas críticos con tiempo para práctica y revisión.

```markdown
# Archivo: Bloque1_JSAvanzado.md

Contenido detallado del Bloque 1 (JavaScript Avanzado)...

```
```markdown
# Archivo: Bloque2_NodeCore.md

Contenido detallado del Bloque 2 (Fundamentos de Node.js)...

```
```markdown
# Archivo: Bloque3_Express.md

Contenido detallado del Bloque 3 (Express.js y APIs)...

```
```markdown
# Archivo: Bloque4_PostgreSQL.md

Contenido detallado del Bloque 4 (PostgreSQL y SQL)...

```
```markdown
# Archivo: Bloque5_Redis.md

Contenido detallado del Bloque 5 (Redis y Caché)...

```
```markdown
# Archivo: Bloque6_Microservicios.md

Contenido detallado del Bloque 6 (Microservicios y Arquitectura)...

```
```markdown
# Archivo: Bloque7_DevOps.md

Contenido detallado del Bloque 7 (Docker, K8s, Nube)...

```
```markdown
# Archivo: Bloque8_SeguridadPruebas.md

Contenido detallado del Bloque 8 (Pruebas, Seguridad)...

```
```markdown
# Archivo: PlanEstudio.md

Incluye el plan de estudio a 7 y 14 días, tabla de temas por prioridad/dificultad, cronogramas (diagramas mermaid)...

```

Cada archivo contiene el resumen ejecutivo, teoría, ejemplos, preguntas de entrevista, ejercicios con soluciones, fuentes citadas y tiempo estimado según lo descrito en esta propuesta.
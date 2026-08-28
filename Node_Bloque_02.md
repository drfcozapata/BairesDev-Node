# Bloque 2: Fundamentos de Node.js

## Resumen Ejecutivo

Node.js es un entorno de ejecución de JavaScript en el servidor, basado en el motor V8 de Chrome. Su modelo clave es el **event loop** de un solo hilo, que permite I/O asíncrono y alto rendimiento. En este bloque se estudian sus componentes fundamentales: el loop de eventos, el sistema de módulos nativos, manejo de archivos y streams, concurrencia (libuv, _threads_, _clusters_) y herramientas de perfilado. Dominar estos conceptos es crucial para entender el comportamiento de una app Node en producción y evitar bloqueos de CPU.

## Material Teórico Detallado

- **Modelo de Concurrencia en Node:** Node usa un hilo principal con un _event loop_. Operaciones de I/O se delegan al kernel (mediante libuv) y se notifican cuando terminan. Esto permite atender miles de conexiones sin bloquear el hilo de JS.
- **Event Loop (Loop de eventos):** Constituido por _fases_ (timers, pending callbacks, poll, check, close callbacks). Un ejemplo simplificado está en la doc oficial. Proceso: Node ejecuta el script inicial, luego entra en un ciclo infinito manejando callbacks y timers.
- **Timers y Async Hooks:** Node proporciona _setTimeout_, _setImmediate_, _process.nextTick_ para programar tareas. `process.nextTick` se ejecuta antes de volver al loop; `setImmediate` se ejecuta en la fase _check_. Saber diferenciar evita bloqueos inesperados.
- **Módulos Nativos de Node:** Node incluye módulos internos (e.g., `fs`, `path`, `http`, `events`) que ofrecen funcionalidades de bajo nivel. Se importan con `const fs = require('fs');`. Ej.: `fs.readFileSync` vs _async_ `fs.readFile`. Dominar estos módulos es básico (Documentación oficial: Node API).
- **Streams y Buffers:** Para I/O eficiente, Node utiliza _streams_ (lectura/escritura por trozos) y _buffers_ (binarios en memoria). Ejemplo: `fs.createReadStream('file.txt').pipe(process.stdout)`. Los streams tienen métodos `.on('data')`, `.on('end')`. Permiten manejar grandes archivos sin cargar todo en memoria.
- **Cluster y Multithreading:** Aunque JS es single-threaded, Node permite crear procesos hijos (_cluster_) para aprovechar múltiples núcleos. `cluster.fork()` clona el proceso. Además, Node 10+ ofrece _Worker Threads_ para tareas CPU-intensivas (hilos reales dentro del proceso).
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
readStream.on('data', (chunk) => writeStream.write(chunk));
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
  http
    .createServer((req, res) => {
      res.writeHead(200);
      res.end(`Hola mundo desde worker ${process.pid}`);
    })
    .listen(8000);
  console.log(`Worker ${process.pid} iniciado`);
}
```

## Preguntas Comunes de Entrevista

- **¿Qué es el event loop de Node.js?**  
  Es un ciclo continuo que permite la ejecución de callbacks asíncronos. Node delega I/O al kernel y, cuando la operación termina, el callback se encola en el loop. Así, aunque JS es single-thread, puede manejar concurrencia.
- **¿Cómo evita Node bloquear el hilo principal en operaciones de I/O?**  
  Todas las operaciones de I/O (lectura/escritura de archivos, red) se realizan de forma **no bloqueante**. Bajo el capó, libuv usa hilos del sistema o mecanismos eficientes del kernel. Los datos se reciben mediante callbacks o promesas cuando estén listos.
- **¿Qué son Streams en Node? ¿Cuándo usarlos?**  
  Los streams permiten procesar datos en trozos. Existen _Readable_, _Writable_, _Duplex_ y _Transform_. Son útiles para leer/escribir archivos grandes o transferir datos (p.ej., en respuestas HTTP) sin cargar todo en memoria.
- **¿Cómo levantar múltiples instancias de una app Node?**  
  Usando el módulo `cluster` o herramientas externas (PM2, etc.). El módulo `cluster` permite forkar procesos workers que comparten el mismo puerto, equilibrando carga entre núcleos (multicore).
- **¿Qué sucede si olvido manejar un error en callback o promesa?**  
  En Node, un error no atrapado puede desacelerar o terminar el proceso. Es crítico usar `try/catch` en funciones `async` o `.catch()` en promesas, y escuchar eventos `'uncaughtException'` para evitar crashes no deseados.

## Ejercicios Prácticos

1. **Temporizadores y eventos:** Escribe un script Node que imprima "Tick" cada 1 segundo (usa `setInterval`) y "Tock" cada 2 segundos. Detenlo después de 5 “Tick”. _Solución:_
   ```js
   let ticks = 0;
   const interval = setInterval(() => {
     console.log('Tick');
     ticks++;
     if (ticks === 5) clearInterval(interval);
   }, 1000);
   setInterval(() => console.log('Tock'), 2000);
   ```
2. **File I/O síncrono vs asíncrono:** Comparar tiempos: lee 10MB de un archivo grande usando `fs.readFileSync` y luego con `fs.readFile` asíncrono. _(Manual):_ Se notará que la versión síncrona bloquea el evento, mientras que la asíncrona permite otras operaciones durante la lectura.
3. **Streams en acción:** Implementa una copia de archivo usando _pipes_:
   ```js
   const fs = require('fs');
   fs.createReadStream('source.bin')
     .pipe(fs.createWriteStream('dest.bin'))
     .on('finish', () => console.log('Copiado con pipe listo'));
   ```

## Fuentes Clave y Lecturas

- _Node.js Documentation:_ guía sobre _The Node.js Event Loop_ y API de _fs_, _stream_, _cluster_.
- _Node.js GitHub Wiki:_ varias entradas (por ejemplo sobre _cluster_).
- _Artículos de la comunidad:_ explicaciones de event loop en español (e.g. javascript.info) y blogs de Node.
- _V8 & libuv:_ documentación oficial del motor V8 y libuv (in glosario/architecture docs) para entender recolección de basura y sistema de hilos.

## Tiempo de Estudio Estimado

- _Event Loop y asincronía en Node:_ 2 horas (leer docs oficiales, hacer ejercicios).
- _Streams y Buffer:_ 1.5 horas (ejemplos de copy, network).
- _Clusters y multithreading:_ 1 hora (experimentos con `cluster`).
- _Práctica de I/O:_ 1 hora (comparativa sync vs async, uso de streams).

# Bloque 5: Redis y Caché en Memoria

## Resumen Ejecutivo

Redis es un almacén de datos en memoria (_in-memory data store_) de código abierto, optimizado para operaciones de lectura/escritura veloces. Se utiliza comúnmente como sistema de caché para reducir la carga de bases de datos y acelerar aplicaciones. Este bloque aborda los conceptos de caché (almacenar temporalmente datos frecuentemente accedidos), estructuras de datos de Redis (strings, hashes, listas, sets, sorted sets, pubs/subs) y patrones de uso (caché al margen, write-back, pub/sub). También se muestra cómo integrar Redis con Node.js usando `node-redis`. Con Redis se consigue baja latencia en escenarios como caching de consultas SQL frecuentes o sesiones de usuario.

## Material Teórico Detallado

- **Caché y Memoria:** El _cache_ consiste en almacenar datos de acceso frecuente en memoria rápida para reducir latencia. Por ejemplo, almacenar resultados de una consulta de base de datos. Redis, por residir en RAM, es ideal para ello.
- **¿Qué es Redis?**  
  Es un _almacén de estructuras de datos en memoria_. Soporta tipos como cadenas, hashes (mapas key-value), listas, conjuntos (`Set`), conjuntos ordenados (`Sorted Set`), geoespacial y Pub/Sub (publish/subscribe). Ejemplos de comandos: `SET key valor`, `GET key`, `HSET hash campo valor`, `LPUSH lista valor`, `ZRANGE sortedSet 0 -1 WITHSCORES`.
- **Estrategias de Caching:**
  - _Cache Aside (al margen)_: La aplicación primero consulta Redis; si no existe la clave, lee de la DB, guarda en cache y retorna. Válida cuando “fallo de cache” (miss) es tolerable.
  - _Write-Through y Write-Behind:_ En _write-through_, los cambios primero van a cache (y sincronizan a DB). En _write-behind_ (write-back), se escribe en cache y se persiste después en DB, mejora escritura pero complejo de manejar.
  - _Invalidate/Expiration:_ Configurar TTL (time-to-live) en claves para invalidar datos obsoletos. Redis permite expiraciones automáticas (`EXPIRE`).
- **Publicación/Suscripción:** Redis tiene un sistema pub/sub para mensajería. Un cliente se suscribe a un canal (`SUBSCRIBE canal`), otro publica (`PUBLISH canal mensaje`). Útil para notificaciones en aplicaciones distribuidas.
- **Respaldo y Persistencia:** Aunque Redis es en memoria, soporta opcionalmente _RDB snapshots_ y _AOF logs_ para persistencia. Importante configurar según caso: en caché simple puede no necesitar persistir (datos reconstruibles).
- **Escalabilidad:** Redis puede _sharding_ de datos (clustered Redis) y _replicación_ maestro-esclavo. En producción, a menudo se usa Redis Enterprise u otra nube.
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
  client.on('error', (err) => console.error('Redis Error', err));
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
  Usar replicación y persistencia. Por ejemplo, habilitar _replica_ (sentinel o cluster) para alta disponibilidad. Hacer snapshots periódicas o AOF para no perder datos importantes. En caché pura a veces se puede simplemente limpiar y reconstruir (aceptando _cache miss_).
- **¿Redis es transaccional?**  
  No en el sentido ACID completo, pero ofrece _MULTI/EXEC_ para agrupar comandos en bloque atómico (ejecuta en serie). Esto asegura que un lote de comandos se ejecute sin interrupción entre ellos.
- **¿Qué diferencia hay entre `SET key value` y `HSET`?**  
  `SET` almacena un string (único campo). `HSET` almacena un hash, es un mapa de campos->valores dentro de una clave, útil para representar objetos.

## Ejercicios Prácticos

1. **Cache simple:** Implementa en Node un caché con Redis: consulta una API externa (p.ej. JSON placeholder) y almacena la respuesta en Redis con TTL de 60 seg. Si en una petición posterior existe en cache, retorna desde allí. Esto simula cache aside. _Pista:_ usa `client.get`; si hay valor, parsea JSON; si no, haz `fetch` y `client.set` con EX.
2. **Pub/Sub Demo:** Crea dos scripts Node: uno se suscribe a un canal (`canal:chat`) y otro publica mensajes en ese canal cada 2 segundos. Observa cómo llegan los mensajes en el subscriber. _Pista:_ usa `client.subscribe` y `client.publish`.
3. **Lista de trabajo:** Usa Redis para simular una cola: clientes encolar tareas con `LPUSH queue dato`, y un _worker_ que haga `BRPOP queue` y procese. Implementar en Node un simple _producer-consumer_.

## Fuentes Clave y Lecturas

- _Redis Documentation (ES):_ sección de caché en el sitio oficial.
- _Tutoriales en español:_ blog de Redis en Apidog explicando concepto.
- _node-redis Docs:_ ejemplos de uso de Redis en Node.js.
- _Redis Patterns:_ guía de patrones de caché (Patrones en RedisLabs, aunque en inglés).

## Tiempo de Estudio Estimado

- _Conceptos de Caché:_ 1 hora (leer teoría y patrones).
- _Redis básico:_ 1 hora (comandos básicos: SET/GET, hashes).
- _Estructuras avanzadas:_ 1 hora (listas, sorted sets, pub/sub).
- _Integración con Node:_ 1 hora (práctica con node-redis).
- _Ejercicios prácticos:_ 2 horas.

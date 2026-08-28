# Bloque 4: PostgreSQL y Bases de Datos Relacionales

## Resumen Ejecutivo

En backend, el manejo de datos es crítico. Para un puesto senior se espera dominio de bases de datos SQL como PostgreSQL. Este bloque cubre conceptos de bases de datos relacionales (modelo entidad-relación, normalización), SQL básico (SELECT, JOIN, transacciones) y transacciones ACID (Atomicidad, Consistencia, Aislamiento, Durabilidad). También se incluye la conexión desde Node.js (p.ej. usando `pg` o un ORM) y técnicas de optimización (índices, queries preparadas). Entender ACID es esencial para garantizar integridad de datos en operaciones concurrentes.

## Material Teórico Detallado

- **Bases de Datos Relacionales:** Repositorios estructurados de datos (tablas, filas/columnas) que siguen el modelo relacional. Soportan SQL (Structured Query Language) para consultas. PostgreSQL es un RDBMS robusto, open source.
- **SQL Básico:**
  - `SELECT`: Extraer datos. Ej: `SELECT nombre, edad FROM usuarios WHERE activo = true;`.
  - `INSERT`, `UPDATE`, `DELETE`: modificar datos.
  - `JOIN`: combinar tablas. Ej: `SELECT p.nombre, c.nombre FROM productos p JOIN categorias c ON p.cat_id = c.id;`.
  - _Subconsultas y agregaciones:_ Uso de `GROUP BY`, `HAVING`, funciones agregadas (`COUNT`, `SUM`, etc.).
- **Índices:** Estructuras que aceleran las búsquedas. PostgreSQL usa B-tree por defecto. Crear índices en columnas buscadas frecuentemente mejora desempeño (`CREATE INDEX idx_nombre ON usuarios(nombre);`).
- **Transacciones y ACID:**  
  Una **transacción** agrupa operaciones en un bloque “todo o nada”. Las propiedades ACID aseguran integridad. Por ejemplo, en una transferencia bancaria, se debe realizar el débito y el abono juntos. Las cuatro garantías: atomicidad (aplicar todas o ninguna), consistencia (pasar de estado válido a válido), aislamiento (transacciones concurrentes no interfieren) y durabilidad (una vez `COMMIT`, cambios persistentes).
- **Control de concurrencia:** PostgreSQL ofrece diferentes niveles de aislamiento (READ COMMITTED por defecto). Familiarizarse con _bloqueos_ y _deadlocks_, y saber cuándo usar _SELECT FOR UPDATE_ para bloquear filas.
- **ORMs y Conexión en Node:**
  - Conector nativo (`pg`): permite consultas SQL directas. Ej: `const res = await client.query('SELECT * FROM usuarios');`.
  - ORMs (Sequelize, TypeORM): abstracción orientada a objetos. Útiles en proyectos grandes pero es bueno conocer el SQL subyacente.
  - Pool de conexiones: crear un _pool_ para reutilizar conexiones y evitar sobrecargar la BD. Ejemplo con `pg.Pool`.
- **Normalización:** Dividir datos en tablas separadas para evitar redundancia (1NF, 2NF, 3NF). Entender claves primarias/foráneas. Esto optimiza integridad y consultas.
- **Backups y migraciones:** conocimiento básico de herramientas (`pg_dump`, migraciones con _migrations_ en ORMs).
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
  } catch (e) {
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
const pool = new Pool({
  /* config */
});
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
  Para acelerar consultas frecuentes en columnas específicas (WHERE, JOIN). Ejemplo: índice único en campo `email` garantiza búsquedas rápidas y unicidad. Atentos a _downsides_: índices ocupan espacio y enlentecen _INSERT_.
- **¿Qué es normalización (3NF)?**  
  Es separar datos en tablas independientes para eliminar redundancia (p.ej. almacenar dirección en tabla aparte). Se aplica hasta la 3ª forma normal: cada dato no clave depende solo de la clave primaria. Mejora integridad.
- **¿Cuál es la diferencia entre DELETE y TRUNCATE?**  
  `DELETE` remueve filas (puede usar WHERE, es transaccional). `TRUNCATE` borra todas las filas más rápido, pero no activa triggers y se comporta diferente (puede hacer COMMIT implícito en algunos DB).
- **¿SQL o NoSQL?**  
  SQL (PostgreSQL) aporta integridad ACID y es ideal para datos estructurados. NoSQL (p.ej. Redis, Mongo) es más flexible y escala horizontal, útil para caching o datos semiestructurados.

## Ejercicios Prácticos

1. **Consulta JOIN:** Dadas dos tablas, `clientes(id,nombre)` y `pedidos(id, cliente_id, total)`, escribe una consulta que muestre nombre de cliente y suma de sus pedidos totales. _Solución:_
   ```sql
   SELECT c.nombre, SUM(p.total) AS total_pedidos
   FROM clientes c
   JOIN pedidos p ON c.id = p.cliente_id
   GROUP BY c.nombre;
   ```
2. **Transacción bancaria:** Simula en SQL (Node o psql) una transferencia: restar monto a una cuenta y sumar a otra dentro de una transacción. Si se trata de transferir de una misma cuenta, realiza rollback. _Pista:_ Ver ejemplo de código arriba con `BEGIN ... COMMIT`.
3. **Índices y rendimiento:** Crea una tabla grande (p.ej. 1M filas), mide el tiempo de una consulta sin índice y luego añade índice en la columna usada en `WHERE`. _(Manual):_ se evidencia mejora drástica tras índice.

## Fuentes Clave y Lecturas

- _PostgreSQL Official Tutorial:_ Introducción a SQL en PostgreSQL (site oficial).
- _Guía de Transacciones y ACID:_ explicación en español de las propiedades ACID.
- _Documentación de pg (node-postgres):_ ejemplos de uso de _Pool_ y transacciones.
- _Libro "PostgreSQL: Up and Running"_ u otro recurso avanzado (incluyendo hints de performance).

## Tiempo de Estudio Estimado

- _SQL y consultas básicas:_ 2 horas (SELECT, JOIN, GROUP BY).
- _Índices y optimización:_ 1 hora (experimentar con index).
- _Transacciones ACID:_ 1 hora (leer teoría y practicar BEGIN/COMMIT/ROLLBACK).
- _Integración con Node:_ 1 hora (configurar pg en Node, probar consultas).
- _Ejercicios prácticos:_ 2 horas.

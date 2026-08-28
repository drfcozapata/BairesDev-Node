# Bloque 3: Express.js y Desarrollo de APIs

## Resumen Ejecutivo

Express.js es el framework web más popular para Node.js. Es minimalista y flexible, diseñado para construir servidores HTTP y APIs REST. En este bloque se cubrirán conceptos de routing (rutas), middleware, manejo de solicitudes/respuestas, y buenas prácticas al estructurar una app Express. Se abordarán temas como autenticación, manejo de errores y escalabilidad de aplicaciones Express. Dominar Express es clave para desarrollar servicios backend eficientes y escalables.

## Material Teórico Detallado

- **¿Qué es Express?**  
  Framework web minimalista para Node.js que provee un conjunto robusto de características para aplicaciones web y móviles. Proporciona capas de _routing_ y _middleware_, manteniendo la simplicidad de Node.js nativo (HTTP).
- **Rutas HTTP:**  
  Se definen con `app.get`, `app.post`, etc. Cada ruta especifica un _handler_ que recibe objetos `req` (request) y `res` (response). Ejemplo:
  ```js
  app.get('/usuario/:id', (req, res) => {
    const id = req.params.id;
    // Buscar usuario...
    res.json({ id, name: 'Ana' });
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
  En apps grandes, se suelen separar en _routers_. Ejemplo:
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
  Express es liviano, pero para alto tráfico se suele combinar con _clusters_ de Node (ver Bloque 2) y balanceadores. Uso de proxy inverso (NGINX) frecuente. El middleware se debe ordenar adecuadamente (por ejemplo, parse JSON antes de rutas).
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
  const data = [
    { id: 1, name: 'Ana' },
    { id: 2, name: 'Luis' },
  ];
  res.json(data);
});
usuarios.get('/:id', (req, res) => {
  const id = Number(req.params.id);
  if (id === 1) res.json({ id: 1, name: 'Ana' });
  else res.status(404).json({ error: 'No encontrado' });
});
app.use('/usuarios', usuarios);

// Manejo de errores
app.use((err, req, res, next) => {
  console.error(err);
  res.status(500).json({ error: 'Error interno' });
});

app.listen(port, () => {
  console.log(`Servidor escuchando en http://localhost:${port}`);
});
```

Este código ilustra rutas GET/POST, middleware de parsing y de logging, un _router_ modular, y middleware de error.

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

1. **API REST básica:** Crea una API que maneje un recurso `libros` con endpoints `GET /libros`, `POST /libros`, `GET /libros/:id`, `PUT /libros/:id`, `DELETE /libros/:id`. Simula una base de datos en memoria (array). _Solución:_ Crear un array global y actualizarlo en cada ruta, cuidando respuestas 404.
2. **Middleware personalizado:** Implementa un middleware que registre el tiempo de respuesta de cada petición. Debe imprimir la ruta y tiempo en ms al terminar. _Pista:_ usar `res.on('finish', ...)`.
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
3. **Validación de datos:** Instala `express-validator` y crea un endpoint `POST /login` que valide que el body tiene un email y password no vacío. Responde con error 400 si faltan. _Pista:_ ver `body('email').isEmail()`.

## Fuentes Clave y Lecturas

- _Express.js Official Docs:_ guía de inicio y API (rutas, middleware, error handling).
- _MDN Express Tutorial:_ introducción a Node/Express (en inglés).
- _Blogs en español:_ tutoriales como [Fazt](https://faztweb.com) o [Platzi](https://platzi.com) sobre Express.
- _Seminal:_ _REST API Design_, principios de diseño (ciertas guías REST pueden ser útiles).

## Tiempo de Estudio Estimado

- _Core de Express (rutas/middleware):_ 2 horas (doc oficial y ejemplos).
- _Seguridad en Express:_ 1 hora (aprender `helmet`, `cors`, sanitización).
- _Estructura de proyectos:_ 1 hora (ejemplo de organizador de routers, servicios).
- _Ejercicios prácticos:_ 2 horas (implementación de API y middlewares).

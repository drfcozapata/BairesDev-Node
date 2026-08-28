# Bloque 8: Pruebas, Seguridad y Buenas Prácticas

## Resumen Ejecutivo

Un Senior Backend debe asegurar calidad y seguridad del código. Este bloque cubre _testing_ (pruebas unitarias, integración y end-to-end en Node.js con Jest o Mocha), además de seguridad (proteger API, OWASP Top 10 en Node). Se verán técnicas como validación de datos (e.g. Joi, express-validator), manejo de autenticación/autorization (JWT, OAuth), cifrado de datos sensibles y mitigación de ataques comunes (inyección, XSS, CSRF). También se destacan prácticas de logging y trazabilidad. El objetivo es implementar código confiable y seguro desde la base.

## Material Teórico Detallado

- **Testing en Node.js:**
  - _Unit tests:_ Probar funciones individuales. Herramientas populares: Jest (incluye aserciones y mocks), Mocha + Chai/Sinon. Ejemplo de test con Jest:

    ```js
    // sum.js
    function sum(a, b) {
      return a + b;
    }
    module.exports = sum;

    // sum.test.js
    const sum = require('./sum');
    test('suma 1 + 2 es 3', () => {
      expect(sum(1, 2)).toBe(3);
    });
    ```

  - _Integración:_ Probar endpoints HTTP de Express (p.ej. con Supertest), simulando peticiones.
  - _Cobertura:_ Medir qué tanto del código fue probado. Se busca >80%. Herramientas como `jest --coverage` generan reportes.

- **Validación de datos:**  
  Antes de procesar datos de usuarios, siempre validar. Ej: Joi o `express-validator` para comprobar formato de cuerpo JSON. Previene errores y ataques de inyección.
- **Autenticación y Autorización:**
  - _JWT:_ Token-based (ej. sign con secret, validar en cada petición).
  - _OAuth2:_ Para integrarse con terceros (p.ej. login con Google).
  - _Session-based:_ Menos común en APIs REST, pero válido para apps web tradicionales (almacenar sesiones en Redis).
- **OWASP Top 10 (Node.js):** Principales riesgos (Inyección de código/SQ, Data exposure, autenticación rota). Ejemplos prácticos:
  - **Inyección (SQL/NoSQL):** Siempre usar consultas parametrizadas (ver Bloque 4) o escaped queries. Evitar concatenar strings en queries.
  - **Cross-Site Scripting (XSS):** Aunque en backend es menor, cuidar de datos que podrían ir al frontend (limpiar entrada).
  - **Cross-Site Request Forgery (CSRF):** No aplica en APIs puras si no usan cookies de sesión. Si usas cookies, implementar tokens CSRF.
  - **Exposición de datos sensibles:** Nunca incluir contraseñas en respuestas JSON. Encriptar contraseñas (bcrypt). Usar HTTPS (certificados TLS, van implicados en cloud).
- **Cifrado y hashing:**
  - _Hashing:_ Para contraseñas (bcrypt, argon2). Nunca almacenarlas en claro.
  - _Cifrado simétrico/ASimétrico:_ Para datos sensibles o JWT (JWT se firma con HMAC o RSA).
- **Logging y Monitoreo:**
  - Registrar eventos relevantes (fallos de login, errores de servidor) usando _winston_ o similar.
  - No loguear datos sensibles (p.ej. contraseñas).
  - Monitoreo de salud: endpoints de _/healthz_ y métricas (Prometheus).
- **Manejo de Errores:**
  - Errores esperados vs excepciones. Siempre manejar excepciones en _async/await_.
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
  if (!isMatch) throw new Error('Credenciales inválidas');
}
```

## Preguntas Comunes de Entrevista

- **¿Por qué son importantes las pruebas unitarias?**  
  Ayudan a detectar errores temprano, documentan el comportamiento y facilitan refactorizaciones. Permiten _confirmar_ que cada unidad de código funciona según lo esperado.
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
   function factorial(n) {
     return n <= 1 ? 1 : n * factorial(n - 1);
   }
   module.exports = factorial;
   // factorial.test.js (usar Jest)...
   ```
2. **API con autenticación JWT:** En un proyecto Express, crea ruta `POST /login` que recibe usuario/clave falsos, si coinciden retorna un JWT firmado (usar `jsonwebtoken`). Luego protege otra ruta `GET /perfil` que sólo responde si el JWT es válido (middleware que verifica token).
3. **Escáner de vulnerabilidades:** Instala `npm audit` en un proyecto. Identifica vulnerabilidades comunes (e.g. versiones inseguras de paquetes), actualiza o parchea. Repite hasta tener lista limpia.
4. **Implementar Helmet:** Añade `app.use(require('helmet')())` en Express y verifica que las cabeceras HTTP cambian (p.ej. `X-Content-Type-Options: nosniff`).

## Fuentes Clave y Lecturas

- _OWASP Node.js CheatSheet:_ mejores prácticas de seguridad específicas (en inglés).
- _OWASP Top 10:_ describe amenazas (eng) aplicables a cualquier stack.
- _Documentación de Jest:_ testing en Node.js.
- _Express-validator / Joi docs:_ validación de datos.
- _Artículos de seguridad en Node:_ blogs (Node.js Security Handbook, etc.).

## Tiempo de Estudio Estimado

- _Pruebas unitarias e integradas:_ 2 horas (aprender Jest/Supertest).
- _Seguridad básica:_ 2 horas (OWASP top 10, middleware de seguridad).
- _Validación y autenticación:_ 1 hora (JWT, bcrypt, validadores).
- _Ejercicios prácticos:_ 2 horas.

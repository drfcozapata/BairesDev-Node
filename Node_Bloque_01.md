# Bloque 1: JavaScript Avanzado (ES2020+)

## Resumen Ejecutivo

JavaScript es el lenguaje base de Node.js y conocer sus características modernas es esencial para un _Senior Backend Node.js_. JS es un lenguaje prototípico, dinámico y de tipado flexible. En este bloque se repasan novedades de ES2020+ (p. ej. _optional chaining_, _nullish coalescing_), sintaxis avanzada (promesas, _async/await_, arrow functions, destructuring) y conceptos clave (alcance de variables, closures). Todo ello permite escribir código backend más limpio, eficiente y fácil de mantener. Además, se introduce cómo Node.js aprovecha el **event loop** de JavaScript para manejar I/O no bloqueante.

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
- **Funciones flecha y `this`:** Las _arrow functions_ (`() => {}`) ofrecen sintaxis concisa y enlazan `this` léxico. Son útiles para callbacks:
  ```js
  const nums = [1, 2, 3];
  const cuadrados = nums.map((n) => n * n);
  ```
- **Clases y prototipos:** Aunque JS es prototípico, ES6 introdujo `class` como azúcar sintáctico. Las clases permiten herencia y métodos:
  ```js
  class Animal {
    constructor(nombre) {
      this.nombre = nombre;
    }
    hablar() {
      console.log(`${this.nombre} hace ruido.`);
    }
  }
  class Perro extends Animal {
    hablar() {
      console.log(`${this.nombre} dice ¡guau!`);
    }
  }
  ```
- **Desestructuración:** Extrae valores de arrays/objetos en variables:
  ```js
  const [a, b] = [10, 20]; // a=10, b=20
  const { id, name } = { id: 1, name: 'Ana' };
  ```
- **Parámetros rest/spread:** Permiten manejar listas de argumentos o expandir arrays/objetos:
  ```js
  function sumar(...numeros) {
    return numeros.reduce((a, b) => a + b);
  }
  const arr2 = [...arr1, 4, 5];
  ```
- **Template literals:** Cadenas con expresiones embebidas y multilínea:
  ```js
  const user = { name: 'Carlos', age: 30 };
  console.log(`Usuario: ${user.name}, edad ${user.age}`);
  ```
- **Promesas y async/await:** Modelo de asincronía moderno.
  - _Promesas_ representan resultados futuros, se encadenan con `.then()` y manejan errores con `.catch()`.
  - `async function` y `await` permiten código asíncrono con estilo sincrónico. Por ejemplo:
    ```js
    async function obtenerDatos() {
      try {
        const res = await fetch('https://api.example.com/data');
        const data = await res.json();
        return data;
      } catch (err) {
        console.error('Error:', err);
      }
    }
    ```
- **Funciones de Primera Clase y Closures:** En JS, las funciones son objetos de primera clase (pueden pasarse como argumentos, retornarse, almacenarse). Un _closure_ es cuando una función interna mantiene acceso a variables de su función contenedora:
  ```js
  function contadorPrueba(inicio) {
    let cuenta = inicio;
    return () => ++cuenta; // este inner function cierra sobre 'cuenta'
  }
  const c = contadorPrueba(10);
  console.log(c()); // 11
  ```
- **Módulos:** Node.js soporta CommonJS (`require`) y desde ES2020 permite módulos ESM (`import/export`). Con ESM, se usan `export` en archivos y `import x from 'archivo'`. Ejemplo:

  ```js
  // utils.js
  export function saludar() {
    console.log('¡Hola!');
  }

  // index.js
  import { saludar } from './utils.js';
  saludar();
  ```

- **Iteradores y Generators (avanzado opcional):** Objetos que definen _Symbol.iterator_ para iterar (p. ej. `for...of`). Los _generators_ (`function*`) crean iteradores personalizados.

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

Este fragmento muestra `async/await`, manejo de promesas (`then/catch`), funciones flecha y destructuring.

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
  JavaScript es de un solo hilo pero no bloqueante: I/O asíncrono se maneja con callbacks o promesas, y el _event loop_ coordina la ejecución cuando llegan datos. Node.js utiliza este modelo para concurrencia.
- **¿Qué es _async/await_?**  
  Sintaxis azúcar para manejar promesas: una función `async` retorna una promesa, y dentro `await` “espera” la resolución de una promesa (no detiene todo el programa, sólo la ejecución local).
- **¿Qué son los módulos en Node y cómo importas uno?**  
  Node usa módulos CommonJS (`module.exports`/`require`). A partir de ES2020 puede usar módulos ESM (`export`/`import`). Ejemplo: `const fs = require('fs');` o con ESM: `import fs from 'fs';`.

## Ejercicios Prácticos

1. **Suma asíncrona:** Escribe una función `sumarAsync(numeros)` que reciba un array y devuelva una promesa que resuelva la suma de los números tras 1 segundo. Utiliza `async/await` y `setTimeout`. _Solución:_

   ```js
   function sumarAsync(nums) {
     return new Promise((res) => {
       setTimeout(() => {
         const suma = nums.reduce((a, b) => a + b, 0);
         res(suma);
       }, 1000);
     });
   }

   (async () => {
     const total = await sumarAsync([1, 2, 3, 4]);
     console.log(total); // 10
   })();
   ```

2. **Clausuras y contenedores:** Crea una función `filtrarPor(n, lista)` que retorna otra función. La función interna debe tomar un array de números y devolver sólo aquellos mayores que `n`, usando cierre. _Solución:_
   ```js
   function filtrarPor(n) {
     return function (lista) {
       return lista.filter((x) => x > n);
     };
   }
   const mayorQue10 = filtrarPor(10);
   console.log(mayorQue10([5, 12, 3, 18])); // [12,18]
   ```
3. **Desestructuración en Node:** Dado un objeto `{a:1, b:2, c:3}`, crea variables `a` y `c` usando destructuración en una sola línea. _Solución:_
   ```js
   const obj = { a: 1, b: 2, c: 3 };
   const { a, c } = obj;
   console.log(a, c); // 1 3
   ```

## Fuentes Clave y Lecturas

- _Mozilla MDN (ES)_: guía de JavaScript (lenguaje, clases, prototipos).
- _javascript.info_: artículo sobre **Event Loop** (explicación en español).
- _Node.js Docs_: apartado _The Event Loop_ (modelo de concurrencia en Node).
- _OWL (Odin) Labs / Ejemplos:_ repaso de ES6+ (en inglés) y blogs de comunidad (v.g. Platzi, TutorialesPoint).
- _TC39/ECMAScript_: especificaciones oficiales de ECMAScript para detalles sintácticos.

## Tiempo de Estudio Estimado

- _ES6/ES2020 sintaxis avanzada:_ 2 horas (arrow, destructuring, clases, módulos).
- _Asincronía en JS:_ 2 horas (promesas, async/await, callbacks, event loop).
- _Closures y scope:_ 1 hora (concepto y ejemplos).
- _Práctica de código:_ 2 horas (resolver ejercicios y revisar soluciones).

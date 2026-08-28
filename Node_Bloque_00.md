# Plan de Estudio y Cronograma

Este plan flexible contempla dos horizontes: 7 días intensivos o 14 días detallados, para cubrir los 8 bloques temáticos con prioridad, dificultad y horas estimadas.

## Tabla de Temas vs Prioridad/Dificultad/Horas

| Bloque                         | Prioridad | Dificultad | Horas Totales Estimadas |
| ------------------------------ | --------- | ---------- | ----------------------- |
| 1. JavaScript Avanzado         | Alta      | Media      | 6 h                     |
| 2. Node.js Core (Event Loop)   | Alta      | Alta       | 6 h                     |
| 3. Express.js (APIs)           | Alta      | Media      | 6 h                     |
| 4. PostgreSQL (SQL, ACID)      | Alta      | Alta       | 6 h                     |
| 5. Redis & Caché               | Media     | Media      | 5 h                     |
| 6. Microservicios/Arquitectura | Alta      | Alta       | 8 h                     |
| 7. Docker/Kubernetes/Cloud     | Media     | Alta       | 7 h                     |
| 8. Testing y Seguridad         | Alta      | Alta       | 7 h                     |

- **Prioridad:** Basada en relevancia común en entrevistas y rol _Senior Back-End_.
- **Dificultad:** Estimada según complejidad del tema.
- **Horas Totales:** Suma de subtemas y práctica por bloque.

## Calendario 7 Días

```mermaid
gantt
    title Plan de 7 Días
    dateFormat  YYYY-MM-DD
    section Día 1
    JS Avanzado (Bloque 1)        :a1, 2026-08-28, 6h
    Node Core (Bloque 2)          :a2, after a1, 6h
    section Día 2
    Express.js (Bloque 3)         :a3, 2026-08-29, 6h
    DB PostgreSQL (Bloque 4)      :a4, after a3, 6h
    section Día 3
    Redis (Bloque 5)              :a5, 2026-08-30, 5h
    Microservicios (Bloque 6)     :a6, after a5, 4h
    section Día 4
    Microservicios (continuación) :a7, 2026-08-31, 4h
    Docker/K8s/Cloud (Bloque 7)   :a8, after a7, 3h
    section Día 5
    Docker/K8s/Cloud (continuación) :a9, 2026-09-01, 4h
    Testing/Security (Bloque 8)   :a10, after a9, 3h
    section Día 6
    Testing/Security (continuación) :a11, 2026-09-02, 4h
    Repaso & Ejercicios          :a12, after a11, 2h
    section Día 7
    Simulacro Entrevista (preguntas) :a13, 2026-09-03, 4h
    Revisión de puntos débiles    :a14, after a13, 4h
```

_(Diagrama Gantt ejemplo en formato mermaid)_

## Calendario 14 Días (flexible)

```mermaid
gantt
    title Plan de 14 Días
    dateFormat  YYYY-MM-DD
    section Sem. 1
    JS Avanzado         :a1, 2026-08-28, 3d
    Node Core           :a2, after a1, 4d
    Express.js          :a3, after a2, 3d
    section Sem. 2
    PostgreSQL          :a4, 2026-09-08, 2d
    Redis               :a5, after a4, 2d
    Microservicios      :a6, after a5, 4d
    Docker/Kubernetes   :a7, after a6, 3d
    Testing/Security    :a8, after a7, 3d
    Repaso & Mock entrev :a9, after a8, 2d
```

_(Timelapse semanal para más detalle)_

**Nota:** Este cronograma puede adaptarse según conocimientos previos. Por ejemplo, si ya dominas JavaScript, dedicar menos tiempo al Bloque 1 y más a temas avanzados. Es recomendable intercalar teoría con práctica diaria.

## Comparación por Prioridad y Horas

Por prioridad, enfocar primero bloques (1,2,3,4,6,8) que tienen _Alta relevancia_. Los bloques 5 y 7 quedan _medios_, pero no son triviales. El plan asegura cubrir los temas críticos con tiempo para práctica y revisión.

Cada archivo contiene el resumen ejecutivo, teoría, ejemplos, preguntas de entrevista, ejercicios con soluciones, fuentes citadas y tiempo estimado según lo descrito en esta propuesta.

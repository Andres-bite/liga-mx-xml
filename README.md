# Práctica: Diseño y validación de resultados de Liga MX con XML y DTD

## 1. Propósito
Diseñar un formato XML para representar información estructurada de partidos de fútbol y definir, mediante un DTD externo, las reglas gramaticales y sintácticas que deben cumplir dichos documentos. El proyecto implementa los datos reales de la jornada correspondiente al domingo 27 de septiembre de 2026 de la Liga MX.

## 2. Modelo Jerárquico
La estructura principal del XML se diseñó siguiendo el siguiente modelo conceptual:

    liga
    │
    └── jornada
        │
        └── partido
            ├── estadio
            ├── local
            │   ├── nombre
            │   ├── marcador
            │   └── estadisticas
            └── visitante
                ├── nombre
                ├── marcador
                └── estadisticas

## 3. Criterios para elementos y atributos
Para definir la estructura, se tomó la siguiente justificación:

| Información | Elemento / Atributo | Justificación |
| :--- | :--- | :--- |
| **Jornada** | Elemento | Contenedor principal de los partidos de una fecha específica. |
| **Fecha / Número** | Atributos | Son metadatos de la jornada. |
| **ID del partido** | Atributo (`ID`) | Identificador único del nodo partido. Al ser tipo `ID` previene duplicidades estructurales en la validación. |
| **Equipo local / visitante** | Elementos | Entidades complejas que requieren sub-elementos (nombre, marcador, estadísticas). |
| **Goles (marcador)** | Elemento | Dato principal de visualización jerarquizado bajo cada equipo. |
| **Estadio** | Elemento | Entidad que describe la ubicación donde ocurre el partido. |
| **Estado del partido** | Atributo | Metadato de control del partido (ej. Finalizado, En curso). |

## 4. Estadísticas Seleccionadas y Diseño
Se tomó la decisión de anidar el elemento `<estadisticas>` dentro de cada equipo (`local` y `visitante`) en lugar de ponerlo a nivel de `<partido>`.
**Justificación:** Esto hace que la relación entre los datos sea directa, evita prefijos redundantes (como "tirosLocal", "tirosVisitante") y facilita enormemente la extracción de datos por equipo si se procesa el XML.

Las estadísticas elegidas (implementadas como atributos obligatorios de `<estadisticas>`) son:
* `posesion`
* `tiros`
* `tirosPuerta`
* `faltas`
* `tarjetasAmarillas`
* `tarjetasRojas`
* `tirosEsquina`

## 5. Instrucciones de validación
Para comprobar que el archivo XML está bien formado y es válido respecto a su DTD, asegúrate de tener instalado `libxml2` y ejecuta en la terminal desde la raíz del proyecto:

    # Validar el XML correcto:
    xmllint --noout --valid xml/resultados.xml

    # Validar el XML con errores intencionales:
    xmllint --noout --valid xml/resultados-invalido.xml

## 6. Resultados de Pruebas Negativas
Se creó el archivo `resultados-invalido.xml` para evaluar el comportamiento del validador DTD frente a errores intencionales.

| Prueba | ¿Bien formado? | ¿Válido? | Error detectado por el validador |
| :--- | :---: | :---: | :--- |
| **Falta visitante** | Sí | No | `Element partido content does not follow the DTD, expecting (estadio , local , visitante)` |
| **Dos locales** | Sí | No | El DTD exige estrictamente la secuencia `(estadio, local, visitante)`. Falla al encontrar el segundo local. |
| **Orden incorrecto** | Sí | No | Falla por no respetar la secuencia estricta separada por comas (`,`) en el `<!ELEMENT>`. |
| **Falta atributo obligatorio**| Sí | No | `Element partido does not carry attribute id` (Falta atributo declarado como `#REQUIRED`). |
| **ID duplicado** | Sí | No | `ID P101 already defined`. El DTD impide asignar la misma clave a dos partidos. |
| **Elemento desconocido** | Sí | No | `No declaration for element X`. |

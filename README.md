# Liga MX - XML y DTD

## 1. Propósito
Diseño y validación de una estructura XML jerárquica con DTD externo para almacenar resultados y estadísticas de la Liga MX.

## 2. Jerarquía Conceptual
```text
liga (nombre, temporada)
└── jornada (numero, fecha)
    └── partido (id, estado)
        ├── estadio
        ├── local
        │   ├── nombre
        │   ├── marcador
        │   └── estadisticas
        └── visitante
            ├── nombre
            ├── marcador
            └── estadisticas
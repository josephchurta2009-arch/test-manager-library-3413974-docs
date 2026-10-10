# ADR-ML-007 — Estructura de documentación y manejo de documento faltante

- **Estado:** aceptada
- **Fecha:** 2026-10-10
- **Decisores:** equipo de Manager Library

## Contexto

El prompt de especificación instruía integrar el contenido de `handoff-antigravity/DOCUMENTACION-GENERAL.md` en el repositorio de documentación (`test-manager-library-3413974-docs`). Sin embargo, dicho archivo no existe en el material entregado. Adicionalmente, es necesario definir la estructura exacta donde residirán la Matriz de Trazabilidad, el Registro de Cambios, los ADRs de React/Laravel y las instrucciones generales.

## Decisión

Al no existir el archivo base `DOCUMENTACION-GENERAL.md`, la documentación se organizará de forma autónoma basándose estrictamente en los requerimientos del prompt. La estructura dentro de `test-manager-library-3413974-docs` será la siguiente:

1. `adr/`: Carpeta que alojará todos los ADRs (migrados desde `handoff-antigravity/adr/`) y futuros ADRs específicos de Laravel/React.
2. `CHANGELOG.md`: Registro de funcionalidades cerradas (objetivo, repositorios afectados, pruebas ejecutadas).
3. `TRACEABILITY.md`: Matriz de trazabilidad que conecte Historias de Usuario (HU) y Criterios de Aceptación (CA) con el código y las pruebas.
4. `README.md`: Se actualizará para incluir las instrucciones de instalación, ejecución mediante Docker y demostración del sistema completo.

Los ADRs propuestos (003, 004, 005) se consideran ahora como definitivos para el desarrollo de la prueba técnica.

## Consecuencias

- Se crearán archivos en blanco/plantillas para la matriz de trazabilidad y el changelog para comenzar a llenarlos conforme avance el código.
- Los ADRs de la carpeta temporal `handoff` deben moverse permanentemente a la documentación del repositorio.
- No dependeremos de información externa fuera de los `specs` provistos.

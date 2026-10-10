# ADR-ML-001 — Fuentes de verdad y alcance de la guía Onion

- **Estado:** aceptada
- **Fecha:** 2026-10-10
- **Decisores:** equipo de Manager Library

## Contexto

La carpeta contiene dos especificaciones técnicas equivalentes de Manager Library (`spec-python` y `spec-.net`) y una guía Onion cuyo ejemplo funcional es otro producto: Simple Stock Flow. Usar la guía como si fuera una especificación funcional produciría entidades, rutas, motor de datos y reglas de negocio incorrectas.

## Decisión

Manager Library es el único producto que se implementa. Se establece este orden de prioridad:

1. Los documentos de `test-manager-library-3413974-docs/spec-python/` definen negocio, alcance, modelo de datos, contrato HTTP y criterios de aceptación. `spec-.net` se usa para confirmar el mismo comportamiento, no para adoptar .NET.
2. `ARQUITECTURA-ONION.md` define la orientación arquitectónica: dependencias hacia el centro, puertos, composición, límites por repositorio y estrategia de pruebas.
3. Los ADR-ML documentan la adaptación a Laravel y React y resuelven huecos, sin cambiar el producto definido por el spec.

No aplican al proyecto Manager Library los ejemplos de Simple Stock Flow: productos, ventas, `seller`, endpoints `/api/*`, MySQL, políticas de monedas ni sus cuerpos de error.

## Consecuencias

- La API pública conserva la base `/api/v1`, los endpoints y el cuerpo de error de `api-contract.md`.
- El motor es PostgreSQL 16, conforme a `data-model.md`; no MySQL.
- La arquitectura Onion se traduce a Laravel y React sin copiar las reglas de negocio del documento guía.
- Cualquier contradicción detectada debe registrarse con un ADR y corregirse en los documentos de Manager Library, no mediante supuestos en código.

## Verificación

- Las rutas implementadas coinciden con `spec-python/api-contract.md`.
- Las migraciones usan PostgreSQL 16 y reflejan `spec-python/data-model.md`, más las correcciones ADR aceptadas.
- Ninguna entidad o ruta de Simple Stock Flow aparece en los repositorios de producción.

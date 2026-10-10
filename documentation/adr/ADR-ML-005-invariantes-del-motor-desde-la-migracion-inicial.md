# ADR-ML-005 — Completar invariantes del motor desde la migración inicial

- **Estado:** propuesta
- **Fecha:** 2026-10-10
- **Decisores:** equipo de Manager Library

## Contexto

El artículo VI de la constitución establece que las invariantes que PostgreSQL puede imponer deben residir en la base. No obstante, `data-model.md` deja `ck_book_total_positive` y `ck_user_role` como pendientes para T-18. Esa secuencia es válida como plan pedagógico, pero una implementación nueva y completa no necesita publicar una base temporalmente menos protegida.

## Decisión

La primera migración de Laravel que cree el esquema completo incluirá desde el inicio estas restricciones:

```sql
CHECK (total_copies >= 1)
CHECK (role IN ('admin', 'librarian'))
```

También incluirá las restricciones, FKs, índices únicos y el índice parcial de ISBN activo ya definidos por el modelo. T-18 se conserva como evidencia verificable: ejecutar inserciones directas inválidas debe fallar. Al aceptar este ADR, `data-model.md` debe cambiar las dos marcas de **pendiente** a **motor**.

## Alternativas descartadas

- **Dejarlas solo en el dominio:** contradice el artículo VI y deja vulnerable la base ante procesos futuros o escritura manual.
- **Añadirlas únicamente al final mediante migración separada:** añade complejidad sin aportar valor a un proyecto que se construye desde cero.

## Consecuencias

- La migración se prueba contra PostgreSQL 16, no solo con SQLite.
- Se preserva la regla «sin DEFAULT»: la aplicación sigue asignando valores explícitos.
- Las pruebas de integración incluyen intentos directos de insertar `total_copies = 0` y `role` inválido.

## Verificación

- Las consultas de `data-model.md` §10 muestran ambos `CHECK`.
- Inserciones directas inválidas fallan en PostgreSQL.
- La documentación y la migración describen el mismo estado final.

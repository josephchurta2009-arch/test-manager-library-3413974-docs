# ADR-ML-003 — Añadir versión del libro para concurrencia optimista

- **Estado:** propuesta
- **Fecha:** 2026-10-10
- **Decisores:** equipo de Manager Library

## Contexto

El ADR-002 del spec ordena concurrencia optimista: el libro debe llevar un testigo de versión y dos préstamos simultáneos del último ejemplar deben acabar con un único `201` y un `409 BOOK_NOT_AVAILABLE`. Sin embargo, la tabla `book` de `data-model.md` no declara ninguna columna de versión. El contrato de concurrencia no puede implementarse ni demostrarse de manera fiable solo con una lectura y escritura convencional.

## Decisión

Añadir al modelo físico de `library.book`:

| Columna | Tipo | Nulable | Regla |
|---|---|---|---|
| `version` | `integer` | no | Entero inicializado por la aplicación en `1`; `CHECK (version >= 1)` |

La implementación de infraestructura actualiza un libro mediante comparación y aumento atómicos de versión, dentro de la misma transacción que crea o devuelve una línea. Conceptualmente:

```sql
UPDATE library.book
SET available_copies = :available_copies,
    total_copies = :total_copies,
    version = version + 1
WHERE id = :id AND version = :expected_version;
```

Si no se actualiza exactamente una fila, el adaptador devuelve un conflicto de concurrencia. El caso de uso revalida disponibilidad y responde `409 BOOK_NOT_AVAILABLE` cuando corresponda; no expone detalles internos de versión.

La versión es un detalle de persistencia: no se expone por la API ni se exige que aparezca en el agregado de dominio. El modelo sigue sin `DEFAULT`; la aplicación asigna `1` al crear el libro.

## Alternativas descartadas

- **Bloqueo pesimista de cada fila:** serializa más de lo necesario y contradice ADR-002.
- **Contador en memoria:** falla con más de un proceso y no protege la base.
- **Confiar solo en `CHECK` de rango:** evita una cantidad inválida, pero no identifica limpiamente el conflicto ni garantiza la semántica de exactamente un ganador.

## Consecuencias

- Se debe actualizar `data-model.md` §3 y §4, la migración y las pruebas de integración.
- Todos los caminos que modifiquen existencias o ejemplares usan la misma política de actualización versionada.
- La prueba concurrente es obligatoria: dos solicitudes simultáneas contra un libro con un ejemplar generan un éxito y un conflicto.

## Verificación

- La consulta de restricciones muestra `ck_book_version_positive` o un nombre equivalente.
- El repositorio incluye `version` en cada actualización de `book`.
- La prueba RNF-2 pasa repetidamente contra PostgreSQL real.

# ADR-ML-004 — Declarar el endpoint de consulta de portada

- **Estado:** propuesta
- **Fecha:** 2026-10-10
- **Decisores:** equipo de Manager Library

## Contexto

El contrato de `POST /books` declara `cover_key` como una clave opaca y explica que la portada se pinta mediante `GET /books/{id}/cover`. Sin embargo, ese `GET` no aparece en la tabla de endpoints de libros. La omisión deja abierto si el frontend puede depender de él, qué autorización usa y qué ocurre cuando el libro o la portada no existen.

## Decisión

Añadir a la tabla §3.4 de `api-contract.md` este endpoint:

| Método | Ruta | Rol | Descripción |
|---|---|---|---|
| GET | `/books/{id}/cover` | autenticado | Devuelve el binario JPEG o PNG de la portada actual del libro activo. |

Comportamiento adicional:

- Respuesta exitosa: `200`, `Content-Type: image/jpeg` o `image/png`, con el binario.
- Si el libro no existe, está dado de baja o no tiene portada: `404` con el cuerpo de error uniforme `NOT_FOUND`.
- No se revela la ruta física, el nombre interno ni el `cover_key` como URL pública.
- La autorización sigue la convención global: toda ruta salvo login y health requiere token válido.

## Alternativas descartadas

- **Exponer `cover_key` como URL:** convierte un detalle de almacenamiento en contrato público y filtra estructura interna.
- **Hacer pública la portada sin decisión explícita:** contradice la regla global de autenticación por omisión.
- **Eliminar el endpoint mencionado:** impediría al frontend obtener el binario de forma definida.

## Consecuencias

- Deben actualizarse la tabla del contrato, la prueba de contrato, la implementación de almacenamiento y el cliente React.
- El adaptador de archivos debe validar el tipo por contenido al subir y conservar el tipo fiable para servirlo.
- Borrar una portada mantiene el orden ya definido en el modelo de datos: confirmar `cover_key = null` y después borrar el binario.

## Verificación

- El endpoint requiere autenticación y sirve correctamente un PNG y un JPEG válidos.
- Los tres casos 404 usan el cuerpo uniforme del contrato.
- La respuesta no contiene rutas ni claves internas de almacenamiento.

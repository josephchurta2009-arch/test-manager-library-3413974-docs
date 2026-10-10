# ADR-ML-002 — Arquitectura Onion para Laravel y React

- **Estado:** aceptada
- **Fecha:** 2026-10-10
- **Decisores:** equipo de Manager Library

## Contexto

El spec de referencia describe una arquitectura hexagonal para Python y .NET, mientras que el reto exige Laravel y React. La guía Onion aporta una forma común de traducirla, pero sus nombres de ejemplos no pertenecen a Manager Library.

## Decisión

El backend Laravel se organiza así:

```text
Domain <- Application <- Presentation
       <- Application <- Infrastructure
                    ^
                Bootstrap
```

- `Domain`: libros, préstamos, líneas, socios, usuarios, objetos de valor e invariantes. No importa Laravel, Eloquent, HTTP ni base de datos.
- `Application`: casos de uso, DTOs y puertos. Declara los repositorios, reloj, hash, token, almacenamiento, lector de reporte y unidad de trabajo que consume.
- `Infrastructure`: implementaciones con Eloquent/PostgreSQL, migraciones, mapeadores, JWT, hash de contraseña, archivos y transacciones.
- `Presentation`: rutas, controladores, validación de forma, serialización y conversión uniforme de errores. Depende de puertos de entrada, no de repositorios concretos.
- `Bootstrap`: único punto de inyección que une un puerto con su adaptador.

El frontend React se organiza en `domain`, `application`, `infrastructure` y `features`. Las pantallas invocan casos de uso; el cliente HTTP y la sesión se quedan en infraestructura.

## Consecuencias

- Las reglas críticas de préstamo se pueden probar sin levantar Laravel ni PostgreSQL.
- Los controladores no contienen reglas como límites de préstamo, vencimientos o disponibilidad.
- El cambio de almacenamiento de portadas o de mecanismo de token no toca el dominio.
- Se deben crear pruebas de arquitectura que detecten dependencias hacia afuera desde el dominio o la aplicación.

## Verificación

- Un test carga el dominio sin iniciar Laravel.
- Una búsqueda estática o una prueba de arquitectura confirma que `Domain` no importa `Illuminate` ni modelos Eloquent.
- Los controladores reciben puertos/casos de uso, no repositorios de infraestructura.
- Solo el proveedor de composición instancia implementaciones de infraestructura.

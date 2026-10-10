# Arquitectura y Decisiones Técnicas (ADRs) — Manager Library

Este directorio contiene las Decisiones de Arquitectura (ADRs) adoptadas para la implementación de Manager Library en PHP (Laravel 11), React y Docker.

| ADR | Estado | Título y Decisión |
|---|---|---|
| [ADR-ML-001](ADR-ML-001-fuentes-de-verdad-y-alcance-onion.md) | Aceptada | Fuentes de verdad y alcance Onion: El spec de Manager Library define el producto; la guía Onion aporta la estructura de capas. |
| [ADR-ML-002](ADR-ML-002-onion-en-laravel-y-react.md) | Aceptada | Arquitectura Onion en Laravel y React con un único Composition Root (`DomainServiceProvider`). |
| [ADR-ML-003](ADR-ML-003-version-para-concurrencia-optimista.md) | Aceptada | Manejo de versiones para concurrencia optimista y control transaccional del stock disponible. |
| [ADR-ML-004](ADR-ML-004-endpoint-de-consulta-de-portada.md) | Aceptada | Endpoint de consulta y almacenamiento binario opaco para portadas de libros (`GET /books/{id}/cover`). |
| [ADR-ML-005](ADR-ML-005-invariantes-del-motor-desde-la-migracion-inicial.md) | Aceptada | Invariantes del motor relacional desde la migración inicial (CHECKs de stock, índices parciales y claves foráneas restrictivas). |
| [ADR-ML-006](ADR-ML-006-gobierno-documental-y-trazabilidad.md) | Aceptada | Gobierno documental y trazabilidad continua de HU/CA a código y pruebas. |
| [ADR-ML-007](ADR-ML-007-estructura-de-documentacion-y-ausencia-doc-general.md) | Aceptada | Estructura de documentación autónoma y resolución ante ausencia de documento general en handoff. |
| [ADR-ML-008](ADR-ML-008-docker-orchestration-reverse-proxy.md) | Aceptada | Orquestación Docker multi-servicio, Reverse Proxy con Nginx y utilidad de sembrado automatizado. |

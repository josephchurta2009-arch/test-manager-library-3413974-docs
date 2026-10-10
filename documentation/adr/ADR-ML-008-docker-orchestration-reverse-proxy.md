# ADR-ML-008 — Orquestación Docker, Reverse Proxy Nginx y Seeder de Datos

- **Estado:** aceptada
- **Fecha:** 2026-10-10
- **Decisores:** equipo de Manager Library

## Contexto

El proyecto requiere integrar armónicamente los seis repositorios componentes:
1. `test-manager-library-3413974-docs`: Especificación, ADRs y contratos de API.
2. `test-manager-library-3413974-infra`: Docker Compose con PostgreSQL, API Laravel y Frontend React.
3. `test-manager-library-3413974-api`: Backend monolítico en Laravel 11 con Arquitectura Onion.
4. `test-manager-library-3413974-app`: Single Page Application (SPA) en React + Vite + TypeScript.
5. `test-manager-library-3413974-page`: Landing estática de presentación.
6. `test-manager-library-3413974-tool`: Utilidad para sembrado automatizado de datos de demostración sobre la API.

Para evitar problemas de CORS, 404 en refrescos de rutas client-side en React y desacoplar dependencias durante el despliegue local, se requiere una estrategia unificada de orquestación y proxy inverso.

## Decisión

1. **Docker Compose Multi-Servicio (`test-manager-library-3413974-infra`):**
   - Servicio `db`: PostgreSQL 16 con healthcheck nativo `pg_isready`.
   - Servicio `api`: PHP 8.4-cli con pdo_pgsql, ejecuta migraciones automáticas al inicio, inicializa el administrador predeterminado desde variables de entorno y expone el puerto 8000.
   - Servicio `frontend`: Nginx Alpine multi-stage sirviendo el build de producción de React en el puerto 4173.

2. **Reverse Proxy Nginx en Frontend:**
   - La configuración Nginx atiende el SPA con fallback `try_files $uri $uri/ /index.html;`.
   - Proxifica `/api/` transparentemente hacia `http://api:8000/api/`, eliminando discrepancias de origen y habilitando consumo unificado.

3. **Herramienta de Sembrado (`test-manager-library-3413974-tool`):**
   - Implementado como script Node.js con API Fetch nativa.
   - Consume los endpoints `/api/v1/auth/login`, `/api/v1/categories`, `/api/v1/books`, `/api/v1/members` y `/api/v1/loans`.
   - Respeta estrictamente los invariantes del dominio (ej. ISBN de 13 dígitos numéricos, unicidad de documento de socio).

## Consecuencias

- Despliegue con un único comando: `docker compose up -d --build`.
- Compatibilidad completa entre contratos de API y UI reactiva sin CORS.
- Datos de prueba listos para presentación y validación inmediata.

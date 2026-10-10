# Registro de Cambios y Funcionalidades (Changelog)

Este documento registra los incrementos funcionales terminados, de acuerdo a las tareas T-01 hasta T-23 y los requerimientos del Spec de Manager Library.

## [v1.0.0] - Implementación Final Completa (2026-10-10)

### Resumen
Implementación completa y funcional del sistema **Manager Library** traduciendo los specs de Python/.NET hacia el stack requerido: **PHP 8.4 (Laravel 11)** para el backend con Arquitectura Onion pura, **React + Vite + TypeScript** para el frontend y **Docker Compose** para la infraestructura orquestada con PostgreSQL 16.

### Repositorios y Tareas Completadas:

#### 1. `test-manager-library-3413974-api` (Backend) — T-02 a T-19
- **Dominio y Reglas (T-02, T-03):** Entidades puras `Book`, `Member`, `Loan`, `LoanItem`, `User`; Value Objects `Isbn` (13 dígitos numéricos), `Quantity`, `DateRange`; cálculo dinámico de estado de préstamo (`open`, `overdue`, `closed`).
- **Base de Datos y Restricciones (T-03, ADR-ML-005):** Migración única en PostgreSQL con tipos UUID, CHECKs de stock (`0 <= available_copies <= total_copies`), índices parciales para soft deletes y seeding inmutable de 5 categorías.
- **Autenticación y Roles (T-04, T-05):** Endpoint `POST /auth/login` emitiendo JWT con 8h de vigencia. Middleware de roles (`admin` y `librarian`). Alta de bibliotecarios (`POST /users`) con sanitización en minúsculas y restricción de creación de admin.
- **Catálogo de Libros (T-06, T-07, T-08):** Búsqueda paginada, consulta por id, creación, edición y baja lógica validando préstamos abiertos (`BOOK_HAS_OPEN_LOANS` 409).
- **Socios (T-09, T-10):** Registro con cédula única y consulta paginada con conteo de préstamos activos. Respeto de CA-05.3 (no se eliminan).
- **Préstamos y Devoluciones (T-11 a T-15):** Validación atómica de hasta 3 líneas, deducción de stock en tiempo real, bloqueo por más de 3 libros sin devolver o presencia de libros vencidos. Congelación de datos en línea de préstamo (RN-4). Devolución unitaria por línea sellando `returned_at` y restaurando disponibles.
- **Vencidos y Reportes (T-16, T-17):** Endpoint `GET /loans/overdue` ordenado por días de mora y reporte `GET /reports/most-borrowed` para admin con rangos de fechas.
- **Composición Onion (T-18):** `DomainServiceProvider` vincula puertos e infraestructura, e inicializa al usuario admin desde variables de entorno.

#### 2. `test-manager-library-3413974-app` (Frontend) — T-22
- **Diseño Moderno:** Sistema de diseño oscuro refinado con `Plus Jakarta Sans`, glassmorphism, contrastes accesibles y micro-animaciones.
- **Módulos Interactivos:**
  - Login con JWT y redirección por estado de sesión.
  - Catálogo de libros con búsqueda, filtros, creación modal, edición y baja lógica (admin).
  - Gestión de socios con modal de alta y modal de detalle con libros activos.
  - Préstamos con selectores desplegables dinámicos de socios y libros, pestañas de filtrado por estado y devolución inmediata por línea.
  - Monitoreo de libros vencidos con cálculo de días de retraso.
  - Reporte de libros más prestados por rango de fechas (admin).
  - Módulo de Operadores para dar de alta bibliotecarios (admin).
- **Nginx Reverse Proxy:** `nginx.conf` integrado que resuelve rutas SPA y proxifica `/api/` a la API eliminando CORS.

#### 3. `test-manager-library-3413974-infra` — T-20
- Docker Compose multi-servicio (`db`, `api`, `frontend`) con inicio sincronizado mediante healthchecks.

#### 4. `test-manager-library-3413974-tool` — T-21
- Script Node.js de sembrado automatizado (`npm start` / `npm run seed`) que consume la API para poblar libros con ISBN-13, socios y préstamos de prueba.

#### 5. `test-manager-library-3413974-page` — T-23
- Landing estática modernizada con presentación del sistema, credenciales de demostración y arquitectura técnica.

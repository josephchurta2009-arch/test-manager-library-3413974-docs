# Matriz de Trazabilidad (HU / CA a Código y Pruebas)

Esta matriz vincula los requisitos funcionales (Historias de Usuario y Criterios de Aceptación) con la implementación técnica y las pruebas correspondientes, asegurando la cobertura del alcance definido en el Spec.

| Requisito | Descripción | Componente | Archivos Clave | Prueba / Evidencia |
|---|---|---|---|---|
| **HU-01** | Iniciar sesión (token de 8h) | API / App | `AuthController.php`, `JwtAuthMiddleware.php`, `LoginPage.tsx` | `POST /api/v1/auth/login` devuelve JWT 28800s. Login UI integrado. |
| **CA-01.1** | Credenciales válidas -> token | API | `AuthService.php`, `JwtService.php` | Devuelve `access_token` Bearer y rol. |
| **CA-01.2** | Credenciales inválidas -> 401 unificado | API | `AuthController.php` | Respuesta 401 uniforme sin revelar si falló usuario o clave. |
| **CA-01.3** | Protección de endpoints con token | API | `JwtAuthMiddleware.php`, `api.php` | Sin token -> 401. Rol insuficiente -> 403 `FORBIDDEN_ROLE`. |
| **HU-02** | Alta de bibliotecarios (admin) | API / App | `UserController.php`, `UsersPage.tsx` | `POST /api/v1/users` crea usuario `librarian`. Formulario en UI admin. |
| **CA-02.2** | Username en minúsculas y único | API | `UserService.php`, `User.php` | `username` sanitizado; conflicto genera 409 `USERNAME_TAKEN`. |
| **CA-02.3** | Prohibido rol admin por API | API | `UserController.php` | Intentar crear rol distinto a `librarian` devuelve 422. |
| **HU-03** | Gestionar libros (admin) | API / App | `BookController.php`, `BookService.php`, `BooksPage.tsx` | CRUD completo: `POST /books`, `PUT /books/{id}`, `DELETE /books/{id}`. |
| **CA-03.2** | ISBN 13 dígitos y único | API / Dominio | `Isbn.php`, `Book.php`, `BooksPage.tsx` | Validación regex 13 dígitos numéricos. Duplicado -> 409 `ISBN_TAKEN`. |
| **CA-03.3** | Ajuste disponible y total >= 1 | Dominio / API | `Book.php`, `InitialSchema.php` | CHECK en BD (`available_copies <= total_copies` y `total_copies >= 1`). |
| **CA-03.5** | Baja lógica de libros | Dominio / BD | `BookRepository.php`, `Book.php` | `deleted_at` timestamptz. Si tiene préstamos abiertos -> 409 `BOOK_HAS_OPEN_LOANS`. |
| **HU-04** | Buscar libros (texto, categoría) | API / App | `BookController.php`, `BooksPage.tsx` | Búsqueda por `q` y `category_id`, paginado (máx 50). Filtro reactivo. |
| **HU-05** | Gestionar socios | API / App | `MemberController.php`, `MembersPage.tsx` | `POST /members`, `GET /members`, `GET /members/{id}` con active_loans. No se eliminan (CA-05.3). |
| **HU-06** | Registrar préstamo | API / App | `LoanController.php`, `LoanService.php`, `LoansPage.tsx` | 1-3 líneas. Descuenta 1 disponible por libro. Plazo 14 días (RN-2). Línea congela título, isbn y categoría (RN-4). |
| **CA-06.2** | No repetir libro en préstamo | Dominio | `LoanService.php` | Valida unicidad de IDs de libro en payload -> 422 `DUPLICATE_BOOK_IN_LOAN`. |
| **CA-06.4** | Límite 3 libros sin devolver | Dominio | `LoanService.php` | Conteo de activos > 3 rechaza con 422 `MEMBER_LIMIT_EXCEEDED`. |
| **CA-06.5** | Bloqueo por libros vencidos | Dominio | `LoanService.php` | Socio con mora no puede solicitar nuevos -> 409 `MEMBER_HAS_OVERDUE`. |
| **HU-07** | Devolución por línea | API / App | `LoanController.php`, `LoanService.php`, `LoansPage.tsx` | `POST /loans/{id}/items/{id}/return` suma 1 disponible y sella `returned_at`. Re-devolución rechaza con 409. |
| **HU-08** | Ver estado calculado de préstamo | Dominio / API | `Loan.php`, `LoanController.php` | Estado dinámico calculado: `open`, `overdue`, `closed` sin columna en BD. |
| **HU-09** | Libros vencidos | API / App | `LoanRepository.php`, `OverduePage.tsx` | `GET /loans/overdue` ordenado por más días de retraso. |
| **HU-10** | Reporte más prestados | API / App | `ReportController.php`, `ReportPage.tsx` | `GET /reports/most-borrowed` por rango de fechas (admin). Respeta datos congelados. |
| **RNF-1** | Búsqueda eficiente | BD | `InitialSchema.php` | Índices sobre `book.title`, `book.isbn`, `book.category_id`. |
| **RNF-2** | Concurrencia y stock | Dominio / BD | `Book.php`, `ADR-ML-003` | Transacciones y control de versiones / bloqueos sobre disponibles. |
| **RNF-5** | Orquestación Docker | Infraestructura | `docker-compose.yml`, `nginx.conf` | `docker compose up -d --build` levanta DB, API y Frontend comunicados. |

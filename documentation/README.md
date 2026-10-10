# test-manager-library-3413974-docs

> **Prueba técnica · Ficha ADSO 3413974**
> Horario: de **9:15 a. m. a 3:00 p. m.** (15:00). Después, **feria de presentación** de cada proyecto.

Este repositorio contiene la **especificación canónica**, la **documentación de arquitectura (ADRs)**, la **matriz de trazabilidad** y las **instrucciones de despliegue y demostración** de *Manager Library*.

---

## 🚀 Guía Rápida de Instalación y Despliegue Local

Toda la aplicación se encuentra orquestada mediante Docker Compose y levanta con un único comando:

### 1. Clonar y Construir los Contenedores
Desde la carpeta raíz del proyecto o desde `FULL APP/test-manager-library-3413974-infra`:

```bash
cd "FULL APP/test-manager-library-3413974-infra"
docker compose up -d --build
```

Esto compilará y levantará los 3 servicios integrados:
1. **`db` (PostgreSQL 16):** Expuesto en el puerto `5432`. Base de datos inicializada automáticamente con las migraciones, CHECKs de stock e índices parciales para soft deletes.
2. **`api` (PHP 8.4 Laravel 11):** Expuesto en el puerto `8000`. Monolito bajo Arquitectura Onion.
3. **`frontend` (React + Vite + Nginx):** Expuesto en el puerto `4173`. SPA reactivo con reverse proxy a `/api/`.

### 2. Sembrado de Datos de Demostración (Tool)
Para poblar el catálogo con libros de prueba (ISBN-13 válidos), socios y préstamos activos vía API REST:

```bash
cd "../test-manager-library-3413974-tool"
npm start
```

---

## 🔑 Credenciales para la Demostración

| Rol | Usuario | Contraseña | Permisos y Alcance |
|---|---|---|---|
| **Administrador** | `admin` | `Admin123!` | Catálogo completo (crear, editar, baja lógica), Socios, Préstamos, Devoluciones, Libros Vencidos, Reporte de demanda y Alta de Operadores. |
| **Bibliotecario** | `bibliotecario` | `Librarian123!` | Catálogo (consulta), Socios (registro y consulta), Préstamos (creación), Devolución por línea y Monitoreo de Libros Vencidos. |

### URLs de Acceso:
* **Aplicación Web:** [http://localhost:4173](http://localhost:4173)
* **API REST Backend:** [http://localhost:8000](http://localhost:8000) (o vía [http://localhost:4173/api/v1/health](http://localhost:4173/api/v1/health))
* **Sitio Público de Presentación:** [`../test-manager-library-3413974-page/index.html`](../test-manager-library-3413974-page/index.html)

---

## 📑 Documentación Técnica y Trazabilidad

| Documento | Ubicación | Descripción |
|---|---|---|
| **Matriz de Trazabilidad** | [`TRACEABILITY.md`](TRACEABILITY.md) | Mapeo detallado de HU-01 a HU-10 y criterios de aceptación hacia código y pruebas. |
| **Registro de Cambios** | [`CHANGELOG.md`](CHANGELOG.md) | Histórico de entregas y tareas completadas (T-01 a T-23) para la versión `v1.0.0`. |
| **Decisiones de Arquitectura** | [`adr/`](adr/README.md) | Los 8 ADRs adoptados para la implementación con Laravel 11, React y Docker. |
| **Especificación de Referencia** | [`spec-python/`](spec-python/) y [`spec-.net/`](spec-.net/) | Especificaciones de dominio originales (contratos de API, modelo relacional e invariantes). |

---

## 🏛️ Los Seis Repositorios del Proyecto

| Repositorio | Qué contiene |
|---|---|
| [`test-manager-library-3413974-docs`](.) | Especificación, ADRs, trazabilidad y changelog. |
| [`test-manager-library-3413974-api`](../test-manager-library-3413974-api) | Backend en PHP (Laravel 11) con Arquitectura Onion. |
| [`test-manager-library-3413974-app`](../test-manager-library-3413974-app) | Frontend en React + Vite + TypeScript con tema moderno y glassmorphism. |
| [`test-manager-library-3413974-page`](../test-manager-library-3413974-page) | Landing estática de presentación para la feria técnica. |
| [`test-manager-library-3413974-infra`](../test-manager-library-3413974-infra) | Docker Compose orquestando PostgreSQL 16, API y Frontend. |
| [`test-manager-library-3413974-tool`](../test-manager-library-3413974-tool) | Sembrador automatizado de datos de prueba sobre la API REST. |

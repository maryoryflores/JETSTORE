# Plan de acción — Pruebas de flujos que usan las APIs

> Generado el 2026-11-18. Base: `openapi.md`, controladores Spring (`backend/.../controller/`), servicios frontend (`Frontend/src/services/`), `api.ts` y el grafo de Graphify (`RespuestaApi` con grado 39; `PagoServiceImpl`, `CarritoServiceImpl`, `UsuarioServiceImpl` como nodos centrales).
>
> Enfoque de UX: cada flujo se valida también desde la perspectiva de la persona que lo usa — feedback visible, estados de carga/vacío/error, mensajes accionables y consistencia entre lo que promete la API y lo que muestra la interfaz.

## 1. Objetivo

Verificar que los flujos de negocio que cruzan frontend → API → backend → base de datos funcionen de extremo a extremo, con contratos coherentes y una experiencia predecible tanto en éxito como en fallo.

## 2. Inventario de la superficie API

Base URL: `http://localhost:8080` (`Frontend/src/services/api.ts:5`). Autenticación: `Authorization: Bearer <token>` inyectado por el interceptor (`api.ts:12-23`); un 401 cierra sesión y redirige a `/login` (`api.ts:25-34`).

Envoltorio estándar: `{ ok, mensaje, payload }` (`openapi.md:51`, helper `RespuestaApi`).

### Documentada en `openapi.md` (24 endpoints)

| Dominio | Endpoints |
|---------|-----------|
| Rol | `POST /rol/crear`, `GET /rol/todos` |
| Usuario | `POST /usuario/registrar`, `POST /usuario/login`, `GET /usuario/perfil/{id}`, `POST /usuario/cerrar-sesion`, `POST /usuario/recuperar`, `POST /usuario/restablecer`, `GET /usuario/todos` |
| Libro | `POST /libro/guardar`, `POST /libro/agregar-stock`, `DELETE /libro/eliminar/{id}`, `GET /libro/todos`, `GET /libro/buscar?q=`, `POST /libro/filtrar`, `GET /libro/ver/{id}`, `GET /libro/categorias`, `GET /libro/categoria/{nombre}`, `GET /libro/recientes` |
| Carrito | `POST /carrito/agregar`, `GET /carrito/ver/{usuarioId}`, `DELETE /carrito/eliminar/{usuarioId}/{libroId}` |
| Pago | `POST /pago/procesar`, `POST /pago/confirmar` |

### Usada por el frontend pero no documentada en `openapi.md` (brechas de contrato)

| Endpoint | Evidencia | Riesgo UX |
|----------|-----------|-----------|
| `POST /pago/crear-preferencia` | `payment.service.ts:42-43`, `PagoController.crearPreferencia` | El checkout de Mercado Pago puede fallar sin mensaje claro |
| `POST /pago/confirmar-mp` | `payment.service.ts:45-46`, `PagoController.confirmarMp` | El usuario no sabe si el pago se confirmó |
| `POST /pago/webhook` | `PagoController.webhook` | Conciliación de pagos sin trazabilidad |
| `GET /categoria/todos`, `POST /categoria/guardar`, `PUT /categoria/actualizar/{id}`, `DELETE /categoria/eliminar/{id}` | `category.service.ts:16-27`, `CategoriaController` | Administración de categorías con contrato implícito |
| `adminService.getStats`, `reportService.*` | `admin.service.ts:11`, `report.service.ts:1-10` devuelven `Promise.resolve` | El panel de administración muestra datos falsos vacíos |

## 3. Estrategia de pruebas (4 capas)

| Capa | Qué prueba | Herramienta | Cuándo |
|------|-----------|-------------|--------|
| C1 Contrato | Formato `RespuestaApi`, códigos, esquemas DTO vs `openapi.md` | @WebMvcTest + `MockMvc`; diff manual con openapi | Antes de integrar |
| C2 Integración API | Flujo completo backend + BD (H2) sin UI | `@SpringBootTest` / `MockMvc` + `./mvnw test` | Sprint 1-2 |
| C3 Contrato frontend | Que cada service llame al endpoint y payload correctos | Vitest + mock de `api` (ya existe en `src/services/__tests__/`) | Sprint 1 |
| C4 E2E / UX | Flujo visible en navegador real, incluyendo errores y estados vacíos | skill `playwright-cli` (instalada) | Sprint 2 |

Datos de prueba (`README.md:84-99`): `admin/admin123` (ADMIN), `invitado/invitado123` (CLIENTE) y modo "Entrar como Invitado" (solo catálogo; compra redirige a `/login`).

## 4. Flujos críticos a probar

### F1 — Autenticación y perfil
1. `POST /usuario/registrar` → `POST /usuario/login` → `GET /usuario/perfil/{id}` → `POST /usuario/cerrar-sesion`.
2. Negativos: usuario duplicado, rol inexistente, password incorrecto, perfil ajeno, token inválido/expirado.
3. Verificar: token devuelto y enviado en el interceptor; 401 cierra sesión y lleva a `/login` con mensaje entendible; en register el error se muestra junto al campo correspondiente.
4. Trazas: `UsuarioController`, `UsuarioServiceImpl.login/registrar`, `RespuestaApi`, `auth.service.ts`, `auth-store.ts`.

### F2 — Recuperación de contraseña
1. `POST /usuario/recuperar` → enlace (SMTP o consola) → `POST /usuario/restablecer` → login con la nueva clave.
2. Negativos: correo no registrado, token inválido/expirado, passwords que no coinciden, reutilización del token.
3. Verificar (UX): confirmación visible tras solicitar, expiración explicada, enlace de un solo uso y retorno guiado a `/login`.
4. Trazas: `PasswordRecoveryController.recuperar/restablecer`, `PasswordRecoveryServiceImpl`, `README.md:117-124`.

### F3 — Catálogo y búsqueda
1. `GET /libro/todos` → `GET /libro/ver/{id}` → `GET /libro/categorias` → `GET /libro/buscar?q=` → `POST /libro/filtrar` → `GET /libro/categoria/{nombre}` → `GET /libro/recientes`.
2. Negativos: id inexistente, búsqueda sin resultados, filtros con rangos invertidos.
3. Verificar (UX): estado vacío con acción sugerida, sin pantallas en blanco, paginación/orden estable, prices formateados `es-PE`.
4. Trazas: `LibroController`, `LibroRepoImpl.filtrarLibros/buscarPorKeyword`, `product.service.ts`.

### F4 — Carrito
1. `POST /carrito/agregar` (nuevo y acumulando) → `GET /carrito/ver/{usuarioId}` → `DELETE /carrito/eliminar/{usuarioId}/{libroId}`.
2. Negativos: usuario inexistente, libro inexistente, carrito vacío, cantidad 0/negativa.
3. Verificar: total recalculado, badge del carrito sincronizado, mensaje al eliminar y opción de deshacer.
4. Trazas: `CarritoController`, `CarritoServiceImpl.agregar/ver/eliminarItem`, `cart.service.ts`, `useCartStore` (god node).

### F5 — Checkout y pagos
1. `POST /pago/procesar` (PENDIENTE) → `POST /pago/confirmar` (venta + carrito vacío).
2. Mercado Pago: `POST /pago/crear-preferencia` → `POST /pago/confirmar-mp` → `POST /pago/webhook`.
3. Negativos: carrito sin items, método inválido, pago ya confirmado, referencia duplicada, webhook repetido (idempotencia).
4. Verificar: transición `PENDIENTE → COMPLETADO/FALLIDO`, venta con `DetalleVenta` correctos, estados visibles en UI ("Procesando…", confirmación con número de venta, error con acción de reintentar).
5. Trazas: `PagoController.procesarPago/confirmarPago/crearPreferencia/confirmarMp/webhook`, `PagoServiceImpl`, `order.service.ts`, `payment.service.ts`.

### F6 — Administración
1. Roles: `POST /rol/crear`, `GET /rol/todos`.
2. Libros: crear, agregar stock, eliminar. Usuarios: `GET /usuario/todos`.
3. Categorías: CRUD (endpoints no documentados, ver brechas).
4. Reportes: `adminService.getStats` / `reportService.*` hoy son mocks → decidir si se implementa API o se elimina el módulo de la UI.
5. Verificar (UX): errores de validación inline, confirmación antes de eliminar, tabla con estados vacíos y feedback after-save; nunca mostrar datos falsos como reales.
6. Trazas: `admin.service.ts`, `category.service.ts`, `RolController`, `CategoriaController`.

## 5. Plan por sprints

### Sprint 1 — Cerrar brechas de contrato (1-2 días)
1. Documentar en `openapi.md` los 6 endpoints usados por el frontend que faltan (F5, F6).
2. Corregir o eliminar los mocks de `reportService` y `adminService.getStats` (K-03).
3. Añadir tests C3 de `payment.service.ts`, `category.service.ts`, `admin.service.ts` verificando URL, método y payload.
4. Exportar la colección Postman (K-01) para usarla como fuente de los casos C1/C2.

### Sprint 2 — Integración de flujos (3-4 días)
1. Tests C2 para F1 y F2 (autenticación y recuperación) — 6 endpoints.
2. Tests C2 para F3 y F4 (catálogo y carrito) — 13 endpoints.
3. Tests C2 para F5 (pagos) — 5 endpoints, incluyendo idempotencia del webhook.
4. Tests C2 para F6 (admin y roles) — 9 endpoints.
5. Ejecutar `cd backend && ./mvnw test` y `cd Frontend && npm test` en cada avance.

### Sprint 3 — E2E y experiencia de usuario (2 días)
1. Con `playwright-cli`, recorrer F1→F5 en navegador real contra backend local.
2. Capturar evidencia de los estados de error y vacíos de cada flujo; comparar mensajes de API con lo que ve el usuario.
3. Verificar teclado/foco, móvil 375px y contraste en formularios de login, checkout y carrito.
4. Registrar defectos UX en `kamban.md` con el mismo ID de flujo (F1-F6).

## 6. Criterios de salida

- Los 24 endpoints documentados + 6 brechas tienen al menos un caso positivo y uno negativo.
- Envoltorio `RespuestaApi` validado en todas las respuestas de los flujos F1-F6.
- 401 redirige a login y limpia el estado; 400/409 muestran mensaje accionable.
- Ningún módulo de UI presenta datos simulados como si fueran reales.
- `npm test` y `./mvnw test` en verde; resultados volcados en un informe único (K-02).
- Cada flujo revisado con criterio UX: estados de carga, vacío, error y confirmación documentados.

## 7. Trazabilidad con el kanban

K-01 (Postman), K-02 (informe único), K-03 (mocks), K-06/K-07 (tests fallidos), K-09 (controllers), K-11 (contrato openapi), K-12 (nodos centrales).

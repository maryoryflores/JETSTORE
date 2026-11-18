# Kanban — JetStore Digital (tablero QA)

> Generado el 2026-11-18. Fuente: mapa de Graphify (`1.991 nodos / 5.073 aristas / 94 comunidades`) + documentación del repo.
>
> Organización: tarjetas agrupadas por área; cada una con estado explícito, evidencia y criterio de aceptación.

## Leyenda de estados

| Estado | Nombre | Uso |
|--------|--------|-----|
| `PEN` | Pendiente | Sin iniciar; priorizada |
| `CUR` | En curso | Trabajándose ahora |
| `VER` | En verificación | Hecho, esperando validación QA |
| `BLO` | Bloqueado | Requiere desbloqueo antes de avanzar |
| `HE`  | Hecho | Verificado y cerrado |

**Regla para pasar a `HE`:** evidencia adjunta + criterio de aceptación cumplido + resultado registrado en el informe.

## Resumen

| Estado | Cantidad | Tarjetas |
|--------|:--------:|----------|
| `PEN` | 24 | K-01, K-02, K-03, K-04, K-05, K-06, K-07, K-08, K-17, K-18, K-20, K-21, K-23, K-24, K-25, K-27, K-28, K-29, K-30, K-31, K-32, K-33, K-34, K-35 |
| `CUR` | 0 | — |
| `VER` | 4 | K-09, K-10, K-11, K-12 |
| `BLO` | 1 | K-13 |
| `HE`  | 5 | K-14, K-15, K-16, K-19, K-22 |

---

## Área 1 — Infraestructura y herramientas

| ID | Tarea | Estado | Prio | Evidencia | Criterio de aceptación |
|----|-------|:------:|:----:|-----------|------------------------|
| K-04 | Reducir fragmentación arquitectónica | `PEN` | Media | 94 comunidades y 9 nodos aislados (`GRAPH_REPORT.md:417`) | Comunidades pequeñas revisadas; módulos a consolidar documentados |
| K-05 | Investigar salud del grafo | `PEN` | Media | 415 aristas colgantes, 2 self-loops, 195/197 colapsadas | Origen identificado y grafo regenerado sin warnings críticos |
| K-08 | Versionar configuración de agentes/skills y grafo | `PEN` | Alta | `git status`: `.agents/`, `.opencode/`, `AGENTS.md`, `opencode.json`, `skills-lock.json`, `graphify-out/` sin seguimiento | Decisión documentada (commitear o ignorar); working tree limpio |
| K-13 | Extracción semántica de docs/imágenes | `BLO` | Media | 4 subagentes cancelados; el grafo cubre solo AST | Reintentar en lotes de 1 archivo; excluir `Frontend/public/*` |
| K-18 | Completar fichas bibliográficas de los antecedentes | `PEN` | Media | `INFORME_FINAL_JETSTORE.md` (bibliografía con corchetes pendientes) | Referencias APA completas sin datos inventados |
| K-19 | Corregir rutas `Frontend/frontend` a `Frontend` | `HE` | Alta | `package.json`, `README.md`, `docker-compose.yml` | Scripts y compose ejecutables desde la raíz |
| K-22 | Reducir ruido de arranque del backend | `HE` | Media | `application.properties` (show-sql=false, open-in-view=false) | Sin logging SQL por defecto; `./mvnw test` en verde |
| K-23 | Resolver conflicto de puerto 3000 con Grafana | `PEN` | Alta | `Get-NetTCPConnection`: PID 6484 (grafana) en 3000; Next usó 3001 | Usuario accede a la URL correcta o libera el puerto |
| K-14 | Mapa de conocimiento del proyecto | `HE` | Alta | `graphify-out/graph.json`, `GRAPH_REPORT.md`, `graph.html` | 1.991 nodos / 5.073 aristas / 94 comunidades generados |

## Área 2 — Calidad y pruebas

| ID | Tarea | Estado | Prio | Evidencia | Criterio de aceptación |
|----|-------|:------:|:----:|-----------|------------------------|
| K-02 | Conciliar informes de pruebas desactualizados | `PEN` | Alta | `informe_test.md` (58 pruebas) vs `coverage_full.txt:843-844` (109 archivos / 591 pruebas) | Un único informe vigente y reproducible |
| K-06 | Verificar y corregir 28 pruebas frontend fallidas | `PEN` | Alta | `failures.txt:2081-2082`: 12 archivos fallidos / 445 pruebas | `cd Frontend && npm test` en verde |
| K-07 | Reparar tests de checkout y catálogo | `PEN` | Alta | `failures.txt:1106-2060`: `ProductInfo` 13 fallos, `ProductGrid` 6, `CheckoutForm`/`OrderSummary` 2 c/u | Selectores y mocks alineados; sin `vi.mock` obsoleto |
| K-09 | Elevar cobertura de controllers/repositories backend | `VER` | Media | `informe_test.md:50-51`: controller 9,1%, repository 1,3% | `@WebMvcTest` para 5 controllers; cobertura ≥50% |
| K-10 | Completar tests de `formatDate` / `formatDateShort` | `VER` | Baja | `informe_test.md:69`; `PLAN_PRUEBAS_UNITARIAS.md:353-360` | 2 casos es-PE en `format.test.ts` |
| K-12 | Revisar nodos centrales antes de refactorizar | `VER` | Media | God nodes: `vitest` 115, `@testing-library/react` 89, `search()` 60, `useAuthStore` 40 | Todo cambio en esos módulos lleva tests |
| K-15 | Suite frontend ampliada | `HE` | Alta | `coverage_full.txt:843-844`, `coverage3.txt:450-451` | 109 archivos / 591 pruebas |
| K-16 | Plan de pruebas unitarias documentado | `HE` | Media | `main/PLAN_PRUEBAS_UNITARIAS.md` | 105 tests propuestos en 3 fases |

## Área 3 — Contratos y APIs

| ID | Tarea | Estado | Prio | Evidencia | Criterio de aceptación |
|----|-------|:------:|:----:|-----------|------------------------|
| K-01 | Exportar colección Postman | `PEN` | Media | `backend/README.md:183` | Archivo en `backend/` y README actualizado |
| K-11 | Validar `openapi.md` vs implementación | `VER` | Alta | `openapi.md:868-1316`; 6 endpoints usados por el frontend sin documentar | Contrato coherente con DTOs y controladores |
| K-17 | Ejecutar plan de pruebas de flujos API | `PEN` | Alta | `PLAN_PRUEBAS_FLUJOS_API.md` (6 flujos, 4 capas, 3 sprints) | 3 sprints completados y resultados en el informe único |
| K-20 | Validar tokens y roles en el servidor | `PEN` | Alta | `openapi.md:41`; sin filtros de seguridad en backend | Control de acceso server-side para ADMIN/CLIENTE |

## Área 4 — Producto y UX

| ID | Tarea | Estado | Prio | Evidencia | Criterio de aceptación |
|----|-------|:------:|:----:|-----------|------------------------|
| K-03 | Auditar datos mock en servicios | `PEN` | Alta | `report.service.ts:1-10`, `admin.service.ts:11` devuelven `Promise.resolve` | Inventario de mocks y plan para conectarlos a la API; ningún dato falso visible como real |
| K-21 | Implementar descarga autenticada de PDF y comprobante | `PEN` | Media | `DigitalLibrary.tsx:69-70`, `OrderConfirmation.tsx:83-85` usan `alert` de simulación | Descarga real con control de acceso y sin datos simulados |

---

## Área 5 — Integración Frontend ↔ Backend (auditoría QA)

Evidencia de la auditoría: `curl` contra `http://localhost:8080` con `Origin` distinto, revisión de `api.ts`, stores y controladores. Hallazgo crítico: **el backend responde 403 cuando el frontend corre en el puerto 3001** porque CORS solo permite `http://localhost:3000`.

| ID | Hallazgo / tarea | Estado | Prio | Evidencia | Criterio de aceptación |
|----|------------------|:------:|:----:|-----------|------------------------|
| K-24 | Ampliar CORS a los puertos reales del frontend | `PEN` | Alta | `WebConfig.java:16`; `curl Origin=http://localhost:3001` → 403 | Orígenes configurables por property; 3000 y 3001 responden 200 con ACAO |
| K-25 | El carrito enmascara errores del backend | `PEN` | Alta | `cart-store.ts:80-85,103-108,140-145` devuelve `true` tras fallo HTTP | Fallo HTTP muestra error al usuario y no modifica estado local |
| K-27 | Webhook de Mercado Pago incompleto y sin verificación | `PEN` | Alta | `PagoController.java:39-60`; confirma solo si llega `external_reference` | Confirma con `data.id`, valida firma y es idempotente |
| K-28 | `confirmar-mp` sin `mpPaymentId` marca pago COMPLETADO | `PEN` | Alta | `PagoServiceImpl.java:186-216`: validación MP es opcional | Rechazar confirmación sin ID de pago aprobado |
| K-29 | Manejador de errores no cubre todos los tipos | `PEN` | Media | `GlobalExceptionHandler.java:9-14`; `cerrar-sesion` sin header devuelve JSON Spring distinto | Toda respuesta de error usa `{ok,mensaje,payload}` |
| K-30 | Servicios duplicados `orderService` y `paymentService` | `PEN` | Baja | `order.service.ts:9-21` vs `payment.service.ts:35-47` | Un único servicio de pagos reutilizado por el checkout |
| K-31 | Posible recursión del interceptor 401 con `logout()` | `PEN` | Media | `api.ts:28-31` llama `logout()`; `auth-store.ts:77-87` vuelve a pedir API | Guardia de reentrada antes de reintentar logout |
| K-32 | Confirmación muestra método de pago fijo | `PEN` | Media | `SuccessClient.tsx:24-26` siempre "Visa **** 4242" | Muestra el método realmente seleccionado |
| K-33 | Rewrite `/api/*` definido pero no utilizado | `PEN` | Baja | `next.config.ts:12-19` vs servicios con URL absoluta | Eliminarlo o usarlo de forma consistente |
| K-34 | Falta `MERCADOPAGO_WEBHOOK_SECRET` en entorno | `PEN` | Media | `application.properties:22` vs `.env.example`/compose | Variable documentada y propagada al contenedor |
| K-35 | Sin reintentos/timeout adaptado para llamadas a MP | `PEN` | Baja | `api.ts:6` timeout fijo de 15 s | Estrategia de reintento y timeout para preferencias |

### Plan de acción de integración

1. **Bloqueantes (K-24, K-25, K-27, K-28):** corregir CORS, dejar de ocultar fallos del carrito y asegurar que ningún pago se confirme sin aprobación verificable.
2. **Seguridad y contrato (K-20, K-29, K-31, K-34):** validación server-side, errores uniformes, evitar recursión y cerrar configuración del webhook.
3. **Calidad y UX (K-30, K-32, K-33, K-35):** unificar servicios, mostrar el método real, limpiar configuración y tolerar latencia de la pasarela.

## Planes de acción (enlazados desde este tablero)

| Plan | Sprints / fases | Tarjetas |
|------|-----------------|----------|
| `main/PLAN_PRUEBAS_UNITARIAS.md` | F1 Services, F2 Controllers, F3 Helpers/Models/DTOs | K-06, K-07, K-09, K-10 |
| `PLAN_PRUEBAS_FLUJOS_API.md` | S1 Contrato, S2 Integración, S3 E2E/UX | K-01, K-03, K-11, K-17 |
| Plan de mapa (este tablero) | S1 Estabilización, S2 Cobertura, S3 Calidad del mapa | K-02, K-04, K-05, K-08, K-13 |

### Orden de ejecución recomendado (QA)

1. **Estabilizar:** K-06 → K-07 → K-02 (obtener verdad reproducible antes de medir).
2. **Contratos:** K-01 → K-11 → K-17 (cerrar brechas de `openapi.md` y correr los 6 flujos).
3. **Cobertura:** K-09 → K-10 → K-12.
4. **Salud del mapa:** K-13 → K-05 → K-04 → K-08.
5. **Integración (bloqueantes):** K-24 → K-28 → K-27 → K-25, luego K-20 y K-29.

## Comandos útiles

```bash
cd Frontend && npm test
cd ../backend && ./mvnw test

graphify query "<pregunta>"
graphify path "<nodo A>" "<nodo B>"
graphify explain "<concepto>"
graphify god-nodes --top 20
```

## Notas del mapeo

- El grafo se construyó solo con AST: 402 archivos de código; 42 documentos y 23 imágenes quedaron fuera.
- Health check: `415 dangling-endpoint edges`, `2 self-loop edges`, `195/197 collapsed edges`.
- Los informes de julio 2026 son anteriores al estado actual del repo; `coverage_full.txt` y `coverage3.txt` son la evidencia más reciente.

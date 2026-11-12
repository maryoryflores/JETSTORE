# Pruebas Unitarias — JetStore Digital

Documentación de la suite de pruebas unitarias del proyecto **JetStore Digital** (tienda de libros digitales y físicos).

## Equipo y responsables

| Integrante | Rol | Capa | Pruebas asignadas |
|------------|-----|------|-------------------|
| Daniel | Backend | API REST (Spring Boot) | 10 |
| Maryory | QA | Validación de datos / modelo (Backend) | 10 |
| Lorena | Frontend | Validadores de formularios (Next.js) | 10 |
| Lucinda | Frontend | Utilidades de formato y UI (Next.js) | 10 |

**Requisito del curso:** 10 pruebas unitarias por integrante (4 × 10 = 40).
**Entregado:** 58 pruebas (38 en backend + 20 en frontend), superando el requisito.

Las pruebas del backend ya existían en el repositorio; durante este trabajo se **corrigieron** para que compilen contra el código fuente actual (los DTO `GuardarLibroDTO` y `VerLibroDTO` cambiaron su cantidad de campos) y se **añadieron** las 20 pruebas del frontend desde cero.

---

## 1. Cómo ejecutar las pruebas

### Backend (JUnit 5 + Mockito)

Requiere Java 17+ y Maven (`./mvnw`).

```bash
cd backend
./mvnw test                    # ejecuta las pruebas
./mvnw test jacoco:report      # ejecuta pruebas + genera reporte de cobertura
```

- Framework: **JUnit Jupiter 5** + **Mockito** (`@ExtendWith(MockitoExtension.class)`).
- Las pruebas son unitarias puras: los repositorios se simulan con `@Mock`, la lógica de servicio se inyecta con `@InjectMocks`. No requieren base de datos ni levantar el contexto de Spring.
- Cobertura: **JaCoCo 0.8.12** (configurado en `pom.xml`).
- Archivos de test en: `backend/src/test/java/com/idasta/jetstore/`
- Reporte de cobertura en: `backend/target/site/jacoco/index.html`

### Frontend (Vitest)

Requiere Node.js 18+ (recomendado 22+).

```bash
cd Frontend/frontend
npm install
npm test                       # ejecuta todas las pruebas (vitest run)
npm run test:watch             # modo interactivo
npm run test:coverage          # ejecuta pruebas + genera reporte de cobertura
```

- Framework: **Vitest 2** + **Testing Library** + **jsdom**.
- Cobertura: **Istanbul** (configurado en `vitest.config.ts`).
- Configuración: `vitest.config.ts` (alias `@` → `src`) y `src/test/setup.ts`.
- Archivos de test en: `src/utils/__tests__/`
- **Resultado actual:** 20/20 pruebas pasando.

### Ver resultados de cobertura

**Backend** — Después de ejecutar `./mvnw test jacoco:report`:
```bash
# Ver reporte HTML en el navegador:
start backend/target/site/jacoco/index.html

# Ver CSV en terminal:
type backend/target/site/jacoco/jacoco.csv
```

**Frontend** — Después de ejecutar `npm run test:coverage`:
```bash
# Ver reporte HTML en el navegador:
start Frontend/frontend/coverage/index.html
```

El output en terminal muestra una tabla como esta:
```
% Coverage report from istanbul
-------------------|---------|----------|---------|---------|-------------------
File               | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s
-------------------|---------|----------|---------|---------|-------------------
All files          |    1.76 |     1.07 |    1.93 |    1.68 |
 utils             |   85.71 |       80 |   84.61 |    87.5 |
  cn.ts            |     100 |      100 |     100 |     100 |
  format.ts        |   80.95 |       75 |      80 |   83.33 | 10-18,35
  validators.ts    |     100 |      100 |     100 |     100 |
-------------------|---------|----------|---------|---------|-------------------
```

---

## 2. Detalle de las pruebas por integrante

### Daniel — Backend (10 pruebas) — Lógica de servicios

Capa de servicios (`*ServiceImpl`): valida el comportamiento de registro, login, carrito y pago.

| # | Clase | Método de prueba | Qué valida |
|---|-------|------------------|------------|
| 1 | `UsuarioServiceImplTest` | `registrar_creaUsuario` | Al registrar un usuario nuevo, se hashea el password y se guarda con el rol indicado. |
| 2 | `UsuarioServiceImplTest` | `registrar_lanzaSiUsuarioExiste` | Lanza `IllegalArgumentException` si el nombre de usuario ya existe. |
| 3 | `UsuarioServiceImplTest` | `registrar_lanzaSiRolNoExiste` | Lanza `IllegalArgumentException` si el rol no está registrado. |
| 4 | `UsuarioServiceImplTest` | `login_lanzaSiPasswordIncorrecto` | Lanza excepción si la contraseña no coincide con el hash. |
| 5 | `CarritoServiceImplTest` | `agregar_creaCarritoSiNoExiste` | Crea el carrito si el usuario no tiene uno y agrega el item. |
| 6 | `CarritoServiceImplTest` | `agregar_acumulaSiLibroYaEnCarrito` | Si el libro ya está, suma cantidades (1 + 3 = 4). |
| 7 | `CarritoServiceImplTest` | `ver_lanzaExcepcionSiCarritoVacio` | Lanza excepción si el usuario no tiene carrito. |
| 8 | `CarritoServiceImplTest` | `eliminarItem_remueveYRetorna` | Elimina un item por `libroId` y retorna el carrito actualizado. |
| 9 | `PagoServiceImplTest` | `procesar_creaPagoPendiente` | Procesa un pago y crea un `Pago` en estado PENDIENTE con el monto total. |
| 10 | `PagoServiceImplTest` | `procesar_lanzaSiCarritoVacio` | Lanza `IllegalArgumentException` si no hay carrito. |

### Maryory — QA (10 pruebas) — Validación de datos, modelo y seguridad

DTOs, modelo de dominio y utilidad de hashing: la base de la calidad de datos.

| # | Clase | Método de prueba | Qué valida |
|---|-------|------------------|------------|
| 1 | `PasswordUtilTest` | `hash_retornaFormatoValido` | El hash tiene formato `salt:hash` (64 + 1 + 32 hex). |
| 2 | `PasswordUtilTest` | `verify_correcto` | `verify` es `true` para la contraseña correcta. |
| 3 | `PasswordUtilTest` | `verify_incorrecto` | `verify` es `false` para contraseña distinta. |
| 4 | `PasswordUtilTest` | `hashesDistintosMismaPassword` | Cada hash usa sal distinta (ainsi no son iguales). |
| 5 | `CarritoTest` | `agregarOActualizarItem_agregaNuevo` | Agrega un item nuevo al carrito. |
| 6 | `CarritoTest` | `agregarOActualizarItem_acumulaSiExiste` | Acumula cantidad si el libro ya existe (2 + 3 = 5). |
| 7 | `CarritoTest` | `eliminarItem_remuevePorLibroId` | Elimina el item correcto por `libroId`. |
| 8 | `CarritoTest` | `getTotal_sumaSubtotales` | El total suma subtotales (10×2 + 15×3 = 65). |
| 9 | `CarritoTest` | `getTotal_carritoVacio` | El total de un carrito vacío es 0. |
| 10 | `CarritoItemTest` | `getSubtotal_calculaPrecioPorCantidad` | `subtotal = precio × cantidad` (25 × 3 = 75). |

### Lorena — Frontend (10 pruebas) — Validadores de formularios

Archivo: `src/utils/__tests__/validators.test.ts`. Valida los esquemas **Zod** usados en los formularios de login, registro, checkout y perfil.

| # | Esquema | Caso | Qué valida |
|---|---------|------|------------|
| 1 | `loginSchema` | acepta credenciales válidas | `safeParse` exitoso. |
| 2 | `loginSchema` | rechaza usuario vacío | Falla si `nombreUsuario` está vacío. |
| 3 | `loginSchema` | rechaza password < 6 | Falla si la contraseña tiene menos de 6 caracteres. |
| 4 | `registerSchema` | acepta registro válido | `safeParse` exitoso. |
| 5 | `registerSchema` | rechaza passwords no coinciden | Falla si `password ≠ confirmPassword`. |
| 6 | `registerSchema` | rechaza correo inválido | Falla si `correo` no es un email. |
| 7 | `registerSchema` | rechaza usuario < 3 | Falla si `nombreUsuario` tiene menos de 3 caracteres. |
| 8 | `checkoutSchema` | acepta formulario válido | `safeParse` exitoso con todos los campos. |
| 9 | `checkoutSchema` | rechaza email inválido | Falla si el email de pago no es válido. |
| 10 | `profileSchema` | rechaza correo inválido | Falla si el correo del perfil no es válido. |

### Lucinda — Frontend (10 pruebas) — Utilidades de formato y UI

Archivos: `src/utils/__tests__/format.test.ts` (9) y `src/utils/__tests__/cn.test.ts` (1). Funciones puras de presentación.

| # | Función | Caso | Qué valida |
|---|---------|------|------------|
| 1 | `formatCurrency` | soles peruanos | Formatea `25.5` como `S/ 25.50` (es-PE, PEN). |
| 2 | `formatCardNumber` | agrupa en 4 | `4111111111111111` → `4111 1111 1111 1111`. |
| 3 | `formatExpiryDate` | inserta barra | `1228` → `12/28`. |
| 4 | `getCardType` | visa | Prefijo `4` → `visa`. |
| 5 | `getCardType` | mastercard | Prefijo `51-55` → `mastercard`. |
| 6 | `getCardType` | unknown | Otro prefijo → `unknown`. |
| 7 | `truncateText` | recorta texto | Agrega `...` cuando supera el límite. |
| 8 | `getInitials` | iniciales | `Daniel Rojas` → `DR`. |
| 9 | `calculateDiscount` | porcentaje | `(100-75)/100` → `25`. |
| 10 | `cn` | une clases | Une strings y omite valores `false`/`null`/`undefined`. |

---

## 3. Cobertura adicional del backend (17 pruebas)

Pruebas de la suite base que complementan las asignadas arriba (todas en `backend/src/test`):

- **`AgregarAlCarritoDTOTest`** (3): normaliza cantidad 0, negativa y válida del DTO de carrito.
- **`GuardarLibroDTOTest`** (5): normaliza precio `null`/negativo/positivo y stock negativo/válido del DTO de libro.
- **`LibroServiceImplTest`** (5): asigna categoría genérica, crea categoría nueva, reusa categoría existente, delega listado y filtrado de libros.
- **`PagoServiceImplTest`** (4 restantes): `procesar_lanzaSiMetodoInvalido`, `confirmar_creaVentaYCompletaPago`, `confirmar_lanzaSiPagoNoExiste`, `confirmar_lanzaSiPagoYaCompletado`.

---

## 4. Funcionalidad probada (visión general)

- **Seguridad de contraseñas** (`PasswordUtil`): hashing con sal aleatoria y verificación segura.
- **Integridad del carrito** (`Carrito`, `CarritoItem`): agregar/acumular/eliminar items y cálculo de totales y subtotales.
- **Reglas de negocio de usuarios** (`UsuarioService`): registro con validación de unicidad y rol, login con verificación de password.
- **Catálogo** (`LibroService`): gestión de categorías (genérica / nueva / existente) y filtrado.
- **Flujo de pago** (`PagoService`): creación de pago pendiente, validación de método, confirmación que genera la venta y limpia el carrito.
- **Validación de formularios del cliente** (Zod): login, registro, checkout y perfil.
- **Presentación** (utilidades): formato de moneda, tarjeta, fechas, descuentos e iniciales.

---

## 5. Resultados de cobertura (13 de julio de 2026)

### Backend — JaCoCo

| Métrica | Cubierto | Total | Cobertura |
|---------|:--------:|:-----:|:---------:|
| Instrucciones | 2,111 | 3,109 | **67.9%** |
| Líneas | 346 | 558 | **62.0%** |
| Ramas (branches) | 37 | 92 | **40.2%** |
| Métodos | 83 | 161 | **51.6%** |

**Cobertura por paquete:**

| Paquete | Cobertura | Descripción |
|---------|:---------:|-------------|
| `config` | **100%** | Configuración CORS |
| `dto` | **89.5%** | Objetos de transferencia de datos |
| `helper` | **87.4%** | Utilidades (PasswordUtil, Mapear, etc.) |
| `model` | **77.4%** | Entidades JPA (Carrito, Venta, etc.) |
| `service` | **70.3%** | Lógica de negocio |
| `application` | **37.5%** | Punto de entrada Spring Boot |
| `controller` | **9.1%** | Controladores REST |
| `repository` | **1.3%** | Repositorios JPA |

### Frontend — Vitest (Istanbul)

| Métrica | Cubierto | Total | Cobertura |
|---------|:--------:|:-----:|:---------:|
| Statements | 24 | 1,361 | **1.8%** |
| Lines | 21 | 1,243 | **1.7%** |
| Branches | 8 | 746 | **1.1%** |
| Functions | 11 | 569 | **1.9%** |

**Cobertura por archivo (con cobertura > 0%):**

| Archivo | Statements | Branches | Functions | Lines |
|---------|:----------:|:--------:|:---------:|:-----:|
| `utils/cn.ts` | 100% | 100% | 100% | 100% |
| `utils/validators.ts` | 100% | 100% | 100% | 100% |
| `utils/format.ts` | 81% | 75% | 80% | 83.3% |

---

## 6. Notas técnicas

- Las pruebas del backend usan **Mockito** para aislar la lógica de servicio de la base de datos; por eso corren rápido y sin PostgreSQL.
- Las pruebas del frontend usan **Vitest** con entorno `jsdom`; como evalúan funciones puras (sin componentes React), no requieren renderización.
- Script `test` y `test:coverage` agregados a `Frontend/frontend/package.json`.
- Plugin **JaCoCo 0.8.12** agregado a `backend/pom.xml` para medir cobertura.
- Configuración de cobertura **Istanbul** agregada a `vitest.config.ts`.

---

## 7. Documentos generados

| Archivo | Ubicación | Contenido |
|---------|-----------|-----------|
| Plan de pruebas | `main/PLAN_PRUEBAS_UNITARIAS.md` | Plan detallado de 105 tests nuevos a crear |
| Informe de tests | `main/informe_test.md` | Informe ejecutivo con resultados y cobertura |
| Reporte de tests | `main/reporte_test.md` | Reporte técnico con análisis por clase |

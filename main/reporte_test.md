# Reporte de Pruebas Unitarias — JetStore Digital

---

## 1. Información General

| Campo | Valor |
|-------|-------|
| **Proyecto** | JetStore Digital |
| **Tipo** | Tienda de libros digitales y físicos |
| **Backend** | Java 17 / Spring Boot 4.0.7-SNAPSHOT / Maven |
| **Frontend** | Next.js 16.2.9 / React 19.2.4 / TypeScript 5 |
| **Base de datos** | PostgreSQL |
| **Fecha de ejecución** | 13 de julio de 2026 |
| **Entorno de ejecución** | Windows (PowerShell 7+) |

---

## 2. Herramientas de Testing Utilizadas

### Backend

| Herramienta | Versión | Uso |
|-------------|---------|-----|
| JUnit Jupiter | 5 (via Spring Boot) | Framework de pruebas |
| Mockito | (via Spring Boot) | Mocking de repositorios |
| JaCoCo | 0.8.12 | Cobertura de código |
| Maven Surefire | (embebido) | Ejecución de tests |
| Spring Boot Test | 4.0.7 | Contexto de integración |

### Frontend

| Herramienta | Versión | Uso |
|-------------|---------|-----|
| Vitest | 2.1.9 | Framework de pruebas |
| Istanbul | 2.1.9 | Cobertura de código |
| Testing Library | 16.3.2 | Testing de componentes React |
| jest-dom | 6.9.1 | Matchers DOM personalizados |
| jsdom | 29.1.1 | Entorno de simulación del navegador |

---

## 3. Comandos de Ejecución

### Backend

```bash
cd backend
./mvnw test jacoco:report
```

**Salida:**
```
Tests run: 38, Failures: 0, Errors: 0, Skipped: 0
BUILD SUCCESS
Total time: 8.76 s
```

### Frontend

```bash
cd Frontend/frontend
npm test                    # sin cobertura
npx vitest run --coverage   # con cobertura
```

**Salida:**
```
Test Files  3 passed (3)
     Tests  20 passed (20)
  Duration  2.67s
```

---

## 4. Resultados Backend — Detalle por Archivo de Test

### 4.1 `JetstoreApplicationTests.java` (1 prueba)

| Método | Estado | Tiempo | Tipo |
|--------|:------:|:------:|------|
| `contextLoads()` | PASÓ | 5.515 s | Integración |

> Prueba de humo: verifica que el contexto de Spring Boot carga correctamente. Requiere PostgreSQL.

### 4.2 `LibroServiceImplTest.java` (5 pruebas)

| # | Método | Estado | Descripción |
|---|--------|:------:|-------------|
| 1 | `guardar_conCategoriaNull_asignaGenerica` | PASÓ | Si `categoria == null`, asigna "Generica" |
| 2 | `guardar_conCategoriaNueva_laCrea` | PASÓ | Si la categoría no existe, la crea |
| 3 | `guardar_conCategoriaExistente_laReusa` | PASÓ | Si la categoría existe, la reutiliza |
| 4 | `verLibros_delegaAlRepo` | PASÓ | Delega listado al repositorio |
| 5 | `filtrarLibros_delegaAlRepoYTransforma` | PASÓ | Filtra y transforma Libro → VerLibroDTO |

**Mocks:** `LibroRepo`
**Patrones:** `ArgumentCaptor`, `when().thenReturn()`, `verify()`

### 4.3 `UsuarioServiceImplTest.java` (4 pruebas)

| # | Método | Estado | Descripción |
|---|--------|:------:|-------------|
| 1 | `registrar_creaUsuario` | PASÓ | Registra, hashea password, guarda con rol |
| 2 | `registrar_lanzaSiUsuarioExiste` | PASÓ | Excepción si usuario ya existe |
| 3 | `registrar_lanzaSiRolNoExiste` | PASÓ | Excepción si rol no encontrado |
| 4 | `login_lanzaSiPasswordIncorrecto` | PASÓ | Excepción si password incorrecto |

**Mocks:** `UsuarioRepo`, `RolRepo`, `SesionRepo`
**Patrones:** `assertThrows()`, `ArgumentCaptor`

### 4.4 `CarritoServiceImplTest.java` (4 pruebas)

| # | Método | Estado | Descripción |
|---|--------|:------:|-------------|
| 1 | `agregar_creaCarritoSiNoExiste` | PASÓ | Crea carrito nuevo + item |
| 2 | `agregar_acumulaSiLibroYaEnCarrito` | PASÓ | Acumula cantidades (1+3=4) |
| 3 | `ver_lanzaExcepcionSiCarritoVacio` | PASÓ | Excepción si no hay carrito |
| 4 | `eliminarItem_remueveYRetorna` | PASÓ | Elimina item, retorna carrito |

**Mocks:** `CarritoRepo`, `LibroRepo`, `UsuarioRepo`
**Patrones:** `ArgumentCaptor`, `when().thenAnswer()`

### 4.5 `PagoServiceImplTest.java` (6 pruebas)

| # | Método | Estado | Descripción |
|---|--------|:------:|-------------|
| 1 | `procesar_creaPagoPendiente` | PASÓ | Crea Pago PENDIENTE con monto correcto |
| 2 | `procesar_lanzaSiCarritoVacio` | PASÓ | Excepción si carrito no existe |
| 3 | `procesar_lanzaSiMetodoInvalido` | PASÓ | Excepción con "BITCOIN" |
| 4 | `confirmar_creaVentaYCompletaPago` | PASÓ | Crea Venta, completa Pago, limpia carrito |
| 5 | `confirmar_lanzaSiPagoNoExiste` | PASÓ | Excepción si pago no encontrado |
| 6 | `confirmar_lanzaSiPagoYaCompletado` | PASÓ | IllegalStateException si ya completado |

**Mocks:** `CarritoRepo`, `PagoRepo`, `VentaRepo`
**Patrones:** `ArgumentCaptor`, helper `crearCarritoConItems()`

### 4.6 `CarritoTest.java` (5 pruebas)

| # | Método | Estado | Descripción |
|---|--------|:------:|-------------|
| 1 | `agregarOActualizarItem_agregaNuevo` | PASÓ | Item nuevo → size=1, cantidad=2 |
| 2 | `agregarOActualizarItem_acumulaSiExiste` | PASÓ | Mismo libro → cantidad=5 (2+3) |
| 3 | `eliminarItem_remuevePorLibroId` | PASÓ | Elimina item 1, queda item 2 |
| 4 | `getTotal_sumaSubtotales` | PASÓ | Total = 10×2 + 15×3 = 65 |
| 5 | `getTotal_carritoVacio` | PASÓ | Total = BigDecimal.ZERO |

**Mocking:** Ninguno (dominio puro)

### 4.7 `CarritoItemTest.java` (1 prueba)

| # | Método | Estado | Descripción |
|---|--------|:------:|-------------|
| 1 | `getSubtotal_calculaPrecioPorCantidad` | PASÓ | subtotal = 25 × 3 = 75 |

### 4.8 `PasswordUtilTest.java` (4 pruebas)

| # | Método | Estado | Descripción |
|---|--------|:------:|-------------|
| 1 | `hash_retornaFormatoValido` | PASÓ | Formato `salt:hash`, 97 caracteres |
| 2 | `verify_correcto` | PASÓ | Password correcto → true |
| 3 | `verify_incorrecto` | PASÓ | Password incorrecto → false |
| 4 | `hashesDistintosMismaPassword` | MISMA_PASSWORD | Cada hash tiene sal diferente |

### 4.9 `GuardarLibroDTOTest.java` (5 pruebas)

| # | Método | Estado | Descripción |
|---|--------|:------:|-------------|
| 1 | `constructor_normalizaPrecioNull` | PASÓ | null → BigDecimal.ZERO |
| 2 | `constructor_normalizaPrecioNegativo` | PASÓ | -10 → BigDecimal.ZERO |
| 3 | `constructor_normalizaPrecioPositivo` | PASÓ | 25.5 → 25.50 (2 decimales) |
| 4 | `constructor_normalizaStockNegativo` | PASÓ | -3 → 0 |
| 5 | `constructor_mantieneStockValido` | PASÓ | 7 → 7 |

### 4.10 `AgregarAlCarritoDTOTest.java` (3 pruebas)

| # | Método | Estado | Descripción |
|---|--------|:------:|-------------|
| 1 | `constructor_normalizaCantidadCero` | PASÓ | 0 → 1 |
| 2 | `constructor_normalizaCantidadNegativa` | PASÓ | -5 → 1 |
| 3 | `constructor_mantieneCantidadValida` | PASÓ | 3 → 3 |

---

## 5. Resultados Frontend — Detalle por Archivo de Test

### 5.1 `validators.test.ts` (10 pruebas)

| # | Esquema | Caso | Estado | Descripción |
|---|---------|------|:------:|-------------|
| 1 | `loginSchema` | credenciales válidas | PASÓ | safeParse success = true |
| 2 | `loginSchema` | usuario vacío | PASÓ | safeParse success = false |
| 3 | `loginSchema` | password < 6 | PASÓ | safeParse success = false |
| 4 | `registerSchema` | registro válido | PASÓ | safeParse success = true |
| 5 | `registerSchema` | passwords no coinciden | PASÓ | safeParse success = false |
| 6 | `registerSchema` | correo inválido | PASÓ | safeParse success = false |
| 7 | `registerSchema` | usuario < 3 | PASÓ | safeParse success = false |
| 8 | `checkoutSchema` | formulario válido | PASÓ | safeParse success = true |
| 9 | `checkoutSchema` | email inválido | PASÓ | safeParse success = false |
| 10 | `profileSchema` | correo inválido | PASÓ | safeParse success = false |

### 5.2 `format.test.ts` (9 pruebas)

| # | Función | Caso | Estado | Descripción |
|---|---------|------|:------:|-------------|
| 1 | `formatCurrency` | soles peruanos | PASÓ | 25.5 → "S/ 25.50" |
| 2 | `formatCardNumber` | agrupa en 4 | PASÓ | "4111..." → "4111 1111 1111 1111" |
| 3 | `formatExpiryDate` | inserta barra | PASÓ | "1228" → "12/28" |
| 4 | `getCardType` | visa | PASÓ | → "visa" |
| 5 | `getCardType` | mastercard | PASÓ | → "mastercard" |
| 6 | `getCardType` | unknown | PASÓ | → "unknown" |
| 7 | `truncateText` | recorta texto | PASÓ | + "..." |
| 8 | `getInitials` | iniciales | PASÓ | → "DR" |
| 9 | `calculateDiscount` | porcentaje | PASÓ | → 25 |

### 5.3 `cn.test.ts` (1 prueba)

| # | Función | Caso | Estado | Descripción |
|---|---------|------|:------:|-------------|
| 1 | `cn` | une clases | PASÓ | Filtra falsy y une con espacio |

---

## 6. Cobertura de Código — Análisis Detallado

### 6.1 Backend (JaCoCo)

#### Resumen General

| Métrica | Cubierto | Total | Porcentaje |
|---------|:--------:|:-----:|:----------:|
| Instrucciones | 2,111 | 3,109 | **67.9%** |
| Líneas | 346 | 558 | **62.0%** |
| Ramas | 37 | 92 | **40.2%** |
| Métodos | 83 | 161 | **51.6%** |

#### Cobertura por Paquete

| Paquete | Instrucciones | Líneas | Ramas | Métodos | Cobertura |
|---------|:-------------:|:------:|:-----:|:-------:|:---------:|
| `config` | 60/60 | 10/10 | 0/0 | 4/4 | **100%** |
| `dto` | 248/277 | 14/16 | 8/8 | 14/16 | **89.5%** |
| `helper` | 1,000/1,144 | 79/101 | 12/16 | 26/31 | **87.4%** |
| `model` | 206/266 | 32/51 | 2/6 | 25/33 | **77.4%** |
| `service` | 561/798 | 146/201 | 7/16 | 26/37 | **70.3%** |
| `application` | 3/8 | 1/3 | 0/0 | 1/2 | **37.5%** |
| `controller` | 30/331 | 3/37 | 0/6 | 5/31 | **9.1%** |
| `repository` | 3/225 | 1/54 | 0/18 | 1/12 | **1.3%** |

#### Detalle por Clase (top cobertura)

| Clase | Instrucciones | Cobertura | Notas |
|-------|:-------------:|:---------:|-------|
| `DataSeeder` | 891/901 | **98.9%** | Seeder de datos iniciales |
| `PagoServiceImpl` | 226/231 | **97.8%** | Mejor cubierto de services |
| `CarritoServiceImpl` | 139/161 | **86.3%** | Carrito con mocks |
| `PasswordUtil` | 66/79 | **83.5%** | Hashing SHA-256 |
| `Carrito` (model) | 76/80 | **95.0%** | Entidad de dominio |
| `Venta` (model) | 42/48 | **87.5%** | Entidad de dominio |
| `Mapear` | 40/43 | **93.0%** | Mapper DTO→Entity |
| `LibroServiceImpl` | 93/225 | **41.3%** | Parcialmente cubierto |
| `UsuarioServiceImpl` | 103/181 | **56.9%** | Parcialmente cubierto |
| `LibroController` | 6/116 | **5.2%** | Sin tests de controller |
| `LibroRepoImpl` | 3/225 | **1.3%** | Custom queries sin tests |

### 6.2 Frontend (Vitest Istanbul)

#### Resumen General

| Métrica | Cubierto | Total | Porcentaje |
|---------|:--------:|:-----:|:----------:|
| Statements | 24 | 1,361 | **1.8%** |
| Lines | 21 | 1,243 | **1.7%** |
| Branches | 8 | 746 | **1.1%** |
| Functions | 11 | 569 | **1.9%** |

#### Cobertura por Archivo (archivos con cobertura > 0%)

| Archivo | Statements | Branches | Functions | Lines | Cobertura |
|---------|:----------:|:--------:|:---------:|:-----:|:---------:|
| `utils/cn.ts` | 100% | 100% | 100% | 100% | **100%** |
| `utils/validators.ts` | 100% | 100% | 100% | 100% | **100%** |
| `utils/format.ts` | 81.0% | 75% | 80% | 83.3% | **81%** |

#### Archivos con 0% de cobertura

| Categoría | Archivos | Total |
|-----------|:--------:|:-----:|
| `app/` (páginas) | 22 | 22 |
| `components/` | 59 | 59 |
| `services/` | 9 | 9 |
| `store/` | 4 | 4 |
| `data/` | 8 | 8 |
| `types/` | 1 | 1 |

---

## 7. Análisis de Brechas de Cobertura

### 7.1 Backend — Áreas sin cobertura

| Área | Archivos | Cobertura | Impacto |
|------|----------|:---------:|---------|
| Controllers | 5 | 9.1% | Alto — No se valida HTTP request/response |
| Repositories | 10 | 1.3% | Medio — Queries custom sin tests |
| `Validar.java` | 1 | 0% | Medio — Validación de DTO sin tests |
| `Util.java` | 1 | 0% | Bajo — Utilidades simples |
| `Jetstore.java` | 1 | 0% | Bajo — Config file reader |

### 7.2 Frontend — Áreas sin cobertura

| Área | Archivos | Cobertura | Impacto |
|------|----------|:---------:|---------|
| Services (API) | 9 | 0% | Alto — Comunicación HTTP sin tests |
| Stores (Zustand) | 4 | 0% | Alto — Estado de aplicación sin tests |
| Components (UI) | 59 | 0% | Medio — Componentes React sin tests |
| Pages | 22 | 0% | Medio — Rutas Next.js sin tests |

---

## 8. Patrones de Testing Aplicados

### Backend

| Patrón | Descripción | Ejemplo |
|--------|-------------|---------|
| `@ExtendWith(MockitoExtension.class)` | Aislamiento de servicios | Todos los `*ServiceImplTest` |
| `@Mock` + `@InjectMocks` | Inyección de dependencias simuladas | Repos mockeados, services reales |
| `ArgumentCaptor` | Capturar argumentos de `save()` | Verificar entidad guardada |
| `when().thenAnswer()` | Retornar el argumento recibido | Simular `repo.save()` |
| `assertThrows()` | Verificar excepciones | Error paths (4 tests) |
| `@SpringBootTest` | Contexto completo | `JetstoreApplicationTests` |
| Tests puros de dominio | Sin mocking | `CarritoTest`, `CarritoItemTest` |
| Tests de validación DTO | Compact constructors | `GuardarLibroDTOTest` |

### Frontend

| Patrón | Descripción | Ejemplo |
|--------|-------------|---------|
| `safeParse()` de Zod | Validar esquemas sin excepciones | `validators.test.ts` |
| Funciones puras | Input → Output directo | `format.test.ts`, `cn.test.ts` |
| `describe` + `it` | Organización BDD | Todos los archivos |

---

## 9. Cumplimiento del Requisito

| Requisito | Objetivo | Alcanzado | Estado |
|-----------|:--------:|:---------:|:------:|
| Pruebas por integrante | 10 | 10 | CUMPLE |
| Total pruebas | 40 | 58 | SUPERA |
| Backend pruebas | — | 38 | OK |
| Frontend pruebas | — | 20 | OK |
| Cobertura backend | — | 67.9% | Aceptable |
| Cobertura frontend | — | 1.8% | Baja (solo utils) |

---

## 10. Recomendaciones

### Prioridad Alta
1. **Tests de Controllers** (`@WebMvcTest`) — Solo 9.1% de cobertura en controllers
2. **Tests de Stores** (Zustand) — Estado de aplicación sin validación
3. **Tests de Services frontend** — Comunicación HTTP sin mocks

### Prioridad Media
4. **Tests de Components** (`@testing-library/react`) — 59 componentes sin tests
5. **Completar cobertura de Services backend** — LibroService y UsuarioService parciales
6. **Tests de `Validar.java`** — Lógica de validación sin cobertura

### Prioridad Baja
7. **Tests de Models con `@PrePersist`** — Hooks JPA sin tests directos
8. **Tests de `formatDate` y `formatDateShort`** — Utilidades de fecha sin cubrir
9. **Tests de repositories** — Queries JPQL personalizadas

---

*Reporte generado el 13 de julio de 2026.*
*Herramientas: JaCoCo 0.8.12 (backend), Vitest 2.1.9 + Istanbul (frontend).*

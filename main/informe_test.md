# Informe de Pruebas Unitarias — JetStore Digital

| Campo | Valor |
|-------|-------|
| **Proyecto** | JetStore Digital (Tienda de Libros) |
| **Fecha de ejecución** | 13 de julio de 2026 |
| **Backend** | Java 17 / Spring Boot 4.0.7 / JUnit 5 + Mockito |
| **Frontend** | Next.js 16 / React 19 / Vitest 2 + Testing Library |
| **Ejecutado por** | Daniel (Backend) |

---

## 1. Resumen Ejecutivo

| Métrica | Backend | Frontend | Total |
|---------|:-------:|:--------:|:-----:|
| Pruebas ejecutadas | 38 | 20 | **58** |
| Pruebas aprobadas | 38 | 20 | **58** |
| Pruebas fallidas | 0 | 0 | **0** |
| Pruebas omitidas | 0 | 0 | **0** |
| Tasa de éxito | 100% | 100% | **100%** |
| Tiempo de ejecución | 8.76 s | 2.67 s | **11.43 s** |
| Archivos de test | 10 | 3 | **13** |

**Resultado: TODAS LAS PRUEBAS PASARON EXITOSAMENTE.**

---

## 2. Cobertura de Código

### 2.1 Backend — JaCoCo

| Métrica | Cubierto | Total | Cobertura |
|---------|:--------:|:-----:|:---------:|
| Instrucciones | 2,111 | 3,109 | **67.9%** |
| Líneas | 346 | 558 | **62.0%** |
| Ramas (branches) | 37 | 92 | **40.2%** |
| Métodos | 83 | 161 | **51.6%** |

### Cobertura por Paquete (Backend)

| Paquete | Instrucciones | Cobertura | Descripción |
|---------|:-------------:|:---------:|-------------|
| `config` | 60/60 | **100%** | Configuración CORS |
| `dto` | 248/277 | **89.5%** | Objetos de transferencia de datos |
| `helper` | 1,000/1,144 | **87.4%** | Utilidades (PasswordUtil, Mapear, Validar, etc.) |
| `model` | 206/266 | **77.4%** | Entidades JPA (Carrito, Venta, Pago, etc.) |
| `service` | 561/798 | **70.3%** | Lógica de negocio |
| `application` | 3/8 | **37.5%** | Punto de entrada Spring Boot |
| `controller` | 30/331 | **9.1%** | Controladores REST |
| `repository` | 3/225 | **1.3%** | Repositorios JPA |

### 2.2 Frontend — Vitest (Istanbul)

| Métrica | Cubierto | Total | Cobertura |
|---------|:--------:|:-----:|:---------:|
| Statements | 24 | 1,361 | **1.8%** |
| Lines | 21 | 1,243 | **1.7%** |
| Branches | 8 | 746 | **1.1%** |
| Functions | 11 | 569 | **1.9%** |

### Cobertura por Módulo (Frontend)

| Módulo | Statements | Cobertura | Notas |
|--------|:----------:|:---------:|-------|
| `utils` | — | **85.7%** | Archivos con tests: cn.ts, format.ts, validators.ts |
| `utils/cn.ts` | — | **100%** | Completamente cubierto |
| `utils/validators.ts` | — | **100%** | Completamente cubierto |
| `utils/format.ts` | — | **81.0%** | Falta: formatDate, formatDateShort |
| `services` | — | **0%** | 9 archivos sin tests |
| `store` | — | **0%** | 4 archivos sin tests |
| `components` | — | **0%** | 59 archivos sin tests |
| `app` | — | **0%** | 22 páginas sin tests |
| `data` | — | **0%** | 8 archivos de datos mock |

---

## 3. Detalle de Pruebas por Integrante

### 3.1 Daniel — Backend (10 pruebas) — Lógica de Servicios

| # | Archivo | Método | Estado | Descripción |
|---|---------|--------|:------:|-------------|
| 1 | `UsuarioServiceImplTest` | `registrar_creaUsuario` | PASÓ | Verifica que el password se hashea y el usuario se guarda con su rol |
| 2 | `UsuarioServiceImplTest` | `registrar_lanzaSiUsuarioExiste` | PASÓ | Lanza `IllegalArgumentException` si el usuario ya existe |
| 3 | `UsuarioServiceImplTest` | `registrar_lanzaSiRolNoExiste` | PASÓ | Lanza `IllegalArgumentException` si el rol no existe |
| 4 | `UsuarioServiceImplTest` | `login_lanzaSiPasswordIncorrecto` | PASÓ | Lanza excepción si la contraseña es incorrecta |
| 5 | `CarritoServiceImplTest` | `agregar_creaCarritoSiNoExiste` | PASÓ | Crea carrito nuevo si el usuario no tiene uno |
| 6 | `CarritoServiceImplTest` | `agregar_acumulaSiLibroYaEnCarrito` | PASÓ | Acumula cantidades (1+3=4) si el libro ya está |
| 7 | `CarritoServiceImplTest` | `ver_lanzaExcepcionSiCarritoVacio` | PASÓ | Lanza excepción si no hay carrito |
| 8 | `CarritoServiceImplTest` | `eliminarItem_remueveYRetorna` | PASÓ | Elimina item por libroId y retorna carrito actualizado |
| 9 | `PagoServiceImplTest` | `procesar_creaPagoPendiente` | PASÓ | Crea pago PENDIENTE con monto total correcto |
| 10 | `PagoServiceImplTest` | `procesar_lanzaSiCarritoVacio` | PASÓ | Lanza excepción si no hay carrito |

### 3.2 Maryory — QA (10 pruebas) — Validación, Modelo y Seguridad

| # | Archivo | Método | Estado | Descripción |
|---|---------|--------|:------:|-------------|
| 1 | `PasswordUtilTest` | `hash_retornaFormatoValido` | PASÓ | Hash con formato `salt:hash` de 97 caracteres |
| 2 | `PasswordUtilTest` | `verify_correcto` | PASÓ | Verify retorna `true` para contraseña correcta |
| 3 | `PasswordUtilTest` | `verify_incorrecto` | PASÓ | Verify retorna `false` para contraseña incorrecta |
| 4 | `PasswordUtilTest` | `hashesDistintosMismaPassword` | PASÓ | Cada hash usa sal distinta |
| 5 | `CarritoTest` | `agregarOActualizarItem_agregaNuevo` | PASÓ | Agrega item nuevo al carrito |
| 6 | `CarritoTest` | `agregarOActualizarItem_acumulaSiExiste` | PASÓ | Acumula cantidad (2+3=5) |
| 7 | `CarritoTest` | `eliminarItem_remuevePorLibroId` | PASÓ | Elimina item correcto |
| 8 | `CarritoTest` | `getTotal_sumaSubtotales` | PASÓ | Total = 10×2 + 15×3 = 65 |
| 9 | `CarritoTest` | `getTotal_carritoVacio` | PASÓ | Total de carrito vacío es 0 |
| 10 | `CarritoItemTest` | `getSubtotal_calculaPrecioPorCantidad` | PASÓ | Subtotal = 25 × 3 = 75 |

### 3.3 Lorena — Frontend (10 pruebas) — Validadores de Formularios

| # | Esquema | Método | Estado | Descripción |
|---|---------|--------|:------:|-------------|
| 1 | `loginSchema` | acepta credenciales válidas | PASÓ | `safeParse` exitoso |
| 2 | `loginSchema` | rechaza usuario vacío | PASÓ | Falla si nombreUsuario está vacío |
| 3 | `loginSchema` | rechaza password < 6 | PASÓ | Falla si contraseña < 6 caracteres |
| 4 | `registerSchema` | acepta registro válido | PASÓ | `safeParse` exitoso |
| 5 | `registerSchema` | rechaza passwords no coinciden | PASÓ | Falla si password ≠ confirmPassword |
| 6 | `registerSchema` | rechaza correo inválido | PASÓ | Falla si correo no es email válido |
| 7 | `registerSchema` | rechaza usuario < 3 | PASÓ | Falla si nombreUsuario < 3 caracteres |
| 8 | `checkoutSchema` | acepta formulario válido | PASÓ | `safeParse` exitoso con todos los campos |
| 9 | `checkoutSchema` | rechaza email inválido | PASÓ | Falla si email no es válido |
| 10 | `profileSchema` | rechaza correo inválido | PASÓ | Falla si correo del perfil no es válido |

### 3.4 Lucinda — Frontend (10 pruebas) — Utilidades de Formato y UI

| # | Función | Método | Estado | Descripción |
|---|---------|--------|:------:|-------------|
| 1 | `formatCurrency` | soles peruanos | PASÓ | 25.5 → "S/ 25.50" (es-PE, PEN) |
| 2 | `formatCardNumber` | agrupa en 4 | PASÓ | "4111..." → "4111 1111 1111 1111" |
| 3 | `formatExpiryDate` | inserta barra | PASÓ | "1228" → "12/28" |
| 4 | `getCardType` | visa | PASÓ | Prefijo 4 → "visa" |
| 5 | `getCardType` | mastercard | PASÓ | Prefijo 51-55 → "mastercard" |
| 6 | `getCardType` | unknown | PASÓ | Otro prefijo → "unknown" |
| 7 | `truncateText` | recorta texto | PASÓ | Agrega "..." al superar límite |
| 8 | `getInitials` | iniciales | PASÓ | "Daniel Rojas" → "DR" |
| 9 | `calculateDiscount` | porcentaje | PASÓ | (100-75)/100 → 25 |
| 10 | `cn` | une clases | PASÓ | Une strings y omite falsy values |

---

## 4. Cobertura Adicional del Backend (17 pruebas)

Estas pruebas complementan las 20 asignadas a los integrantes:

| # | Archivo | Pruebas | Estado |
|---|---------|:-------:|:------:|
| 1 | `AgregarAlCarritoDTOTest` | 3 | PASÓ |
| 2 | `GuardarLibroDTOTest` | 5 | PASÓ |
| 3 | `LibroServiceImplTest` | 5 | PASÓ |
| 4 | `PagoServiceImplTest` (resto) | 4 | PASÓ |

---

## 5. Análisis de Cobertura por Capa

### Backend — Porcentaje de cobertura por componente

```
Config (WebConfig)     ████████████████████ 100%
DTOs                   ██████████████████░░  89.5%
Helpers                █████████████████░░░  87.4%
Models (entidades)     ███████████████░░░░░  77.4%
Services               ██████████████░░░░░░  70.3%
Application            ████████░░░░░░░░░░░░  37.5%
Controllers            ██░░░░░░░░░░░░░░░░░░   9.1%
Repositories           ░░░░░░░░░░░░░░░░░░░░   1.3%
```

### Frontend — Porcentaje de cobertura por módulo

```
Utils (cn)             ████████████████████ 100%
Utils (validators)     ████████████████████ 100%
Utils (format)         ████████████████░░░░  81%
Services               ░░░░░░░░░░░░░░░░░░░░   0%
Stores                 ░░░░░░░░░░░░░░░░░░░░   0%
Components             ░░░░░░░░░░░░░░░░░░░░   0%
Pages/App              ░░░░░░░░░░░░░░░░░░░░   0%
```

---

## 6. Archivos de Test Existentes

### Backend (10 archivos)

| Archivo | Paquete | Pruebas | Tipo |
|---------|---------|:-------:|------|
| `JetstoreApplicationTests.java` | raíz | 1 | Integración (context load) |
| `LibroServiceImplTest.java` | service | 5 | Unitario (Mockito) |
| `UsuarioServiceImplTest.java` | service | 4 | Unitario (Mockito) |
| `CarritoServiceImplTest.java` | service | 4 | Unitario (Mockito) |
| `PagoServiceImplTest.java` | service | 6 | Unitario (Mockito) |
| `CarritoTest.java` | model | 5 | Unitario (dominio) |
| `CarritoItemTest.java` | model | 1 | Unitario (dominio) |
| `PasswordUtilTest.java` | helper | 4 | Unitario (utility) |
| `GuardarLibroDTOTest.java` | dto | 5 | Unitario (DTO) |
| `AgregarAlCarritoDTOTest.java` | dto | 3 | Unitario (DTO) |

### Frontend (3 archivos)

| Archivo | Directorio | Pruebas | Tipo |
|---------|-----------|:-------:|------|
| `validators.test.ts` | utils/__tests__ | 10 | Unitario (Zod schemas) |
| `format.test.ts` | utils/__tests__ | 9 | Unitario (funciones puras) |
| `cn.test.ts` | utils/__tests__ | 1 | Unitario (utility) |

---

## 7. Conclusión

- **Todas las 58 pruebas pasaron exitosamente** (38 backend + 20 frontend).
- Se **cumple y supera** el requisito del curso de 40 pruebas (se entregaron 58).
- La **cobertura general del backend es del 67.9%** en instrucciones, con cobertura completa en DTOs (89.5%), helpers (87.4%) y modelos (77.4%).
- Los **services alcanzan 70.3%** de cobertura, lo cual es adecuado para lógica de negocio aislada con mocks.
- La **cobertura del frontend es baja (1.8%)** porque solo se testearon las 3 funciones utilitarias; componentes, stores y services no tienen tests unitarios.
- Las **pruebas del backend son unitarias puras** (Mockito, sin base de datos), lo que las hace rápidas y determinísticas.
- Las **pruebas del frontend son funciones puras** (Vitest, jsdom), sin interacción con DOM ni componentes React.

---

*Informe generado el 13 de julio de 2026.*

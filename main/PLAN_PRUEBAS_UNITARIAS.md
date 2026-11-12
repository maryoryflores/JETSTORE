# Plan de Pruebas Unitarias — JetStore Digital

Documento de planificación de pruebas unitarias del proyecto **JetStore Digital** (tienda de libros digitales y físicos).

---

## Tabla de Contenido

1. [Estado Actual](#1-estado-actual)
2. [Backend — Plan de Pruebas Nuevas](#2-backend--plan-de-pruebas-nuevas)
   - 2.1 Completar Services (13 tests)
   - 2.2 Controllers (22 tests)
   - 2.3 Helpers (10 tests)
   - 2.4 Models (12 tests)
   - 2.5 DTOs (3 tests)
3. [Frontend — Plan de Pruebas Nuevas](#3-frontend--plan-de-pruebas-nuevas)
   - 3.1 Completar Utils (2 tests)
   - 3.2 Stores (16 tests)
   - 3.3 Services (12 tests)
   - 3.4 Components (15 tests)
4. [Resumen General](#4-resumen-general)
5. [Estrategia y Priorización](#5-estrategia-y-priorización)

---

## 1. Estado Actual

| Capa | Archivos fuente | Archivos con tests | Casos de prueba |
|------|:--------------:|:------------------:|:--------------:|
| Backend — Services | 4 | 4 (parcial) | 24 |
| Backend — Models | 14 | 2 | 6 |
| Backend — Helpers | 7 | 1 | 4 |
| Backend — DTOs | 15 | 2 | 8 |
| Backend — Controllers | 5 | 0 | 0 |
| **Backend Total** | **45** | **9** | **42** |
| Frontend — Utils | 3 | 3 | 20 |
| Frontend — Services | 9 | 0 | 0 |
| Frontend — Stores | 4 | 0 | 0 |
| Frontend — Components | 59 | 0 | 0 |
| **Frontend Total** | **75** | **3** | **20** |
| **GRAN TOTAL** | **120** | **12** | **62** |

---

## 2. Backend — Plan de Pruebas Nuevas

**Stack de testing:** JUnit 5 (Jupiter) + Mockito (`@ExtendWith(MockitoExtension.class)`)
**Ejecutar:** `cd backend && ./mvnw test`
**Directorio de tests:** `backend/src/test/java/com/idasta/jetstore/`

---

### 2.1 Completar tests de Services (existentes, métodos faltantes)

#### 2.1.1 `LibroServiceImplTest.java` — agregar 7 tests

**Clase bajo prueba:** `LibroServiceImpl`
**Mocks:** `LibroRepo`

| # | Método de prueba | Qué valida | Tipo |
|---|-----------------|------------|------|
| 1 | `agregarStock_agregaCantidadAlLibro` | Suma `stock actual + cantidad del DTO`. Verifica que `repo.save()` se invoca con el libro cuyo stock es la suma. | Happy path |
| 2 | `agregarStock_lanzaSiLibroNoExiste` | `IllegalArgumentException("Libro no encontrado")` cuando `repo.findById()` retorna `Optional.empty()`. | Error path |
| 3 | `eliminar_libroExiste_loElimina` | Verifica que `repo.deleteById(libroId)` se invoca cuando `repo.existsById()` retorna `true`. | Happy path |
| 4 | `eliminar_libroNoExiste_lanzaExcepcion` | `IllegalArgumentException("Libro no encontrado")` cuando `repo.existsById()` retorna `false`. Verifica que `repo.deleteById()` NO se invoca. | Error path |
| 5 | `buscar_delegaAlRepoYTransforma` | Llama a `repo.buscarPorKeyword(keyword)` y mapea cada `Libro` a `VerLibroDTO` con los campos correctos. | Happy path |
| 6 | `obtenerPorId_libroExiste_retornaDTO` | Llama a `repo.obtenerLibroPorId(id)` y retorna el `VerLibroDTO` con todos los campos correctos. | Happy path |
| 7 | `obtenerPorId_libroNoExiste_lanzaExcepcion` | `IllegalArgumentException("Libro no encontrado")` cuando `repo.obtenerLibroPorId(id)` retorna `null`. | Error path |

**Patrón de implementación:**
```java
@ExtendWith(MockitoExtension.class)
class LibroServiceImplTest {
    @Mock LibroRepo repo;
    @InjectMocks LibroServiceImpl service;

    // Usar ArgumentCaptor para verificar argumentos de save()
    // Usar when(repo.findById(...)).thenReturn(Optional.empty()) para error paths
}
```

---

#### 2.1.2 `UsuarioServiceImplTest.java` — agregar 3 tests

**Clase bajo prueba:** `UsuarioServiceImpl`
**Mocks:** `UsuarioRepo`, `RolRepo`, `SesionRepo`

| # | Método de prueba | Qué valida | Tipo |
|---|-----------------|------------|------|
| 1 | `login_exito_creaSesionYRetornaToken` | Login exitoso: `repo.findByNombreUsuario()` retorna usuario, `PasswordUtil.verify()` retorna true, se crea `Sesion` con `sesionRepo.save()`, retorna `LoginResponseDTO` con token, id, nombre y rol. | Happy path |
| 2 | `obtenerPerfil_usuarioExiste_retornaPerfil` | `repo.findById()` retorna usuario, retorna `UsuarioPerfilDTO` con id, nombreUsuario, correo y rol correctos. | Happy path |
| 3 | `obtenerPerfil_usuarioNoExiste_lanzaExcepcion` | `IllegalArgumentException("Usuario no encontrado")` cuando `repo.findById()` retorna `Optional.empty()`. | Error path |

---

#### 2.1.3 `CarritoServiceImplTest.java` — agregar 2 tests

**Clase bajo prueba:** `CarritoServiceImpl`
**Mocks:** `CarritoRepo`, `LibroRepo`, `UsuarioRepo`

| # | Método de prueba | Qué valida | Tipo |
|---|-----------------|------------|------|
| 1 | `agregar_usuarioNoExiste_lanzaExcepcion` | `IllegalArgumentException("Usuario no encontrado")` cuando `usuarioRepo.findById()` retorna `Optional.empty()`. | Error path |
| 2 | `agregar_libroNoExiste_lanzaExcepcion` | `IllegalArgumentException("Libro no encontrado")` cuando `libroRepo.findById()` retorna `Optional.empty()`. | Error path |

---

#### 2.1.4 `PagoServiceImplTest.java` — agregar 1 test

**Clase bajo prueba:** `PagoServiceImpl`
**Mocks:** `CarritoRepo`, `PagoRepo`, `VentaRepo`

| # | Método de prueba | Qué valida | Tipo |
|---|-----------------|------------|------|
| 1 | `procesar_carritoSinItems_lanzaExcepcion` | `IllegalStateException("No hay libros en el carrito")` cuando el carrito existe pero `items.isEmpty()` es `true`. | Error path |

---

### 2.2 Tests de Controllers (nuevos)

**Framework:** `@WebMvcTest(XxxController.class)` + `@MockBean` para servicios/repos
**Patrón:** `MockMvc.perform(...)` para simular requests HTTP, verificar status codes y estructura de respuesta JSON.

#### 2.2.1 `controller/LibroControllerTest.java` — 10 tests

**Clase bajo prueba:** `LibroController`
**MockBean:** `LibroService`

| # | Endpoint | Método de prueba | Qué valida |
|---|----------|-----------------|------------|
| 1 | `POST libro/guardar` | `guardar_libroValido_retorna200` | Status 200, body contiene `ok=true` y mensaje "Libro guardado correctamente". Verifica que `service.guardar()` se invoca. |
| 2 | `POST libro/agregar-stock` | `agregarStock_retorna200` | Status 200, body `ok=true`. Verifica que `service.agregarStock()` se invoca con el DTO correcto. |
| 3 | `DELETE libro/eliminar/1` | `eliminar_retorna200` | Status 200, body `ok=true`. Verifica que `service.eliminar(1L)` se invoca. |
| 4 | `GET libro/todos` | `verLibros_retorna200ConLista` | Status 200, body contiene lista en payload. Verifica que `service.verLibros()` se invoca. |
| 5 | `GET libro/buscar?q=java` | `buscar_retorna200` | Status 200, pasa query param `q`, body contiene resultados. |
| 6 | `POST libro/filtrar` | `filtrar_retorna200` | Status 200, body contiene lista filtrada en payload. |
| 7 | `GET libro/categoria/Ficcion` | `porCategoria_retorna200` | Status 200, pasa path variable. |
| 8 | `GET libro/recientes` | `masRecientes_retorna200` | Status 200. |
| 9 | `GET libro/ver/1` | `obtenerPorId_retorna200` | Status 200, payload contiene libro. |
| 10 | `GET libro/categorias` | `listarCategorias_retorna200` | Status 200, payload contiene lista de strings. |

```java
@WebMvcTest(LibroController.class)
class LibroControllerTest {
    @Autowired MockMvc mockMvc;
    @MockBean LibroService service;
    @Autowired ObjectMapper objectMapper;
}
```

---

#### 2.2.2 `controller/UsuarioControllerTest.java` — 5 tests

**Clase bajo prueba:** `UsuarioController`
**MockBean:** `UsuarioService`

| # | Endpoint | Método de prueba | Qué valida |
|---|----------|-----------------|------------|
| 1 | `POST usuario/registrar` | `registrar_retorna200` | Status 200, payload contiene perfil con nombreUsuario y rol. |
| 2 | `POST usuario/login` | `login_retorna200` | Status 200, payload contiene token y datos de usuario. |
| 3 | `GET usuario/perfil/1` | `perfil_retorna200` | Status 200, payload contiene perfil. |
| 4 | `POST usuario/cerrar-sesion` | `cerrarSesion_retorna200` | Status 200, pasa header `Authorization: Bearer xxx`, body `ok=true`. |
| 5 | `GET usuario/todos` | `listarUsuarios_retorna200` | Status 200, payload contiene lista. |

---

#### 2.2.3 `controller/CarritoControllerTest.java` — 3 tests

**Clase bajo prueba:** `CarritoController`
**MockBean:** `CarritoService`

| # | Endpoint | Método de prueba | Qué valida |
|---|----------|-----------------|------------|
| 1 | `POST carrito/agregar` | `agregar_retorna200` | Status 200, payload contiene carrito con items. |
| 2 | `GET carrito/ver/1` | `verCarrito_retorna200` | Status 200, payload contiene carrito. |
| 3 | `DELETE carrito/eliminar/1/2` | `eliminarItem_retorna200` | Status 200, payload contiene carrito actualizado. |

---

#### 2.2.4 `controller/PagoControllerTest.java` — 2 tests

**Clase bajo prueba:** `PagoController`
**MockBean:** `PagoService`

| # | Endpoint | Método de prueba | Qué valida |
|---|----------|-----------------|------------|
| 1 | `POST pago/procesar` | `procesar_retorna200` | Status 200, payload contiene pago con estado PENDIENTE. |
| 2 | `POST pago/confirmar` | `confirmar_retorna200` | Status 200, payload contiene venta con total y libros. |

---

#### 2.2.5 `controller/RolControllerTest.java` — 2 tests

**Clase bajo prueba:** `RolController`
**MockBean:** `RolRepo`

| # | Endpoint | Método de prueba | Qué valida |
|---|----------|-----------------|------------|
| 1 | `POST rol/crear` (nombre válido) | `crear_rolValido_retorna200` | Status 200, `ok=true`, verifica que `rolRepo.save()` se invoca. |
| 2 | `POST rol/crear` (nombre vacío) | `crear_nombreVacio_retorna400` | Status 400, `ok=false`, mensaje "El nombre del rol es requerido". |

---

### 2.3 Tests de Helpers (nuevos)

#### 2.3.1 `helper/UtilTest.java` — 5 tests

**Clase bajo prueba:** `Util` (métodos estáticos)
**Mocking:** Ninguno (tests puros)

| # | Método de prueba | Qué valida |
|---|-----------------|------------|
| 1 | `esNullVacio_null_retornaTrue` | `Util.esNullVacio(null)` retorna `true` |
| 2 | `esNullVacio_vacio_retornaTrue` | `Util.esNullVacio("")` retorna `true` |
| 3 | `esNullVacio_espaciosEnBlanco_retornaTrue` | `Util.esNullVacio("   ")` retorna `true` |
| 4 | `esNullVacio_textoValido_retornaFalse` | `Util.esNullVacio("hola")` retorna `false` |
| 5 | `url_construyePathCorrecto` | `Util.url("config/")` retorna un `Path` cuya representación string contiene `"src/main/java/com/idasta/jetstore/config/"` |

---

#### 2.3.2 `helper/ValidarTest.java` — 4 tests

**Clase bajo prueba:** `Validar` (método estático `GuardarLibroDTO`)
**Mocking:** Ninguno (tests puros con DTOs de prueba)

| # | Método de prueba | Qué valida |
|---|-----------------|------------|
| 1 | `guardarLibroDTO_autorVacio_lanzaExcepcion` | `assertThrows(IllegalArgumentException.class)` con mensaje que contiene "autor es null o vacio" cuando `dto.autor()` es `""` o `null`. |
| 2 | `guardarLibroDTO_tituloVacio_lanzaExcepcion` | `assertThrows` con mensaje "titulo no puede estar vacio" cuando autor es válido pero título es vacío. |
| 3 | `guardarLibroDTO_formatoVacio_lanzaExcepcion` | `assertThrows` con mensaje "ingrese un formato" cuando autor y título son válidos pero formato es vacío. |
| 4 | `guardarLibroDTO_camposValidos_noLanzaExcepcion` | `assertDoesNotThrow()` cuando todos los campos son válidos (autor, título, formato no vacíos). |

**Nota:** `Validar.GuardarLibroDTO()` solo valida autor, título y formato. Precio, stock e imagen se normalizan en el compact constructor del DTO.

---

#### 2.3.3 `helper/MapearTest.java` — 1 test

**Clase bajo prueba:** `Mapear` (métodos estáticos `libro()` y `categoria()`)
**Mocking:** Ninguno (tests puros)

| # | Método de prueba | Qué valida |
|---|-----------------|------------|
| 1 | `libro_mapeaTodosLosCampos` | Crea un `GuardarLibroDTO` con todos los campos, llama `Mapear.libro(dto)`, verifica que el `Libro` resultante tiene titulo, autor, precio, formato, imagen y stock correctos. |

**Nota adicional:** Se podría agregar un test `categoria_mapeaNombre` pero `GuardarCategoriaDTO` es un record puro sin lógica. El mapeo es trivial (un solo campo).

---

### 2.4 Tests de Models (nuevos)

**Patrón:** Tests puros de dominio, sin mocking ni contexto de Spring.

#### 2.4.1 `model/VentaTest.java` — 3 tests

**Clase bajo prueba:** `Venta`

| # | Método de prueba | Qué valida |
|---|-----------------|------------|
| 1 | `agregarDetalle_agregaYCalculaTotal` | Crea un `DetalleVenta` con subTotal=50, llama `venta.agregarDetalle(detalle)`. Verifica: `venta.getDetalles().size() == 1`, `venta.getTotal() == 50`, `detalle.getVenta() == venta`. |
| 2 | `agregarDetalle_variosDetalles_totalCorrecto` | Agrega 3 detalles (10, 20, 30). Verifica `total == 60`. |
| 3 | `agregarDetalle_seteaRelacionConVenta` | Verifica que después de `agregarDetalle()`, `detalle.getVenta()` apunta a la venta. |

**Fixture helper:**
```java
private DetalleVenta crearDetalle(int subtotal) {
    DetalleVenta d = new DetalleVenta();
    d.setSubTotal(BigDecimal.valueOf(subtotal));
    return d;
}
```

---

#### 2.4.2 `model/DetalleVentaTest.java` — 2 tests

**Clase bajo prueba:** `DetalleVenta`

| # | Método de prueba | Qué valida |
|---|-----------------|------------|
| 1 | `calcularTotal_precioXCantidad` | Crea `DetalleVenta` con libro.precio=25, cantidad=3. Verifica `subTotal == 75`. |
| 2 | `calcularTotal_cantidadUno` | Crea con precio=99, cantidad=1. Verifica `subTotal == 99`. |

**Nota:** `DetalleVenta.calcularTotal()` es privado y se invoca desde `@PrePersist`/`@PreUpdate`. Para probarlo sin JPA, se puede testear indirectamente a través de `Venta.agregarDetalle()` o se puede refactorizar para que `calcularTotal()` sea包-visible (package-private). Alternativamente, se puede crear un `DetalleVenta` con valores y verificar que `getSubTotal()` retorna lo esperado después de setear libro y cantidad.

---

#### 2.4.3 `model/SesionTest.java` — 4 tests

**Clase bajo prueba:** `Sesion`

| # | Método de prueba | Qué valida |
|---|-----------------|------------|
| 1 | `prePersist_generaTokenUUID` | Crea una `Sesion`, invoca `prePersist()` (vía reflexión o haciéndolo package-private). Verifica que `token` no es null y tiene longitud 36 (formato UUID). |
| 2 | `prePersist_fechaCreacionEsAhora` | Verifica que `fechaCreacion` es `LocalDateTime.now()` (dentro de un rango de 1 segundo). |
| 3 | `prePersist_expiracionEn7Dias` | Verifica `fechaExpiracion` == `fechaCreacion.plusDays(7)`. |
| 4 | `prePersist_activaEsTrue` | Verifica `activa == true`. |

**Nota:** `prePersist()` es privado. Para testear se puede: (a) hacer el método package-private, (b) usar reflexión, o (c) extraer la lógica a un método testable.

---

#### 2.4.4 `model/PagoTest.java` — 3 tests

**Clase bajo prueba:** `Pago`

| # | Método de prueba | Qué valida |
|---|-----------------|------------|
| 1 | `prePersist_estadoPorDefectoPENDIENTE` | Crea `Pago` con `estado=null`, invoca `prePersist()`. Verifica `estado == EstadoPago.PENDIENTE`. |
| 2 | `prePersist_estadoExistente_noSeSobreescribe` | Crea `Pago` con `estado=COMPLETADO`, invoca `prePersist()`. Verifica `estado == EstadoPago.COMPLETADO`. |
| 3 | `prePersist_fechaCreacionEsAhora` | Verifica `fechaCreacion` no es null y es cercana a `LocalDateTime.now()`. |

---

### 2.5 Tests de DTOs (nuevos)

#### 2.5.1 `dto/AgregarStockDTOTest.java` — 3 tests

**Clase bajo prueba:** `AgregarStockDTO` (compact constructor con normalización)
**Mocking:** Ninguno (tests puros)

| # | Método de prueba | Qué valida |
|---|-----------------|------------|
| 1 | `constructor_cantidadCero_normalizaAUno` | `new AgregarStockDTO(1L, 0)` → `cantidad() == 1` |
| 2 | `constructor_cantidadNegativa_normalizaAUno` | `new AgregarStockDTO(1L, -5)` → `cantidad() == 1` |
| 3 | `constructor_cantidadValida_seMantiene` | `new AgregarStockDTO(1L, 10)` → `cantidad() == 10` |

---

### Resumen Backend — Tests nuevos a crear

| Categoría | Archivos a crear/modificar | Tests nuevos |
|-----------|:-------------------------:|:------------:|
| Services (completar) | 4 (modificar) | +13 |
| Controllers | 5 (nuevos) | 22 |
| Helpers | 3 (nuevos) | 10 |
| Models | 4 (nuevos) | 12 |
| DTOs | 1 (nuevo) | 3 |
| **Total Backend** | **17** | **+60** |

---

## 3. Frontend — Plan de Pruebas Nuevas

**Stack de testing:** Vitest 2 + @testing-library/react + jsdom
**Ejecutar:** `cd Frontend/frontend && npm test`
**Directorio de tests:** `Frontend/frontend/src/`

---

### 3.1 Completar Utils existentes (2 tests)

#### 3.1.1 `utils/__tests__/format.test.ts` (modificar)

| # | Función | Caso | Qué valida |
|---|---------|------|------------|
| 1 | `formatDate` | fechaVálida | `formatDate("2026-07-13")` retorna string con "13 de julio de 2026" (mes en formato largo, es-PE). |
| 2 | `formatDateShort` | fechaVálida | `formatDateShort("2026-07-13")` retorna string con "13 jul. 2026" (mes abreviado, es-PE). |

---

### 3.2 Tests de Stores (16 tests)

#### 3.2.1 `store/__tests__/ui-store.test.ts` — 5 tests

**Store bajo prueba:** `useUIStore`
**Mocking:** Ninguno (Zustand store puro)

| # | Test | Qué valida |
|---|------|------------|
| 1 | `toggleCart_alteraIsCartOpen` | Estado inicial `false`, después de `toggleCart()` → `true`, después de otro `toggleCart()` → `false`. |
| 2 | `toggleMobileMenu_alteraIsMobileMenuOpen` | Alterna `isMobileMenuOpen` de false→true→false. |
| 3 | `toggleSidebar_alteraIsSidebarOpen` | Estado inicial `true`, después de `toggleSidebar()` → `false`. |
| 4 | `closeCart_estableceFalse` | Después de `toggleCart()` (true), `closeCart()` → `isCartOpen == false`. |
| 5 | `closeMobileMenu_estableceFalse` | Después de `toggleMobileMenu()` (true), `closeMobileMenu()` → `isMobileMenuOpen == false`. |

---

#### 3.2.2 `store/__tests__/favorites-store.test.ts` — 3 tests

**Store bajo prueba:** `useFavoritesStore`
**Mocking:** Ninguno

| # | Test | Qué valida |
|---|------|------------|
| 1 | `toggle_agregaFavorito` | `toggle(1)` → `items` contiene `[1]`. |
| 2 | `toggle_remueveFavoritoSiYaExiste` | `toggle(1)`, `toggle(1)` → `items` es `[]`. |
| 3 | `isFavorite_retornaTrueSiExiste` | `toggle(5)` → `isFavorite(5) == true`, `isFavorite(3) == false`. |

---

#### 3.2.3 `store/__tests__/auth-store.test.ts` — 3 tests

**Store bajo prueba:** `useAuthStore`
**Mocking:** `authService` (mockear módulo `@/services/auth.service`)

| # | Test | Qué valida |
|---|------|------------|
| 1 | `login_exito_estableceTokenYUsuario` | Mockea `authService.login` para retornar `ok:true` con payload. Verifica `isAuthenticated == true`, `token != null`, `usuario.nombreUsuario` correcto. |
| 2 | `login_fallo_retornaError` | Mockea `authService.login` para lanzar error con `response.data.mensaje`. Verifica retorna `{success: false, error: "..."}` y `isAuthenticated == false`. |
| 3 | `logout_limpiaEstado` | Después de login exitoso, llama `logout()`. Verifica `usuario == null`, `token == null`, `isAuthenticated == false`. |

**Setup:** Resetear el store antes de cada test:
```ts
beforeEach(() => {
  useAuthStore.setState({ usuario: null, token: null, isAuthenticated: false });
});
```

---

#### 3.2.4 `store/__tests__/cart-store.test.ts` — 5 tests

**Store bajo prueba:** `useCartStore`
**Mocking:** `cartService`, `useAuthStore` (mockear módulos)

| # | Test | Qué valida |
|---|------|------------|
| 1 | `agregar_exito_actualizaItems` | Mockea auth store autenticado + `cartService.addItem` retornando items. Verifica `items` y `total` se actualizan. |
| 2 | `agregar_noAutenticado_retornaFalse` | Auth store con `isAuthenticated: false`. Verifica `agregar()` retorna `false` y items queda vacío. |
| 3 | `eliminar_exito_remueveItem` | Mockea `cartService.removeItem` con items actualizados. Verifica items se reducen. |
| 4 | `vaciar_reseteaEstado` | `vaciar()` → `items == []` y `total == 0`. |
| 5 | `totalItems_calculaCorrectamente` | Agrega items con cantidades 2 y 3. Verifica `totalItems() == 5`. |

---

### 3.3 Tests de Services (12 tests)

#### 3.3.1 `services/__tests__/api.test.ts` — 2 tests

**Módulo bajo prueba:** `api` (instancia de Axios con interceptores)
**Mocking:** `localStorage`, `window.location`

| # | Test | Qué valida |
|---|------|------------|
| 1 | `interceptor_request_agregaToken` | Mockea `localStorage.getItem` para retornar JSON con `state.token`. Verifica que el request incluye header `Authorization`. |
| 2 | `interceptor_response_401_redirigeALogin` | Simula respuesta 401. Verifica que `localStorage.removeItem` se llama y `window.location.href` es `/login`. |

**Nota:** Estos tests pueden ser complejos de configurar con Axios interceptores. Alternativa: testear solo los servicios que usan la instancia `api`.

---

#### 3.3.2 `services/__tests__/auth.service.test.ts` — 3 tests

**Módulo bajo prueba:** `authService`
**Mocking:** Mockear la instancia `api` (mock de axios)

| # | Test | Qué valida |
|---|------|------------|
| 1 | `login_llamaEndpointCorrecto` | Verifica `api.post` se llama con `/usuario/login` y body `{nombreUsuario, password}`. |
| 2 | `register_llamaEndpointCorrecto` | Verifica `api.post` se llama con `/usuario/registrar` y body con rol `CLIENTE`. |
| 3 | `logout_enviaTokenEnHeader` | Verifica `api.post` se llama con `/usuario/cerrar-sesion` y `headers: { Authorization: token }`. |

---

#### 3.3.3 `services/__tests__/product.service.test.ts` — 4 tests

**Módulo bajo prueba:** `productService`
**Mocking:** Mockear instancia `api`

| # | Test | Qué valida |
|---|------|------------|
| 1 | `getAll_llamaGET_libroTodos` | Verifica `api.get` con `/libro/todos`. |
| 2 | `search_llamaGET_conQueryParam` | Verifica `api.get` con `/libro/buscar` y `params: { q: query }`. |
| 3 | `filter_llamaPOST_conFiltros` | Verifica `api.post` con `/libro/filtrar` y body de filtros. |
| 4 | `save_llamaPOST_guardar` | Verifica `api.post` con `/libro/guardar` y body del libro. |

---

#### 3.3.4 `services/__tests__/cart.service.test.ts` — 3 tests

**Módulo bajo prueba:** `cartService`
**Mocking:** Mockear instancia `api`

| # | Test | Qué valida |
|---|------|------------|
| 1 | `getCart_llamaGET_verId` | Verifica `api.get` con `/carrito/ver/${usuarioId}`. |
| 2 | `addItem_llamaPOST_agregar` | Verifica `api.post` con `/carrito/agregar` y body `{usuarioId, libroId, cantidad}`. |
| 3 | `removeItem_llamaDELETE` | Verifica `api.delete` con `/carrito/eliminar/${usuarioId}/${libroId}`. |

---

### 3.4 Tests de Components (15 tests)

#### 3.4.1 `components/ui/__tests__/Button.test.tsx` — 5 tests

**Componente bajo prueba:** `Button`
**Librerías:** `@testing-library/react` (render, screen, fireEvent)

| # | Test | Qué valida |
|---|------|------------|
| 1 | `renderizaTextoChildren` | `render(<Button>Click me</Button>)` → `screen.getByText("Click me")` existe. |
| 2 | `variantPrimary_aplicaClasesRojo` | Renderiza con `variant="primary"` → el elemento tiene clase que contiene `bg-[#DC2626]`. |
| 3 | `loading_muestraSpinnerYOcultaChildren` | Renderiza con `loading={true}` → el SVG spinner existe, el texto children no se muestra. |
| 4 | `disabled_estaDeshabilitado` | Renderiza con `disabled={true}` → `button.disabled == true`. |
| 5 | `onClick_seEjecutaAlClickear` | Renderiza con `onClick={mockFn}`, `fireEvent.click(button)` → `mockFn` fue llamado. |

---

#### 3.4.2 `components/ui/__tests__/Input.test.tsx` — 4 tests

**Componente bajo prueba:** `Input`

| # | Test | Qué valida |
|---|------|------------|
| 1 | `renderizaLabel` | `render(<Input label="Nombre" />)` → `screen.getByText("Nombre")` es un label asociado al input. |
| 2 | `muestraErrorCuandoSePasaProp` | `render(<Input error="Requerido" />)` → `screen.getByText("Requerido")` existe con clase `text-red-500`. |
| 3 | `renderizaIcono` | `render(<Input icon={<span data-testid="icon" />} />)` → `screen.getByTestId("icon")` existe. |
| 4 | `permiteEscribir` | `render(<Input />)`, `fireEvent.change(input, {target: {value: "hola"}})` → `input.value == "hola"`. |

---

#### 3.4.3 `components/ui/__tests__/Modal.test.tsx` — 3 tests

**Componente bajo prueba:** `Modal`

| # | Test | Qué valida |
|---|------|------------|
| 1 | `cuandoCerrado_noSeMuestra` | `render(<Modal isOpen={false}><p>Contenido</p></Modal>)` → `screen.queryByText("Contenido")` es `null`. |
| 2 | `cuandoAbierto_muestraTituloYChildren` | `render(<Modal isOpen={true} title="Alerta"><p>Info</p></Modal>)` → `screen.getByText("Alerta")` y `screen.getByText("Info")` existen. |
| 3 | `clickEnCerrar_llamaOnClose` | Renderiza abierto con `onClose={mockFn}`, hace click en el botón de cerrar (aria-label="Cerrar") → `mockFn` fue llamado. |

---

#### 3.4.4 `components/ui/__tests__/Alert.test.tsx` — 3 tests

**Componente bajo prueba:** `Alert`

| # | Test | Qué valida |
|---|------|------------|
| 1 | `success_muestraMensajeConEstilosVerdes` | `render(<Alert type="success" message="OK" />)` → `screen.getByText("OK")` existe, el contenedor tiene clase `bg-green-50`. |
| 2 | `error_muestraMensajeConEstilosRojos` | `render(<Alert type="error" message="Fallo" />)` → `screen.getByText("Fallo")` existe, el contenedor tiene clase `bg-red-50`. |
| 3 | `clickEnCerrar_llamaOnClose` | Renderiza con `onClose={mockFn}`, hace click en button `aria-label="Cerrar"` → `mockFn` fue llamado. |

---

### Resumen Frontend — Tests nuevos a crear

| Categoría | Archivos a crear/modificar | Tests nuevos |
|-----------|:-------------------------:|:------------:|
| Utils (completar) | 1 (modificar) | +2 |
| Stores | 4 (nuevos) | 16 |
| Services | 4 (nuevos) | 12 |
| Components | 4 (nuevos) | 15 |
| **Total Frontend** | **13** | **+45** |

---

## 4. Resumen General

| | Tests existentes | Tests nuevos | Total proyectado |
|---|:----------------:|:------------:|:----------------:|
| **Backend** | 42 | +60 | **102** |
| **Frontend** | 20 | +45 | **65** |
| **TOTAL** | **62** | **+105** | **167** |

### Archivos de test a crear

**Backend — 17 archivos (13 nuevos + 4 a modificar):**

| Archivo | Acción |
|---------|--------|
| `service/LibroServiceImplTest.java` | Modificar (+7 tests) |
| `service/UsuarioServiceImplTest.java` | Modificar (+3 tests) |
| `service/CarritoServiceImplTest.java` | Modificar (+2 tests) |
| `service/PagoServiceImplTest.java` | Modificar (+1 test) |
| `controller/LibroControllerTest.java` | **Crear** (10 tests) |
| `controller/UsuarioControllerTest.java` | **Crear** (5 tests) |
| `controller/CarritoControllerTest.java` | **Crear** (3 tests) |
| `controller/PagoControllerTest.java` | **Crear** (2 tests) |
| `controller/RolControllerTest.java` | **Crear** (2 tests) |
| `helper/UtilTest.java` | **Crear** (5 tests) |
| `helper/ValidarTest.java` | **Crear** (4 tests) |
| `helper/MapearTest.java` | **Crear** (1 test) |
| `model/VentaTest.java` | **Crear** (3 tests) |
| `model/DetalleVentaTest.java` | **Crear** (2 tests) |
| `model/SesionTest.java` | **Crear** (4 tests) |
| `model/PagoTest.java` | **Crear** (3 tests) |
| `dto/AgregarStockDTOTest.java` | **Crear** (3 tests) |

**Frontend — 13 archivos (12 nuevos + 1 a modificar):**

| Archivo | Acción |
|---------|--------|
| `utils/__tests__/format.test.ts` | Modificar (+2 tests) |
| `store/__tests__/ui-store.test.ts` | **Crear** (5 tests) |
| `store/__tests__/favorites-store.test.ts` | **Crear** (3 tests) |
| `store/__tests__/auth-store.test.ts` | **Crear** (3 tests) |
| `store/__tests__/cart-store.test.ts` | **Crear** (5 tests) |
| `services/__tests__/api.test.ts` | **Crear** (2 tests) |
| `services/__tests__/auth.service.test.ts` | **Crear** (3 tests) |
| `services/__tests__/product.service.test.ts` | **Crear** (4 tests) |
| `services/__tests__/cart.service.test.ts` | **Crear** (3 tests) |
| `components/ui/__tests__/Button.test.tsx` | **Crear** (5 tests) |
| `components/ui/__tests__/Input.test.tsx` | **Crear** (4 tests) |
| `components/ui/__tests__/Modal.test.tsx` | **Crear** (3 tests) |
| `components/ui/__tests__/Alert.test.tsx` | **Crear** (3 tests) |

---

## 5. Estrategia y Priorización

### Fase 1 — Alta prioridad (Services Backend + Stores Frontend)

**Razón:** Lógica de negocio crítica y estado de aplicación.

| Archivo | Tests | Dependencias |
|---------|:-----:|-------------|
| `LibroServiceImplTest` (+7) | 7 | Mockito, LibroRepo mock |
| `UsuarioServiceImplTest` (+3) | 3 | Mockito, 3 repo mocks |
| `CarritoServiceImplTest` (+2) | 2 | Mockito, 3 repo mocks |
| `PagoServiceImplTest` (+1) | 1 | Mockito, 3 repo mocks |
| `auth-store.test.ts` | 3 | Mock authService |
| `cart-store.test.ts` | 5 | Mock cartService + authStore |
| `ui-store.test.ts` | 5 | Ninguno |
| `favorites-store.test.ts` | 3 | Ninguno |
| **Subtotal** | **29** | |

### Fase 2 — Media prioridad (Controllers Backend + Components Frontend)

**Razón:** Capa HTTP y UI son importantes pero dependen de la lógica de la Fase 1.

| Archivo | Tests | Dependencias |
|---------|:-----:|-------------|
| `LibroControllerTest` | 10 | @WebMvcTest, MockMvc |
| `UsuarioControllerTest` | 5 | @WebMvcTest, MockMvc |
| `CarritoControllerTest` | 3 | @WebMvcTest, MockMvc |
| `PagoControllerTest` | 2 | @WebMvcTest, MockMvc |
| `RolControllerTest` | 2 | @WebMvcTest, MockMvc |
| `Button.test.tsx` | 5 | @testing-library/react |
| `Input.test.tsx` | 4 | @testing-library/react |
| `Modal.test.tsx` | 3 | @testing-library/react |
| `Alert.test.tsx` | 3 | @testing-library/react |
| **Subtotal** | **37** | |

### Fase 3 — Baja prioridad (Helpers, Models, DTOs, Utils, Services Frontend)

**Razón:** Utilidades simples, entidades de bajo nivel, y wrappers de axios.

| Archivo | Tests | Dependencias |
|---------|:-----:|-------------|
| `UtilTest` | 5 | Ninguno |
| `ValidarTest` | 4 | Ninguno |
| `MapearTest` | 1 | Ninguno |
| `VentaTest` | 3 | Ninguno |
| `DetalleVentaTest` | 2 | Ninguno |
| `SesionTest` | 4 | Reflexión (método privado) |
| `PagoTest` | 3 | Reflexión (método privado) |
| `AgregarStockDTOTest` | 3 | Ninguno |
| `format.test.ts` (+2) | 2 | Ninguno |
| `api.test.ts` | 2 | Mock localStorage |
| `auth.service.test.ts` | 3 | Mock axios |
| `product.service.test.ts` | 4 | Mock axios |
| `cart.service.test.ts` | 3 | Mock axios |
| **Subtotal** | **39** | |

---

### Consideraciones Técnicas

#### Backend

- **Controllers:** Usar `@WebMvcTest` (no `@SpringBootTest`) para tests rápidos que solo cargan la capa web.
- **Models con `@PrePersist`:** El método `prePersist()` es privado. Opciones: (a) hacerlo package-private para testing, (b) usar reflexión con `Method.setAccessible(true)`, o (c) extraer la lógica a un método testable.
- **ArgumentCaptor:** Usar para verificar los argumentos exactos pasados a `repo.save()` en los tests de controller.
- **Excepciones en controllers:** Los controllers delegan a services. Las excepciones del service son capturadas por `GlobalExceptionHandler` y retornan 400. En los tests de controller, si el service lanza excepción, MockMvc esperará status 400.

#### Frontend

- **Zustand stores:** Llamar `useXxxStore.setState(...)` en `beforeEach` para resetear estado entre tests.
- **Services:** Mockear el módulo `@/services/api` con `vi.mock()` para aislar cada service del HTTP real.
- **Components con Framer Motion:** `motion.button` y `motion.div` funcionan con jsdom. No se necesitan mocks especiales.
- **Components con `'use client'`:** Funcionan correctamente en Vitest con jsdom ya que no usan APIs de servidor de Next.js.

---

*Documento generado el 13 de julio de 2026.*

# Arquitectura C4 — JetStore Digital

> Plataforma web para la comercialización de libros digitales (PDF) y físicos.

---

## Nivel 1 — Contexto del Sistema (Context Diagram)

```
┌─────────────────────────────────────────────────────────────────────┐
│                        ACTORES / PERSONAS                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────────────┐   │
│  │  Cliente /    │   │ Administrador│   │  Visitante / Guest   │   │
│  │  Usuario      │   │   del Sistema│   │  (sin sesión)        │   │
│  └──────┬───────┘   └──────┬───────┘   └──────────┬───────────┘   │
│         │                   │                      │                │
└─────────┼───────────────────┼──────────────────────┼────────────────┘
          │                   │                      │
          ▼                   ▼                      ▼
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│                    SISTEMA: JetStore Digital                        │
│                                                                     │
│  Plataforma e-commerce para la venta de libros digitales y          │
│  físicos. Permite explorar catálogo, gestionar carrito de           │
│  compras, procesar pagos y acceder a biblioteca personal.           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Actores del Sistema

| Actor | Rol | Interacción Principal |
|-------|-----|----------------------|
| **Cliente** | Usuario registrado (rol `CLIENTE`) | Explorar catálogo, agregar al carrito, comprar, ver historial y biblioteca |
| **Administrador** | Usuario con rol `ADMIN` | CRUD de libros, usuarios, categorías, pedidos, pagos, reportes |
| **Visitante / Guest** | Sin sesión activa | Explorar catálogo. Al intentar comprar → redirige a `/login` |

### Dependencias Externas

| Sistema Externo | Tipo | Propósito |
|----------------|------|-----------|
| **PostgreSQL 16** | Base de datos | Almacenamiento persistente de todas las entidades del negocio |

> **Nota:** No existen integraciones con sistemas de pago externos (pasarelas), servicios de email, ni APIs de terceros. Los pagos se simulan internamente.

---

## Nivel 2 — Diagrama de Contenedores (Container Diagram)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          SISTEMA: JetStore Digital                         │
│                                                                             │
│  ┌─────────────────────────┐    HTTP/REST    ┌───────────────────────────┐  │
│  │                         │◄────────────────►│                           │  │
│  │   CONTENEDOR 1:         │    JSON          │   CONTENEDOR 2:          │  │
│  │   Frontend Web          │                  │   Backend API            │  │
│  │   (SPA)                 │                  │   (Servidor de Aplicación)│  │
│  │                         │                  │                           │  │
│  │   Next.js 16 + React 19│                  │   Spring Boot 4.0.7      │  │
│  │   TypeScript + Tailwind │                  │   Java 17 + Maven        │  │
│  │   Zustand + Axios       │                  │   Spring Data JPA        │  │
│  │                         │                  │   Hibernate              │  │
│  │   Puerto: 3000          │                  │   Puerto: 8080           │  │
│  └─────────────────────────┘                  └─────────────┬─────────────┘  │
│                                                             │                │
│                                                     JDBC   │                │
│                                                             ▼                │
│                                                  ┌─────────────────────┐    │
│                                                  │                     │    │
│                                                  │  CONTENEDOR 3:      │    │
│                                                  │  Base de Datos      │    │
│                                                  │  PostgreSQL 16      │    │
│                                                  │                     │    │
│                                                  │  Puerto: 5432       │    │
│                                                  │  BD: jetstore       │    │
│                                                  └─────────────────────┘    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Contenedores

| Contenedor | Tecnología | Puerto | Responsabilidad |
|------------|------------|--------|-----------------|
| **Frontend Web** | Next.js 16, React 19, TypeScript, Tailwind CSS v4, Zustand, Axios, Framer Motion | `3000` | Interfaz de usuario SPA, enrutamiento App Router, estado global, formularios, animaciones |
| **Backend API** | Java 17, Spring Boot 4.0.7-SNAPSHOT, Spring Data JPA, Hibernate, Lombok, Maven | `8080` | API REST, lógica de negocio, acceso a datos, autenticación por token UUID, validación |
| **Base de Datos** | PostgreSQL 16 (Alpine) | `5432` | Persistencia de datos: usuarios, libros, categorías, carritos, pagos, ventas, sesiones |

### Comunicación entre Contenedores

| Origen | Destino | Protocolo | Formato | Descripción |
|--------|---------|-----------|---------|-------------|
| Navegador | Frontend Web | HTTP | HTML/CSS/JS | El usuario accede a la interfaz web |
| Frontend Web | Backend API | HTTP/REST | JSON | Llamadas API para autenticación, catálogo, carrito, pagos |
| Backend API | PostgreSQL | JDBC | SQL | Consultas y escritura de datos via JPA/Hibernate |

### Despliegue Docker

El sistema se despliega mediante **Docker Compose** con 3 servicios:

| Servicio | Imagen / Build | Dependencia | Red |
|----------|---------------|-------------|-----|
| `postgres` | `postgres:16-alpine` | — | Puerto 5432 expuesto |
| `backend` | Build multi-stage (`./backend`) | Espera `postgres` healthcheck | Puerto 8080 expuesto |
| `frontend` | Build multi-stage (`./Frontend/frontend`) | Espera `backend` | Puerto 3000 expuesto |

---

## Nivel 3 — Diagrama de Componentes (Component Diagram)

### 3.1 — Componentes del Frontend

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         CONTENEDOR: Frontend Web                            │
│                         (Next.js 16 + React 19)                             │
│                                                                             │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │                        CAPA DE PRESENTACIÓN                         │   │
│  │                                                                      │   │
│  │  ┌──────────────────────┐  ┌──────────────────────┐                 │   │
│  │  │  Páginas (App Router)│  │  Componentes UI      │                 │   │
│  │  │                      │  │                      │                 │   │
│  │  │  / (Home)            │  │  ui/ (14 primitivas) │                 │   │
│  │  │  /login              │  │  layout/ (4 comps)   │                 │   │
│  │  │  /registro           │  │  home/ (9 comps)     │                 │   │
│  │  │  /catalogo           │  │  catalog/ (5 comps)  │                 │   │
│  │  │  /producto/[id]      │  │  product/ (3 comps)  │                 │   │
│  │  │  /carrito            │  │  cart/ (2 comps)     │                 │   │
│  │  │  /checkout           │  │  checkout/ (9 comps) │                 │   │
│  │  │  /checkout/success   │  │  dashboard/ (5 comps)│                 │   │
│  │  │  /contacto           │  │  admin/ (11 comps)   │                 │   │
│  │  │  /dashboard/*        │  │                      │                 │   │
│  │  │  /admin/*            │  │                      │                 │   │
│  │  └──────────┬───────────┘  └──────────┬───────────┘                 │   │
│  │             │                          │                             │   │
│  └─────────────┼──────────────────────────┼─────────────────────────────┘   │
│                │                          │                                 │
│  ┌─────────────▼──────────────────────────▼─────────────────────────────┐   │
│  │                        CAPA DE ESTADO                               │   │
│  │                                                                      │   │
│  │  ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐        │   │
│  │  │  Zustand Stores  │ │  Tipos TS       │ │  Utilidades     │        │   │
│  │  │                   │ │  (interfaces)   │ │                  │        │   │
│  │  │  auth-store      │ │                  │ │  cn.ts          │        │   │
│  │  │  cart-store      │ │  Producto        │ │  format.ts      │        │   │
│  │  │  favorites-store │ │  Usuario         │ │  validators.ts  │        │   │
│  │  │  ui-store        │ │  CarritoItem     │ │  (Zod schemas)  │        │   │
│  │  │                   │ │  Pedido          │ │                  │        │   │
│  │  │  (persist en     │ │  Pago            │ │                  │        │   │
│  │  │   localStorage)   │ │  ...             │ │                  │        │   │
│  │  └────────┬──────────┘ └─────────────────┘ └─────────────────┘        │   │
│  │           │                                                           │   │
│  └───────────┼───────────────────────────────────────────────────────────┘   │
│              │                                                               │
│  ┌───────────▼───────────────────────────────────────────────────────────┐   │
│  │                        CAPA DE SERVICIOS (API)                       │   │
│  │                                                                      │   │
│  │  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐                 │   │
│  │  │ authService  │ │productService│ │ cartService  │                 │   │
│  │  └──────┬───────┘ └──────┬───────┘ └──────┬───────┘                 │   │
│  │         │                │                │                          │   │
│  │  ┌──────┴───────┐ ┌──────┴───────┐ ┌──────┴───────┐                 │   │
│  │  │orderService  │ │userService   │ │adminService  │                 │   │
│  │  └──────┬───────┘ └──────────────┘ └──────────────┘                 │   │
│  │         │                                                            │   │
│  │  ┌──────▼───────────────────────────────────────────┐                │   │
│  │  │  api.ts — Instancia Axios centralizada           │                │   │
│  │  │  • baseURL: NEXT_PUBLIC_API_URL (default:8080)   │                │   │
│  │  │  • Interceptor request: adjunta token Bearer     │                │   │
│  │  │  • Interceptor response: 401 → auto-logout       │                │   │
│  │  │  • Timeout: 15 segundos                          │                │   │
│  │  └──────────────────────────────────────────────────┘                │   │
│  │                                                                      │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │                        CAPA DE DATOS (MOCK)                         │   │
│  │                                                                      │   │
│  │  data/productos.ts     data/categorias.ts     data/testimonios.ts    │   │
│  │  data/faqs.ts          data/stats.ts          data/compras.ts        │   │
│  │  data/pedidos.ts       data/usuarios.ts                               │   │
│  │                                                                      │   │
│  │  Fallback estático cuando la API backend no está disponible.         │   │
│  │  Permite funcionar en modo demo/offline.                             │   │
│  │                                                                      │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Componentes del Frontend — Detalle

| Capa | Componente | Cantidad | Responsabilidad |
|------|------------|----------|-----------------|
| **Páginas** | App Router pages | 25 rutas | Enrutamiento, layouts protegidos (dashboard, admin), Server Components |
| **UI Primitivas** | Button, Input, Textarea, Select, Card, Badge, Table, Modal, Toast, Alert, Spinner, Loader, Pagination, Breadcrumb | 14 | Componentes reutilizables con `forwardRef`, variantes, soporte formularios |
| **Layout** | Navbar, Footer, DashboardLayout, Sidebar | 4 | Navegación, pie de página, layouts de dashboard y admin colapsables |
| **Home** | Hero, Stats, Categories, FeaturedProducts, Benefits, HowItWorks, Testimonials, FAQ, Newsletter | 9 | Secciones de la página de aterrizaje |
| **Catálogo** | CatalogoView, SearchBar, ProductFilters, ProductSort, ProductGrid | 5 | Búsqueda, filtrado, ordenamiento, paginación de productos |
| **Producto** | ProductGallery, ProductInfo, RelatedProducts | 3 | Detalle de producto con galería y productos relacionados |
| **Carrito** | CartItem, CartSummary | 2 | Items del carrito y resumen de orden |
| **Checkout** | PaymentMethods, CheckoutForm, PaymentVisa/MC/Yape/Plin/Transfer/PayPal, OrderSummary, OrderConfirmation | 9 | Flujo de pago de 2 pasos con formularios por método |
| **Dashboard** | ProfileForm, PurchaseHistory, DigitalLibrary, FavoritesList, SettingsForm | 5 | Panel del cliente |
| **Admin** | AdminSidebar, AdminHeader, StatsCards, Charts, ProductsTable, OrdersTable, UsersTable, PaymentsTable, CategoriesTable, ReportsView, LogsView | 11 | Panel de administración completo |
| **Estado** | Zustand stores (auth, cart, favorites, ui) | 4 | Estado global con persistencia localStorage |
| **Servicios** | authService, productService, cartService, orderService, userService, adminService | 6 | Capa de comunicación HTTP con el backend |

---

### 3.2 — Componentes del Backend

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                       CONTENEDOR: Backend API                               │
│                       (Spring Boot 4.0.7 + Java 17)                        │
│                                                                             │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │                        CAPA DE CONTROLADORES                        │   │
│  │                                                                      │   │
│  │  ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐        │   │
│  │  │ LibroController │ │UsuarioController│ │CarritoController│        │   │
│  │  │ /libro/*        │ │/usuario/*       │ │/carrito/*       │        │   │
│  │  └────────┬────────┘ └────────┬────────┘ └────────┬────────┘        │   │
│  │           │                   │                   │                  │   │
│  │  ┌────────┴────────┐ ┌────────┴────────┐                             │   │
│  │  │ PagoController  │ │ RolController   │                             │   │
│  │  │ /pago/*         │ │ /rol/*          │                             │   │
│  │  └────────┬────────┘ └────────┬────────┘                             │   │
│  │           │                   │                                       │   │
│  │  ┌────────▼───────────────────▼──────────────────────────────────┐   │   │
│  │  │  RespuestaApi<T> — Envoltorio estándar: {ok, mensaje, payload}│   │   │
│  │  └───────────────────────────────────────────────────────────────┘   │   │
│  │                                                                      │   │
│  │  Endpoints: 22 (5 controllers)                                       │   │
│  │  CORS: http://localhost:3000, credenciales: true                     │   │
│  │                                                                      │   │
│  └──────────────────────────┬───────────────────────────────────────────┘   │
│                             │                                               │
│  ┌──────────────────────────▼───────────────────────────────────────────┐   │
│  │                        CAPA DE SERVICIOS                             │   │
│  │                                                                      │   │
│  │  ┌─────────────────┐ ┌─────────────────┐                             │   │
│  │  │ LibroService    │ │ UsuarioService  │                             │   │
│  │  │ (Interface +    │ │ (Interface +    │                             │   │
│  │  │  Impl)          │ │  Impl)          │                             │   │
│  │  └────────┬────────┘ └────────┬────────┘                             │   │
│  │           │                   │                                       │   │
│  │  ┌────────┴────────┐ ┌────────┴────────┐                             │   │
│  │  │ CarritoService  │ │ PagoService     │                             │   │
│  │  │ (Interface +    │ │ (Interface +    │                             │   │
│  │  │  Impl)          │ │  Impl)          │                             │   │
│  │  └────────┬────────┘ └────────┬────────┘                             │   │
│  │           │                   │                                       │   │
│  │  Responsabilidades:                                                    │   │
│  │  • LibroService: CRUD libros, búsqueda LIKE, filtrado dinámico,      │   │
│  │    creación automática de categorías, categoría genérica fallback     │   │
│  │  • UsuarioService: registro con unicidad, login con token UUID,       │   │
│  │    cierre de sesión, hash SHA-256 + salt                             │   │
│  │  • CarritoService: crear carrito lazy, agregar/actualizar/eliminar    │   │
│  │    items, cálculo automático de totales                               │   │
│  │  • PagoService: flujo de 2 fases (procesar → confirmar),             │   │
│  │    creación de Venta + DetalleVenta, vaciado del carrito             │   │
│  │                                                                      │   │
│  └──────────────────────────┬───────────────────────────────────────────┘   │
│                             │                                               │
│  ┌──────────────────────────▼───────────────────────────────────────────┐   │
│  │                        CAPA DE REPOSITORIOS                          │   │
│  │                                                                      │   │
│  │  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐                 │   │
│  │  │ LibroRepo    │ │ UsuarioRepo  │ │ CarritoRepo  │                 │   │
│  │  │ + LibroRepo  │ │              │ │              │                 │   │
│  │  │   Custom     │ │              │ │              │                 │   │
│  │  │ + LibroRepo  │ │              │ │              │                 │   │
│  │  │   Impl       │ │              │ │              │                 │   │
│  │  └──────────────┘ └──────────────┘ └──────────────┘                 │   │
│  │                                                                      │   │
│  │  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐                 │   │
│  │  │CarritoItem   │ │ VentaRepo    │ │ PagoRepo     │                 │   │
│  │  │    Repo      │ │              │ │              │                 │   │
│  │  └──────────────┘ └──────────────┘ └──────────────┘                 │   │
│  │                                                                      │   │
│  │  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐                 │   │
│  │  │ SesionRepo   │ │ RolRepo      │ │CategoriaRepo │                 │   │
│  │  └──────────────┘ └──────────────┘ └──────────────┘                 │   │
│  │                                                                      │   │
│  │  Total: 10 repositorios                                              │   │
│  │  LibroRepo usa consultas JPQL dinámicas via EntityManager            │   │
│  │                                                                      │   │
│  └──────────────────────────┬───────────────────────────────────────────┘   │
│                             │                                               │
│  ┌──────────────────────────▼───────────────────────────────────────────┐   │
│  │                        CAPA DE MODELO (ENTIDADES JPA)                │   │
│  │                                                                      │   │
│  │  ┌─────────┐    ┌──────────┐    ┌────────────┐                      │   │
│  │  │  Rol    │◄┐  │ Usuario  │◄┐  │  Sesion    │                      │   │
│  │  │         │ │  │          │ │  │  (token    │                      │   │
│  │  │ id      │ │  │ id       │ │  │   UUID)   │                      │   │
│  │  │ nombre  │ │  │ nombre   │ │  │            │                      │   │
│  │  └─────────┘ │  │ correo   │ │  └────────────┘                      │   │
│  │       ▲      │  │ passHash │ │                                      │   │
│  │       │      │  │ FK→rol   │ │  ┌────────────┐                      │   │
│  │       │      │  └────┬─────┘ │  │  Carrito   │◄─┐                   │   │
│  │       │      │       │       │  │            │  │                   │   │
│  │  1:N  │      │  1:1  │       │  │ id         │  │  1:1              │   │
│  │       │      │       │       │  │ FK→usuario │  │                   │   │
│  │       │      └───────┘       │  │ fechaCreac │  │                   │   │
│  │       │                      │  └─────┬──────┘  │                   │   │
│  │       │                      │        │          │                   │   │
│  │       │                      │  1:N   │          │                   │   │
│  │       │                      │        ▼          │                   │   │
│  │       │                      │  ┌────────────┐   │  ┌────────────┐  │   │
│  │       │                      │  │CarritoItem │   │  │   Pago     │  │   │
│  │       │                      │  │            │   │  │            │  │   │
│  │       │                      │  │ id         │   │  │ id         │  │   │
│  │       │                      │  │ FK→carrito │   │  │ FK→carrito │──┘   │
│  │       │                      │  │ FK→libro   │   │  │ FK→venta   │      │
│  │       │                      │  │ cantidad   │   │  │ metodoPago │      │
│  │       │                      │  └────────────┘   │  │ estado     │      │
│  │       │                      │                   │  │ montoTotal │      │
│  │       │                      │  ┌────────────┐   │  │ referencia │      │
│  │       │                      │  │   Venta    │───┘  └────────────┘      │
│  │       │                      │  │            │                          │
│  │       │                      │  │ id         │                          │
│  │       │                      │  │ FK→usuario │                          │
│  │       │                      │  │ total      │                          │
│  │       │                      │  │ aplicaBono │                          │
│  │       │                      │  │ fechaHora  │                          │
│  │       │                      │  └─────┬──────┘                          │
│  │       │                      │        │                                  │
│  │       │                      │  1:N   ▼                                  │
│  │       │                      │  ┌──────────────┐                        │
│  │       │                      │  │ DetalleVenta │                        │
│  │       │                      │  │              │                        │
│  │       │                      │  │ id           │                        │
│  │       │                      │  │ FK→venta     │                        │
│  │       │                      │  │ FK→libro     │                        │
│  │       │                      │  │ cantidad     │                        │
│  │       │                      │  │ subTotal     │                        │
│  │       │                      │  └──────────────┘                        │
│  │       │                      │                                          │
│  │       │                      └──────────────────────────                │
│  │       │                                                                 │
│  │  ┌────┴─────┐    ┌──────────────────────┐                              │
│  │  │ Libro    │◄───┤  Categoria           │                              │
│  │  │          │    │                      │                              │
│  │  │ id       │    │ id                   │                              │
│  │  │ titulo   │    │ nombre               │                              │
│  │  │ autor    │    └──────────────────────┘                              │
│  │  │ FK→categ │                                                          │
│  │  │ precio   │                                                          │
│  │  │ formato  │    ┌──────────────────────┐                              │
│  │  │ imagen   │    │  Cliente (standalone)│                              │
│  │  │ stock    │    │  (no vinculado aún)  │                              │
│  │  │ fechaCre │    └──────────────────────┘                              │
│  │  └──────────┘                                                          │
│  │                                                                      │   │
│  │  Total: 13 entidades JPA (11 activas + Cliente + ClienteVenta)        │   │
│  │  Enums: MetodoPago (TARJETA, YAPE, PLIN), EstadoPago (4 estados)     │   │
│  │                                                                      │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │                        CAPA DE AYUDA (CROSS-CUTTING)                 │   │
│  │                                                                      │   │
│  │  ┌────────────────────┐ ┌──────────────────┐ ┌────────────────────┐  │   │
│  │  │GlobalException     │ │  PasswordUtil    │ │  DataSeeder        │  │   │
│  │  │    Handler         │ │  (SHA-256+salt)  │ │  (@PostConstruct)  │  │   │
│  │  │                    │ │                  │ │  • Roles (2)       │  │   │
│  │  │ Captura todas las  │ │                  │ │  • Usuarios (2)    │  │   │
│  │  │ RuntimeException   │ │                  │ │  • Categorías (6)  │  │   │
│  │  │ → 400 + Respuesta  │ │                  │ │  • Libros (15)     │  │   │
│  │  │   Api(false, msg)  │ │                  │ │                    │  │   │
│  │  └────────────────────┘ └──────────────────┘ └────────────────────┘  │   │
│  │                                                                      │   │
│  │  ┌────────────────────┐ ┌──────────────────┐ ┌────────────────────┐  │   │
│  │  │  Mapear            │ │  Validar          │ │  Jetstore          │  │   │
│  │  │  (DTO↔Entity)     │ │  (Book DTOs)     │ │  (config.json)     │  │   │
│  │  └────────────────────┘ └──────────────────┘ └────────────────────┘  │   │
│  │                                                                      │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │                        CAPA DE CONFIGURACIÓN                         │   │
│  │                                                                      │   │
│  │  ┌────────────────────────────────────────────────────────────────┐  │   │
│  │  │  WebConfig.java — CORS                                        │  │   │
│  │  │  • Allowed Origin: http://localhost:3000                       │  │   │
│  │  │  • Methods: GET, POST, PUT, DELETE, OPTIONS                    │  │   │
│  │  │  • Credentials: true                                           │  │   │
│  │  │  • Max Age: 3600s                                              │  │   │
│  │  └────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                      │   │
│  │  ┌────────────────────────────────────────────────────────────────┐  │   │
│  │  │  application.properties                                        │  │   │
│  │  │  • server.port: 8080                                           │  │   │
│  │  │  • datasource: PostgreSQL jdbc:postgresql://localhost:5432     │  │   │
│  │  │  • jpa.hibernate.ddl-auto: create-drop                         │  │   │
│  │  │  • jackson.default-property-inclusion: non_null                │  │   │
│  │  └────────────────────────────────────────────────────────────────┘  │   │
│  │                                                                      │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Componentes del Backend — Detalle

| Capa | Componente | Cantidad | Responsabilidad |
|------|------------|----------|-----------------|
| **Controladores** | LibroController, UsuarioController, CarritoController, PagoController, RolController | 5 | Endpoints REST (22 rutas), manejo de request/response, validación de entrada |
| **Servicios** | LibroServiceImpl, UsuarioServiceImpl, CarritoServiceImpl, PagoServiceImpl | 4 | Lógica de negocio, transacciones (`@Transactional`), orquestación de repositorios |
| **Repositorios** | LibroRepo(+Custom+Impl), UsuarioRepo, CarritoRepo, CarritoItemRepo, VentaRepo, PagoRepo, SesionRepo, RolRepo, CategoriaRepo | 10 | Acceso a datos via JPA, consultas JPQL dinámicas, queries derivadas |
| **Modelo** | 13 entidades JPA + 2 enums | 15 | Mapeo对象-relacional de todas las tablas de la base de datos |
| **DTOs** | Request DTOs + Response DTOs (records Java) | 16 | Transferencia de datos entre capas, inmutabilidad |
| **Ayuda** | GlobalExceptionHandler, PasswordUtil, DataSeeder, Mapear, Validar, Util, Jetstore, RespuestaApi | 8 | Manejo de errores, hashing, seeding, mapeo, validación, configuración |

---

### 3.3 — Componentes de la Base de Datos

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                       CONTENEDOR: PostgreSQL 16                             │
│                       Base de datos: jetstore                               │
│                                                                             │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │  TABLAS GENERADAS POR JPA (ddl-auto=create-drop)                    │   │
│  │                                                                      │   │
│  │  ┌───────────┐   ┌───────────┐   ┌───────────┐   ┌───────────┐     │   │
│  │  │   rol     │   │  usuario  │   │  sesion   │   │categoria  │     │   │
│  │  │───────────│   │───────────│   │───────────│   │───────────│     │   │
│  │  │ id (PK)   │◄──│ id (PK)   │◄──│ id (PK)   │   │ id (PK)   │     │   │
│  │  │ nombre    │   │ nombreUsu │   │ token(UUID│   │ nombre    │     │   │
│  │  └───────────┘   │ correo    │   │ usuarioId │   └─────┬─────┘     │   │
│  │                  │ passHash  │   │ fechaCre  │         │           │   │
│  │                  │ rol_id(FK)│   │ fechaExp  │         │ 1:N       │   │
│  │                  └─────┬─────┘   │ activa    │         │           │   │
│  │                        │         └───────────┘         ▼           │   │
│  │                        │                                 ┌─────────┐│   │
│  │                        │ 1:1                             │ libro   ││   │
│  │                        ▼                                 │─────────││   │
│  │  ┌───────────┐   ┌───────────┐   ┌───────────┐         │ id (PK) ││   │
│  │  │ carrito   │   │carrito_   │   │   pago    │         │ titulo  ││   │
│  │  │───────────│   │  item     │   │───────────│         │ autor   ││   │
│  │  │ id (PK)   │◄──│───────────│   │ id (PK)   │         │ categ_id││   │
│  │  │ usuario_id│   │ id (PK)   │   │ carrito_id│────────►│ precio  ││   │
│  │  │ fechaCre  │   │ carrito_id│   │ venta_id  │         │ formato ││   │
│  │  └─────┬─────┘   │ libro_id  │   │ metodoPago│         │ imagen  ││   │
│  │        │          │ cantidad  │   │ estado    │         │ stock   ││   │
│  │        │          └───────────┘   │ montoTotal│         │ fechaCr ││   │
│  │        │                          │ referencia│         └─────────┘│   │
│  │        │ 1:1                      │ fechaCre  │                    │   │
│  │        ▼                          │ fechaConf │                    │   │
│  │  ┌───────────┐   ┌───────────┐   └───────────┘                    │   │
│  │  │  venta    │   │detalle_   │                                    │   │
│  │  │───────────│   │  venta    │                                    │   │
│  │  │ id (PK)   │◄──│───────────│                                    │   │
│  │  │ usuario_id│   │ id (PK)   │                                    │   │
│  │  │ total     │   │ venta_id  │                                    │   │
│  │  │ aplicaBono│   │ libro_id  │                                    │   │
│  │  │ fechaHora │   │ cantidad  │                                    │   │
│  │  └───────────┘   │ subTotal  │                                    │   │
│  │                   └───────────┘                                    │   │
│  │                                                                      │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  Datos semilla (DataSeeder @PostConstruct):                                 │
│  • 2 roles: ADMIN, CLIENTE                                                  │
│  • 2 usuarios: admin/admin123, invitado/invitado123                         │
│  • 6 categorías: Tecnologia, Negocios, Salud, Educacion, Diseno, Marketing │
│  • 15 libros de ejemplo                                                     │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Nivel 4 — Diagrama de Código (Code Diagram)

### Modelo de Dominio — Entidades y Relaciones

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                           MODELO DE ENTIDADES JPA                               │
│                                                                                 │
│                                                                                 │
│  ┌──────────┐  1:N  ┌──────────┐  1:1  ┌──────────┐                           │
│  │   Rol    │◄──────│ Usuario  │──────►│  Sesion  │                           │
│  │──────────│       │──────────│       │──────────│                           │
│  │ Long id  │       │ Long id  │       │ Long id  │                           │
│  │ String   │       │ String   │       │ String   │                           │
│  │  nombre  │       │  nombre  │       │  token   │  ← UUID, unique, 36 chars │
│  └──────────┘       │ String   │       │ Date     │                           │
│                     │  correo  │       │  fechaCre│                           │
│                     │ String   │       │ Date     │                           │
│                     │  passHash│       │  fechaExp│  ← creación + 7 días      │
│                     │ Rol rol  │       │ Boolean  │                           │
│                     └────┬─────┘       │  activa  │                           │
│                          │             └──────────┘                           │
│                          │ 1:1                                                  │
│                          ▼                                                      │
│                     ┌──────────┐                                               │
│                     │ Carrito  │  1:N  ┌──────────────┐                       │
│                     │──────────│──────►│ CarritoItem  │                       │
│                     │ Long id  │       │──────────────│                       │
│                     │ Date     │       │ Long id      │                       │
│                     │  fechaCr │       │ Carrito      │                       │
│                     │ Usuario  │       │ Libro libro  │                       │
│                     │  usuario │       │ Integer      │                       │
│                     └────┬─────┘       │  cantidad    │                       │
│                          │             └──────────────┘                       │
│                          │ 1:1                                                  │
│                          ▼                                                      │
│                     ┌──────────┐                                               │
│                     │   Pago   │                                               │
│                     │──────────│                                               │
│                     │ Long id  │                                               │
│                     │ Carrito  │                                               │
│                     │  carrito │                                               │
│                     │ Venta    │  ← nullable                                   │
│                     │  venta   │                                               │
│                     │ MetodoPag│  ← enum: TARJETA, YAPE, PLIN                  │
│                     │  o metodo│                                               │
│                     │ EstadoPag│  ← enum: PENDIENTE, COMPLETADO, FALLIDO, REEMB│
│                     │  o estado│                                               │
│                     │ Decimal  │                                               │
│                     │  montoTtl│                                               │
│                     │ String   │  ← nullable, máx 100 chars                   │
│                     │  referenc│                                               │
│                     │ Date     │                                               │
│                     │  fechaCre│                                               │
│                     │ Date     │  ← nullable                                   │
│                     │  fechaCon│                                               │
│                     └──────────┘                                               │
│                                                                                │
│  ┌──────────┐  1:N  ┌──────────────┐                                          │
│  │   Venta  │──────►│ DetalleVenta │                                          │
│  │──────────│       │──────────────│                                          │
│  │ Long id  │       │ Long id      │                                          │
│  │ Usuario  │       │ Venta venta  │                                          │
│  │  usuario │       │ Libro libro  │                                          │
│  │ Decimal  │       │ Integer      │                                          │
│  │  total   │       │  cantidad    │  ← @Min(1)                               │
│  │ Boolean  │       │ Decimal      │                                          │
│  │  aplica  │       │  subTotal    │  ← calculado: precio * cantidad          │
│  │  Bono    │       └──────────────┘                                          │
│  │ Date     │                                                                  │
│  │  fechaHr │                                                                  │
│  └──────────┘                                                                  │
│                                                                                │
│  ┌──────────┐  1:N  ┌──────────┐                                               │
│  │Categoria │◄──────│  Libro   │                                               │
│  │──────────│       │──────────│                                               │
│  │ Long id  │       │ Long id  │                                               │
│  │ String   │       │ String   │                                               │
│  │  nombre  │       │  titulo  │                                               │
│  └──────────┘       │ String   │                                               │
│                     │  autor   │                                               │
│                     │Categoria │                                               │
│                     │  categoria│                                              │
│                     │ Decimal  │  ← @Digits(integer=8, fraction=2)            │
│                     │  precio  │                                               │
│                     │ String   │                                               │
│                     │  formato │  ← "Físico" o "Digital"                      │
│                     │ String   │                                               │
│                     │  imagen  │  ← URL, nullable                              │
│                     │ Integer  │  ← @Min(0)                                    │
│                     │  stock   │                                               │
│                     │ Date     │  ← @PrePersist automático                    │
│                     │  fechaCre│                                               │
│                     └──────────┘                                               │
│                                                                                │
│  ┌──────────┐  (standalone, sin FK)                                            │
│  │ Cliente  │                                                                  │
│  │──────────│                                                                  │
│  │ Long id  │                                                                  │
│  │ String   │  ← @Size(max=255)                                                │
│  │  nombres │                                                                  │
│  │ String   │  ← @Size(max=255)                                                │
│  │ apellidos│                                                                  │
│  │ String   │  ← @Size(max=8)                                                  │
│  │  dni     │                                                                  │
│  │ String   │  ← @Email                                                        │
│  │  correo  │                                                                  │
│  │ String   │  ← @Size(max=15), @Pattern("^[0-9+]+$")                         │
│  │ telefono │                                                                  │
│  └──────────┘                                                                  │
│                                                                                │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### Flujo de Autenticación

```
┌─────────┐         ┌───────────────┐         ┌──────────────────┐       ┌────────┐
│ Cliente  │         │ Frontend      │         │ Backend          │       │   BD   │
│          │         │ (Next.js)     │         │ (Spring Boot)    │       │(Postgr)│
└────┬─────┘         └──────┬────────┘         └────────┬─────────┘       └───┬────┘
     │                      │                           │                     │
     │  1. POST /login      │                           │                     │
     │  {usuario, password} │                           │                     │
     │─────────────────────►│  2. POST /usuario/login   │                     │
     │                      │──────────────────────────►│                     │
     │                      │                           │  3. SELECT usuario  │
     │                      │                           │────────────────────►│
     │                      │                           │◄────────────────────│
     │                      │                           │                     │
     │                      │                           │  4. Verificar hash  │
     │                      │                           │  SHA-256 + salt     │
     │                      │                           │                     │
     │                      │                           │  5. Crear Sesion    │
     │                      │                           │  (UUID token, 7d)   │
     │                      │                           │────────────────────►│
     │                      │                           │◄────────────────────│
     │                      │                           │                     │
     │                      │  6. {token, userId,       │                     │
     │                      │      nombre, rol}         │                     │
     │                      │◄──────────────────────────│                     │
     │                      │                           │                     │
     │                      │  7. Guardar en Zustand    │                     │
     │                      │  (localStorage persist)   │                     │
     │◄─────────────────────│                           │                     │
     │                      │                           │                     │
     │  Request con header  │                           │                     │
     │  Authorization: UUID │  8. GET /libro/todos      │                     │
     │─────────────────────►│  + Header Authorization   │                     │
     │                      │──────────────────────────►│  9. SELECT ...     │
     │                      │                           │────────────────────►│
     │                      │                           │◄────────────────────│
     │                      │  10. {ok, mensaje, data}  │                     │
     │                      │◄──────────────────────────│                     │
     │◄─────────────────────│                           │                     │
     │                      │                           │                     │
     │  POST /logout        │                           │                     │
     │─────────────────────►│  11. POST /usuario/       │                     │
     │                      │      cerrar-sesion        │                     │
     │                      │  + Header Authorization   │                     │
     │                      │──────────────────────────►│  12. UPDATE sesion  │
     │                      │                           │  activa = false     │
     │                      │                           │────────────────────►│
     │                      │                           │◄────────────────────│
     │                      │  13. {ok, mensaje}        │                     │
     │                      │◄──────────────────────────│                     │
     │  14. Limpiar Zustand │                           │                     │
     │◄─────────────────────│                           │                     │
     │                      │                           │                     │
```

### Flujo de Compra (Pago)

```
┌──────────┐      ┌──────────┐      ┌────────────┐      ┌──────────┐      ┌─────┐
│ Cliente  │      │ Frontend │      │  Backend   │      │  Pago    │      │ BD  │
│          │      │          │      │  Service   │      │ Service  │      │     │
└────┬─────┘      └────┬─────┘      └─────┬──────┘      └────┬─────┘      └──┬──┘
     │                 │                  │                   │               │
     │  Agregar items  │                  │                   │               │
     │  al carrito     │                  │                   │               │
     │────────────────►│ POST /carrito/   │                   │               │
     │                 │ agregar          │                   │               │
     │                 │─────────────────►│ SELECT carrito    │               │
     │                 │                  │──────────────────────────────────►│
     │                 │                  │ INSERT/UPDATE     │               │
     │                 │                  │──────────────────────────────────►│
     │                 │                  │◄──────────────────────────────────│
     │                 │◄─────────────────│                   │               │
     │                 │                  │                   │               │
     │  Proceder al    │                  │                   │               │
     │  checkout       │                  │                   │               │
     │────────────────►│ POST /pago/      │                   │               │
     │                 │ procesar         │                   │               │
     │                 │─────────────────►│                   │               │
     │                 │                  │  crearPago()      │               │
     │                 │                  │──────────────────►│               │
     │                 │                  │                   │ INSERT Pago   │
     │                 │                  │                   │ (PENDIENTE)   │
     │                 │                  │                   │──────────────►│
     │                 │                  │                   │◄──────────────│
     │                 │                  │◄──────────────────│               │
     │                 │◄─────────────────│                   │               │
     │                 │                  │                   │               │
     │  Seleccionar    │                  │                   │               │
     │  método pago    │                  │                   │               │
     │  y confirmar    │                  │                   │               │
     │────────────────►│ POST /pago/      │                   │               │
     │                 │ confirmar        │                   │               │
     │                 │─────────────────►│                   │               │
     │                 │                  │  confirmarPago()  │               │
     │                 │                  │──────────────────►│               │
     │                 │                  │                   │ 1. UPDATE Pago│
     │                 │                  │                   │  → COMPLETADO │
     │                 │                  │                   │──────────────►│
     │                 │                  │                   │               │
     │                 │                  │                   │ 2. Crear Venta│
     │                 │                  │                   │  + Detalles   │
     │                 │                  │                   │──────────────►│
     │                 │                  │                   │               │
     │                 │                  │                   │ 3. Vaciar     │
     │                 │                  │                   │  Carrito      │
     │                 │                  │                   │──────────────►│
     │                 │                  │                   │◄──────────────│
     │                 │                  │◄──────────────────│               │
     │                 │◄─────────────────│                   │               │
     │                 │                  │                   │               │
     │  Página éxito   │                  │                   │               │
     │◄────────────────│                  │                   │               │
     │                 │                  │                   │               │
```

---

## Decisiones Arquitectónicas

| # | Decisión | Alternativa Considerada | Justificación |
|---|----------|------------------------|---------------|
| 1 | **Monolito front/back separados** | Monolito único (e.g., Next.js API routes) | Separación clara de responsabilidades; backend en Java legado por el equipo; frontend independiente permite despliegue separado |
| 2 | **Spring Boot sin Spring Security** | Spring Security + JWT | Simplificación para fase de desarrollo; la autenticación se gestiona en frontend con token UUID |
| 3 | **Zustand sobre Redux** | Redux Toolkit, Context API | Menor boilerplate, mejor rendimiento, middleware `persist` nativo para localStorage |
| 4 | **PostgreSQL como BD única** | MySQL + PostgreSQL (el POM incluye ambos drivers) | Consistencia: solo PostgreSQL está configurado; driver MySQL incluido pero no utilizado |
| 5 | **Token UUID en vez de JWT** | JWT con claims | Simplificación: sin biblioteca JWT, sin verificación de firma; el token es opaco y se valida contra BD |
| 6 | **Fallback a datos mock** | Sin fallback (error directo) | Permite demo/offline; experiencia de usuario continua incluso sin backend |
| 7 | **CORS centralizado** | Proxy inverso / API Gateway | Adecuado para desarrollo con 2 orígenes; en producción se usaría un dominio único o reverse proxy |
| 8 | **Docker Compose multi-stage** | Despliegue manual sin Docker | Reproducibilidad total del entorno; un solo comando levanta toda la plataforma |
| 9 | **App Router (Next.js 16)** | Pages Router | Server Components nativos, layouts anidados para rutas protegidas, code splitting automático |
| 10 | **Sin pasarela de pago externa** | Integración Stripe/PayPal | Fase de desarrollo; el flujo de pago está simulado con estados internos |

---

## Notas de Seguridad

| Área | Estado Actual | Recomendación |
|------|---------------|---------------|
| **Autenticación** | Token UUID sin validación server-side en endpoints | Implementar filtro/FIFO interceptor que valide el token en cada request protegido |
| **Autorización** | Solo controlada en frontend (rutas protegidas en layout) | Agregar anotaciones `@PreAuthorize` o filtro de seguridad por rol |
| **Contraseñas** | SHA-256 con salt (16 bytes) | Considerar bcrypt/scrypt/argon2 para mayor resistencia a ataques de fuerza bruta |
| **CORS** | Solo permite `localhost:3000` | Ajustar a dominio de producción; considerar API Gateway |
| **DDL** | `create-drop` (borra y recrea en cada inicio) | Cambiar a `validate` o `update` en producción |
| **Datos sensibles** | Credenciales de BD en `application.properties` y `docker-compose.yml` | Usar variables de entorno o vault de secrets |

---

## Resumen de Métricas

| Métrica | Valor |
|---------|-------|
| **Contenedores** | 3 (Frontend, Backend, PostgreSQL) |
| **Endpoints REST** | 22 |
| **Entidades JPA** | 13 (+ 2 enums) |
| **DTOs** | 16 (records Java inmutables) |
| **Repositorios** | 10 |
| **Servicios** | 4 (interfaces + impl) |
| **Controladores** | 5 |
| **Componentes Frontend** | ~65 (14 UI + 4 layout + 9 home + 5 catalog + 3 product + 2 cart + 9 checkout + 5 dashboard + 11 admin) |
| **Páginas/Rutas** | 25 |
| **Zustand Stores** | 4 (auth, cart, favorites, ui) |
| **Servicios API** | 6 |
| **Tipos TypeScript** | ~15 interfaces |

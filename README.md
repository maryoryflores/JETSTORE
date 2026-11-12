# JetStore Digital

Plataforma web para la comercialización de libros digitales en formato PDF y físicos. Los usuarios pueden explorar el catálogo, realizar compras y acceder a su biblioteca personal.

## Arquitectura

```
jetstore/
├── backend/        # API REST — Spring Boot + PostgreSQL
│   └── src/main/java/com/idasta/jetstore/
│       ├── controller/   # Endpoints REST
│       ├── service/      # Lógica de negocio
│       ├── repository/   # Acceso a datos (JPA)
│       ├── model/        # Entidades JPA
│       ├── dto/          # DTOs (record)
│       ├── config/       # CORS, configuración
│       └── helper/       # Seeder, hashing, manejo de errores
├── Frontend/
│   └── frontend/   # SPA — Next.js 16 + React 19 + Tailwind CSS v4
│       └── src/
│           ├── app/            # App Router (rutas)
│           ├── components/     # Componentes UI
│           ├── services/       # Llamadas HTTP (Axios)
│           ├── store/          # Estado global (Zustand)
│           ├── types/          # Interfaces TypeScript
│           └── utils/          # Utilidades, validadores Zod
└── README.md
```

## Tecnologías

| Capa      | Stack |
|-----------|-------|
| Frontend  | Next.js 16, React 19, TypeScript, Tailwind CSS v4, Zustand, Axios |
| Backend   | Java 17, Spring Boot 4.0.7-SNAPSHOT, Spring Data JPA, Hibernate, Lombok, Maven |
| Base datos| PostgreSQL |
| Auth      | Token UUID personalizado (no JWT), 7 días de expiración |

## Requisitos previos

- Node.js 18+ (recomendado: v22+)
- JDK 17+
- PostgreSQL corriendo en `localhost:5432`
- Base de datos `jetstore` ya creada (o ajusta `application.properties`)

## Inicio rápido

### 1. Base de datos

```sql
CREATE DATABASE jetstore;
```

Opcionalmente crea el usuario:

```sql
CREATE USER jetstore_admin WITH PASSWORD '1234';
GRANT ALL PRIVILEGES ON DATABASE jetstore TO jetstore_admin;
```

> Las tablas se crean automáticamente (`ddl-auto=create-drop`). Los roles `ADMIN` y `CLIENTE` y los usuarios por defecto se crean al iniciar el backend.

### 2. Backend

```bash
cd backend
./mvnw spring-boot:run
```

El API estará disponible en **http://localhost:8080**.

Ver `backend/README.md` para endpoints y documentación completa.

### 3. Frontend

```bash
cd Frontend/frontend
npm install
npm run dev
```

Abrir **http://localhost:3000**.

## Usuarios de prueba

| Usuario  | Contraseña   | Rol     |
|----------|-------------|---------|
| admin    | admin123    | ADMIN   |
| invitado | invitado123 | CLIENTE |

> También existe el flujo **"Entrar como Invitado"** en la página de login: inicia sesión automáticamente con las credenciales de `invitado` (solo catálogo, sin compras).

## Roles

| Rol     | Permisos |
|---------|----------|
| ADMIN   | Acceso total: CRUD de libros, usuarios, categorías, pedidos, pagos, reportes |
| CLIENTE | Explorar catálogo, agregar al carrito, comprar, ver historial y biblioteca |
| Guest   | Explorar catálogo. Al intentar comprar, se redirige a `/login` |

## Endpoints principales

| Método | Ruta | Descripción |
|--------|------|-------------|
| POST | `/usuario/login` | Iniciar sesión |
| POST | `/usuario/registrar` | Crear cuenta |
| POST | `/usuario/recuperar` | Solicitar enlace de recuperación de contraseña |
| POST | `/usuario/restablecer` | Restablecer contraseña con el token recibido |
| GET  | `/libro/todos` | Listar todos los libros |
| GET  | `/libro/ver/{id}` | Ver detalle de un libro |
| GET  | `/libro/categorias` | Listar categorías |
| POST | `/carrito/agregar` | Agregar al carrito |
| POST | `/pago/procesar` | Procesar compra |

Ver `backend/README.md` para la lista completa.

## Recuperación de contraseña

1. En `/login` pulsa **"¿Olvidaste tu contraseña?"**.
2. En `/recuperar-contrasena` ingresa el correo asociado a la cuenta.
3. Recibirás un enlace (correo SMTP o consola del backend) hacia `/reset-password?token=...`.
4. Define la nueva contraseña (mínimo 6 caracteres). El enlace es de un solo uso y expira según `RECUPERACION_EXPIRACION_HORAS` (1 hora por defecto).

Configuración mediante variables de entorno (ver `.env.example`): `FRONTEND_URL`, `RECUPERACION_EXPIRACION_HORAS`, `MAIL_HABILITADO`, `MAIL_REMITENTE` y, para envío real, `SPRING_MAIL_HOST`, `SPRING_MAIL_PORT`, `SPRING_MAIL_USERNAME`, `SPRING_MAIL_PASSWORD`. Sin SMTP configurado, el enlace se muestra en la consola del backend.

## Manuales de uso

### Como cliente

1. **Crear cuenta** en `/registro` o usar "Entrar como Invitado".
2. **Explorar catálogo** en `/catalogo`. Filtrar por categoría, precio o búsqueda libre.
3. **Agregar al carrito** haciendo clic en el ícono del carrito en un producto.
4. **Completar compra** en `/checkout`, eligiendo método de pago.
5. **Ver historial** y **biblioteca** desde `/dashboard`.

### Como administrador

1. Iniciar sesión con `admin` / `admin123`.
2. Acceder al panel en `/admin`.
3. **Gestionar libros**: crear, editar, eliminar, agregar stock.
4. **Gestionar usuarios**: ver lista de usuarios registrados.
5. **Ver pedidos y pagos**: desde `/admin/pedidos` y `/admin/pagos`.
6. **Reportes y gráficas**: en `/admin/reportes`.

## Docker (ejecución con un solo comando)

Para evitar problemas de base de datos y de entorno, todo el proyecto se puede levantar con Docker Compose (PostgreSQL + Backend + Frontend).

### Requisitos

- Docker Engine 24+ y Docker Compose v2.

### Levantar todo

```bash
docker compose up --build
```

Esto crea:

- **PostgreSQL 16** en `localhost:5432` (BD `jetstore`, usuario `jetstore_admin`, password `1234`).
- **Backend** (Spring Boot) en `http://localhost:8080`.
- **Frontend** (Next.js) en `http://localhost:3000`.

El backend espera a que PostgreSQL esté sano antes de iniciar. Las tablas se crean solas (`ddl-auto=create-drop`) y el seeder genera los roles y usuarios por defecto.

### Usuarios de prueba

| Usuario  | Contraseña   | Rol     |
|----------|-------------|---------|
| admin    | admin123    | ADMIN   |
| invitado | invitado123 | CLIENTE |

### Notas

- El primer `build` del backend descarga las dependencias SNAPSHOT de Spring Boot, por lo que puede tardar varios minutos.
- Para detener y eliminar contenedores: `docker compose down`. Para borrar también la base de datos: `docker compose down -v`.
- La URL del backend en el frontend se configura con la variable de entorno `NEXT_PUBLIC_API_URL` (por defecto `http://localhost:8080`, tal como espera el navegador).

> Documentación de pruebas unitarias: ver [`PRUEBAS_UNITARIAS.md`](./PRUEBAS_UNITARIAS.md).


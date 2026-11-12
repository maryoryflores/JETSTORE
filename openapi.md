# JetStore Digital API - Documentación OpenAPI 3.0

> Plataforma de e-commerce para la venta de libros digitales (PDF) y físicos.

**Base URL:** `http://localhost:8080`

**Versión:** 1.0.0

---

## Tabla de Contenidos

- [Autenticación](#autenticación)
- [Respuesta Estándar](#respuesta-estándar)
- [Endpoints](#endpoints)
  - [Rol](#1-rol)
  - [Usuario](#2-usuario)
  - [Libro](#3-libro)
  - [Carrito](#4-carrito)
  - [Pago](#5-pago)
- [Esquemas / Schemas](#esquemas--schemas)
  - [Entidades](#entidades)
  - [DTOs Request](#dtos-request)
  - [DTOs Response](#dtos-response)
  - [Enums](#enums)

---

## Autenticación

El sistema utiliza **tokens UUID personalizados** (no JWT).

1. El cliente envía credenciales a `POST /usuario/login`.
2. El servidor retorna un `token` UUID válido por **7 días**.
3. El cliente debe enviar el token en el header `Authorization` en las peticiones que lo requieran.

```
Authorization: <uuid-token>
```

> **Nota:** Actualmente el backend no aplica validación de tokens ni control de roles a nivel servidor. La protección por roles (ADMIN / CLIENTE) se gestiona únicamente desde el frontend.

### Cifrado de Contraseñas

- Algoritmo: **SHA-256**
- Salt: 16 bytes aleatorios por contraseña
- Formato en BD: `<salt_hex>:<hash_hex>`

---

## Respuesta Estándar

Todas las respuestas de la API siguen el siguiente envolvente:

```json
{
  "ok": true,
  "mensaje": "Descripción del resultado",
  "payload": {} | null
}
```

| Campo     | Tipo    | Descripción                          |
|-----------|---------|--------------------------------------|
| `ok`      | boolean | `true` si la operación fue exitosa   |
| `mensaje` | string  | Mensaje descriptivo del resultado    |
| `payload` | any     | Datos de respuesta o `null`          |

---

## CORS

| Header              | Valor                          |
|---------------------|--------------------------------|
| Allowed Origin      | `http://localhost:3000`        |
| Allowed Methods     | `GET, POST, PUT, DELETE, OPTIONS` |
| Allow Credentials   | `true`                         |
| Max Age             | `3600s`                        |

---

## Endpoints

---

### 1. Rol

#### `POST /rol/crear`

Crea un nuevo rol en el sistema.

| Campo    | Tipo   | Ubicación | Requerido | Descripción      |
|----------|--------|-----------|-----------|------------------|
| `nombre` | string | body      | Sí        | Nombre del rol   |

**Request Body:**

```json
{
  "nombre": "ADMIN"
}
```

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Rol creado exitosamente",
  "payload": null
}
```

---

#### `GET /rol/todos`

Lista todos los roles registrados.

**Parámetros:** Ninguno

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Roles obtenidos exitosamente",
  "payload": [
    {
      "id": 1,
      "nombre": "ADMIN"
    },
    {
      "id": 2,
      "nombre": "CLIENTE"
    }
  ]
}
```

---

### 2. Usuario

#### `POST /usuario/registrar`

Registra un nuevo usuario en el sistema.

**Request Body:**

| Campo            | Tipo   | Requerido | Descripción                       |
|------------------|--------|-----------|-----------------------------------|
| `nombreUsuario`  | string | Sí        | Nombre de usuario único           |
| `correo`         | string | Sí        | Correo electrónico (formato email)|
| `password`       | string | Sí        | Contraseña en texto plano         |
| `rol`            | string | Sí        | Nombre del rol (ej: `CLIENTE`)    |

**Ejemplo:**

```json
{
  "nombreUsuario": "juan123",
  "correo": "juan@correo.com",
  "password": "miPassword123",
  "rol": "CLIENTE"
}
```

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Usuario registrado exitosamente",
  "payload": {
    "id": 3,
    "nombreUsuario": "juan123",
    "correo": "juan@correo.com",
    "rol": "CLIENTE"
  }
}
```

---

#### `POST /usuario/login`

Inicia sesión y retorna un token de sesión UUID.

**Request Body:**

| Campo            | Tipo   | Requerido | Descripción           |
|------------------|--------|-----------|-----------------------|
| `nombreUsuario`  | string | Sí        | Nombre de usuario     |
| `password`       | string | Sí        | Contraseña            |

**Ejemplo:**

```json
{
  "nombreUsuario": "juan123",
  "password": "miPassword123"
}
```

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Inicio de sesión exitoso",
  "payload": {
    "token": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "usuarioId": 3,
    "nombreUsuario": "juan123",
    "rol": "CLIENTE"
  }
}
```

---

#### `GET /usuario/perfil/{usuarioId}`

Obtiene el perfil de un usuario por su ID.

**Parámetros:**

| Param       | Tipo   | Ubicación | Requerido | Descripción |
|-------------|--------|-----------|-----------|-------------|
| `usuarioId` | long   | path      | Sí        | ID del usuario |

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Perfil obtenido exitosamente",
  "payload": {
    "id": 3,
    "nombreUsuario": "juan123",
    "correo": "juan@correo.com",
    "rol": "CLIENTE"
  }
}
```

---

#### `POST /usuario/cerrar-sesion`

Cierra la sesión activa del usuario (desactiva el token).

**Headers:**

| Header        | Tipo   | Requerido | Descripción          |
|---------------|--------|-----------|----------------------|
| `Authorization`| string | Sí        | Token de sesión UUID |

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Sesión cerrada exitosamente",
  "payload": null
}
```

---

#### `POST /usuario/recuperar`

Solicita un enlace de recuperación de contraseña para el correo indicado.

El backend genera un **token UUID de un solo uso** con expiración configurable (`RECUPERACION_EXPIRACION_HORAS`, 1 hora por defecto) y envía el enlace `{FRONTEND_URL}/reset-password?token=...` por correo SMTP. Si no hay SMTP configurado o falla el envío, el enlace se registra en la consola del backend. Solicitar un nuevo enlace invalida los tokens previos no usados del mismo correo.

La respuesta es **genérica** (siempre la misma) para no revelar si el correo está registrado.

**Request Body:**

| Campo    | Tipo   | Requerido | Descripción                    |
|----------|--------|-----------|--------------------------------|
| `correo` | string | Sí        | Correo asociado a la cuenta     |

**Ejemplo:**

```json
{
  "correo": "juan@correo.com"
}
```

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Si el correo está registrado, recibirás instrucciones para recuperar tu contraseña",
  "payload": null
}
```

**Posibles errores (400):**

| Descripción |
|-------------|
| `Ingresa un correo electrónico válido.` |

---

#### `POST /usuario/restablecer`

Establece una nueva contraseña usando el token recibido por correo.

El token debe existir, no estar utilizado y no haber expirado. Al restablecer la contraseña se invalidan las sesiones activas del usuario y el token queda marcado como utilizado (un solo uso).

**Request Body:**

| Campo             | Tipo   | Requerido | Descripción                  |
|-------------------|--------|-----------|------------------------------|
| `token`           | string | Sí        | Token recibido por correo    |
| `password`        | string | Sí        | Nueva contraseña (mín. 6)    |
| `confirmPassword` | string | Sí        | Confirmación de la contraseña|

**Ejemplo:**

```json
{
  "token": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "password": "nuevaPassword123",
  "confirmPassword": "nuevaPassword123"
}
```

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Tu contraseña ha sido actualizada correctamente",
  "payload": null
}
```

**Posibles errores (400):**

| Descripción |
|-------------|
| `El enlace de recuperación no es válido.` |
| `El enlace de recuperación ha expirado. Solicita uno nuevo.` |
| `La contraseña debe tener al menos 6 caracteres.` |
| `Las contraseñas no coinciden.` |

---

#### `GET /usuario/todos`

Lista todos los usuarios registrados (admin).

**Parámetros:** Ninguno

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Usuarios obtenidos exitosamente",
  "payload": [
    {
      "id": 1,
      "nombreUsuario": "admin",
      "correo": "admin@jetstore.com",
      "rol": "ADMIN"
    },
    {
      "id": 3,
      "nombreUsuario": "juan123",
      "correo": "juan@correo.com",
      "rol": "CLIENTE"
    }
  ]
}
```

---

### 3. Libro

#### `POST /libro/guardar`

Crea un nuevo libro o actualiza uno existente. Si se envía el campo `id`, se actualiza el libro con ese ID.

**Request Body:**

| Campo        | Tipo    | Requerido | Descripción                       |
|--------------|---------|-----------|-----------------------------------|
| `id`         | long    | No        | ID del libro (para actualizar)    |
| `titulo`     | string  | Sí        | Título del libro (máx. 255)       |
| `autor`      | string  | Sí        | Autor del libro (máx. 255)        |
| `categoria`  | object  | No        | Objeto `{ "nombre": string }`     |
| `precio`     | decimal | Sí        | Precio (hasta 8 enteros, 2 decimales) |
| `formato`    | string  | Sí        | Formato del libro (ej: `Físico`, `Digital`) |
| `imagen`     | string  | No        | URL de imagen (máx. 255)          |
| `stock`      | integer | Sí        | Cantidad en stock (mín. 0)        |

**Ejemplo - Crear:**

```json
{
  "titulo": "Cien Años de Soledad",
  "autor": "Gabriel García Márquez",
  "categoria": { "nombre": "Novela" },
  "precio": 45.90,
  "formato": "Físico",
  "imagen": "https://ejemplo.com/imagen.jpg",
  "stock": 50
}
```

**Ejemplo - Actualizar:**

```json
{
  "id": 1,
  "titulo": "Cien Años de Soledad",
  "autor": "Gabriel García Márquez",
  "categoria": { "nombre": "Novela" },
  "precio": 49.90,
  "formato": "Físico",
  "imagen": "https://ejemplo.com/imagen-nueva.jpg",
  "stock": 45
}
```

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Libro guardado exitosamente",
  "payload": null
}
```

---

#### `POST /libro/agregar-stock`

Añade stock adicional a un libro existente.

**Request Body:**

| Campo      | Tipo    | Requerido | Descripción       |
|------------|---------|-----------|-------------------|
| `libroId`  | long    | Sí        | ID del libro      |
| `cantidad` | integer | Sí        | Cantidad a agregar |

**Ejemplo:**

```json
{
  "libroId": 1,
  "cantidad": 20
}
```

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Stock agregado exitosamente",
  "payload": null
}
```

---

#### `DELETE /libro/eliminar/{libroId}`

Elimina un libro por su ID.

**Parámetros:**

| Param     | Tipo | Ubicación | Requerido | Descripción  |
|-----------|------|-----------|-----------|--------------|
| `libroId` | long | path      | Sí        | ID del libro |

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Libro eliminado exitosamente",
  "payload": null
}
```

---

#### `GET /libro/todos`

Lista todos los libros ordenados por fecha de creación (más recientes primero).

**Parámetros:** Ninguno

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Libros obtenidos exitosamente",
  "payload": [
    {
      "id": 1,
      "titulo": "Cien Años de Soledad",
      "autor": "Gabriel García Márquez",
      "categoria": "Novela",
      "precio": 49.90,
      "formato": "Físico",
      "imagen": "https://ejemplo.com/imagen.jpg",
      "stock": 45,
      "fechaCreacion": "2025-06-15T10:30:00"
    }
  ]
}
```

---

#### `GET /libro/buscar?q={query}`

Busca libros por palabra clave en título o autor (búsqueda parcial LIKE).

**Parámetros:**

| Param | Tipo   | Ubicación | Requerido | Descripción              |
|-------|--------|-----------|-----------|--------------------------|
| `q`   | string | query     | Sí        | Término de búsqueda      |

**Ejemplo:** `GET /libro/buscar?q=garcia`

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Búsqueda exitosa",
  "payload": [
    {
      "id": 1,
      "titulo": "Cien Años de Soledad",
      "autor": "Gabriel García Márquez",
      "categoria": "Novela",
      "precio": 49.90,
      "formato": "Físico",
      "imagen": "https://ejemplo.com/imagen.jpg",
      "stock": 45,
      "fechaCreacion": "2025-06-15T10:30:00"
    }
  ]
}
```

---

#### `POST /libro/filtrar`

Filtra libros por categoría, formato y rango de precios.

**Request Body:**

| Campo          | Tipo    | Requerido | Descripción              |
|----------------|---------|-----------|--------------------------|
| `categoria`    | string  | No        | Nombre de categoría      |
| `formato`      | string  | No        | Formato del libro        |
| `precioDesde`  | decimal | No        | Precio mínimo            |
| `precioHasta`  | decimal | No        | Precio máximo            |

**Ejemplo:**

```json
{
  "categoria": "Novela",
  "formato": "Digital",
  "precioDesde": 10.00,
  "precioHasta": 50.00
}
```

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Filtro aplicado exitosamente",
  "payload": [
    {
      "id": 2,
      "titulo": "El Principito",
      "autor": "Antoine de Saint-Exupéry",
      "categoria": { "id": 2, "nombre": "Novela" },
      "precio": 25.00,
      "formato": "Digital",
      "imagen": "https://ejemplo.com/principito.jpg",
      "stock": 100,
      "fechaCreacion": "2025-06-10T08:00:00"
    }
  ]
}
```

---

#### `GET /libro/ver/{id}`

Obtiene los detalles de un libro específico por su ID.

**Parámetros:**

| Param | Tipo | Ubicación | Requerido | Descripción  |
|-------|------|-----------|-----------|--------------|
| `id`  | long | path      | Sí        | ID del libro |

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Libro obtenido exitosamente",
  "payload": {
    "id": 1,
    "titulo": "Cien Años de Soledad",
    "autor": "Gabriel García Márquez",
    "categoria": "Novela",
    "precio": 49.90,
    "formato": "Físico",
    "imagen": "https://ejemplo.com/imagen.jpg",
    "stock": 45,
    "fechaCreacion": "2025-06-15T10:30:00"
  }
}
```

---

#### `GET /libro/categorias`

Lista todos los nombres de categorías disponibles.

**Parámetros:** Ninguno

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Categorías obtenidas exitosamente",
  "payload": ["Novela", "Ciencia Ficción", "Técnico", "Infantil"]
}
```

---

#### `GET /libro/categoria/{nombre}`

Lista todos los libros de una categoría específica.

**Parámetros:**

| Param   | Tipo   | Ubicación | Requerido | Descripción       |
|---------|--------|-----------|-----------|-------------------|
| `nombre`| string | path      | Sí        | Nombre de la categoría |

**Ejemplo:** `GET /libro/categoria/Novela`

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Libros obtenidos por categoría",
  "payload": [
    {
      "id": 1,
      "titulo": "Cien Años de Soledad",
      "autor": "Gabriel García Márquez",
      "categoria": { "id": 1, "nombre": "Novela" },
      "precio": 49.90,
      "formato": "Físico",
      "imagen": "https://ejemplo.com/imagen.jpg",
      "stock": 45,
      "fechaCreacion": "2025-06-15T10:30:00"
    }
  ]
}
```

---

#### `GET /libro/recientes`

Obtiene los libros más recientes (máximo 20).

**Parámetros:** Ninguno

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Libros recientes obtenidos",
  "payload": [
    {
      "id": 5,
      "titulo": "Modern JavaScript",
      "autor": "Evan You",
      "categoria": { "id": 3, "nombre": "Técnico" },
      "precio": 35.00,
      "formato": "Digital",
      "imagen": "https://ejemplo.com/js.jpg",
      "stock": 200,
      "fechaCreacion": "2025-06-20T14:00:00"
    }
  ]
}
```

---

### 4. Carrito

#### `POST /carrito/agregar`

Agrega un libro al carrito del usuario. Si el libro ya existe en el carrito, incrementa la cantidad.

**Request Body:**

| Campo      | Tipo    | Requerido | Descripción       |
|------------|---------|-----------|-------------------|
| `usuarioId`| long    | Sí        | ID del usuario    |
| `libroId`  | long    | Sí        | ID del libro      |
| `cantidad` | integer | Sí        | Cantidad a agregar |

**Ejemplo:**

```json
{
  "usuarioId": 3,
  "libroId": 1,
  "cantidad": 2
}
```

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Producto agregado al carrito",
  "payload": {
    "carritoId": 1,
    "items": [
      {
        "itemId": 1,
        "libroId": 1,
        "titulo": "Cien Años de Soledad",
        "autor": "Gabriel García Márquez",
        "precioUnitario": 49.90,
        "cantidad": 2,
        "subtotal": 99.80
      }
    ],
    "total": 99.80
  }
}
```

---

#### `GET /carrito/ver/{usuarioId}`

Obtiene el contenido del carrito de un usuario.

**Parámetros:**

| Param       | Tipo | Ubicación | Requerido | Descripción  |
|-------------|------|-----------|-----------|--------------|
| `usuarioId` | long | path      | Sí        | ID del usuario |

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Carrito obtenido exitosamente",
  "payload": {
    "carritoId": 1,
    "items": [
      {
        "itemId": 1,
        "libroId": 1,
        "titulo": "Cien Años de Soledad",
        "autor": "Gabriel García Márquez",
        "precioUnitario": 49.90,
        "cantidad": 2,
        "subtotal": 99.80
      },
      {
        "itemId": 2,
        "libroId": 3,
        "titulo": "El Principito",
        "autor": "Antoine de Saint-Exupéry",
        "precioUnitario": 25.00,
        "cantidad": 1,
        "subtotal": 25.00
      }
    ],
    "total": 124.80
  }
}
```

---

#### `DELETE /carrito/eliminar/{usuarioId}/{libroId}`

Elimina un libro específico del carrito de un usuario.

**Parámetros:**

| Param       | Tipo | Ubicación | Requerido | Descripción  |
|-------------|------|-----------|-----------|--------------|
| `usuarioId` | long | path      | Sí        | ID del usuario |
| `libroId`   | long | path      | Sí        | ID del libro   |

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Producto eliminado del carrito",
  "payload": {
    "carritoId": 1,
    "items": [
      {
        "itemId": 2,
        "libroId": 3,
        "titulo": "El Principito",
        "autor": "Antoine de Saint-Exupéry",
        "precioUnitario": 25.00,
        "cantidad": 1,
        "subtotal": 25.00
      }
    ],
    "total": 25.00
  }
}
```

---

### 5. Pago

#### `POST /pago/procesar`

Inicia el proceso de pago. Crea un registro de pago en estado `PENDIENTE` asociado al carrito del usuario.

**Request Body:**

| Campo        | Tipo   | Requerido | Descripción                                  |
|--------------|--------|-----------|----------------------------------------------|
| `usuarioId`  | long   | Sí        | ID del usuario                               |
| `metodoPago` | string | Sí        | Método de pago: `TARJETA`, `YAPE` o `PLIN`  |

**Ejemplo:**

```json
{
  "usuarioId": 3,
  "metodoPago": "TARJETA"
}
```

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Pago procesado exitosamente",
  "payload": {
    "pagoId": 1,
    "metodoPago": "TARJETA",
    "estado": "PENDIENTE",
    "montoTotal": 124.80,
    "referencia": null,
    "fechaCreacion": "2025-06-20T15:30:00",
    "fechaConfirmacion": null
  }
}
```

---

#### `POST /pago/confirmar`

Confirma un pago previamente procesado. Crea la venta, registra los detalles y vacía el carrito.

**Request Body:**

| Campo       | Tipo   | Requerido | Descripción                       |
|-------------|--------|-----------|-----------------------------------|
| `pagoId`    | long   | Sí        | ID del pago a confirmar           |
| `referencia`| string | No        | Referencia o código de transacción|

**Ejemplo:**

```json
{
  "pagoId": 1,
  "referencia": "TXN-20250620-001"
}
```

**Respuesta (200):**

```json
{
  "ok": true,
  "mensaje": "Pago confirmado exitosamente",
  "payload": {
    "ventaId": 1,
    "usuario": "juan123",
    "total": 124.80,
    "fechaHora": "2025-06-20T15:35:00",
    "libros": ["Cien Años de Soledad", "El Principito"]
  }
}
```

---

## Esquemas / Schemas

### Entidades

#### `Libro`

| Campo          | Tipo       | Constraints                     | Descripción                |
|----------------|------------|---------------------------------|----------------------------|
| `id`           | long       | `@Id`, auto-increment           | Identificador único        |
| `titulo`       | string     | `@Size(max=255)`                | Título del libro           |
| `autor`        | string     | `@Size(max=255)`                | Autor del libro            |
| `categoria`    | Categoria  | `@ManyToOne`, obligatorio        | Categoría del libro        |
| `precio`       | decimal    | `@Digits(integer=8, fraction=2)`| Precio                     |
| `formato`      | string     | `@Size(max=255)`                | Formato (Físico/Digital)   |
| `imagen`       | string     | `@Size(max=255)`                | URL de imagen              |
| `stock`        | integer    | `@Min(0)`                       | Stock disponible           |
| `fechaCreacion`| datetime   | `@PrePersist`                   | Fecha de creación automática|

---

#### `Categoria`

| Campo    | Tipo   | Constraints          | Descripción         |
|----------|--------|----------------------|---------------------|
| `id`     | long   | `@Id`, auto-increment| Identificador único |
| `nombre` | string | `@Size(max=255)`     | Nombre de categoría |

---

#### `Usuario`

| Campo           | Tipo   | Constraints          | Descripción                  |
|-----------------|--------|----------------------|------------------------------|
| `id`            | long   | `@Id`, auto-increment| Identificador único          |
| `nombreUsuario` | string | `@Size(max=255)`     | Nombre de usuario único      |
| `correo`        | string | `@Email`             | Correo electrónico           |
| `passwordHash`  | string | —                    | Hash SHA-256 de la contraseña|
| `rol`           | Rol    | `@ManyToOne`, obligatorio | Rol asignado             |

---

#### `Rol`

| Campo    | Tipo   | Constraints          | Descripción         |
|----------|--------|----------------------|---------------------|
| `id`     | long   | `@Id`, auto-increment| Identificador único |
| `nombre` | string | `@Size(max=255)`     | Nombre del rol      |

---

#### `Recuperacion`

| Campo             | Tipo     | Constraints                         | Descripción                        |
|-------------------|----------|-------------------------------------|------------------------------------|
| `id`              | long     | `@Id`, auto-increment               | Identificador único                |
| `token`           | string   | `@Column(unique, length=36)`        | Token UUID de un solo uso          |
| `usuario`         | Usuario  | `@ManyToOne`, obligatorio           | Usuario que solicita la recuperación |
| `fechaExpiracion` | datetime | `@NotNull`                          | Fecha de expiración del enlace     |
| `utilizado`       | boolean  | Column (default `false`)            | Si el token ya fue usado           |

---

#### `Sesion`

| Campo             | Tipo     | Constraints                  | Descripción                    |
|-------------------|----------|------------------------------|--------------------------------|
| `id`              | long     | `@Id`, auto-increment        | Identificador único            |
| `token`           | string   | `@Column(unique, length=36)` | Token UUID                     |
| `usuario`         | Usuario  | `@ManyToOne`, obligatorio     | Usuario propietario            |
| `fechaCreacion`   | datetime | `@PrePersist`                | Fecha de creación automática   |
| `fechaExpiracion` | datetime | `@PrePersist` (creación +7d) | Fecha de expiración automática |
| `activa`          | boolean  | `@PrePersist` (true)         | Estado de la sesión            |

---

#### `Carrito`

| Campo          | Tipo              | Constraints    | Descripción         |
|----------------|-------------------|----------------|---------------------|
| `id`           | long              | `@Id`, auto-increment | Identificador único |
| `usuario`      | Usuario           | `@OneToOne`, obligatorio | Dueño del carrito  |
| `items`        | List\<CarritoItem\> | `@OneToMany`, cascade ALL | Items del carrito |
| `fechaCreacion`| datetime          | `@PrePersist` | Fecha de creación   |

---

#### `CarritoItem`

| Campo     | Tipo    | Constraints          | Descripción         |
|-----------|---------|----------------------|---------------------|
| `id`      | long    | `@Id`, auto-increment| Identificador único |
| `carrito` | Carrito | `@ManyToOne`, obligatorio | Carrito padre    |
| `libro`   | Libro   | `@ManyToOne`, obligatorio | Libro asociado  |
| `cantidad`| integer | `@Min(1)`            | Cantidad            |

---

#### `Pago`

| Campo              | Tipo       | Constraints                         | Descripción          |
|--------------------|------------|-------------------------------------|----------------------|
| `id`               | long       | `@Id`, auto-increment               | Identificador único  |
| `carrito`          | Carrito    | `@OneToOne`, obligatorio             | Carrito asociado     |
| `venta`            | Venta      | `@OneToOne`, nullable                | Venta generada       |
| `metodoPago`       | enum       | `@Enumerated(STRING)`                | Método de pago       |
| `estado`           | enum       | `@Enumerated(STRING)`                | Estado del pago      |
| `montoTotal`       | decimal    | `@Digits(integer=8, fraction=2)`    | Monto total          |
| `referencia`       | string     | `@Column(length=100)`, nullable      | Referencia externa   |
| `fechaCreacion`    | datetime   | `@PrePersist`                       | Fecha de creación    |
| `fechaConfirmacion`| datetime   | nullable                            | Fecha de confirmación|

---

#### `Venta`

| Campo        | Tipo                | Constraints    | Descripción         |
|--------------|---------------------|----------------|---------------------|
| `id`         | long                | `@Id`, auto-increment | Identificador único |
| `usuario`    | Usuario             | `@OneToOne`, obligatorio | Comprador       |
| `detalles`   | List\<DetalleVenta\> | `@OneToMany`, cascade ALL | Detalles de venta |
| `total`      | decimal             | Calculado automáticamente | Total de la venta |
| `aplicaBono` | boolean             | `@Column(nullable=false)` | Si aplica bono  |
| `fechaHora`  | datetime            | `@NotNull`    | Fecha y hora         |

---

#### `DetalleVenta`

| Campo     | Tipo    | Constraints          | Descripción         |
|-----------|---------|----------------------|---------------------|
| `id`      | long    | `@Id`, auto-increment| Identificador único |
| `venta`   | Venta   | `@ManyToOne`, obligatorio | Venta padre     |
| `libro`   | Libro   | `@ManyToOne`, obligatorio | Libro vendido   |
| `cantidad`| integer | `@Min(1)`            | Cantidad vendida    |
| `subTotal`| decimal | Calculado automáticamente | Subtotal        |

---

#### `Cliente`

| Campo      | Tipo   | Constraints            | Descripción     |
|------------|--------|------------------------|-----------------|
| `id`       | long   | `@Id`, auto-increment  | Identificador único |
| `nombres`  | string | `@Size(max=255)`       | Nombres         |
| `apellidos`| string | `@Size(max=255)`       | Apellidos       |
| `dni`      | string | `@Size(max=8)`         | DNI             |
| `correo`   | string | `@Email`               | Correo          |
| `telefono` | string | `@Size(max=15)`, `@Pattern("^[0-9+]+$")` | Teléfono |

---

### DTOs Request

#### `RegistrarUsuarioDTO`

```json
{
  "nombreUsuario": "string (requerido)",
  "correo": "string (requerido, formato email)",
  "password": "string (requerido)",
  "rol": "string (requerido, ej: CLIENTE)"
}
```

#### `LoginDTO`

```json
{
  "nombreUsuario": "string (requerido)",
  "password": "string (requerido)"
}
```

#### `SolicitarRecuperacionDTO`

```json
{
  "correo": "string (requerido)"
}
```

#### `RestablecerPasswordDTO`

```json
{
  "token": "string (requerido)",
  "password": "string (requerido, mín 6)",
  "confirmPassword": "string (requerido)"
}
```

#### `GuardarLibroDTO`

```json
{
  "id": "long (opcional, para actualizar)",
  "titulo": "string (requerido, máx 255)",
  "autor": "string (requerido, máx 255)",
  "categoria": {
    "nombre": "string (opcional)"
  },
  "precio": "decimal (requerido, máx 8 enteros, 2 decimales)",
  "formato": "string (requerido)",
  "imagen": "string (opcional, máx 255)",
  "stock": "integer (requerido, mín 0)"
}
```

#### `GuardarCategoriaDTO`

```json
{
  "nombre": "string (requerido)"
}
```

#### `FiltroLibroDTO`

```json
{
  "categoria": "string (opcional)",
  "formato": "string (opcional)",
  "precioDesde": "decimal (opcional)",
  "precioHasta": "decimal (opcional)"
}
```

#### `AgregarStockDTO`

```json
{
  "libroId": "long (requerido)",
  "cantidad": "integer (requerido)"
}
```

#### `AgregarAlCarritoDTO`

```json
{
  "usuarioId": "long (requerido)",
  "libroId": "long (requerido)",
  "cantidad": "integer (requerido)"
}
```

#### `PagarRequestDTO`

```json
{
  "usuarioId": "long (requerido)",
  "metodoPago": "string (requerido: TARJETA | YAPE | PLIN)"
}
```

#### `ConfirmarPagoDTO`

```json
{
  "pagoId": "long (requerido)",
  "referencia": "string (opcional)"
}
```

---

### DTOs Response

#### `LoginResponseDTO`

```json
{
  "token": "string (UUID)",
  "usuarioId": "long",
  "nombreUsuario": "string",
  "rol": "string"
}
```

#### `UsuarioPerfilDTO`

```json
{
  "id": "long",
  "nombreUsuario": "string",
  "correo": "string",
  "rol": "string"
}
```

#### `VerLibroDTO`

```json
{
  "id": "long",
  "titulo": "string",
  "autor": "string",
  "categoria": "string (nombre de la categoría)",
  "precio": "decimal",
  "formato": "string",
  "imagen": "string (URL)",
  "stock": "integer",
  "fechaCreacion": "datetime (ISO 8601)"
}
```

#### `CarritoResponseDTO`

```json
{
  "carritoId": "long",
  "items": ["ItemCarritoDTO"],
  "total": "decimal"
}
```

#### `ItemCarritoDTO`

```json
{
  "itemId": "long",
  "libroId": "long",
  "titulo": "string",
  "autor": "string",
  "precioUnitario": "decimal",
  "cantidad": "integer",
  "subtotal": "decimal"
}
```

#### `PagoResponseDTO`

```json
{
  "pagoId": "long",
  "metodoPago": "string",
  "estado": "string (PENDIENTE | COMPLETADO | FALLIDO | REEMBOLSADO)",
  "montoTotal": "decimal",
  "referencia": "string | null",
  "fechaCreacion": "datetime",
  "fechaConfirmacion": "datetime | null"
}
```

#### `VentaResponseDTO`

```json
{
  "ventaId": "long",
  "usuario": "string (nombre de usuario)",
  "total": "decimal",
  "fechaHora": "datetime",
  "libros": ["string (títulos de los libros)"]
}
```

---

### Enums

#### `MetodoPago`

| Valor    | Descripción                  |
|----------|------------------------------|
| `TARJETA`| Pago con tarjeta (crédito/débito) |
| `YAPE`   | Pago mediante Yape           |
| `PLIN`   | Pago mediante Plin           |

#### `EstadoPago`

| Valor        | Descripción             |
|--------------|-------------------------|
| `PENDIENTE`  | Pago creado, pendiente de confirmación |
| `COMPLETADO` | Pago confirmado y procesado |
| `FALLIDO`    | Pago que falló          |
| `REEMBOLSADO`| Pago reembolsado        |

---

## Resumen de Endpoints

| #  | Método   | Ruta                                   | Descripción                     |
|----|----------|----------------------------------------|---------------------------------|
| 1  | `POST`   | `/rol/crear`                           | Crear rol                       |
| 2  | `GET`    | `/rol/todos`                           | Listar roles                    |
| 3  | `POST`   | `/usuario/registrar`                   | Registrar usuario               |
| 4  | `POST`   | `/usuario/login`                       | Iniciar sesión                  |
| 5  | `GET`    | `/usuario/perfil/{usuarioId}`          | Obtener perfil                  |
| 6  | `POST`   | `/usuario/cerrar-sesion`               | Cerrar sesión                   |
| 7  | `GET`    | `/usuario/todos`                       | Listar usuarios                 |
| 8  | `POST`   | `/libro/guardar`                       | Crear / actualizar libro        |
| 9  | `POST`   | `/libro/agregar-stock`                 | Agregar stock                   |
| 10 | `DELETE` | `/libro/eliminar/{libroId}`            | Eliminar libro                  |
| 11 | `GET`    | `/libro/todos`                         | Listar todos los libros         |
| 12 | `GET`    | `/libro/buscar?q={query}`              | Buscar libros                   |
| 13 | `POST`   | `/libro/filtrar`                       | Filtrar libros                  |
| 14 | `GET`    | `/libro/ver/{id}`                      | Ver libro por ID                |
| 15 | `GET`    | `/libro/categorias`                    | Listar categorías               |
| 16 | `GET`    | `/libro/categoria/{nombre}`            | Libros por categoría            |
| 17 | `GET`    | `/libro/recientes`                     | Libros recientes                |
| 18 | `POST`   | `/carrito/agregar`                     | Agregar al carrito              |
| 19 | `GET`    | `/carrito/ver/{usuarioId}`             | Ver carrito                     |
| 20 | `DELETE` | `/carrito/eliminar/{usuarioId}/{libroId}` | Eliminar del carrito         |
| 21 | `POST`   | `/pago/procesar`                       | Procesar pago                   |
| 22 | `POST`   | `/pago/confirmar`                      | Confirmar pago                  |
| 23 | `POST`   | `/usuario/recuperar`                   | Solicitar enlace de recuperación de contraseña |
| 24 | `POST`   | `/usuario/restablecer`                 | Restablecer contraseña con el token |

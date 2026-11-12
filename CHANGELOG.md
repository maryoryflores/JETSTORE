# Changelog

Todas las modificaciones notables de JetStore Digital se documentan en este archivo.

El formato sigue [Keep a Changelog](https://keepachangelog.com/es/1.1.0/) y el proyecto respeta [Semantic Versioning](https://semver.org/lang/es/).

## [1.0.0] - 2026-09-07

### Agregado

- **Recuperación de contraseña** para clientes (CLIENTE) y administradores (ADMIN).
  - Frontend:
    - Página `/recuperar-contrasena` para solicitar un enlace con el correo.
    - Página `/reset-password` para definir una nueva contraseña con el enlace recibido.
    - Enlace **"¿Olvidaste tu contraseña?"** en la página de login (`/login`).
    - Validación con Zod (`forgotPasswordSchema` y `resetPasswordSchema`) en `src/utils/validators.ts`.
    - Métodos `solicitarRecuperacion` y `restablecerPassword` en `src/services/auth.service.ts`.
  - Backend:
    - `POST /usuario/recuperar` solicita un enlace de recuperación (respuesta genérica si el correo existe o no).
    - `POST /usuario/restablecer` valida el token y actualiza la contraseña.
    - Nueva entidad `Recuperacion` (token UUID único de un solo uso, expiración configurable).
    - Al solicitar un nuevo enlace se invalidan los tokens previos no usados del mismo usuario.
    - Al restablecer la contraseña se invalidan las sesiones activas del usuario.
    - Envío de correo SMTP a través de `CorreoService` (`spring-boot-starter-mail`). Si no hay SMTP configurado o falla el envío, el enlace se registra en la consola del backend.
  - Configuración mediante variables de entorno (ver `.env.example`):
    - `FRONTEND_URL`, `RECUPERACION_EXPIRACION_HORAS`, `MAIL_HABILITADO`, `MAIL_REMITENTE`,
      `SPRING_MAIL_HOST`, `SPRING_MAIL_PORT`, `SPRING_MAIL_USERNAME`, `SPRING_MAIL_PASSWORD`.
  - Pruebas:
    - Backend: `PasswordRecoveryServiceImplTest` (14 casos).
    - Frontend: `OlvidastePasswordClient.test.tsx` (5 casos), `ResetPasswordClient.test.tsx` (4 casos) y casos nuevos en `LoginClient.test.tsx`.
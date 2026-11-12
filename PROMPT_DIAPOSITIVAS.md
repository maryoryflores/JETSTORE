# Prompt para generar las diapositivas (presentación final)

Copia y pega el siguiente prompt a un agente generador de diapositivas
(por ejemplo: un agente de IA conectado a PowerPoint / Google Slides / reveal.js,
o una herramienta como Gamma, SlidesAI, Tome, etc.).

---

## PROMPT

Actúa como **diseñador y redactor de presentaciones académicas**. Tu tarea es
generar la estructura y el contenido de las diapositivas para la defensa de un
**proyecto final de carrera** de un grupo de estudiantes de **Diseño y Programación Web**.

### Contexto del proyecto
- **Nombre:** JetStore Digital
- **Qué es:** una plataforma web para comercializar libros digitales (PDF) y físicos.
  Los usuarios exploran un catálogo, agregan al carrito, compran y acceden a su
  biblioteca personal.
- **Equipo (4 estudiantes):**
  - Daniel — Backend (API REST con Spring Boot + PostgreSQL)
  - Lorena — Frontend (Next.js, React, TypeScript, Tailwind)
  - Lucinda — Frontend (Next.js, React, TypeScript, Tailwind)
  - Maryory — QA (aseguranza de calidad y validación)
- **Stack técnico:**
  - Frontend: Next.js 16 (App Router), React 19, TypeScript, Tailwind CSS v4, Zustand, Axios.
  - Backend: Java 17, Spring Boot, Spring Data JPA / Hibernate, PostgreSQL.
  - Auth: token UUID (no JWT), expira en 7 días.
- **Pruebas unitarias (requisito del curso: 10 por integrante = 40):**
  - Se entregaron **57 pruebas**: 37 en backend + 20 en frontend.
  - Backend: JUnit 5 + Mockito (pruebas de servicios, DTO, modelo y hashing).
  - Frontend: Vitest (validadores Zod de formularios y utilidades de formato).
  - Detalle completo en el archivo `PRUEBAS_UNITARIAS.md` del repositorio.

### Audiencia
Docentes evaluadores y compañeros de la carrera. Tono profesional pero accesible,
académico, sin jerga innecesaria.

### Estructura sugerida (12 a 15 diapositivas)
1. **Portada:** título "JetStore Digital", subtítulo, nombres de los 4 integrantes y rol.
2. **Equipo y responsabilidades:** Daniel (Backend), Lorena y Lucinda (Frontend), Maryory (QA).
3. **Problema / contexto:** dificultad de comprar y gestionar libros digitales en un solo lugar.
4. **Solución:** qué es JetStore Digital y sus ventajas (catálogo, carrito, pagos, biblioteca).
5. **Arquitectura general:** diagrama cliente-servidor (Frontend Next.js ↔ API REST Spring Boot ↔ PostgreSQL).
6. **Tecnologías usadas:** tabla Frontend / Backend / Base de datos / Auth.
7. **Funcionalidades principales:** catálogo y filtros, carrito, checkout con varios métodos de pago (Yape, Plin, tarjeta, etc.), panel de administración, reportes.
8. **Roles y autenticación:** ADMIN, CLIENTE, Invitado; token UUID de 7 días.
9. **Calidad de software — pruebas unitarias:** explica por qué las pruebas importan y la meta del curso (10 por persona).
10. **Resultados de pruebas:** resumen 57 pruebas (37 backend + 20 frontend) y que el frontend pasa 20/20. Incluye la tabla: Daniel 10, Maryory 10, Lorena 10, Lucinda 10.
11. **Ejemplo de pruebas (backend):** 2-3 ejemplos breves (hashing de contraseña, total del carrito, validación de pago).
12. **Ejemplo de pruebas (frontend):** validadores de formulario (Zod) y formato de moneda/tarjeta.
13. **Cómo ejecutar las pruebas:** comandos `./mvnw test` (backend) y `npm test` (frontend).
14. **Demo / capturas:** menciona las pantallas clave (catálogo, checkout, dashboard, admin).
15. **Conclusiones y aprendizajes:** buenas prácticas, trabajo en equipo, desafíos superados.
16. **Cierre:** agradecimiento y "Preguntas".

### Requisitos de formato
- Usa viñetas cortas y frases directas (nada de párrafos largos por diapositiva).
- Sugiere un esquema de color coherente con una librería (ej. azul/blanco corporativo).
- Si la herramienta lo permite, incluye notas del orador (speaker notes) con lo que decir en voz alta.
- Entrega: (a) lista de diapositivas con título + contenido, y (b) sugerencias de diagramas/imágenes.
- Idioma: **español**.

### Entregable esperado
Devuélveme el contenido listo para volcar en la herramienta de diapositivas, siguiendo
la estructura de 12 a 15 diapositivas, con texto conciso y, donde aplique, notas para el presentador.

---

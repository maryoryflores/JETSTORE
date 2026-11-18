# Graph Report - jetstore  (2026-11-18)

## Corpus Check
- 442 files · ~308,846 words
- Verdict: corpus is large enough that graph structure adds value.
- Unclassified: 53 file(s) not represented in the graph (top: .csv 37, (none) 10, .properties 2)

## Summary
- 2792 nodes · 6035 edges · 156 communities (121 shown, 35 thin omitted)
- Extraction: 94% EXTRACTED · 6% INFERRED · 0% AMBIGUOUS · INFERRED: 369 edges (avg confidence: 0.9)
- Token cost: 0 input · 0 output

## Graph Freshness
- Built from commit: `3b4f04bb`
- Run `git rev-parse HEAD` and compare to check if the graph is stale.
- Run `graphify update .` after code changes (no API cost).

## Community Hubs (Navigation)
- PasswordRecoveryServiceImplTest
- read_rows
- app/page.tsx
- validate_data.py
- search
- react
- lombok.Getter
- Usuario
- core.py
- cn
- Button
- search_stack
- environment
- vitest
- design_system.py
- ReportsView.tsx
- ContactoClient.tsx
- GuardarLibroDTO
- api.ts
- browser-qa/package.json
- Carrito
- list
- generate_design_system
- preview.py
- GuardarCategoriaDTO
- index.ts
- LibroService
- org.junit.jupiter.api.Test
- Libro
- JetStore Digital
- BM25
- browser-qa
- DigitalLibrary.tsx
- Reporte de Pruebas Unitarias — JetStore Digital
- test_design_system_mode.py
- Browser QA v2 — Universal Browser Testing
- 3. Heal
- TestLandingAndStackContract
- detect_domain
- TestThresholdGate
- compilerOptions
- LibroServiceImplTest.java
- PagoController
- DetalleVenta
- LibroRepoImpl
- UsuarioService
- DashboardClient.tsx
- format_ascii_box
- DbConstraintFix.java
- VerLibroDTO
- formatCurrency
- CatalogRefreshTest
- dependencies
- Jetstore.java
- devDependencies
- Arquitectura C4 — JetStore Digital
- scripts
- LibroServiceImpl.java
- CorreoServiceImpl.java
- _palette_is_dark
- RespuestaApi
- Group 1: Hard Gate (absolute, no exceptions)
- CarritoService
- Frontend/package.json
- Browser Session Management
- DesignSystemGenerator
- Storage Management
- app/layout.tsx
- _select_palette_for_mode
- UI/UX Pro Max - Design Intelligence
- OrderConfirmation.tsx
- scripts
- test_skill_script_paths.py
- Informe de Pruebas Unitarias — JetStore Digital
- Tracing
- CatalogSummaryLineEndingsTest
- TokenCliTest
- EstadoPago
- eslint.config.mjs
- Jetstore
- TestStackFlagWithDesignSystem
- RtlCliTest
- GlobalExceptionHandler.java
- JetstoreApplication.java
- setup.tsx
- ui-store.ts
- opencode.json
- report.service.test.ts
- typescript@^5
- postcss.config.mjs
- com.idasta:jetstore
- Browser Automation with playwright-cli
- LibroRepo
- Group 2: Purpose-Gate (technique allowed, purpose required)
- Extending browser-qa with Framework-Specific Skills
- playwright-cli/SKILL.md
- Running Custom Playwright Code
- Pre-Delivery Checklist (canonical — the only one)
- Quick Reference
- Codebase-Driven Route Discovery
- Commands
- CategoriaServiceImpl.java
- Entidades
- Coverage Tracking
- 4.1. Prototipo de la solución
- Kanban — JetStore Digital (tablero QA)
- Endpoints
- 3. Libro
- Group 3: Quality Locks (consistency)
- User Flow Generation
- Video Recording
- PasswordRecoveryController
- CAPÍTULO II: DESCRIPCIÓN DE LA PROPUESTA DE INNOVACIÓN
- INFORME FINAL DEL PROYECTO DE INNOVACIÓN TECNOLÓGICA
- DTOs Request
- Cookies
- Util
- antislop
- Part 1: AI Slop Patterns (Warning Signs)
- Spec-Driven Testing Reference
- Advanced Mocking with run-code
- Local Storage
- JetStore — Frontend
- 2. Backend — Plan de Pruebas Nuevas
- 3. Frontend — Plan de Pruebas Nuevas
- JetStore Digital API - Documentación OpenAPI 3.0
- 2. Usuario
- PROMPT
- MetodoPago
- The Craftsmanship Standard
- _generate_intelligent_overrides
- split_values
- RolController
- FAQ.tsx
- INFORME_FINAL_JETSTORE.md
- CAPÍTULO I: PLANTEAMIENTO DEL PROBLEMA
- 7.1. Dimensiones de sostenibilidad
- Plan de Pruebas Unitarias — JetStore Digital
- Delivery Gate (Mandatory)
- Attaching Screenshots and Videos to Pull Requests
- TestMetricMath
- TestTextLayoutDataContracts
- CAPÍTULO VI: IMPLEMENTACIÓN DE LA SOLUCIÓN
- 3.2 Tests de Stores (16 tests)
- 3.3 Tests de Services (12 tests)
- AGENTS.md
- Part 3: Liveliness Toolkit
- TestFixtureValidation
- CAPÍTULO III: METODOLOGÍA
- CAPÍTULO V: VALIDACIÓN DE LA SOLUCIÓN
- Responsive Device Presets
- Frontend/AGENTS.md

## God Nodes (most connected - your core abstractions)
1. `vitest` - 115 edges
2. `@testing-library/react` - 89 edges
3. `search()` - 61 edges
4. `react` - 55 edges
5. `react-icons` - 54 edges
6. `Button` - 49 edges
7. `Input` - 45 edges
8. `RespuestaApi` - 44 edges
9. `cn()` - 42 edges
10. `DesignSystemGenerator` - 41 edges

## Surprising Connections (you probably didn't know these)
- ``AgregarAlCarritoDTO`` --references--> `AgregarAlCarritoDTO`  [INFERRED]
  openapi.md → backend/src/main/java/com/idasta/jetstore/dto/AgregarAlCarritoDTO.java
- `2.5.1 `dto/AgregarStockDTOTest.java` — 3 tests` --references--> `AgregarStockDTO`  [INFERRED]
  main/PLAN_PRUEBAS_UNITARIAS.md → backend/src/main/java/com/idasta/jetstore/dto/AgregarStockDTO.java
- ``CarritoResponseDTO`` --references--> `CarritoResponseDTO`  [INFERRED]
  openapi.md → backend/src/main/java/com/idasta/jetstore/dto/CarritoResponseDTO.java
- ``ConfirmarPagoDTO`` --references--> `ConfirmarPagoDTO`  [INFERRED]
  openapi.md → backend/src/main/java/com/idasta/jetstore/dto/ConfirmarPagoDTO.java
- ``FiltroLibroDTO`` --references--> `FiltroLibroDTO`  [INFERRED]
  openapi.md → backend/src/main/java/com/idasta/jetstore/dto/FiltroLibroDTO.java

## Import Cycles
- 3-file cycle: `Frontend/src/services/api.ts -> Frontend/src/store/auth-store.ts -> Frontend/src/services/auth.service.ts -> Frontend/src/services/api.ts`

## Communities (156 total, 35 thin omitted)

### Community 0 - "PasswordRecoveryServiceImplTest"
Cohesion: 0.08
Nodes (11): RestablecerPasswordDTO, SolicitarRecuperacionDTO, Recuperacion, Sesion, RecuperacionRepo, CorreoService, PasswordRecoveryServiceImpl, PasswordRecoveryServiceImplTest (+3 more)

### Community 1 - "read_rows"
Cohesion: 0.17
Nodes (3): _resolve_dial(), read_rows(), TestReasoningContract

### Community 2 - "app/page.tsx"
Cohesion: 0.06
Nodes (35): HomePage(), BenefitCard(), Benefits(), cardVariants, containerVariants, iconMap, cardVariants, Categories() (+27 more)

### Community 3 - "validate_data.py"
Cohesion: 0.06
Nodes (44): read_rows(), TestAccessibilityGuidance, TestChartsTypographyAndIcons, TestCurrentReactGuidance, TestSemanticColors, TestGeneratedCatalogContract, _catalog_date(), _check_app_interface_contract() (+36 more)

### Community 4 - "search"
Cohesion: 0.05
Nodes (12): _normalize_rtl_level(), _rtl_row_filter(), search(), _style_search_destination(), TestSearchDomains, TestImeGuidance, TestMapGuidance, TestMapProductTypes (+4 more)

### Community 5 - "react"
Cohesion: 0.06
Nodes (36): AdminCategoriasPage(), AdminConfiguracionPage(), AdminLogsPage(), AdminPedidosPage(), AdminProductosPage(), AdminUsuariosPage(), ComprasPage(), CategoriesTable() (+28 more)

### Community 6 - "lombok.Getter"
Cohesion: 0.09
Nodes (4): CarritoItem, Cliente, ClienteVenta, CarritoItemRepo

### Community 7 - "Usuario"
Cohesion: 0.08
Nodes (15): LoginResponseDTO, RegistrarUsuarioDTO, UsuarioPerfilDTO, DataSeeder, Rol, Usuario, RolRepo, SesionRepo (+7 more)

### Community 8 - "core.py"
Cohesion: 0.07
Nodes (22): _contains_phrase(), _domain_keywords(), _exact_match_diagnostic(), _exact_row_identity(), _file_signature(), _get_bm25(), _legacy_successor_guidance(), _load_csv() (+14 more)

### Community 9 - "cn"
Cohesion: 0.06
Nodes (34): AdminLayout(), CatalogoPage(), AdminHeader(), AdminHeaderProps, AdminSidebar(), AdminSidebarProps, NAV_ITEMS, CatalogoView() (+26 more)

### Community 10 - "Button"
Cohesion: 0.08
Nodes (32): LoginPage(), OlvidastePasswordClient(), Page(), RegisterPage(), Page(), ResetPasswordClient(), CheckoutForm(), CheckoutFormProps (+24 more)

### Community 11 - "search_stack"
Cohesion: 0.08
Nodes (10): _exact_stack_identifier(), _project_row(), search_stack(), _valid_max_results(), TestDiagnosticsContracts, _rows(), TestNativeDesktopStackFreshness, TestTextLayoutRetrieval (+2 more)

### Community 12 - "environment"
Cohesion: 0.05
Nodes (38): NEXT_PUBLIC_API_URL, build, depends_on, environment, ports, args, context, postgres (+30 more)

### Community 13 - "vitest"
Cohesion: 0.06
Nodes (19): mockProducts, mockFeatured, navItems, Sidebar(), SidebarProps, ProductGallery(), ProductGalleryProps, mockRelatedProducts (+11 more)

### Community 14 - "design_system.py"
Cohesion: 0.07
Nodes (9): format_tokens_dtcg(), apply_decision_rules(), _object_without_duplicates(), parse_decision_rules(), _validate_action(), format_output(), _node_available(), read_rows() (+1 more)

### Community 15 - "ReportsView.tsx"
Cohesion: 0.11
Nodes (18): AdminDashboardPage(), AdminReportesPage(), Charts(), SalesChart(), TopProductsChart(), ReportsView(), STATS, StatsCards() (+10 more)

### Community 16 - "ContactoClient.tsx"
Cohesion: 0.13
Nodes (9): ContactoPage(), ConfiguracionPage(), PerfilPage(), ProfileForm(), SettingsForm(), Textarea, TextareaProps, Toast() (+1 more)

### Community 17 - "GuardarLibroDTO"
Cohesion: 0.15
Nodes (7): GuardarLibroDTO, PasswordUtil, LibroServiceImplTest, 5. Estrategia y Priorización, Fase 1 — Alta prioridad (Services Backend + Stores Frontend), Fase 2 — Media prioridad (Controllers Backend + Components Frontend), Fase 3 — Baja prioridad (Helpers, Models, DTOs, Utils, Services Frontend)

### Community 18 - "api.ts"
Cohesion: 0.10
Nodes (22): mockProducts, adminService, ApiResponse, api, ApiResponse, CarritoResponse, cartService, ApiResponse (+14 more)

### Community 19 - "browser-qa/package.json"
Cohesion: 0.07
Nodes (30): author, dependencies, playwright@^1.50.0, description, keywords, license, name, optionalDependencies (+22 more)

### Community 20 - "Carrito"
Cohesion: 0.14
Nodes (9): CarritoResponseDTO, ItemCarritoDTO, MercadoPagoPreferenceDTO, PagarRequestDTO, Carrito, CarritoServiceImpl, CarritoServiceImplTest, 3.1 Daniel — Backend (10 pruebas) — Lógica de Servicios (+1 more)

### Community 21 - "list"
Cohesion: 0.05
Nodes (19): PagoResponseDTO, VentaResponseDTO, Pago, Venta, CarritoRepo, PagoRepo, VentaRepo, MercadoPagoService (+11 more)

### Community 22 - "generate_design_system"
Cohesion: 0.07
Nodes (15): build_design_tokens(), _css_font_family(), _flatten_tokens(), format_tokens_css(), generate_design_system(), persist_design_system(), _resolve_token_reference(), _root_block() (+7 more)

### Community 23 - "preview.py"
Cohesion: 0.05
Nodes (27): _button_outline_text_color(), _contrast_ratio(), _derive_dark_palette(), format_master_md(), safe_slug(), _write_persisted_file(), _attr(), _css_color() (+19 more)

### Community 24 - "GuardarCategoriaDTO"
Cohesion: 0.16
Nodes (4): CategoriaController, CategoriaDTO, GuardarCategoriaDTO, CategoriaService

### Community 25 - "index.ts"
Cohesion: 0.05
Nodes (64): nextConfig, CarritoPage(), mockActualizarCantidad, mockAgregar, mockCargarCarrito, mockEliminar, mockSubtotal, mockVaciar (+56 more)

### Community 26 - "LibroService"
Cohesion: 0.11
Nodes (4): LibroController, AgregarStockDTO, LibroService, `AgregarStockDTO`

### Community 27 - "org.junit.jupiter.api.Test"
Cohesion: 0.11
Nodes (13): AgregarAlCarritoDTO, AgregarAlCarritoDTOTest, GuardarLibroDTOTest, PasswordUtilTest, JetstoreApplicationTests, CarritoItemTest, CarritoTest, 3.2 Maryory — QA (10 pruebas) — Validación, Modelo y Seguridad (+5 more)

### Community 28 - "Libro"
Cohesion: 0.11
Nodes (4): FiltroLibroDTO, Categoria, Libro, LibroRepoCustom

### Community 29 - "JetStore Digital"
Cohesion: 0.06
Nodes (34): 1. Cómo ejecutar las pruebas, 2. Detalle de las pruebas por integrante, 4. Funcionalidad probada (visión general), 5. Resultados de cobertura (13 de julio de 2026), 6. Notas técnicas, 7. Documentos generados, Backend — JaCoCo, Backend (JUnit 5 + Mockito) (+26 more)

### Community 30 - "BM25"
Cohesion: 0.15
Nodes (4): BM25, _suggest_identities(), TestBm25CoreBehavior, TestTokenizer

### Community 31 - "browser-qa"
Cohesion: 0.06
Nodes (33): Accessibility audit, After deploying a change, All Modes, Automated verification in CI-like workflows, Background, browser-qa, Contributing tests, Evaluations (+25 more)

### Community 32 - "DigitalLibrary.tsx"
Cohesion: 0.29
Nodes (4): BibliotecaPage(), DigitalLibrary(), compras, Compra

### Community 33 - "Reporte de Pruebas Unitarias — JetStore Digital"
Cohesion: 0.08
Nodes (23): 10. Recomendaciones, 1. Información General, 2. Herramientas de Testing Utilizadas, 3. Comandos de Ejecución, 5.1 `validators.test.ts` (10 pruebas), 5.3 `cn.test.ts` (1 prueba), 5. Resultados Frontend — Detalle por Archivo de Test, 6.2 Frontend (Vitest Istanbul) (+15 more)

### Community 34 - "test_design_system_mode.py"
Cohesion: 0.14
Nodes (6): _filter_anti_patterns_for_mode(), _query_wants_dark(), _resolve_color_mode(), _style_is_dark_primary(), TestAntiPatternGating, TestModeResolution

### Community 35 - "Browser QA v2 — Universal Browser Testing"
Cohesion: 0.09
Nodes (22): Browser QA v2 — Universal Browser Testing, Codebase-Driven Test Generation Mode, Coverage Tracking, Framework-Specific Extensions, --from-verify Mode (verify-change integration), Interactive Testing Details, Modes, Phase 1: Quick Smoke (agent-browser) ~3s (+14 more)

### Community 36 - "3. Heal"
Cohesion: 0.09
Nodes (23): 0. How generation works, 1.1 Prerequisite: workspace, 1.2 Prerequisite: seed test, 1.3 Explore the app, 1.4 Write the spec file, 1. Planning, 2.1 Inputs, 2.2 Generate one scenario (+15 more)

### Community 40 - "compilerOptions"
Cohesion: 0.11
Nodes (18): compilerOptions, allowJs, esModuleInterop, incremental, isolatedModules, jsx, lib, module (+10 more)

### Community 42 - "LibroServiceImplTest.java"
Cohesion: 0.22
Nodes (3): ConfirmarPagoDTO, PagoServiceImplTest, 3. Cobertura adicional del backend (17 pruebas)

### Community 46 - "UsuarioService"
Cohesion: 0.15
Nodes (5): UsuarioController, LoginDTO, UsuarioService, 2.2.2 `controller/UsuarioControllerTest.java` — 5 tests, `LoginDTO`

### Community 48 - "format_ascii_box"
Cohesion: 0.13
Nodes (9): ansi_ljust(), format_ascii_box(), add_wrapped(), wrap_text(), format_markdown(), hex_to_ansi(), _mode_support_labels(), section_header() (+1 more)

### Community 49 - "DbConstraintFix.java"
Cohesion: 0.20
Nodes (3): WebConfig, WebMvcConfigurer, DbConstraintFix

### Community 51 - "formatCurrency"
Cohesion: 0.11
Nodes (27): AdminPagosPage(), PaymentsTable(), STATUS_VARIANT, CartSummary(), CartSummaryProps, methodLabels, OrderSummary(), OrderSummaryProps (+19 more)

### Community 53 - "dependencies"
Cohesion: 0.13
Nodes (15): dependencies, axios@^1.18.0, framer-motion@^12.40.0, @hookform/resolvers@^5.4.0, next@16.2.9, react@19.2.4, react-dom@19.2.4, react-hook-form@^7.79.0 (+7 more)

### Community 55 - "devDependencies"
Cohesion: 0.14
Nodes (14): devDependencies, babel-plugin-react-compiler@1.0.0, jsdom@^29.1.1, tailwindcss@^4, @testing-library/react@^16.3.2, @testing-library/user-event@^14.6.1, @types/react@^19, @vitejs/plugin-react@^6.0.3 (+6 more)

### Community 56 - "Arquitectura C4 — JetStore Digital"
Cohesion: 0.09
Nodes (21): 3.1 — Componentes del Frontend, 3.2 — Componentes del Backend, 3.3 — Componentes de la Base de Datos, Actores del Sistema, Arquitectura C4 — JetStore Digital, Componentes del Backend — Detalle, Componentes del Frontend — Detalle, Comunicación entre Contenedores (+13 more)

### Community 57 - "scripts"
Cohesion: 0.14
Nodes (13): name, private, scripts, build, db:down, db:reset, db:up, dev (+5 more)

### Community 58 - "LibroServiceImpl.java"
Cohesion: 0.27
Nodes (6): Mapear, 2.3.3 `helper/MapearTest.java` — 1 test, 6.1 Backend (JaCoCo), Cobertura por Paquete, Detalle por Clase (top cobertura), Resumen General

### Community 60 - "_palette_is_dark"
Cohesion: 0.17
Nodes (4): _palette_is_dark(), _relative_luminance(), TestEndToEndCoherence, TestLuminance

### Community 61 - "RespuestaApi"
Cohesion: 0.11
Nodes (19): RespuestaApi, 1. Objetivo, 2. Inventario de la superficie API, 3. Estrategia de pruebas (4 capas), 4. Flujos críticos a probar, 5. Plan por sprints, 6. Criterios de salida, 7. Trazabilidad con el kanban (+11 more)

### Community 62 - "Group 1: Hard Gate (absolute, no exceptions)"
Cohesion: 0.10
Nodes (20): Group 1: Hard Gate (absolute, no exceptions), R-02 — Copywriting, R-03 — Mobile Responsiveness, R-17 — Data & Numbers, R-18 — Testimonials, R-23 — Clarification & Visual Assets, R-24 — Navigation, R-25 — Color Contrast (+12 more)

### Community 63 - "CarritoService"
Cohesion: 0.12
Nodes (7): CarritoController, CarritoService, 2.2.1 `controller/LibroControllerTest.java` — 10 tests, 2.2.3 `controller/CarritoControllerTest.java` — 3 tests, 2.2.4 `controller/PagoControllerTest.java` — 2 tests, 2.2.5 `controller/RolControllerTest.java` — 2 tests, 2.2 Tests de Controllers (nuevos)

### Community 64 - "Frontend/package.json"
Cohesion: 0.17
Nodes (11): @tailwindcss/postcss@^4, @types/node@^20, @types/react-dom@^19, @vitest/coverage-v8@^2.1.9, name, private, version, @tailwindcss/postcss (+3 more)

### Community 65 - "Browser Session Management"
Cohesion: 0.10
Nodes (20): 1. Name Browser Sessions Semantically, 2. Always Clean Up, 3. Delete Stale Browser Data, A/B Testing Sessions, Attach by channel name, Attach via browser extension, Attach via CDP endpoint, Attaching to a Running Browser (+12 more)

### Community 67 - "Storage Management"
Cohesion: 0.11
Nodes (18): Authentication State Reuse, Clear sessionStorage, Common Patterns, Delete Database, Delete Single Item, Get Single Value, IndexedDB, List All sessionStorage Items (+10 more)

### Community 68 - "app/layout.tsx"
Cohesion: 0.19
Nodes (9): geistMono, geistSans, metadata, RootLayout(), categorias, Footer(), quickLinks, socialLinks (+1 more)

### Community 71 - "UI/UX Pro Max - Design Intelligence"
Cohesion: 0.11
Nodes (18): Before Delivering App UI, Example Workflow, If a search returns 0 results, Output Formats, Query Contract, Rule Categories by Priority, Running the search tool, Step 1: Analyze User Requirements (+10 more)

### Community 72 - "OrderConfirmation.tsx"
Cohesion: 0.33
Nodes (4): CheckoutSuccessPage(), methodLabels, OrderConfirmation(), OrderConfirmationProps

### Community 73 - "scripts"
Cohesion: 0.25
Nodes (8): scripts, build, dev, lint, start, test, test:coverage, test:watch

### Community 74 - "test_skill_script_paths.py"
Cohesion: 0.38
Nodes (3): resolve(), shipped_invocations(), SkillScriptPathsTest

### Community 75 - "Informe de Pruebas Unitarias — JetStore Digital"
Cohesion: 0.11
Nodes (17): 1. Resumen Ejecutivo, 2.1 Backend — JaCoCo, 2.2 Frontend — Vitest (Istanbul), 2. Cobertura de Código, 3.3 Lorena — Frontend (10 pruebas) — Validadores de Formularios, 3. Detalle de Pruebas por Integrante, 4. Cobertura Adicional del Backend (17 pruebas), 5. Análisis de Cobertura por Capa (+9 more)

### Community 76 - "Tracing"
Cohesion: 0.12
Nodes (16): 1. Start Tracing Before the Problem, 2. Clean Up Old Traces, Analyzing Performance, Basic Usage, Best Practices, Capturing Evidence, Debugging Failed Actions, Limitations (+8 more)

### Community 79 - "EstadoPago"
Cohesion: 0.20
Nodes (9): EstadoPago, COMPLETADO, FALLIDO, PENDIENTE, REEMBOLSADO, Enums, Esquemas / Schemas, `EstadoPago` (+1 more)

### Community 80 - "eslint.config.mjs"
Cohesion: 0.33
Nodes (5): eslintConfig, eslint@^9, eslint-config-next@16.2.9, eslint, eslint-config-next

### Community 81 - "Jetstore"
Cohesion: 0.12
Nodes (15): Carrito, Colección Postman, Configuración, Ejecutar, Endpoints, Estructura del proyecto, Jetstore, Libro (+7 more)

### Community 84 - "GlobalExceptionHandler.java"
Cohesion: 0.32
Nodes (4): GlobalExceptionHandler, Backend, Consideraciones Técnicas, Frontend

### Community 86 - "setup.tsx"
Cohesion: 0.50
Nodes (3): @testing-library/jest-dom@^6.9.1, localStorageMock, @testing-library/jest-dom

### Community 88 - "opencode.json"
Cohesion: 0.50
Nodes (3): $schema, skills, paths

### Community 94 - "Browser Automation with playwright-cli"
Cohesion: 0.13
Nodes (15): Attaching screenshots and videos to pull requests, Browser Automation with playwright-cli, Browser Sessions, Example: Debugging with DevTools, Example: Form submission, Example: Interactive session, Example: Multi-tab workflow, Installation (+7 more)

### Community 95 - "LibroRepo"
Cohesion: 0.15
Nodes (12): LibroRepo, 2.1.1 `LibroServiceImplTest.java` — agregar 7 tests, 2.1.3 `CarritoServiceImplTest.java` — agregar 2 tests, 2.1 Completar tests de Services (existentes, métodos faltantes), 4.10 `AgregarAlCarritoDTOTest.java` (3 pruebas), 4.2 `LibroServiceImplTest.java` (5 pruebas), 4.4 `CarritoServiceImplTest.java` (4 pruebas), 4.6 `CarritoTest.java` (5 pruebas) (+4 more)

### Community 96 - "Group 2: Purpose-Gate (technique allowed, purpose required)"
Cohesion: 0.15
Nodes (13): Group 2: Purpose-Gate (technique allowed, purpose required), R-01 — Color & Gradients, R-04 — Icons, R-06 — Typography, R-07 — Background, R-08 — Button Arrows, R-09 — Badges, R-10 — Glassmorphism (+5 more)

### Community 97 - "Extending browser-qa with Framework-Specific Skills"
Cohesion: 0.15
Nodes (12): browser-qa-astro, browser-qa-cloudflare, browser-qa-nextjs, browser-qa-svelte / browser-qa-sveltekit, browser-qa-vue / browser-qa-nuxt, Example Extensions, Extending browser-qa with Framework-Specific Skills, Extension Skill Structure (+4 more)

### Community 98 - "playwright-cli/SKILL.md"
Cohesion: 0.21
Nodes (4): Examples, Inspecting Element Attributes, Debugging Playwright Tests, Running Playwright Tests

### Community 99 - "Running Custom Playwright Code"
Cohesion: 0.15
Nodes (13): Clipboard, Complex Workflows, Error Handling, File Downloads, Frames and Iframes, Geolocation, JavaScript Execution, Media Emulation (+5 more)

### Community 100 - "Pre-Delivery Checklist (canonical — the only one)"
Cohesion: 0.15
Nodes (12): Accessibility, Common Rules for Professional UI + Pre-Delivery Checklist, Icons & Visual Elements, Interaction, Interaction (App), Layout, Layout & Spacing, Light/Dark Mode (+4 more)

### Community 101 - "Quick Reference"
Cohesion: 0.15
Nodes (12): 10. Charts & Data (LOW), 1. Accessibility (CRITICAL), 2. Touch & Interaction (CRITICAL), 3. Performance (HIGH), 4. Style Selection (HIGH), 5. Layout & Responsive (HIGH), 6. Typography & Color (MEDIUM), 7. Animation (MEDIUM) (+4 more)

### Community 102 - "Codebase-Driven Route Discovery"
Cohesion: 0.17
Nodes (11): API Calls, Codebase-Driven Route Discovery, Extracting Page Components, Forms, Interactive Elements, Links, Mapping file paths to URL routes, Strategy 1: File-Based Routing (+3 more)

### Community 103 - "Commands"
Cohesion: 0.17
Nodes (12): Commands, Core, DevTools, Emulation, Keyboard, Mouse, Navigation, Network (+4 more)

### Community 105 - "Entidades"
Cohesion: 0.17
Nodes (12): `Carrito`, `CarritoItem`, `Categoria`, `Cliente`, `DetalleVenta`, Entidades, `Libro`, `Pago` (+4 more)

### Community 106 - "Coverage Tracking"
Cohesion: 0.18
Nodes (10): API endpoints (denominator), Collecting Counts, Coverage Report Format, Coverage Tracking, Forms (denominator), Improving Coverage, Links (denominator), Metrics (+2 more)

### Community 107 - "4.1. Prototipo de la solución"
Cohesion: 0.18
Nodes (11): 4.1. Prototipo de la solución, 4.2. Planificación de entrevistas cualitativas, API REST, Arquitectura C4, CAPÍTULO IV: DESARROLLO DEL PRODUCTO MÍNIMO VIABLE, Catálogo real y estructura de precios, Descripción funcional de las interfaces, Estructura del Frontend (+3 more)

### Community 108 - "Kanban — JetStore Digital (tablero QA)"
Cohesion: 0.18
Nodes (10): Comandos útiles, Kanban — JetStore Digital (tablero QA), Leyenda de estados, Notas del mapeo, Orden de ejecución recomendado (QA), Planes de acción (enlazados desde este tablero), Resumen, Área 1 — Infraestructura y herramientas (+2 more)

### Community 109 - "Endpoints"
Cohesion: 0.18
Nodes (11): 1. Rol, 4. Carrito, 5. Pago, `DELETE /carrito/eliminar/{usuarioId}/{libroId}`, Endpoints, `GET /carrito/ver/{usuarioId}`, `GET /rol/todos`, `POST /carrito/agregar` (+3 more)

### Community 110 - "3. Libro"
Cohesion: 0.18
Nodes (11): 3. Libro, `DELETE /libro/eliminar/{libroId}`, `GET /libro/buscar?q={query}`, `GET /libro/categoria/{nombre}`, `GET /libro/categorias`, `GET /libro/recientes`, `GET /libro/todos`, `GET /libro/ver/{id}` (+3 more)

### Community 111 - "Group 3: Quality Locks (consistency)"
Cohesion: 0.20
Nodes (10): Group 3: Quality Locks (consistency), R-05 — Layout & Page Structure, R-11 — Border Radius, R-15 — CTA (Call to Action), R-16 — Copywriting & Buzzwords, R-20 — Visual Identity, R-21 — Dark Mode, R-29 — Color Palette (+2 more)

### Community 112 - "User Flow Generation"
Cohesion: 0.20
Nodes (9): Building the Flow Graph, Error Flow Pattern, Flow Assertions, Flow Test Template, Generating Flows Automatically, Step 1: Discover entry points, Step 2: Build edges, Step 3: Identify distinct flows (+1 more)

### Community 113 - "Video Recording"
Cohesion: 0.20
Nodes (10): 1. Use Descriptive Filenames, 2. Record entire hero scripts., 3. Attach the recording to the pull request, Basic Recording, Best Practices, Cursor, Target Highlight and Click Point, Limitations, Overlay API Summary (+2 more)

### Community 115 - "CAPÍTULO II: DESCRIPCIÓN DE LA PROPUESTA DE INNOVACIÓN"
Cohesion: 0.20
Nodes (10): 2.1. Visión y misión del proyecto, 2.2. Objetivos, 2.3. Justificación, 2.4. Marco teórico, 2.5. Análisis de la Competencia, 2.6. Análisis FODA, 2.7. Formulación del modelo de negocio, Antecedentes (+2 more)

### Community 116 - "INFORME FINAL DEL PROYECTO DE INNOVACIÓN TECNOLÓGICA"
Cohesion: 0.20
Nodes (10): AGRADECIMIENTO, DEDICATORIA, INFORME FINAL DEL PROYECTO DE INNOVACIÓN TECNOLÓGICA, Instituto de Educación Superior Tecnológico Público "Santiago Antúnez de Mayolo", INTRODUCCIÓN, Modalidad: Aplicación Profesional, PORTADA, ÍNDICE DE FIGURAS (+2 more)

### Community 117 - "DTOs Request"
Cohesion: 0.20
Nodes (10): `AgregarAlCarritoDTO`, `ConfirmarPagoDTO`, DTOs Request, `FiltroLibroDTO`, `GuardarCategoriaDTO`, `GuardarLibroDTO`, `PagarRequestDTO`, `RegistrarUsuarioDTO` (+2 more)

### Community 118 - "Cookies"
Cohesion: 0.22
Nodes (9): Advanced: Multiple Cookies or Custom Options, Clear All Cookies, Cookies, Delete a Cookie, Filter Cookies by Domain, Filter Cookies by Path, Get Specific Cookie, List All Cookies (+1 more)

### Community 119 - "Util"
Cohesion: 0.28
Nodes (5): Util, Validar, 2.3.1 `helper/UtilTest.java` — 5 tests, 2.3.2 `helper/ValidarTest.java` — 4 tests, 2.3 Tests de Helpers (nuevos)

### Community 120 - "antislop"
Cohesion: 0.25
Nodes (7): Already installed, and the user asks how to update, antislop, Core Principle, Functional Patterns, Part 2: Mandatory Rules (R-01 to R-40, grouped), Two Usage Modes, What This Is (and What It Isn't)

### Community 121 - "Part 1: AI Slop Patterns (Warning Signs)"
Cohesion: 0.25
Nodes (8): Accessibility, Copywriting & Content, Decorative Elements, Functionality & Content, Identity & Originality, Layout & Components, Part 1: AI Slop Patterns (Warning Signs), Visual & Color

### Community 122 - "Spec-Driven Testing Reference"
Cohesion: 0.25
Nodes (7): Best Practices, Generating Tests from Spec, Input: Spec Acceptance Criteria, Output: Playwright Tests, Playwright Agents Integration, Spec-Driven Testing Reference, The Pattern

### Community 123 - "Advanced Mocking with run-code"
Cohesion: 0.25
Nodes (8): Advanced Mocking with run-code, CLI Route Commands, Conditional Response Based on Request, Delayed Response, Modify Real Response, Request Mocking, Simulate Network Failures, URL Patterns

### Community 124 - "Local Storage"
Cohesion: 0.25
Nodes (8): Advanced: Multiple Operations, Clear All localStorage, Delete Single Item, Get Single Value, List All localStorage Items, Local Storage, Set JSON Value, Set Value

### Community 125 - "JetStore — Frontend"
Cohesion: 0.25
Nodes (7): Autenticación, Estructura, Inicio, JetStore — Frontend, Roles, Scripts, Variables de entorno

### Community 126 - "2. Backend — Plan de Pruebas Nuevas"
Cohesion: 0.25
Nodes (8): 2.4.1 `model/VentaTest.java` — 3 tests, 2.4.3 `model/SesionTest.java` — 4 tests, 2.4.4 `model/PagoTest.java` — 3 tests, 2.4 Tests de Models (nuevos), 2.5.1 `dto/AgregarStockDTOTest.java` — 3 tests, 2.5 Tests de DTOs (nuevos), 2. Backend — Plan de Pruebas Nuevas, Resumen Backend — Tests nuevos a crear

### Community 127 - "3. Frontend — Plan de Pruebas Nuevas"
Cohesion: 0.25
Nodes (8): 3.1 Completar Utils existentes (2 tests), 3.4.1 `components/ui/__tests__/Button.test.tsx` — 5 tests, 3.4.2 `components/ui/__tests__/Input.test.tsx` — 4 tests, 3.4.3 `components/ui/__tests__/Modal.test.tsx` — 3 tests, 3.4.4 `components/ui/__tests__/Alert.test.tsx` — 3 tests, 3.4 Tests de Components (15 tests), 3. Frontend — Plan de Pruebas Nuevas, Resumen Frontend — Tests nuevos a crear

### Community 128 - "JetStore Digital API - Documentación OpenAPI 3.0"
Cohesion: 0.25
Nodes (7): Autenticación, Cifrado de Contraseñas, CORS, JetStore Digital API - Documentación OpenAPI 3.0, Respuesta Estándar, Resumen de Endpoints, Tabla de Contenidos

### Community 129 - "2. Usuario"
Cohesion: 0.25
Nodes (8): 2. Usuario, `GET /usuario/perfil/{usuarioId}`, `GET /usuario/todos`, `POST /usuario/cerrar-sesion`, `POST /usuario/login`, `POST /usuario/recuperar`, `POST /usuario/registrar`, `POST /usuario/restablecer`

### Community 130 - "PROMPT"
Cohesion: 0.25
Nodes (7): Audiencia, Contexto del proyecto, Entregable esperado, Estructura sugerida (12 a 15 diapositivas), PROMPT, Prompt para generar las diapositivas (presentación final), Requisitos de formato

### Community 131 - "MetodoPago"
Cohesion: 0.29
Nodes (6): MetodoPago, MERCADOPAGO, PLIN, TARJETA, YAPE, Autenticación y flujos

### Community 132 - "The Craftsmanship Standard"
Cohesion: 0.33
Nodes (6): C-1 — Intentionality, C-2 — Functional Completeness, C-3 — Content-Driven Composition, C-4 — Resilience, C-5 — Evidence Over Claims, The Craftsmanship Standard

### Community 133 - "_generate_intelligent_overrides"
Cohesion: 0.33
Nodes (3): _detect_page_type(), format_page_override_md(), _generate_intelligent_overrides()

### Community 134 - "split_values"
Cohesion: 0.47
Nodes (3): split_values(), style_identities(), TestStyleIdentityContract

### Community 136 - "FAQ.tsx"
Cohesion: 0.53
Nodes (3): AccordionItem(), FAQ(), faqs

### Community 137 - "INFORME_FINAL_JETSTORE.md"
Cohesion: 0.33
Nodes (5): ANEXOS, BIBLIOGRAFÍA, Conclusiones, CONCLUSIONES Y RECOMENDACIONES, Recomendaciones

### Community 138 - "CAPÍTULO I: PLANTEAMIENTO DEL PROBLEMA"
Cohesion: 0.33
Nodes (6): 1.1. Identificación del problema, 1.2. Identificación del ODS y EDS al que se relaciona el problema, 1.3. Descripción del perfil del cliente, 1.4. Formulación del Mapa de Empatía, 1.5. Formulación del Mapa de trayectoria del cliente, CAPÍTULO I: PLANTEAMIENTO DEL PROBLEMA

### Community 139 - "7.1. Dimensiones de sostenibilidad"
Cohesion: 0.33
Nodes (6): 7.1.1. Aspecto Social, 7.1.2. Aspecto Ambiental, 7.1.3. Aspecto Económico, 7.1.4. Aspecto Tecnológico, 7.1. Dimensiones de sostenibilidad, CAPÍTULO VII: SOSTENIBILIDAD DEL PROYECTO

### Community 140 - "Plan de Pruebas Unitarias — JetStore Digital"
Cohesion: 0.33
Nodes (5): 1. Estado Actual, 4. Resumen General, Archivos de test a crear, Plan de Pruebas Unitarias — JetStore Digital, Tabla de Contenido

### Community 141 - "Delivery Gate (Mandatory)"
Cohesion: 0.40
Nodes (5): Block 1: Hard Gate (absolute), Block 2: Purpose-Gate (technique allowed, reason required), Block 3: Liveliness (required to be alive, not just clean), Block 4: Craftsmanship & Quality Locks, Delivery Gate (Mandatory)

### Community 142 - "Attaching Screenshots and Videos to Pull Requests"
Cohesion: 0.40
Nodes (5): Attaching Screenshots and Videos to Pull Requests, From a local session, From CI, Limits, When to attach

### Community 145 - "CAPÍTULO VI: IMPLEMENTACIÓN DE LA SOLUCIÓN"
Cohesion: 0.40
Nodes (5): 6.1. Alcance esperado del proyecto, 6.2. Gantt proyectado de ejecución, 6.3. Determinación del costo del producto/servicio, 6.4. Presupuesto y financiamiento, CAPÍTULO VI: IMPLEMENTACIÓN DE LA SOLUCIÓN

### Community 146 - "3.2 Tests de Stores (16 tests)"
Cohesion: 0.40
Nodes (5): 3.2.1 `store/__tests__/ui-store.test.ts` — 5 tests, 3.2.2 `store/__tests__/favorites-store.test.ts` — 3 tests, 3.2.3 `store/__tests__/auth-store.test.ts` — 3 tests, 3.2.4 `store/__tests__/cart-store.test.ts` — 5 tests, 3.2 Tests de Stores (16 tests)

### Community 147 - "3.3 Tests de Services (12 tests)"
Cohesion: 0.40
Nodes (5): 3.3.1 `services/__tests__/api.test.ts` — 2 tests, 3.3.2 `services/__tests__/auth.service.test.ts` — 3 tests, 3.3.3 `services/__tests__/product.service.test.ts` — 4 tests, 3.3.4 `services/__tests__/cart.service.test.ts` — 3 tests, 3.3 Tests de Services (12 tests)

### Community 148 - "AGENTS.md"
Cohesion: 0.50
Nodes (3): graphify, Kanban rule, UX decision rule

### Community 149 - "Part 3: Liveliness Toolkit"
Cohesion: 0.50
Nodes (4): Design Read (how the dials are set), Levers (how the dials become visual decisions), Part 3: Liveliness Toolkit, Three Dials (required)

### Community 151 - "CAPÍTULO III: METODOLOGÍA"
Cohesion: 0.50
Nodes (4): 3.1.1. Fases, etapas y herramientas aplicadas, 3.1.2. Actividades, 3.1. Método, CAPÍTULO III: METODOLOGÍA

### Community 152 - "CAPÍTULO V: VALIDACIÓN DE LA SOLUCIÓN"
Cohesion: 0.50
Nodes (4): 5.1. Informe de entrevistas cualitativas, 5.2. Formulación de la Malla receptora de información, 5.3. Formulación del modelo de negocio actualizado post entrevista, CAPÍTULO V: VALIDACIÓN DE LA SOLUCIÓN

## Knowledge Gaps
- **9 isolated node(s):** `com.idasta:jetstore`, `PENDIENTE`, `COMPLETADO`, `FALLIDO`, `REEMBOLSADO` (+4 more)
  These have ≤1 connection - possible missing edges or undocumented components. (Counts symbols only; 1087 node(s) total have ≤1 connection when file, concept and rationale nodes are included.)
- **35 thin communities (<3 nodes) omitted from report** — run `graphify query` to explore isolated nodes.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `search()` connect `search` to `read_rows`, `DesignSystemGenerator`, `validate_data.py`, `_generate_intelligent_overrides`, `detect_domain`, `core.py`, `search_stack`, `design_system.py`, `formatCurrency`, `BM25`?**
  _High betweenness centrality (0.266) - this node is a cross-community bridge._
- **What connects `com.idasta:jetstore`, `PENDIENTE`, `COMPLETADO` to the rest of the system?**
  _9 weakly-connected nodes found - possible documentation gaps or missing edges._
- **Should `PasswordRecoveryServiceImplTest` be split into smaller, more focused modules?**
  _Cohesion score 0.08357685563997662 - nodes in this community are weakly interconnected._
- **Why does `Área 2 — Calidad y pruebas` connect `formatCurrency` to `Kanban — JetStore Digital (tablero QA)`, `search`?**
  _High betweenness centrality (0.262) - this node is a cross-community bridge._
- **Should `app/page.tsx` be split into smaller, more focused modules?**
  _Cohesion score 0.06485671191553545 - nodes in this community are weakly interconnected._
- **Why does `formatDate()` connect `formatCurrency` to `react`?**
  _High betweenness centrality (0.184) - this node is a cross-community bridge._
- **Should `validate_data.py` be split into smaller, more focused modules?**
  _Cohesion score 0.06338028169014084 - nodes in this community are weakly interconnected._
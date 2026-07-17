# SAQI-003: Documentación del Caso de Estudio Open-RootERP (Versión Corregida)

**Versión:** 1.1
**Fecha:** 2026-07-17
**Estado:** Corregido con datos reales del proyecto
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Introducción y Alcance

Este documento registra el **primer caso de estudio experimental** de la metodología SAQI: el desarrollo de **Open-RootERP**, un sistema ERP ligero (lightweight), 100% offline-first, desarrollado con arquitectura MVC vanilla JavaScript ES Modules, IndexedDB vía Dexie.js, y Service Worker.

**IMPORTANTE — Corrección metodológica:** La versión anterior de este documento (v1.0) contenía **imprecisiones factuales** derivadas de una descripción objetivo del proyecto que no coincidía con la implementación real. Esta versión v1.1 corrige todos los datos basándose en la inspección directa del código fuente, documentación del proyecto (`README.md`, `ARCHITECTURE.md`, `QA_TESTING_PLAN.md`, `QA_RESULTS.md`, `PROJECT_STATE.md`, `docs/Context.md`), y resultados de testing ejecutables.

**Objetivo del caso de estudio:** Servir como **instancia observacional única (N=1)** para:
- Documentar la aplicación práctica de SAQI (con las correcciones necesarias)
- Recopilar métricas base (baseline) reales y verificables
- Identificar patrones, problemas y oportunidades de mejora reales
- Formular hipótesis comprobables para validación experimental futura

**Principio metodológico:** Separación estricta entre **HECHOS OBSERVADOS** (datos medibles, verificables), **INTERPRETACIONES** (análisis causal plausible) e **HIPÓTESIS** (predicciones comprobables). **No se afirma causalidad** entre SAQI y los resultados observados.

---

## 2. Descripción Real del Proyecto Open-RootERP

### 2.1 Visión General (Datos Verificados)

| Atributo | Valor Real (Verificado en Código) |
|----------|-----------------------------------|
| **Nombre** | Open RootERP |
| **Tipo** | ERP **ligero** (lightweight), 100% offline-first |
| **Arquitectura** | **MVC estricto** + Repository Pattern + Service Layer + Inyección de dependencias manual |
| **Stack Principal** | **JavaScript ES2022 (ES Modules)** — **NO TypeScript**, NO React, NO Vite, NO build step |
| **Persistencia** | **IndexedDB vía Dexie.js v4.4.4** (servido localmente desde `assets/lib/dexie.js`) |
| **Offline** | **Service Worker** (Cache API, stale-while-revalidate) + IndexedDB nativo |
| **Gráficos** | Chart.js 4.x UMD (`assets/lib/chart.umd.min.js`) |
| **Servidor Dev** | Python `http.server` (puerto 3000) — ES Modules requieren HTTP |
| **PWA** | `manifest.json` (display: standalone), iconos SVG, SW registra 118 assets |
| **Instalación** | `setup.sh` (Linux) / `setup.bat` (Windows) — automatizan npm install + copia Dexie + servidor |

### 2.2 Arquitectura Real (Verificada en Código)

```
┌─────────────────────────────────────────────────────┐
│                    index.html (SPA)                 │
├─────────────────────────────────────────────────────┤
│                      app.js                          │
│              Router + Bootstrap + DI manual          │
├──────────┬──────────┬──────────┬────────────────────┤
│  Views   │Controllers│ Services │   Repositories     │
│ (render) │ (orquest)│(negocio) │      (datos)       │
├──────────┴──────────┴──────────┴────────────────────┤
│                    Database                            │
│              Dexie.js + IndexedDB                     │
├─────────────────────────────────────────────────────┤
│              Service Worker (sw.js)                  │
│               Cache API + Offline                    │
└─────────────────────────────────────────────────────┘
```

**Regla de oro verificada:** La View **NUNCA** accede directamente al Repository o Database. Flujo obligatorio: View → Controller → Service → Repository → DB.

### 2.3 Estructura de Directorios Real

```
open-rooterp/
├── index.html, manifest.json, sw.js, setup.sh/.bat
├── assets/
│   ├── css/ (variables.css, reset.css, layout.css, components.css~1160 líneas, utilities.css, main.css)
│   ├── lib/ (dexie.js, chart.umd.min.js — copias locales para offline)
│   └── icons/
├── src/
│   ├── app.js                 # Entry point, router, DI wiring
│   ├── config/app.js          # Constantes globales
│   ├── database/
│   │   ├── db.js              # Dexie schema v8 (índices compuestos)
│   │   └── seed.js            # Datos iniciales + transacciones seed
│   ├── models/                # 16 modelos (Account, Product, Sale, etc.)
│   ├── repositories/          # 12 repos encapsulan Dexie
│   ├── services/              # 15 servicios (lógica de negocio)
│   ├── controllers/           # 14 controladores (orquestación)
│   ├── views/                 # 19 vistas (render + eventos) — AccountingView=4 sub-vistas, ReportView=3
│   ├── components/            # 10 componentes UI reutilizables (Table, Form, Modal, Toast, Loader, ConfirmDialog, Header, Sidebar, Pagination, SearchBar)
│   ├── store/AppState.js      # Estado global (reservado, clase vacía)
│   └── utils/                 # sanitizer, validators, helpers, formatters, errors, ThemeManager
├── tests/
│   ├── run-all.js             # Runner unit/integration CON DOM SHIM (Node.js)
│   ├── runner.html            # Runner para navegador
│   ├── services/              # 13 suites servicios
│   ├── controllers/           # 10 suites controladores
│   ├── integration/           # 9 suites integración (54+ tests)
│   ├── utils/                 # 5 suites utilidades
│   └── e2e/                   # Playwright: phase2-adversarial, phase3-stress, phase4-security, phase5-a11y
├── ARCHITECTURE.md, QA_TESTING_PLAN.md, ROADMAP.md, TODO.md, SESSION.md, package.json
└── docs/ (Context.md, PROJECT_STATE.md, QA_RESULTS.md, E2E_TESTS.md, INSTALL.md, THEME_MANAGER.md)
```

### 2.4 Módulos Funcionales Implementados (14/14 Verificados)

| Módulo | Service | Controller | View | Tests (Service+Controller) |
|--------|---------|------------|------|---------------------------|
| Productos | 8 métodos | 8 métodos | 17 públicos | 9 + 7 = 16 |
| Clientes | 6 métodos | 8 métodos | 17 públicos | 7 + 7 = 14 |
| Inventario | 6 métodos (+ `_executeAdjustment`) | 6 métodos | 15 públicos | 8 + 6 = 14 |
| Ventas | 6 métodos | 6 métodos | 15 + 4 (FormView) | 9 + 7 = 16 |
| Proveedores | 6 métodos | 8 métodos | 17 públicos | 7 + 7 = 14 |
| Compras | 5 métodos | 6 métodos | 15 + 4 (FormView) | 8 + 7 = 15 |
| Reportes | 5 métodos | 5 métodos | 17 públicos | 6 + 6 = 12 |
| Dashboard | 4 métodos (incl. gráficos) | 4 métodos | 8 públicos | 10 + 7 = 17 |
| Configuración | 4 métodos | 3 métodos | 10 públicos | 11 + 9 = 20 |
| Exportación | 5 métodos | 2 métodos | 16 públicos | 7 + 5 = 12 |
| Importación | 13 métodos | 2 métodos | 10 públicos | 23 + 7 = 30 |
| Autenticación | 5 servicios | 2 controllers | 2 views | 5 + 2 = 7 |
| Usuarios (RBAC) | — | UserController (7) | UserView | 7 controller |
| Contabilidad | AccountingService (17) | AccountingController (7) | AccountingView + 4 sub | 12 + 12 = 24 |
| **Integración** | — | — | — | **9 suites · 54+ tests** |

---

## 3. HECHOS OBSERVADOS (Datos Medibles y Verificables)

> **Fuentes primarias:** `README.md`, `ARCHITECTURE.md`, `QA_RESULTS.md`, `QA_TESTING_PLAN.md`, `PROJECT_STATE.md`, `package.json`, código fuente en `src/`, `tests/`.

### 3.1 Métricas de Testing (Ejecutables y Verificables)

| Métrica | Valor Observado | Comando de Verificación |
|---------|-----------------|------------------------|
| **Tests Unitarios/Integración** | **410+** (suma de suites en QA_RESULTS.md) | `npm test` → 45 suites, 0 fallos |
| **Suites Unitarias/Integración** | **45** (listadas en QA_RESULTS.md tabla) | `npm test` |
| **Tests E2E (Playwright)** | **38** (QA_RESULTS.md, PROJECT_STATE.md) | `npm run test:e2e` |
| **Tests QA Adversariales (Fases 2-5)** | **76** (44+10+10+12) | Parte de `npm run test:e2e` |
| **Total Tests Reportados (README)** | **524+** | README.md badges |
| **Total Tests QA_RESULTS.md** | **159** (45 unit + 38 e2e + 44+10+10+12 adversarial) | QA_RESULTS.md resumen |
| **Tasa de Aprobación** | **100%** (0 fallos en todas las fases) | `npm test && npm run test:e2e` |
| **Cobertura Módulos Funcionales** | **14/14 completos** | PROJECT_STATE.md |

**Nota sobre discrepancia 524+ vs 159:** El README suma tests unitarios individuales (410+) + E2E (38) + adversariales (76) ≈ 524. QA_RESULTS.md cuenta "suites" o "escenarios" de forma diferente. Ambos son hechos observados en sus respectivas fuentes.

### 3.2 Métricas de Defectos (Registrados en QA_RESULTS.md y README)

| Métrica | Valor Observado | Detalle |
|---------|-----------------|---------|
| **Defectos Críticos (P0) Encontrados y Corregidos** | **4** | Race condition inventory, innerHTML XSS, Prototype pollution, CSV formula injection |
| **Defectos Altos (P1) Encontrados y Corregidos** | **8** | Dark mode, ConfirmDialog recursion, JSON import, CSV IDs, Date range, Auto-detect, Supplier/Customer confusion, Setup redirect |
| **Defectos Medios (P2) Encontrados y Corregidos** | **3** | Dark mode badges, Import tiebreaker, Test cleanup |
| **Total Defectos Documentados y Corregidos** | **15+** | README tabla + QA_RESULTS.md hallazgos |
| **Regresiones Detectadas en Fixes** | **0** (reportado) | Suite completa pasa tras cada fix |

### 3.3 Métricas de Código y Arquitectura

| Métrica | Valor Observado |
|---------|-----------------|
| **Archivos JS/ES Modules (src/)** | ~100+ (models: 16, repos: 12, services: 15, controllers: 14, views: 19, components: 10, utils: 6) |
| **Líneas de Código Aprox. (src/)** | ~15,000-20,000 (estimado por conteo archivos × ~200 líneas promedio) |
| **Límite Arquitectónico** | **Máx 300 líneas/archivo, 40 líneas/función** (Context.md, PROJECT_STATE.md) |
| **Cumplimiento Límite** | **Sí** — Vistas grandes divididas (AccountingView 624→4 sub-vistas, ReportView 329→3 sub-vistas) |
| **Índices DB Compuestos (v8)** | 4: `[customerId+date]`, `[supplierId+date]`, `[referenceType+referenceId]`, `stock` |
| **Reglas OWASP Mitigadas** | 7 documentadas (XSS stored/reflected, Prototype Pollution, CSV Injection, SQLi n/a, RBAC, Session) |

### 3.4 Convenciones de Proyecto ("Skills" Internas del Proyecto)

El archivo `docs/Context.md` define una **taxonomía de convenciones obligatorias** (llamadas "Skills" en el proyecto, **no son Skills de OpenCode**):

| Nivel | Convención | Descripción |
|-------|------------|-------------|
| **A (Obligatorio siempre)** | A-coding-standards | SOLID, DRY, KISS, máx 300 líneas/archivo, 40 líneas/función |
| | A-project-architecture | MVC estricto, capas separadas, DI manual |
| | A-secure-coding | Sin innerHTML dinámico, sin eval, sin estilos inline, sanitizar entradas |
| | A-testing | Código testeable, AAA, FIRST, casos borde, mocks |
| **B (Obligatorio cuando aplica)** | B-authentication-security | PBKDF2, RBAC, mínimo privilegio |
| | B-html-css | HTML semántico, CSS con clases, sin estilos inline |
| | B-javascript-clean | ES Modules, const/let, async/await, sin var |
| | B-ui-components | Componentes reutilizables, separación concerns |
| **C (Debugging - obligatorio al finalizar)** | C-debugging | Flujo: 1. Encontrar causa → 2. Explicar → 3. Proponer → 4. Implementar → 5. Verificar (tests 0 fallos) |
| **D (Documentación - al final)** | C-documentation | Documentar solo cuando código verificado y funcionando. Sin comentarios en código. |

**Diferencia clave:** Estas son **convenciones de proyecto** documentadas en `Context.md`, no Skills de OpenCode reutilizables entre proyectos.

---

## 4. INTERPRETACIONES (Análisis Causal Plausible)

> **Basadas en hechos observados. No probadas causalmente. Requieren validación experimental.**

| ID | Interpretación | Evidencia de Soporte (Hechos) | Fortaleza |
|----|----------------|-------------------------------|-----------|
| **INT-01** | El **QA Adversarial integrado en E2E (Fases 2-5)** detectó defectos críticos que tests unitarios/integración no cubrían. | 4 P0 + 8 P1 encontrados en Fases 2-5; 0 P0/P1 en tests unit/integración (QA_RESULTS.md). Race condition, prototype pollution, CSV injection solo aparecen en adversarial. | **Fuerte** — Diferencia observable directa |
| **INT-02** | La **arquitectura MVC + Repository + Service con DI manual** facilitó testing unitario aislado (mocks de Dexie) y testing de integración con BD real en memoria. | 410+ tests unit/integración pasan; repositorios mockeables; servicios testeables sin DOM (PROJECT_STATE.md, tests/services/). | **Media-Fuerte** — Diseño deliberado verificado |
| **INT-03** | Las **convenciones obligatorias (Nivel A en Context.md)** actuaron como "Skills" de facto, reduciendo variabilidad y previniendo clases de defectos (XSS via innerHTML, eval, estilos inline). | 0 instancias de `innerHTML` con datos dinámicos en código final (verificado); 0 `eval`; 0 estilos inline en JS; sanitizer.js usado en todas las capas. | **Media-Fuerte** — Cumplimiento verificable |
| **INT-04** | El **flujo de debugging obligatorio (C-debugging, Nivel C)** forzó análisis de causa raíz antes de fix, reduciendo fixes superficiales. | Cada fix documentado en PROJECT_STATE.md incluye causa raíz y solución estructurada (ej. race condition → transacción atómica + helper). | **Media** — Correlación temporal |
| **INT-05** | **Service Worker + Dexie local + assets locales** lograron offline-first real sin backend, simplificando arquitectura y testing. | 100% funcional offline; SW cachea 118 assets; Dexie servido desde `assets/lib/`; setup.sh automatiza todo. | **Fuerte** — Verificación funcional directa |
| **INT-06** | **PBKDF2-SHA512 (Web Crypto API) + RBAC en Service Layer** proporcionó seguridad adecuada para ERP offline client-side. | PasswordService usa 100k iteraciones, sal 32 bytes; PermissionService 30 permisos; checks en Service no View. | **Media** — Implementación verificada, no auditada externamente |
| **INT-07** | La **auto-detección de importación v2 (weighted scoring + 30 field names intl + tiebreaker)** resolvió ambigüedades CSV multi-entidad. | Fix documentado en PROJECT_STATE.md: antes fallaba con headers inglés; ahora detecta customers/suppliers/products/sales/purchases/movements. | **Fuerte** — Antes/después verificable |

---

## 5. HIPÓTESIS (Predicciones Comprobables)

> **Formuladas a partir de interpretaciones. Diseñadas para validación experimental controlada (ver SAQI-008).**

| ID | Hipótesis | Variables | Diseño Experimental Sugerido |
|----|-----------|-----------|------------------------------|
| **H1** | La **integración de QA Adversarial como fase obligatoria en pipeline** detecta ≥ 30% más defectos de seguridad y casos límite que testing convencional solo (unit/integración/E2E happy path). | DV: Defectos seguridad/edge detectados; IV: QA Type (Adversarial vs Convencional) | Within-subject: mismo código, 2 fases QA separadas, medir defectos únicos por fase |
| **H2** | **Convenciones obligatorias estilo "Skill" (Nivel A: arquitectura, seguridad, testing, coding standards)** reducen **variabilidad en calidad de código** entre desarrolladores de distinta experiencia. | DV: Métricas calidad (complejidad, cobertura, violaciones seguridad); IV: Convenciones (Sí/No) | RCT: 2 grupos, mismo task, mismo modelo LLM, uno con convenciones obligatorias, otro ad-hoc |
| **H3** | **Arquitectura MVC + Repository + Service con DI manual** permite **cobertura de testing unitario ≥ 80%** con mocks ligeros, sin framework DI ni contenedores. | DV: Coverage unit tests; IV: Arquitectura | Comparativo: proyectos MVC+Repo vs arquitectura emergente, medir coverage y effort testing |
| **H4** | **Offline-first con Service Worker + IndexedDB local** reduce **complejidad de sincronización y conflictos** vs arquitecturas online-first con sync posterior. | DV: Defectos sync, complejidad código sync; IV: Arquitectura offline-first vs online-first | Estudio comparativo casos reales: métricas defectos sync, LOC sync logic |
| **H5** | **Flujo de debugging estructurado obligatorio (C-debugging: causa→explicación→propuesta→implementación→verificación)** reduce **tasa de re-apertura de defectos (re-open rate)**. | DV: Re-open rate; IV: Debugging process (Estructurado vs Ad-hoc) | Longitudinal: tracking defectos por iteración con/sin proceso estructurado |
| **H6** | **Documentación viva (ARCHITECTURE.md, Context.md, PROJECT_STATE.md, QA_RESULTS.md) mantenida sincrónicamente con código** reduce **pérdida de conocimiento contextual entre sesiones/desarrolladores**. | DV: Context Staleness Index (decisiones contradichas/sesión); IV: Doc viva (Sí/No) | Cuasi-experimental: medir staleness con/sin docs vivas actualizadas por iteración |

---

## 6. Aplicación de SAQI al Proyecto (Análisis Retrospectivo)

### 6.1 Mapeo de Fases SAQI a Actividades Reales del Proyecto

| Fase SAQI | Actividad Real en Open-RootERP | Evidencia |
|-----------|--------------------------------|-----------|
| **1. Plan** | `ROADMAP.md` (fases 1-5), `TODO.md` (371 líneas checklist), issues implícitos | ROADMAP.md, TODO.md completados ✅ |
| **2. Architect** | `ARCHITECTURE.md` documentado *a priori* y actualizado; decisiones: MVC, Repository, Service, DI manual, Dexie schema | ARCHITECTURE.md v1.0, decisiones registradas |
| **3. Skill Selection** | **Convenciones de Context.md (Niveles A-D)** aplicadas como "Skills" obligatorias del proyecto | Context.md líneas 84-106: prioridad estricta A→B→C→D |
| **4. Build** | Desarrollo iterativo por módulo (14 módulos), código + tests simultáneos, DI en `app.js` | PROJECT_STATE.md módulos terminados, commits históricos |
| **5. Test (Unit/Int)** | `npm test` — 45 suites, 410+ tests, runner custom con DOM shim | QA_RESULTS.md Fase 1, tests/services/, tests/controllers/ |
| **6. Test (Func/E2E)** | `npm run test:e2e` — Playwright 38 tests, cubre todos 14 módulos | QA_RESULTS.md Fase 1 E2E, PROJECT_STATE.md cobertura |
| **7. Break (QA Adversarial)** | **Fases 2-5 integradas en E2E**: phase2-adversarial (44), phase3-stress (10), phase4-security (10), phase5-a11y (12) = 76 tests | QA_RESULTS.md Fases 2-5, tests/e2e/phase*.js |
| **8. Diagnose** | Análisis causa raíz documentado en PROJECT_STATE.md para cada fix (ej. race condition → transacción atómica) | PROJECT_STATE.md "Mejoras recientes", fixes detallados |
| **9. Fix** | Fixes atómicos con tests de regresión (ej. `resetDOM()` en runner, transacción inventory) | Commits fixes, QA_RESULTS.md "Hallazgos corregidos" |
| **10. Regression** | Suite completa `npm test && npm run test:e2e` tras cada fix — 100% pass | QA_RESULTS.md: 0 fallos tras fixes |
| **11. Verify** | Release v1.0.0 tras pasar todas las fases; badges 100% pass en README | README.md badges, ROADMAP.md v1.0.0 |
| **12. Document** | `ARCHITECTURE.md`, `QA_TESTING_PLAN.md`, `QA_RESULTS.md`, `PROJECT_STATE.md`, `Context.md`, `SESSION.md` actualizados | Todos los .md en raíz y docs/ |
| **13. Learn (Context Update)** | `Context.md` actualizado con fixes recientes, convenciones, patrones, stack | Context.md líneas 52-76 "Fixes recientes" |
| **14. Improve Skills** | **Convenciones (Context.md) refinadas**: Nivel A-D, límites 300/40 líneas, patrones transacción, sanitización | Context.md v actual — evolución desde versiones previas |

### 6.2 Diferencias: SAQI Teórico vs Práctica Real en Open-RootERP

| Aspecto SAQI | Realidad Open-RootERP | Brecha / Nota |
|--------------|----------------------|---------------|
| **Skills** | Convenciones de proyecto en `Context.md` (Niveles A-D), **no** Skills de OpenCode reutilizables | SAQI propone Skills versionadas, gobernadas, reutilizables entre proyectos. Aquí son convenciones *ad-hoc* del proyecto. |
| **Agente IA** | Desarrollo **asistido por IA manual** (chat), **no** agente autónomo (OpenCode) | SAQI asume agente con tool use, loops, contexto persistente. Aquí: humano orquesta, IA genera snippets. |
| **Contexto Persistente** | 4 documentos vivos (`ARCHITECTURE.md`, `Context.md`, `PROJECT_STATE.md`, `QA_RESULTS.md`) + `SESSION.md` | **Coincide** con SAQI-013. Funcionó como memoria externa. |
| **QA Adversarial** | **Integrado en E2E** como 4 archivos de fase (phase2-5), **no fase separada 7** | SAQI define Fase 7 distinta. Aquí: parte de `npm run test:e2e`. |
| **Ciclo Learn→Skills** | Convenciones refinadas en `Context.md` tras fixes (ej. límite 300 líneas, transacciones atómicas) | **Coincide conceptualmente**, pero artefacto es `Context.md` no Skill versionada. |
| **Métricas** | 524+ tests, 15+ defectos, 14 módulos, 100% pass | **Coincide** con SAQI-007, pero recolección manual, no automatizada. |
| **TypeScript/React** | **NO** — Vanilla JS ES Modules | SAQI-003 v1.0 asumía TS/React incorrectamente. |

---

## 7. Lecciones Aprendidas Reales (Desde el Código)

### 7.1 Defectos Críticos → Mejoras de Convenciones (Simulando Learn→Skills)

| Defecto (P0/P1) | Causa Raíz | Mejora en Convenciones (Context.md) |
|-----------------|------------|-------------------------------------|
| Race condition inventory (P0) | Lectura/escritura stock sin transacción atómica | **Patrón obligatorio**: `db.transaction('rw', [tables], async () => {...})` en Services que modifican stock |
| innerHTML con datos dinámicos (P1) | `app.js` usaba `innerHTML` para render | **Regla A-secure-coding**: `clearElement()` + `createElement`/`textContent` obligatorio |
| Prototype pollution JSON import (P0) | Keys `__proto__`, `constructor.prototype` no sanitizadas | **Regla A-secure-coding**: `ImportService._sanitizeRecord` elimina claves peligrosas |
| CSV Formula Injection (P1) | Exportación no prefijaba fórmulas | **Regla A-secure-coding**: Prefijo `'` en celdas que empiezan con `=`, `+`, `-`, `@` |
| ConfirmDialog recursión infinita (P1) | `_resolve()` → `modal.close()` → `onClose()` → `_resolve()` | **Patrón**: Limpiar `onClose` antes de `close()` en componentes modales |
| findByDateRange excluye fin de día (P2) | Fechas ISO vs filtro solo fecha | **Helper**: `endDate + 'T23:59:59.999Z'` en queries de rango |
| Auto-detección import solo español (P2) | Patrones hardcoded español | **Algoritmo v2**: Weighted scoring + 30 field names intl + tiebreaker documentId |

### 7.2 Patrones de Éxito Codificados (Implícitos en Convenciones)

| Patrón | Dónde se Aplica | Descripción |
|--------|-----------------|-------------|
| **Repository Pattern + Dexie** | 12 repos | Encapsulan Dexie; Services no importan Dexie; testeables con mock |
| **Service Layer = Business Logic** | 15 services | Validaciones, cálculos, transacciones, RBAC checks — sin DOM, sin Dexie directo |
| **Controller = Orquestador** | 14 controllers | Recibe eventos View, llama Service, actualiza View — sin lógica negocio |
| **View = Render + Events** | 19 views | Crea DOM, `addEventListener`, delega a Controller via callbacks (`onSave`, `onSearch`) |
| **DI Manual en app.js** | `app.js` | `new Repo(db)`, `new Service(repo)`, `new Controller(service, view)` — wiring explícito |
| **Transacciones Atómicas Dexie** | SaleService, PurchaseService, InventoryService | `db.transaction('rw', [tables], async () => {...})` para consistencia stock/contabilidad |
| **Sanitización en Capas** | sanitizer.js + validators.js | View: escapeHtml; Service: validate; Import: sanitizeRecord + prototype guard |
| **DocumentFragment para Batch DOM** | SaleFormView, PurchaseFormView, Table | 3x faster render — `document.createDocumentFragment()` |

---

## 8. Limitaciones del Caso de Estudio (Críticas para Validez)

| Limitación | Impacto en Generalización | Mitigación Futura |
|------------|---------------------------|-------------------|
| **N=1 (un solo proyecto)** | Validez externa **nula** — no generalizable | Replicar en ≥3 proyectos distintos (SAQI-021) |
| **Stack específico** (Vanilla JS, Dexie, IndexedDB, SW, Chart.js) | Sesgo tecnológico — no valida TS, React, SQL, backend, mobile nativo | Proyectos con stacks diversos |
| **Un solo desarrollador/investigador** | Sesgo de investigador (Hawthorne, confirmación) | Replicación independiente; ciego parcial |
| **Sin grupo control formal** | No comparación causal SAQI vs ad-hoc | Ejecutar protocolo SAQI-008 (RCT o cuasi-experimental) |
| **Métricas de costo/tokens aproximadas** | No hay medición precisa de tokens LLM, tiempo real, costo $ | Instrumentar tooling para telemetría exacta |
| **Proyecto "greenfield"** | No valida mantenimiento, legacy, migración, brownfield | Caso estudio brownfield futuro |
| **Convenciones ≠ Skills SAQI** | Las "Skills" del proyecto son convenciones locales, no artefactos versionados/gobernados SAQI | Desarrollar Skills SAQI reales y aplicarlas en réplica |
| **QA Adversarial integrado en E2E, no fase 7 separada** | Difiere de definición SAQI canónica | Alinear en réplica o ajustar definición SAQI |

---

## 9. Trazabilidad: Defectos → Convenciones → Mejoras

| Defecto ID | Tipo | Fase Detección | Convención Afectada | Mejora en Context.md |
|------------|------|----------------|---------------------|----------------------|
| Race condition inventory | DATA_INTEGRITY | Adversarial (Stress) | A-project-architecture (transacciones) | Patrón transacción atómica obligatorio en Services stock |
| innerHTML dinámico | SECURITY (XSS) | Adversarial (Security) | A-secure-coding | `clearElement()` + DOM API obligatorio; 0 innerHTML |
| Prototype pollution | SECURITY | Adversarial (Security) | A-secure-coding | `_sanitizeRecord` elimina `__proto__`, `constructor.prototype` |
| CSV Formula Injection | SECURITY | Adversarial (Security) | A-secure-coding | Prefijo `'` en exportación para `=`, `+`, `-`, `@` |
| ConfirmDialog recursión | LOGIC | Adversarial (General) | B-ui-components | Limpiar `onClose` antes de `close()` |
| findByDateRange fin día | LOGIC | Adversarial (General) | B-javascript-clean | Helper `endOfDay(date)` en queries rango |
| Auto-detect import v1 | DATA_INTEGRITY | Adversarial (General) | B-javascript-clean | Weighted scoring + 30 intl fields + tiebreaker |

---

## 10. Conclusiones del Caso de Estudio (Corregidas)

1. **Open-RootERP es un caso de existencia válido** de desarrollo asistido por IA que produjo un ERP offline funcional, 14 módulos, 524+ tests, 100% pass, 15+ defectos críticos/altos corregidos.

2. **La arquitectura MVC + Repository + Service + DI manual** con convenciones obligatorias (Nivel A en Context.md) facilitó testing exhaustivo y prevención de clases de defectos (XSS, prototype pollution, race conditions).

3. **El QA Adversarial integrado como fases de E2E (Fases 2-5)** fue **instrumental** para detectar defectos que testing convencional no cubría (4 P0, 8 P1 solo en adversarial).

4. **La documentación viva (4-5 documentos)** funcionó como **memoria externa persistente** efectiva, permitiendo continuidad entre sesiones y trazabilidad de decisiones.

5. **El flujo de debugging estructurado (C-debugging)** forzó análisis de causa raíz documentado para cada fix, evitando parches superficiales.

6. **NO hay evidencia de que "SAQI" como metodología formal se aplicara**. El proyecto usó **prácticas alineadas con principios SAQI** (convenciones obligatorias, QA adversarial, docs vivas, debugging estructurado, retroalimentación a convenciones) pero **sin**:
   - Skills versionadas y gobernadas (SAQI-003)
   - Agente IA autónomo con tool use (SAQI-006)
   - Fase 7 separada y gate de release (SAQI-005)
   - Métricas automatizadas y protocolo experimental (SAQI-007, SAQI-008)

7. **Las métricas observadas son base para hipótesis (H1-H6)**, no evidencia de efectividad de SAQI. Requieren validación experimental controlada.

---

## 11. Referencias Internas (Documentos Fuente Verificados)

| Documento | Ubicación | Uso en Este Análisis |
|-----------|-----------|---------------------|
| `README.md` | Raíz | Métricas badges, módulos, setup, defectos tabla |
| `ARCHITECTURE.md` | Raíz | Stack, arquitectura, patrones, DB schema, testing strategy |
| `QA_TESTING_PLAN.md` | Raíz | 22 áreas, 570+ escenarios adversariales, 6 personas, estrategia ejecución |
| `QA_RESULTS.md` | `docs/` | Resultados fase 1-5, 159 tests, hallazgos corregidos, métricas |
| `PROJECT_STATE.md` | `docs/` | Estado módulos, fixes detallados, decisiones, deuda técnica, roles |
| `Context.md` | `docs/` | Stack, 14 módulos, utils, RBAC, fixes recientes, **convenciones Nivel A-D** |
| `package.json` | Raíz | Dependencias (Dexie, Playwright), scripts test, tipo modulo |
| Código fuente `src/` | `src/` | Verificación arquitectura, patrones, convenciones, fixes |
| Tests `tests/` | `tests/` | Verificación runners, suites, adversarial phases, helpers |

---

## 12. Referencias Internas SAQI

- SAQI-001: Resumen Ejecutivo y Definición
- SAQI-002: Fundamentos Teóricos
- SAQI-004: Marco de QA Funcional y Adversarial
- SAQI-005: Definición del Proceso SAQI (14 Fases)
- SAQI-007: Marco de Métricas
- SAQI-008: Protocolo Experimental
- SAQI-009: Amenazas a Validez
- SAQI-011: Guía de Aplicación Open-RootERP
- SAQI-012: Paquete de Replicación
- SAQI-017: Apéndice A - Arquitectura Open-RootERP Detallada
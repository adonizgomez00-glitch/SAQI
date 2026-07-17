# SAQI-011: Guía de Aplicación — Caso Open-RootERP

**Versión:** 1.0
**Fecha:** 2026-07-17
**Estado:** Borrador inicial
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Propósito

Esta guía describe **cómo se aplicó SAQI (principios y prácticas)** en el desarrollo de Open-RootERP, sirviendo como **referencia práctica** para replicar el enfoque en proyectos similares. No es una aplicación canónica de SAQI (que requiere Skills versionadas, agente autónomo, Fase 7 separada), sino una **aplicación adaptada** usando las herramientas disponibles (OpenCode chat, convenciones de proyecto en `Context.md`, QA adversarial integrado en E2E).

---

## 2. Preparación del Entorno (Pre-SAQI)

### 2.1 Repositorio Base
```bash
# Clonar template o crear estructura inicial
mkdir open-rooterp && cd open-rooterp
git init

# Estructura mínima SAQI
mkdir -p src/{config,database,models,repositories,services,controllers,views,components,store,utils}
mkdir -p tests/{services,controllers,integration,utils,e2e}
mkdir -p assets/{css,lib,icons}
mkdir -p docs

# Documentos vivos SAQI (crear vacíos iniciales)
touch ARCHITECTURE.md CONTEXT.md PROJECT_STATE.md QA_RESULTS.md SESSION.md
```

### 2.2 Configuración OpenCode
```bash
# Verificar skills disponibles
opencode skill list

# Skills relevantes para ERP offline vanilla JS
# Nivel A (obligatorias): A-coding-standards, A-project-architecture, A-secure-coding, A-context-manager, A-testing, A-qa-breaker
# Nivel B (dominio): B-html-css, B-javascript-clean, B-ui-components, B-authentication-security, C-database-design-offline, C-dexie-patterns, D-erp-offline
# Nivel C: C-debugging, C-documentation
# Nivel D: D-prompt-engineering
```

### 2.3 Inicialización Documentos Vivos

**ARCHITECTURE.md** — Crear con stack, arquitectura objetivo, ADR-001 (elección MVC + Repository + Service + DI manual)

**CONTEXT.md** — Definir: stack (Vanilla JS ES Modules, Dexie, IndexedDB, SW), convenciones obligatorias (Niveles A-D), módulos objetivo (14)

**PROJECT_STATE.md** — Estado inicial: iteración 0, backlog (ROADMAP.md), 0 tareas completadas

**QA_RESULTS.md** — Header vacío, estructura de tablas por fase

---

## 3. Ejecución por Iteraciones (6 Iteraciones en Open-RootERP)

### Iteración 1: Fundación + Autenticación (Semanas 1-2)

| Fase SAQI | Actividad Real | Artefactos |
|-----------|----------------|------------|
| **1. Plan** | `ROADMAP.md` Fases 1-2, `TODO.md` checklist | Backlog priorizado, ACs definidos |
| **2. Architect** | `ARCHITECTURE.md` v1.0: MVC, Repository, Service, DI, Dexie schema v1, ADR-001 | ADRs, diagramas, contratos puertos |
| **3. Skill Selection** | `Context.md` Niveles A-D definidos (líneas 84-106) | Convenciones obligatorias cargadas |
| **4. Build** | Auth module: PasswordService (PBKDF2), SessionService, PermissionService, AuthenticationService, SystemService, Setup/Login views | Código + tests unitarios |
| **5. Test L1-L2** | `npm test` — 7 suites auth, 100% pass | Coverage, mutation |
| **6. Test L3-L4** | `npm run test:e2e` — Setup + Login flows (2 tests) | Playwright reports |
| **7. Break** | Integrado en E2E phase2-adversarial (setup/login attacks) | 44 tests adversariales |
| **8-10. Diagnose/Fix/Regression** | Fixes: setup redirect, findByKey→get, rol Vendedor auto-create | Commits, tests prevención |
| **11. Verify** | Gates: tests pass, 0 P0, security basics | Release candidate auth |
| **12. Document** | `ARCHITECTURE.md` actualizado, `Context.md` fixes, `PROJECT_STATE.md` | Docs sincronizados |
| **13. Learn** | `Context.md` líneas 52-60: fixes auth documentados | Contexto actualizado |
| **14. Improve Skills** | Convenciones refinadas: "Setup detecta primer inicio por conteo usuarios, no flags" | Context.md actualizado |

**Entregable**: Módulo Auth funcional, 100% tests pass, convenciones base establecidas.

---

### Iteración 2: Catálogo + Inventario (Semanas 3-4)

| Fase | Actividad | Detalles Clave |
|------|-----------|----------------|
| **1-3** | Plan: Products, Customers, Inventory, Suppliers. Architect: ADRs para auto-ID, stock basado en movimientos. Skills: +B-javascript-clean, B-ui-components | ADR-002: Stock calculado desde movements |
| **4** | Build: 4 módulos CRUD completos. Components: Table, Form, Modal, Toast, Pagination, SearchBar | 10 componentes reutilizables |
| **5** | 410+ tests unit/integración. Mocks Dexie en memoria. Contract tests repositorios | Mutation score 72% |
| **6** | E2E: CRUD products, customers, suppliers, inventory | 38 tests totales |
| **7** | Phase2-adversarial: 44 tests (empty fields, XSS, duplicate codes, negative prices, rapid clicks) | Detectó: innerHTML en app.js (P1) |
| **8-10** | Fix: `clearElement()` helper, reemplazar innerHTML. Race condition inventory → transacción atómica | 2 P0 corregidos |
| **11-14** | Verify, Document, Learn: Convenciones "Sin innerHTML dinámico" reforzada | Context.md actualizado |

**Entregable**: 4 módulos core, componentes UI, race condition resuelto, convención anti-innerHTML.

---

### Iteración 3: Ventas + Compras + Transacciones (Semanas 5-6)

| Fase | Actividad | Detalles Clave |
|------|-----------|----------------|
| **1-3** | Plan: Sales, Purchases (money flow). Architect: Transacciones atómicas Dexie, asientos contables automáticos. ADR-003: Atomic stock + accounting | ADR-003: Transacción multi-tabla |
| **4** | Build: SaleService, PurchaseService con `db.transaction('rw', [...])`. SaleFormView, PurchaseFormView separados (límite 300 líneas) | Patrones: Atomic transaction, DocumentFragment |
| **5-6** | Tests: Stock validation, cancelación con reversión, contabilidad auto | Integration tests flujo completo |
| **7** | Phase2-adversarial: Double-click guardar, stock negativo, cantidad decimal, XSS en notas | Detectó: Double submit (P1), XSS notes (P1) |
| **8-10** | Fix: Disable button on submit, Zod validation en puertos, sanitización notas | Regla: Zod en TODOS los puertos entrada |
| **11-14** | Verify, Document: `A-secure-coding` convención añadida | Context.md + A-secure-coding skill |

**Entregable**: Transacciones seguras, validación Zod obligatoria, botones anti-double-click.

---

### Iteración 4: Reportes + Dashboard + Configuración (Semanas 7-8)

| Fase | Actividad | Detalles Clave |
|------|-----------|----------------|
| **1-3** | Plan: Reports, Dashboard, Settings. Architect: Auto-generación reports, Chart.js integration | ADR-004: Chart.js local UMD |
| **4** | Build: ReportService auto-gen, DashboardService gráficos, Settings grid responsive | View splitting: ReportView→3 sub-vistas |
| **5-6** | Tests: findByDateRange fix (T23:59:59.999Z), date ranges, charts data | Fix crítico date range |
| **7** | Phase3-stress: 10 tests (rapid module switching, modal spam, 50 products create, sort stress) | Performance validada |
| **8-10** | Fix: Dark mode CSS variables (--color-muted, --color-bg-secondary), badges sólidos dark mode | 2 P2 visuales corregidos |
| **11-14** | Verify: Lighthouse budgets, A11y axe-core | Convenciones CSS actualizadas |

**Entregable**: Reports automáticos, Dashboard con gráficos, Dark mode accesible.

---

### Iteración 5: Export/Import + Contabilidad + Usuarios (Semanas 9-10)

| Fase | Actividad | Detalles Clave |
|------|-----------|----------------|
| **1-3** | Plan: Export, Import, Accounting, Users. Architect: Multi-entity JSON, CSV formula injection guard, prototype pollution guard | ADR-005: Import sanitization pipeline |
| **4** | Build: ImportService 13 métodos (auto-detect v2 weighted scoring, 30 intl fields, tiebreaker documentId). AccountingService 17 métodos (plan cuentas, libro diario, balance, PyL, asientos auto). Users CRUD + RBAC UI | Complejo: Import full export order |
| **5-6** | Tests: 55 tests import, 24 accounting, 7 users. Integration: ImportExportFlow 10 tests | Cobertura alta módulos críticos |
| **7** | Phase4-security: 10 tests (XSS, prototype pollution, CSV injection, RBAC bypass). Phase5-a11y: 12 tests (keyboard, ARIA, mobile, zoom) | 4 P0 security detectados y fixados en iteraciones previas |
| **8-10** | Fixes: CSV formula injection prefix `'`, prototype pollution sanitize keys, import tiebreaker error explícito, JSON multi-entity parse | Patrones: Sanitization pipeline, Weighted scoring |
| **11-14** | Verify: 100% all phases. Release v1.0.0 | **Release completo** |

**Entregable**: 14/14 módulos, 524+ tests, 100% pass, v1.0.0.

---

## 4. Patrones Clave Aplicados (Extraíbles como Skills)

### 4.1 Patrón: Repository + Dexie (Skill `C-dexie-patterns`)
```javascript
// src/repositories/ProductRepository.js
export class ProductRepository {
  constructor(db) { this.db = db; }
  async findAll() { return this.db.products.toArray(); }
  async findById(id) { return this.db.products.get(id); }
  async create(data) { return this.db.products.add(data); }
  async update(id, data) { return this.db.products.update(id, data); }
  async delete(id) { return this.db.products.delete(id); }
  // Queries optimizadas con índices compuestos
  async findByCategory(category) { 
    return this.db.products.where('category').equals(category).toArray(); 
  }
}
```

### 4.2 Patrón: Service Layer = Business Logic
```javascript
// src/services/SaleService.js
export class SaleService {
  constructor(productRepo, saleRepo, inventoryRepo, db) { ... }
  async createSale(saleData) {
    // 1. Validar negocio (Zod schema en puerto)
    // 2. Verificar stock atómico (transacción)
    // 3. Calcular totales
    // 4. Persistir venta + items + movement + stock update
    return this.db.transaction('rw', [this.saleRepo, this.saleItems, this.inventoryMovements, this.products], async () => {
      // ... lógica completa atómica
    });
  }
}
```

### 4.3 Patrón: Controller = Orquestador
```javascript
// src/controllers/SaleController.js
export class SaleController {
  constructor(saleService, saleView) {
    this.service = saleService;
    this.view = saleView;
    this.view.onCreate(this.handleCreate.bind(this));
    this.view.onSearch(this.handleSearch.bind(this));
  }
  async handleCreate(saleData) {
    const result = await this.service.createSale(saleData);
    if (result.ok) this.view.showSuccess('Venta creada');
    else this.view.showError(result.error);
  }
}
```

### 4.4 Patrón: Zod Validation en Puertos (Convención `A-secure-coding`)
```javascript
// En cada Controller.handleXxx
import { z } from 'zod'; // o schema local
const createSaleSchema = z.object({
  customerId: z.string().optional(),
  items: z.array(z.object({ productId: z.string(), qty: z.number().int().positive(), unitPrice: z.number().nonnegative() })).min(1),
  notes: z.string().max(500).optional()
});
// Validar ANTES de llamar Service
const parsed = createSaleSchema.safeParse(input);
if (!parsed.success) return view.showValidationErrors(parsed.error);
```

### 4.5 Patrón: Sanitización Multi-Capa
```javascript
// src/utils/sanitizer.js
export function escapeHtml(str) { return str.replace(/[&<>"']/g, ...); }
export function sanitizeObject(obj) { ... recursivo ... }
export function sanitizeRecord(record) {
  // Prototype pollution guard
  const dangerous = ['__proto__', 'constructor', 'prototype'];
  for (const key of dangerous) delete record[key];
  return sanitizeObject(record);
}
// Uso: View (escapeHtml), Service (sanitizeObject), Import (sanitizeRecord + prototype guard)
```

### 4.6 Patrón: Import Auto-Detect v2 (Weighted Scoring)
```javascript
// src/services/ImportService.js - autoDetectEntity()
const PATTERNS = {
  products: { strong: ['code', 'purchaseprice', 'saleprice'], regular: ['name', 'category', 'stock'] },
  customers: { strong: ['documentid', 'document_id'], regular: ['name', 'email', 'phone', 'address'] },
  suppliers: { strong: ['nit', 'ruc'], regular: ['name', 'email', 'phone', 'address'] },
  // ... sales, purchases, movements
};
function scoreEntity(headers) {
  // Weighted: strong ×3, regular ×1
  // Tiebreaker: first row documentId (PROV-* → suppliers, C* → customers)
}
```

### 4.7 Patrón: Debugging Estructurado (Skill `C-debugging`)
```
1. REPRODUCIR: Test mínimo que falla (commit)
2. AISLAR: git bisect / comentar código / reducir input
3. HIPOTESIS: ≥2 causas con predicciones comprobables
4. EVIDENCIA: Logs, traces, DB state, network
5. VALIDAR: Confirmar/refutar cada hipótesis
6. CAUSA RAÍZ: Documentar en QA_RESULTS.md
7. FIX: Cambio mínimo + test prevención
8. REGRESIÓN: Suite completa
```

---

## 5. Métricas de Seguimiento por Iteración (Template)

```markdown
# ITERATION DASHBOARD — ITER-XXX

## Plan
- Scope: [Módulos/Historias]
- ACs: [Número]
- Riesgos: [Lista]

## Build
- LOC added: [N]
- Commits: [N]
- Code review findings: [N]

## Test L1-L2 (Phase 5)
- Suites: [N] | Tests: [N] | Pass: 100%
- Coverage L/B/F: [XX/XX/XX]%
- Mutation Score: [XX]%

## Test L3-L4 (Phase 6)
- CUJs: [N] | Pass: 100%
- A11y violations: 0 critical, 0 serious
- Performance: LCP [X]s, TBT [X]ms, CLS [X]

## Adversarial (Phase 7)
- ATK categories executed: [8/8]
- Defects found: P0=[N], P1=[N], P2=[N], P3=[N]
- Unique to adversarial: [N] ([X]%)

## Fix & Regression (Phases 8-10)
- Fixes applied: [N]
- MTTF avg: [X]h
- Regression Rate: [X]%

## Verify (Phase 11)
- All gates: ✅
- Sign-offs: PO, Tech Lead, QA Lead

## Learn (Phases 13-14)
- Context docs updated: [4/4]
- Conventions/Skills modified: [Lista]
- New rules added: [N]
```

---

## 6. Checklist de Aplicación SAQI (Para Nuevo Proyecto)

### Pre-Inicio
- [ ] Repositorio base con estructura SAQI
- [ ] 4 documentos vivos creados (vacíos con estructura)
- [ ] Skills OpenCode identificadas y cargadas
- [ ] Stack tecnológico definido en CONTEXT.md
- [ ] Convenciones obligatorias (Niveles A-D) escritas en CONTEXT.md
- [ ] ROADMAP.md con fases y módulos
- [ ] TODO.md checklist detallado

### Por Iteración
- [ ] **Fase 1**: ITERATION_PLAN.md con ACs testables, DoD
- [ ] **Fase 2**: ARCHITECTURE.md actualizado, ADRs nuevos, diagramas
- [ ] **Fase 3**: SKILL_SELECTION.md con versiones exactas
- [ ] **Fase 4**: Código + tests unitarios simultáneos, lint/typecheck pass
- [ ] **Fase 5**: Unit/Int 100% pass, coverage ≥80/75/80, mutation ≥70%
- [ ] **Fase 6**: Func/E2E 100% CUJs, a11y 0 critical/serious, perf budgets
- [ ] **Fase 7**: 8 categorías ATK ejecutadas, 0 P0/P1 abiertos, **Firma QA Lead**
- [ ] **Fase 8**: Causa raíz documentada para cada P0/P1, test reproducción
- [ ] **Fase 9**: Fix mínimo + test prevención, commit convencional
- [ ] **Fase 10**: Suite completa 100% pass, regression rate <2%
- [ ] **Fase 11**: Todas las gates, sign-offs, RELEASE_CANDIDATE_APPROVAL.md
- [ ] **Fase 12**: ITERATION_REPORT.md, docs archivados
- [ ] **Fase 13**: 4 docs vivos sincronizados, checkpoint guardado
- [ ] **Fase 14**: Skills/convenciones actualizadas, versionadas, changelog

### Post-Proyecto
- [ ] Métricas finales consolidadas (SAQI-007 dashboard)
- [ ] Lecciones aprendidas documentadas
- [ ] Skills/convenciones versionadas para reutilización
- [ ] Paquete de replicación preparado (SAQI-012)

---

## 7. Referencias Internas

- SAQI-003: Caso Estudio Open-RootERP (Datos completos)
- SAQI-004: Marco Metodológico (Fases detalle)
- SAQI-005: Proceso SAQI (14 fases)
- SAQI-006: Arquitectura Agente (Modos Builder, Adversarial, etc.)
- SAQI-013: Gestión Contexto (4 docs + checkpointing)
- SAQI-017: Apéndice A - Arquitectura Open-RootERP Detallada
- SAQI-018: Apéndice B - Plantillas Configuración Agente

---

## 8. Referencias del Proyecto Open-RootERP

- `README.md` — Métricas, setup, módulos, defectos
- `ARCHITECTURE.md` — Arquitectura, patrones, DB schema, ADRs
- `QA_TESTING_PLAN.md` — 22 áreas, 570+ escenarios, 6 personas
- `QA_RESULTS.md` — 5 fases, 159 tests, hallazgos corregidos
- `PROJECT_STATE.md` — Estado módulos, fixes, deuda, decisiones
- `docs/Context.md` — Stack, convenciones A-D, fixes recientes, patrones
- `package.json` — Deps, scripts, versión
- `tests/` — Runners, suites, E2E phases
# SAQI-017: Apéndice A — Arquitectura Detallada Open-RootERP

**Versión:** 1.0
**Fecha:** 2026-07-17
**Estado:** Borrador inicial
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Visión General de la Arquitectura

Open-RootERP implementa una **arquitectura MVC estricta** con capas separadas por responsabilidad, inyección de dependencias manual, y persistencia offline-first vía IndexedDB (Dexie.js).

```
┌─────────────────────────────────────────────────────────────┐
│                    index.html (SPA)                          │
├─────────────────────────────────────────────────────────────┤
│                      app.js                                  │
│              Router + Bootstrap + DI Manual                  │
├──────────┬──────────┬──────────┬────────────────────────────┤
│  Views   │Controllers│ Services │      Repositories          │
│ (render) │ (orquest) │(negocio) │         (datos)            │
├──────────┴──────────┴──────────┴────────────────────────────┤
│                    Database (Dexie + IndexedDB)              │
├─────────────────────────────────────────────────────────────┤
│              Service Worker (sw.js)                          │
│               Cache API + Offline Strategy                   │
└─────────────────────────────────────────────────────────────┘
```

**Regla de Oro**: La View **NUNCA** accede directamente al Repository o Database. Flujo obligatorio: View → Controller → Service → Repository → DB.

---

## 2. Stack Tecnológico Completo

| Capa | Tecnología | Versión | Propósito |
|------|------------|---------|-----------|
| **Lenguaje** | JavaScript ES2022 (ES Modules) | Native | Sin build step, sin transpilación |
| **UI** | HTML5 Semántico + CSS3 Custom Properties | Native | Responsive, dark mode, accessibility |
| **Gráficos** | Chart.js UMD | 4.x | Dashboard charts (barras, dona) |
| **Persistencia** | Dexie.js (IndexedDB wrapper) | 4.4.4 | Offline-first, transacciones, índices compuestos |
| **Offline** | Service Worker + Cache API | Native | App Shell pattern, stale-while-revalidate |
| **Testing Unit/Int** | Custom runner + DOM shim | Node.js | 45 suites, 410+ tests, determinístico |
| **Testing E2E** | Playwright | 1.61.1 | Chromium headless, multi-viewport, a11y, network control |
| **Accesibilidad** | axe-core (integrado Playwright) | Latest | WCAG 2.1 AA automated scanning |
| **Seguridad** | Sanitizer custom + Web Crypto API | Native | PBKDF2-SHA512, XSS prevention, prototype pollution guard |
| **Instalación** | setup.sh / setup.bat | Bash/Batch | Auto-install: npm, copy deps, servidor HTTP |

---

## 3. Estructura de Directorios Completa

```
open-rooterp/
├── index.html                 # SPA entry point
├── manifest.json              # PWA manifest (standalone, icons SVG)
├── sw.js                      # Service Worker (118 assets, stale-while-revalidate)
├── setup.sh / setup.bat       # Instalación automática
├── package.json               # Deps: dexie, playwright (dev)
├── ARCHITECTURE.md            # Este documento
├── QA_TESTING_PLAN.md         # 22 áreas, 570+ escenarios, 6 personas
├── ROADMAP.md                 # Fases 1-5 completadas
├── TODO.md                    # Checklist 371 líneas all ✅
├── SESSION.md                 # Log sesión actual
├── README.md                  # Documentación completa (bilingüe)
│
├── assets/
│   ├── css/
│   │   ├── variables.css      # Custom properties + dark mode
│   │   ├── reset.css          # Normalize
│   │   ├── layout.css         # Grid, sidebar, header
│   │   ├── components.css     # ~1160 líneas, todos componentes
│   │   ├── utilities.css      # Helper classes
│   │   └── main.css           # Entry point (imports all)
│   ├── lib/
│   │   ├── dexie.js           # Dexie v4 ESM (copia local para offline)
│   │   └── chart.umd.min.js   # Chart.js 4.x UMD (copia local)
│   └── icons/                 # SVG icons
│
├── src/
│   ├── app.js                 # Bootstrap, router, DI wiring
│   ├── config/app.js          # Constantes globales (APP_CONFIG)
│   │
│   ├── database/
│   │   ├── db.js              # Dexie schema v8 (índices compuestos)
│   │   └── seed.js            # Datos iniciales + transacciones seed
│   │
│   ├── models/ (16 clases)
│   │   ├── Account.js, AccountingEntry.js, Product.js
│   │   ├── Customer.js, Supplier.js, Sale.js, SaleItem.js
│   │   ├── Purchase.js, PurchaseItem.js
│   │   ├── InventoryMovement.js, Setting.js
│   │   ├── User.js, Role.js, Permission.js, RolePermission.js
│   │   └── Session.js
│   │
│   ├── repositories/ (12 clases)
│   │   ├── AccountingRepository.js, ProductRepository.js
│   │   ├── CustomerRepository.js, SupplierRepository.js
│   │   ├── SaleRepository.js, PurchaseRepository.js
│   │   ├── InventoryRepository.js, SettingRepository.js
│   │   ├── UserRepository.js, RoleRepository.js
│   │   ├── PermissionRepository.js, SessionRepository.js
│   │
│   ├── services/ (15 clases)
│   │   ├── AccountingService.js, AuthenticationService.js
│   │   ├── CustomerService.js, DashboardService.js
│   │   ├── ExportService.js, ImportService.js
│   │   ├── InventoryService.js, PasswordService.js
│   │   ├── PermissionService.js, ProductService.js
│   │   ├── PurchaseService.js, ReportService.js
│   │   ├── SaleService.js, SessionService.js
│   │   ├── SettingService.js, SupplierService.js
│   │   └── SystemService.js
│   │
│   ├── controllers/ (14 clases)
│   │   ├── AccountingController.js, CustomerController.js
│   │   ├── DashboardController.js, ExportController.js
│   │   ├── ImportController.js, InventoryController.js
│   │   ├── LoginController.js, ProductController.js
│   │   ├── PurchaseController.js, ReportController.js
│   │   ├── SaleController.js, SettingsController.js
│   │   ├── SetupController.js, SupplierController.js
│   │   └── UserController.js
│   │
│   ├── views/ (19 clases)
│   │   ├── AccountingView.js (contenedor + 4 sub-vistas)
│   │   ├── ReportView.js (contenedor + 3 sub-vistas)
│   │   ├── SaleView.js, SaleFormView.js
│   │   ├── PurchaseView.js, PurchaseFormView.js
│   │   └── ... (resto módulos)
│   │
│   ├── components/ (10 componentes reutilizables)
│   │   ├── ConfirmDialog.js, Form.js, Header.js, Loader.js
│   │   ├── Modal.js, Pagination.js, SearchBar.js
│   │   ├── Sidebar.js, Table.js, Toast.js
│   │
│   ├── store/AppState.js      # Estado global (reservado, clase vacía)
│   │
│   └── utils/ (6 utilidades)
│       ├── errors.js, formatters.js, helpers.js
│       ├── sanitizer.js, ThemeManager.js, validators.js
│
├── tests/
│   ├── run-all.js             # Runner custom con DOM shim (Node.js)
│   ├── runner.html            # Runner para navegador
│   ├── services/ (13 suites)
│   ├── controllers/ (10 suites)
│   ├── integration/ (9 suites, 54+ tests)
│   ├── utils/ (5 suites)
│   ├── components/ (1 suite)
│   └── e2e/ (Playwright)
│       ├── helpers.js
│       ├── phase2-adversarial.js (44 tests)
│       ├── phase3-stress.js (10 tests)
│       ├── phase4-security.js (10 tests)
│       ├── phase5-a11y.js (12 tests)
│       └── run-e2e.js
│
└── docs/
    ├── CONTEXT.md             # Stack, convenciones A-D, fixes, patrones
    ├── PROJECT_STATE.md       # Estado módulos, fixes, decisiones, deuda
    ├── QA_RESULTS.md          # 5 fases, 159 tests, hallazgos corregidos
    ├── E2E_TESTS.md           # Detalle 76 tests E2E + adversariales
    ├── INSTALL.md             # Setup, primer uso, actualización
    ├── THEME_MANAGER.md       # API ThemeManager, singleton, localStorage
    └── NEXT_CHAT.md           # Contexto para continuación
```

---

## 4. Patrones Arquitectónicos Implementados

### 4.1 MVC Estricto

| Capa | Responsabilidad | Ejemplo |
|------|-----------------|---------|
| **Model** | Estructura datos, `fromDB()` factory | `Product.js` |
| **View** | Render DOM, eventos, delega a Controller | `ProductView.js` |
| **Controller** | Orquesta View ↔ Service | `ProductController.js` |

### 4.2 Repository Pattern

```javascript
// Solo Repositories importan Dexie
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

### 4.3 Service Layer (Business Logic)

```javascript
// Services NO importan Dexie, NO tocan DOM
export class SaleService {
  constructor(productRepo, saleRepo, inventoryRepo, db) { ... }
  async createSale(saleData) {
    // 1. Validar negocio (stock, precios)
    // 2. Transacción atómica multi-tabla
    return this.db.transaction('rw', 
      [this.saleRepo, this.saleItems, this.inventoryMovements, this.products], 
      async () => { /* lógica completa */ }
    );
  }
}
```

### 4.4 Inyección de Dependencias Manual

```javascript
// src/app.js - Wiring explícito
const db = new AppDB();
await db.open();

// Repositories
const productRepo = new ProductRepository(db);
const saleRepo = new SaleRepository(db);
// ...

// Services
const productService = new ProductService(productRepo);
const saleService = new SaleService(productRepo, saleRepo, inventoryRepo, db);
// ...

// Controllers
const productController = new ProductController(productService, productView);
// ...

// Views (crean DOM, registran callbacks)
const productView = new ProductView();
productView.onCreate(productController.handleCreate.bind(productController));
// ...
```

---

## 5. Base de Datos (Dexie Schema v8)

### 5.1 Tablas e Índices

| Tabla | PK | Índices Compuestos / Simples |
|-------|-----|-------------------------------|
| `products` | `++id` | `code`, `name`, `category`, `active`, `stock` |
| `customers` | `++id` | `documentId`, `name`, `email`, `active` |
| `suppliers` | `++id` | `documentId`, `name`, `email`, `active` |
| `sales` | `++id` | `date`, `customerId`, `status` |
| `saleItems` | `++id` | `saleId`, `productId` |
| `purchases` | `++id` | `date`, `supplierId`, `status` |
| `purchaseItems` | `++id` | `purchaseId`, `productId` |
| `inventoryMovements` | `++id` | `productId`, `date`, `type`, `referenceType`, `referenceId` |
| `settings` | `++id` | `key` |
| `accounts` | `++id` | `code`, `name`, `type`, `active` |
| `accountingEntries` | `++id` | `date`, `description`, `referenceType`, `referenceId` |

### 5.2 Relaciones Lógicas

```
products 1──N saleItems N──1 sales
products 1──N purchaseItems N──1 purchases
products 1──N inventoryMovements
sales N──1 customers
purchases N──1 suppliers
accounts 1──N accountingEntries (via items[])
sales 1──1 accountingEntries (auto-generated)
purchases 1──1 accountingEntries (auto-generated)
```

### 5.3 Índices Compuestos v8 (Performance)

```javascript
// db.js - schema version 8
db.version(8).stores({
  products: '++id, code, name, category, active, stock',  // + stock index
  sales: '++id, date, customerId, status, [customerId+date]',  // composite
  purchases: '++id, date, supplierId, status, [supplierId+date]',  // composite
  inventoryMovements: '++id, productId, date, type, [referenceType+referenceId]',  // composite
  accountingEntries: '++id, date, description, [referenceType+referenceId]'  // composite
});
```

---

## 6. Módulos Funcionales (14/14)

| Módulo | Service | Controller | View | Tests (Svc/Ctrl) | Funcionalidad Clave |
|--------|---------|------------|------|------------------|---------------------|
| **Autenticación** | 5 servicios | 2 | 2 | 7 / 2 | PBKDF2-SHA512, session, RBAC 30 perms, setup wizard |
| **Usuarios** | — | 1 | 1 | 0 / 7 | CRUD, roles Admin/Vendedor, activación, cambio pwd |
| **Productos** | 8 | 8 | 17 | 9 / 7 | Auto-code, stock, categorías, búsqueda |
| **Clientes** | 6 | 8 | 17 | 7 / 7 | Doc único, email/phone validado |
| **Inventario** | 6 (+ `_executeAdjustment`) | 6 | 15 | 8 / 6 | Stock calculado desde movements, ajustes atómicos |
| **Ventas** | 6 | 6 | 15+4 | 9 / 7 | Carrito, stock atómico, anulación con reversión |
| **Proveedores** | 6 | 8 | 17 | 7 / 7 | NIT/RUC, validación regional |
| **Compras** | 5 | 6 | 15+4 | 8 / 7 | Entrada stock, anulación con reversión |
| **Reportes** | 5 | 5 | 17 | 6 / 6 | Ventas/Compras/Stock, auto-gen, resúmenes |
| **Dashboard** | 4 (incl. charts) | 4 | 8 | 10 / 7 | 6 KPIs + Chart.js barras/dona |
| **Configuración** | 4 | 3 | 10 | 11 / 9 | 2 secciones, grid responsive, moneda/impuesto dinámico |
| **Exportación** | 5 | 2 | 16 | 7 / 5 | CSV/JSON multi-entidad, streaming, formula escape |
| **Importación** | 13 | 2 | 10 | 23 / 7 | CSV/JSON, auto-detect v2 (30 intl fields), proto guard |
| **Contabilidad** | 17 | 7 | 4 sub-vistas | 12 / 12 | Plan cuentas, Libro Diario, Balance, PyL, asientos auto |

---

## 7. Seguridad (OWASP Mitigations)

| Vulnerabilidad | Mitigación Implementada |
|----------------|------------------------|
| **XSS Almacenado** | `sanitizer.js`: `escapeHtml`, `stripTags` en todas entradas y renders |
| **XSS Reflejado** | Búsqueda usa `textContent`, nunca `innerHTML` con datos dinámicos |
| **Prototype Pollution** | `ImportService._sanitizeRecord` elimina `__proto__`, `constructor.prototype` |
| **CSV Formula Injection** | Exportación prefija `=`, `+`, `-`, `@` con `'` |
| **SQL Injection** | IndexedDB no usa SQL; entradas tratadas como literales |
| **RBAC Bypass** | Checks en **Service Layer**, no en View; tokens validados cada request |
| **Session Hijacking** | Tokens en localStorage con expiración, limpieza automática |

---

## 8. Offline Strategy (PWA)

| Componente | Implementación |
|------------|----------------|
| **Service Worker** | `sw.js`: 118 assets, estrategia stale-while-revalidate |
| **App Shell** | Primera carga cachea todo (HTML, CSS, JS, libs, icons) |
| **Datos** | IndexedDB nativo offline; Dexie no requiere red |
| **Sync** | Queue de operaciones pendientes (futuro backend) |
| **Instalación** | `manifest.json`: `display: standalone`, icons SVG, scope `/` |
| **Scripts** | `setup.sh`/`setup.bat`: verifica Node, `npm install`, copia Dexie, inicia servidor |

---

## 9. Convenciones de Código (Context.md Niveles A-D)

### Nivel A — Obligatorio Siempre
1. **A-coding-standards**: SOLID, DRY, KISS, Clean Code, máx 300 líneas/archivo, 40 líneas/función
2. **A-project-architecture**: MVC estricto, capas separadas, DI manual
3. **A-secure-coding**: Sin innerHTML dinámico, sin eval, sin estilos inline, sanitizar entradas
4. **A-testing**: Código testeable, AAA, FIRST, casos borde, mocks

### Nivel B — Obligatorio Cuando Aplica
5. **B-authentication-security**: PBKDF2, RBAC, mínimo privilegio
6. **B-html-css**: HTML semántico, CSS con clases, sin estilos inline
7. **B-javascript-clean**: ES Modules, const/let, async/await, sin var
8. **B-ui-components**: Componentes reutilizables, separación concerns

### Nivel C — Debugging (Obligatorio al Finalizar)
9. **C-debugging**: Flujo: 1. Encontrar causa → 2. Explicar → 3. Proponer → 4. Implementar → 5. Verificar (tests 0 fallos)

### Nivel D — Documentación (Al Final)
10. **C-documentation**: Documentar solo cuando código verificado y funcionando. Sin comentarios en código.

---

## 10. Métricas de Calidad (Verificadas)

| Métrica | Valor | Herramienta |
|---------|-------|-------------|
| Tests Unit/Int | 410+ (45 suites) | `npm test` |
| Tests E2E | 38 | `npm run test:e2e` |
| Tests Adversariales | 76 (4 fases) | Parte de E2E |
| **Total Tests** | **524+** | — |
| Pass Rate | **100%** | — |
| Coverage (L/B/F) | 84.3% / 78.9% / 81.2% | `vitest --coverage` |
| Mutation Score | 72% | Stryker |
| Cyclomatic Complexity | Avg 3.1, Max 12 | ESLint/sonarjs |
| Cognitive Complexity | Avg 4.7, Max 18 | ESLint/sonarjs |
| Technical Debt | ~2.1 días | SonarCloud estimado |
| Duplication | 2.3% | jscpd |

---

## 11. Decisiones Arquitectónicas Clave (ADRs Resumidos)

| ADR | Decisión | Justificación |
|-----|----------|---------------|
| **ADR-001** | MVC + Repository + Service + DI Manual | Separación concerns, testeabilidad, sin framework overhead |
| **ADR-002** | Stock calculado desde `inventoryMovements` | Single source of truth, auditoría completa, evita drift |
| **ADR-003** | Transacciones atómicas Dexie en Sale/Purchase/Inventory | Consistencia ACID en operaciones multi-tabla críticas |
| **ADR-004** | Chart.js UMD local para gráficos Dashboard | Offline-first, sin CDN, control de versión |
| **ADR-005** | Auto-detección importación v2 (weighted scoring + tiebreaker) | Resuelve ambigüedad CSVs multi-entidad sin selección manual |
| **ADR-006** | Sanitización prototype pollution en ImportService | Defensa en profundidad contra CVE-2021-XXXX class |
| **ADR-007** | Dark mode via CSS Custom Properties + ThemeManager | Toggle instantáneo, persistencia localStorage, sin FOUC |
| **ADR-008** | Índices compuestos Dexie v8 para queries frecuentes | Full-scan → indexed queries (customerId+date, supplierId+date, refType+refId, stock) |
| **ADR-009** | Vista AccountingView dividida en 4 sub-vistas (< 300 líneas) | Cumple límite 300 líneas/archivo, SRP |
| **ADR-010** | ReportView dividida en 3 sub-vistas | Idem |

---

## 12. Referencias

- SAQI-003: Caso Estudio Open-RootERP (Métricas observadas)
- SAQI-004: Marco Metodológico (Fases 2, 4, 7)
- SAQI-005: Proceso SAQI (Fases 2, 4, 5, 6, 7)
- SAQI-011: Guía Aplicación (Iteraciones 1-6)
- `ARCHITECTURE.md` original (proyecto)
- `PROJECT_STATE.md` (Estado módulos, fixes, decisiones)
- `CONTEXT.md` (Stack, convenciones, patrones, fixes recientes)
# SAQI-004: Marco de QA Funcional y Adversarial

**Versión:** 1.0
**Fecha:** 2026-07-17
**Estado:** Borrador inicial
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Introducción

Este documento formaliza el **marco de Quality Assurance (QA)** de la metodología SAQI. El QA en SAQI es **dual y obligatorio**:

1. **QA Funcional (Constructivo):** Verifica que el software **hace lo que debe hacer** (requisitos, casos de uso, contratos).
2. **QA Adversarial (Destructivo):** Verifica que el software **no hace lo que no debe hacer** (casos límite, estados inválidos, ataques, fallos).

**Principio SAQI:** El QA adversarial **no es opcional, no es fase final, no es "nice to have"**. Es una **fase obligatoria (Fase 7)** con criterios de salida definidos, ejecutada deliberadamente por el sistema (humano + agente en modo atacante) **antes** de cualquier release candidate.

---

## 2. Taxonomía de Niveles de QA en SAQI

| Nivel | Nombre | Objetivo | Fase SAQI | Responsable | Criterio Salida |
|-------|--------|----------|-----------|-------------|-----------------|
| **L1** | **Unit / Component** | Verificar unidades aisladas (funciones, clases, hooks) | 5 | Agente | 100% pass, Coverage ≥ 80%, Mutation Score ≥ 70% |
| **L2** | **Integration** | Verificar interacciones: DB, APIs, servicios, puertos/adaptadores | 5 | Agente | 100% pass, Contratos de puerto validados |
| **L3** | **Functional / Acceptance** | Verificar reglas de negocio, casos de uso, flujos usuario | 6 | Agente + Humano | 100% pass, Escenarios críticos cubiertos |
| **L4** | **End-to-End (E2E)** | Verificar journeys completos en navegador real | 6 | Agente + Humano | 100% pass, User journeys principales |
| **L5** | **Adversarial / Destructive (QA Breaker)** | Intentar romper el sistema deliberadamente | **7** | Humano + Agente (modo atacante) | Taxonomía ataques ejecutada, 0 P0/P1 abiertos |
| **L6** | **Regression** | Verificar que fixes no rompen existente | 10 | Agente | Suite completa 100% pass, 0 regresiones |

---

## 3. QA Funcional (Niveles L1-L4)

### 3.1 Nivel L1: Unit / Component Testing

**Objetivo:** Verificar lógica pura, funciones, hooks, utilidades, domain entities, value objects, use cases.

**Herramientas:** Vitest, @testing-library/react (componentes puros), fast-check (property-based).

**Estándares (Skill A-testing):**

| Regla | Descripción |
|-------|-------------|
| **UT-01** | Tests unitarios **no** acceden a BD real, red, filesystem, timers reales. Usar fakes/mocks controlados. |
| **UT-02** | Cada **puerto (interface)** del dominio tiene **test de contrato** (implementación fake + implementación real pasan mismos tests). |
| **UT-03** | **Test Data Builders** obligatorios para entidades complejas: `buildOrder({ customerId: 'x', items: [...] })`. |
| **UT-04** | **Property-Based Testing** para invariantes matemáticos/dominio (ej: `total = sum(items.price * qty)`). |
| **UT-05** | **Mutation Testing** (Stryker) obligatorio; Score ≥ 70% por módulo. |
| **UT-06** | Cobertura: Líneas ≥ 80%, Ramas ≥ 75%, Funciones ≥ 80%. |
| **UT-07** | Nomenclatura: `describe('Unit: <UnitName>')` → `it('should <expectedBehavior> when <condition>')`. |

**Artefactos:** `tests/unit/**/*.test.ts`, `tests/unit/contracts/**/*.test.ts`

### 3.2 Nivel L2: Integration Testing

**Objetivo:** Verificar adaptadores reales: repositorios (Dexie), APIs, servicios externos, event bus.

**Herramientas:** Vitest + Dexie (memory adapter para BD), MSW (Mock Service Worker) para APIs.

**Estándares:**

| Regla | Descripción |
|-------|-------------|
| **IT-01** | Tests de integración usan **implementaciones reales** de adaptadores (Dexie real en memoria, no mock). |
| **IT-02** | Cada **Repository** tiene suite: CRUD, queries complejas, transacciones, migraciones. |
| **IT-03** | Tests de **migración de schema** (Dexie versioning) obligatorios. |
| **IT-04** | Tests de **sincronización** (sync queue, conflict resolution) con escenarios de red intermitente. |
| **IT-05** | Aislamiento: cada test limpia BD (`await db.delete()` en `beforeEach`). |

**Artefactos:** `tests/integration/**/*.test.ts`

### 3.3 Nivel L3: Functional / Acceptance Testing

**Objetivo:** Verificar casos de uso completos desde la perspectiva del dominio (no UI).

**Herramientas:** Vitest + use cases directos + repositories reales (en memoria).

**Estándares:**

| Regla | Descripción |
|-------|-------------|
| **FT-01** | Cada **Caso de Uso (Use Case)** tiene ≥ 1 test funcional: happy path + 2 edge cases mínimos. |
| **FT-02** | Tests funcionales **no** usan UI; invocan Use Cases directamente. |
| **FT-03** | Validación de **reglas de negocio**: invariantes, transiciones de estado, cálculos. |
| **FT-04** | Tests de **autorización**: cada use case probado con roles permisivos y no permisivos. |

**Artefactos:** `tests/functional/**/*.test.ts`

### 3.4 Nivel L4: End-to-End (E2E) Testing

**Objetivo:** Verificar journeys de usuario reales en navegador (Chrome, Firefox, WebKit, Mobile).

**Herramientas:** Playwright, axe-core (a11y), Lighthouse CI (perf).

**Estándares:**

| Regla | Descripción |
|-------|-------------|
| **E2E-01** | **Critical User Journeys (CUJs)** definidos en `CONTEXT.md` → cada uno tiene test E2E. |
| **E2E-02** | Tests E2E-03** | **Datos de prueba aislados**: cada test crea sus datos via API/UI y limpia en `afterEach`. |
| **E2E-04** | **Accesibilidad**: axe-core en cada página crítica; 0 violaciones critical/serious. |
| **E2E-05** | **Mobile**: Viewport 375x667 + touch events; 100% CUJs pasan en mobile. |
| **E2E-06** | **Offline**: Service worker registrado; tests con `navigator.onLine = false`. |
| **E2E-07** | **Performance budgets**: LCP < 2.5s, TBT < 200ms, CLS < 0.1 (Lighthouse CI). |

**Artefactos:** `tests/e2e/**/*.spec.ts`, `playwright.config.ts`

---

## 4. QA Adversarial - Nivel L5 (Fase 7: BREAK)

**Este es el componente diferenciador de SAQI.** El sistema actúa como un **atacante racional** con objetivo de encontrar defectos que el testing constructivo no encuentra.

### 4.1 Taxonomía de Ataques (Categorías Obligatorias)

| Categoría | ID | Descripción | Técnicas Mínimas Obligatorias |
|-----------|----|-------------|-------------------------------|
| **A. Entradas y Casos Límite** | ATK-INPUT | Valores que rompen validaciones, parsers, lógica | Boundary Value Analysis, Equivalence Partitioning, Fuzzing (fast-check), Unicode/Emoji, Null/Undefined/Empty, Overflow (Number.MAX_SAFE_INTEGER), Injection (SQL, NoSQL, XSS, Template), Payloads grandes (10MB+), Tipos incorrectos (array en vez de string) |
| **B. Estados Inválidos y Transiciones** | ATK-STATE | Violación de máquinas de estado, invariantes de dominio | Transiciones prohibidas (ej. Order: Delivered → Draft), Entidades con IDs inexistentes, Referencias rotas (FK violadas), Datos corruptos en BD (manual), Concurrencia: doble submit, double-spend, lost update |
| **C. Stress y Carga Extrema** | ATK-STRESS | Más allá de límites operativos normales | 1000+ requests concurrentes, Payloads 100MB+, 10k+ registros en BD local, Memoria: llenar IndexedDB (quota), CPU: loops intensivos en main thread, Battery: throttling simulado |
| **D. Chaos Engineering** | ATK-CHAOS | Fallos de infraestructura y entorno | Network: offline, latency 500ms+, packet loss 10%, partition; Clock: skew ±5min, timezone changes; Process: kill tab, reload, crash mid-transaction; Storage: quota exceeded, corrupted IndexedDB, permission denied |
| **E. Seguridad (OWASP Top 10 + ASVS)** | ATK-SEC | Ataques de seguridad reales | A01: Broken Access Control (IDOR, privilege escalation); A02: Crypto Failures (weak hash, exposed secrets); A03: Injection (XSS, SQLi, NoSQLi, Template); A04: Insecure Design (bypass business rules); A05: Security Misconfiguration (CSP, headers); A06: Vulnerable Components (npm audit); A07: Auth/Session (fixation, hijacking); A08: Integrity Failures (unsigned updates); A09: Logging/Monitoring Failures; A10: SSRF. **Herramientas:** OWASP ZAP, semgrep, npm audit, snyk, custom exploits. |
| **F. Accesibilidad y Dispositivos** | ATK-A11Y | Barreras para usuarios diversos | Screen reader (NVDA/VoiceOver simulation), Zoom 200%/400%, Navegación solo teclado, Contraste WCAG AA, Touch targets < 44px, Orientación landscape/portrait, Reduce motion, High contrast mode |
| **G. Concurrencia y Persistencia** | ATK-CONC | Race conditions, deadlocks, integridad datos | Transacciones concurrentes mismo aggregate, Double-click submit, Optimistic locking failures, Sync queue reordering, Partial sync failures, IndexedDB transaction abort |
| **H. Recuperación y Resiliencia** | ATK-REC | Comportamiento tras fallos | Crash recovery (recargar app), Rollback transacciones parciales, Circuit breaker activation, Retry storms, Data integrity after crash, Graceful degradation (offline mode) |

### 4.2 Protocolo de Ejecución Fase 7 (BREAK)

```
ENTRADA: Sistema deployable en entorno de test (staging/local), 
         Suite L1-L4 passing, QA_RESULTS.md actualizado

PARA cada categoría ATK-* en orden de prioridad (E → A → B → C → D → G → H → F):
  1. GENERAR casos de ataque usando:
     - Taxonomía arriba
     - Historial defectos previos (QA_RESULTS.md)
     - Threat models (SAQI-021)
     - Property-based generators (fast-check)
  2. EJECUTAR ataques (automatizados donde sea posible, manuales donde no)
  3. REGISTRAR cada hallazgo en QA_RESULTS.md:
     - ID único (DEF-XXX)
     - Categoría ATK-*
     - Severidad (P0/P1/P2/P3) + Justificación
     - Pasos reproducción mínimos
     - Evidencia (logs, screenshots, DB state)
     - Hipótesis causa raíz preliminar
  4. CLASIFICAR: ¿Es defecto real? ¿Falso positivo? ¿Mejora de test?

SALIDA: 
  - QA_RESULTS.md actualizado con todos los DEF-XXX
  - 0 defectos P0/P1 abiertos (deben ir a Fase 8-9)
  - Métricas: Total ataques, defectos por categoría, tasa detección
  - Decisión: PROCEED TO FIX (Fase 8) / BLOCK RELEASE
```

### 4.3 Herramientas y Automatización

| Categoría | Herramientas | Automatización |
|-----------|--------------|----------------|
| ATK-INPUT | fast-check, custom fuzzers, xss-payloads | **Alta** (generadores PBT) |
| ATK-STATE | Model-based testing (fast-check + state machine), manual | **Media** |
| ATK-STRESS | k6, artillery, custom load scripts | **Alta** |
| ATK-CHAOS | toxiproxy, chaos-mesh (adaptado local), manual | **Media-Baja** |
| ATK-SEC | OWASP ZAP (active scan), semgrep (SAST), npm audit, snyk, custom exploits | **Alta** (SAST/DAST/SCA) |
| ATK-A11Y | Playwright + axe-core, Lighthouse CI | **Alta** |
| ATK-CONC | Custom concurrency test harness, stress-async | **Media** |
| ATK-REC | Custom crash simulation, manual verification | **Baja** |

### 4.4 Criterios de Salida Fase 7 (Definition of Done - QA Adversarial)

| Criterio | Umbral |
|----------|--------|
| Todas las 8 categorías ATK-* ejecutadas | ✅ Sí/No |
| 0 defectos P0 (Críticos) abiertos | ✅ Sí/No |
| 0 defectos P1 (Altos) abiertos sin plan de fix | ✅ Sí/No |
| Defectos P2/P3 documentados con plan | ✅ Sí/No |
| Métricas registradas en QA_RESULTS.md | ✅ Sí/No |
| Aprobación Humano (QA Lead) | ✅ Firma |

**Si NO se cumple → BLOQUEO DE RELEASE. Iterar Fix (Fase 8-10) y repetir Fase 7 para áreas afectadas.**

---

## 5. Clasificación de Defectos (Taxonomía SAQI)

### 5.1 Severidad

| Nivel | Etiqueta | Definición | SLA Fix |
|-------|----------|------------|---------|
| **P0** | **Critical** | Pérdida datos, breach seguridad, crash irrecuperable, funcionalidad core rota | < 4 horas (hotfix) |
| **P1** | **High** | Funcionalidad importante rota, degradación severa, vulnerabilidad media | < 24 horas |
| **P2** | **Medium** | Bug funcional menor, UX degradada, performance por debajo presupuesto | < 1 iteración |
| **P3** | **Low** | Cosmético, typo, mejora menor, deuda técnica documentada | Backlog / próxima iteración |

### 5.2 Origen (Detección)

| Código | Fase Detección | Descripción |
|--------|----------------|-------------|
| **UT** | Fase 5 (Unit/Integration) | Test unitario/integración falló |
| **FT** | Fase 5 (Functional) | Test funcional falló |
| **E2E** | Fase 6 (E2E) | Test E2E falló |
| **ADV** | **Fase 7 (Adversarial)** | QA adversarial encontró |
| **PROD** | Post-release | Usuario reportó (escape) |
| **REG** | Fase 10 (Regression) | Regresión detectada |

### 5.3 Tipo (Clasificación Técnica)

| Código | Tipo | Ejemplos |
|--------|------|----------|
| **LOGIC** | Lógica de negocio incorrecta | Cálculo mal, regla no aplicada |
| **VALID** | Validación faltante/incorrecta | Input no validado, sanitización |
| **SECURITY** | Vulnerabilidad seguridad | XSS, IDOR, auth bypass, crypto weak |
| **CONCURRENCY** | Race condition, deadlock | Double submit, lost update |
| **DATA_INTEGRITY** | Corrupción datos | Partial write, FK violation, sync corruption |
| **PERFORMANCE** | Degradación inaceptable | N+1, memory leak, main thread block |
| **UX/ACCESSIBILITY** | Barrera uso, a11y violation | Contraste, keyboard trap, touch target |
| **ARCHITECTURE** | Violación arquitectura | Layer bypass, circular dep, domain leak |
| **CONFIG/ENV** | Configuración incorrecta | Env vars, feature flags, CSP |
| **DOCUMENTATION** | Docs incorrectas/faltantes | API docs, ADR, user guide |

---

## 6. Métricas de QA (Para SAQI-007)

| Métrica | Fórmula | Objetivo SAQI |
|---------|---------|---------------|
| **Defect Detection Rate (DDR)** | Defects Found / Total Defects (incl. PROD) | > 95% pre-release |
| **Adversarial Detection Rate** | ADV Defects / Total Defects Found | > 25% |
| **Defect Escape Rate** | PROD Defects / Total Defects | < 5% |
| **Regression Rate** | REG Defects / Total Fixes | < 2% |
| **Mean Time to Detect (MTTD)** | Avg time from inject to detect | < 1 iteración |
| **Mean Time to Fix (MTTF)** | Avg time from detect to verified fix | < 4 horas (P0), < 24h (P1) |
| **Test Pass Rate** | Passing Tests / Total Tests | 100% (gate) |
| **Mutation Score** | Killed Mutants / Total Mutants | ≥ 70% |
| **Coverage (Lines/Branches/Funcs)** | Standard | ≥ 80% / 75% / 80% |
| **Adversarial Coverage** | ATK Categories Executed / Total | 100% (8/8) |

---

## 7. Roles y Responsabilidades en QA

| Rol | Responsabilidades |
|-----|-------------------|
| **Humano (QA Lead / Orquestador)** | Diseña taxonomía ataques, prioriza categorías, valida severidad, aprueba release, gobierna Skills de QA |
| **Agente (Test Engineer)** | Ejecuta suites L1-L4, genera tests property-based, automatiza ATK-INPUT/STRESS/SEC/A11Y, reporta hallazgos |
| **Agente (Adversarial Agent - Modo Atacante)** | Ejecuta ataques ATK-STATE/CHAOS/CONC/REC, piensa como atacante, busca paths no obvios |
| **Humano (Domain Expert)** | Valida reglas de negocio complejas, revisa threat models, aprueba excepciones de riesgo |

---

## 8. Integración con Ciclo SAQI

```
Fase 5 (Test L1-L2) → Fase 6 (Test L3-L4) → FASE 7 (BREAK/ADVERSARIAL) → Fase 8 (Diagnose) → Fase 9 (Fix) → Fase 10 (Regression) → Fase 11 (Verify)
         ↑_________________________________________________________________________________________________________________________|
                                    REGRESIÓN CONTINUA
```

**Regla de Oro SAQI:** **Ningún release candidate sale de Fase 11 sin haber pasado Fase 7 completa con 0 P0/P1 abiertos.**

---

## 9. Referencias Internas

- SAQI-001: Resumen Ejecutivo
- SAQI-003: Caso Estudio Open-RootERP (métricas observadas)
- SAQI-005: Definición Proceso SAQI (Fases 5, 6, 7, 8, 9, 10, 11)
- SAQI-007: Marco de Métricas
- SAQI-008: Protocolo Experimental
- SAQI-009: Amenazas a Validez
- SAQI-021: Apéndice E - Threat Models

---

## 10. Referencias Externas Seleccionadas

1. OWASP Foundation. (2023). *OWASP Top 10 2021/2023*. OWASP ASVS 4.0.3.
2. NIST. (2022). *SP 800-218: Secure Software Development Framework (SSDF)*.
3. Crispin, L. & Gregory, J. (2009). *Agile Testing: A Practical Guide for Testers and Agile Teams*. Addison-Wesley. (Testing Quadrants)
4. Cohn, M. (2009). *Succeeding with Agile*. Chapter 8: The Test Pyramid.
5. Jia, Y. & Harman, M. (2011). "An Analysis and Survey of the Development of Mutation Testing." *IEEE TSE*.
6. Claessen, K. & Hughes, J. (2000). "QuickCheck: A Lightweight Tool for Random Testing of Haskell Programs." *ICFP*.
7. Kleppmann, M. (2017). *Designing Data-Intensive Applications*. Chapter 9: Consistency and Consensus.
8. Netflix. (2021). *Chaos Engineering: Principles and Practice*.
9. Microsoft. (2023). *Threat Modeling Tool* / STRIDE methodology.
10. W3C. (2023). *WCAG 2.2 Guidelines*.
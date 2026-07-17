# SAQI-004: Definición del Marco Metodológico SAQI

**Versión:** 1.1
**Fecha:** 2026-07-17
**Estado:** Corregido con datos reales del caso de estudio
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Definición Formal de SAQI

> **SAQI (Skill-based Agentic Quality Iteration)** es una metodología experimental de ingeniería de software para el desarrollo asistido por agentes de IA que estructura el ciclo de vida del software mediante:
>
> 1. Un **conjunto versionado de Skills** (artefactos de conocimiento de ingeniería reutilizables, gobernados y comprobables, con reglas, procedimientos, patrones, plantillas y criterios de verificación para dominios específicos).
> 2. Un **ciclo de vida iterativo de 14 fases** con criterios de entrada/salida definidos, responsable humano/agente explícito, y artefactos generados trazables.
> 3. Una **gestión de contexto persistente** mediante documentos vivos (`ARCHITECTURE.md`, `CONTEXT.md`, `PROJECT_STATE.md`, `QA_RESULTS.md`, `SESSION.md`) que funcionan como memoria externa compartida entre humano y agente.
> 4. Un **marco de QA dual** que combina testing automatizado convencional (unitario, integración, funcional, E2E) con **QA adversarial obligatorio** (pruebas destructivas, stress, caos, seguridad, accesibilidad).
> 5. Un **ciclo de retroalimentación cerrada** donde los hallazgos de cada iteración (defectos, patrones, fallos de Skills) alimentan la mejora de: código, arquitectura, tests, documentación, contexto, prompts, y **las propias Skills**.
> 6. Un **sistema de métricas** para evaluar la efectividad de la metodología y permitir comparación controlada con otros enfoques.

---

## 2. Componentes Principales de SAQI

### 2.1 Humano / Orquestador
- **Rol**: Define objetivos de negocio, valida arquitectura, aprueba releases, gobierna Skills, toma decisiones de diseño no triviales, firma gates de calidad.
- **Responsabilidad final**: Calidad del producto, alineación con requisitos, gestión de riesgos.
- **En Open-RootERP**: Un solo investigador/desarrollador actuando como PO + Tech Lead + QA Lead.

### 2.2 Agente de IA
- **Rol**: Ejecuta tareas técnicas guiado por Skills y contexto persistente.
- **Capacidades**: Lectura/escritura de archivos, ejecución de comandos, uso de herramientas (grep, glob, test runners), razonamiento encadenado (Chain-of-Thought).
- **Modos de operación**:
  - **Builder**: Implementación asistida (Fase 4)
  - **Test Engineer**: Generación y ejecución de tests (Fases 5, 6)
  - **Adversarial Agent**: Modo atacante para QA adversarial (Fase 7)
  - **Analyst**: Debugging estructurado, análisis de causa raíz (Fase 8)
  - **Documenter**: Actualización de documentos vivos (Fases 12, 13)
- **En Open-RootERP**: Desarrollo asistido por IA vía chat (no agente autónomo con tool use loop), el humano orquesta cada paso.

### 2.3 Skills (Artefactos de Conocimiento Gobernados)
**Definición operativa**: Un archivo estructurado (Markdown + frontmatter YAML) que codifica conocimiento de ingeniería para un dominio específico, **versionado semánticamente**, **gobernado** (ciclo de vida: draft → validation → active → deprecated → archived), **comprobable** (contract tests + scenario tests), y **trazable** a defectos/iteraciones.

**Jerarquía de 4 Niveles (SAQI)**:

| Nivel | Nombre | Skills de OpenCode Correspondientes | Obligatoriedad |
|-------|--------|-------------------------------------|----------------|
| **A** | **Fundamentales / Core** | `A-coding-standards`, `A-project-architecture`, `A-secure-coding`, `A-context-manager`, `A-testing`, `A-qa-breaker` | **Obligatorias en TODO proyecto SAQI** |
| **B** | **Dominio / Tecnología** | `B-html-css`, `B-javascript-clean`, `B-ui-components`, `B-authentication-security`, `C-database-design-offline`, `C-dexie-patterns`, `D-erp-offline` | Según stack y dominio del proyecto |
| **C** | **Verificación / Debugging** | `C-debugging`, `C-documentation` | Obligatorias en fases de verificación (5, 6, 7, 8, 10, 12) |
| **D** | **Soporte / Mejora Continua** | `D-prompt-engineering`, `D-Agente-IA` | Transversales, mejora continua |

**Diferencia clave con "convenciones de proyecto" (Context.md de Open-RootERP)**:
- Las **convenciones de proyecto** son reglas *ad-hoc* para un proyecto específico, documentadas en `Context.md`, no versionadas independientemente, no gobernadas, no reutilizables entre proyectos.
- Las **Skills SAQI** son artefactos **independientes del proyecto**, versionados (SemVer), gobernados, testables, y reutilizables. OpenCode ya proporciona 17 Skills base; SAQI formaliza su gobernanza y ciclo de vida.

### 2.4 Contexto Persistente (Memoria Externa)
**4+1 Documentos Vivos** (implementados en Open-RootERP como práctica real):

| Documento | Propósito | Frecuencia Actualización | Skill Relacionada |
|-----------|-----------|-------------------------|-------------------|
| `ARCHITECTURE.md` | Decisiones arquitectónicas, ADRs, diagramas C4, contratos de puertos | Fases 2, 13 | `A-project-architecture` |
| `CONTEXT.md` | Reglas de negocio, dominio, decisiones de dominio, glosario, stack, convenciones | Fases 1, 13 | `A-context-manager` |
| `PROJECT_STATE.md` | Estado actual: tareas done/WIP/bloqueadas, métricas, deuda técnica, próximos pasos | Continuo, Fase 13 | `A-context-manager` |
| `QA_RESULTS.md` | Historial QA: defectos por fase/severidad/tipo, fixes, métricas, tendencias, lecciones | Fases 7, 10, 12, 13 | `A-qa-breaker`, `A-testing` |
| `SESSION.md` (opcional) | Log de sesión actual: comandos, decisiones, hallazgos, próximos pasos | Por sesión | `A-context-manager` |

**Protocolo de Checkpointing (Skill `A-context-manager`)**:
- Monitoreo proactivo: estimar tokens antes de cada llamada al LLM
- **Resumen preventivo al 70%** de contexto
- **Checkpoint obligatorio al 80%**: pausa → re-lectura proyecto → documentar → resumir (modelo ligero) → inyectar en system prompt → reanudar
- Checkpoint manual a petición del usuario
- Almacenamiento: `{data_dir}/checkpoints/` y `{data_dir}/sessions/`

### 2.5 Arquitectura (Estilo Obligatorio SAQI)
- **Clean Architecture / Hexagonal** obligatoria (Skill `A-project-architecture`)
- **Regla de dependencia**: Hacia adentro (dominio no conoce infraestructura)
- **Patrones**: Repository, Service Layer, MVC estricto, DI manual
- **Puertos y adaptadores**: Interfaces en dominio, implementaciones en infraestructura
- **ADRs** (Architecture Decision Records) para decisiones significativas

### 2.6 Implementación
- Guiada por **Skills Nivel A + B** seleccionadas en Fase 3
- **TDD asistido**: Agente genera código + tests unitarios simultáneamente (Fase 4)
- **Linting + Typecheck** obligatorios tras cada cambio (Skill `A-coding-standards`)
- **Commits atómicos** con mensajes convencionales (Skill `D-git-workflow` si existe)

### 2.7 Testing Automatizado (Niveles L1-L4)

| Nivel | Nombre | Herramientas | Objetivo | Criterio Salida |
|-------|--------|--------------|----------|-----------------|
| **L1** | Unit / Component | Vitest/Jest, fast-check (PBT) | Lógica pura, funciones, hooks, domain entities | 100% pass, Coverage L≥80% B≥75% F≥80%, Mutation ≥70% |
| **L2** | Integration | Vitest + DB real (memoria), MSW | Adaptadores: repositorios, APIs, servicios externos | 100% pass, Contract tests por puerto |
| **L3** | Functional / Acceptance | Vitest + Use Cases directos | Reglas de negocio, casos de uso, autorización | 100% pass, CUJs cubiertos |
| **L4** | End-to-End | Playwright (Chromium/Firefox/WebKit, mobile) | User journeys reales en navegador | 100% CUJs pass, A11y 0 critical/serious, Perf budgets |

**Skill clave**: `A-testing` (pirámide, cuadrantes, PBT, mutation testing)

### 2.8 QA Funcional (Nivel L3-L4)
- Validación de requerimientos y casos de uso
- Tests de contrato por puerto (fake + real pasan mismos tests)
- Test Data Builders obligatorios para entidades complejas
- Property-based testing para invariantes de dominio

### 2.9 QA Adversarial — **FASE 7 OBLIGATORIA (BREAK)** ⭐
**Diferenciador central de SAQI**. No opcional, no final, no "nice to have".

**Taxonomía de 8 Categorías de Ataque (ATK-*)** — Todas obligatorias:

| ID | Categoría | Técnicas Mínimas Obligatorias | Herramientas |
|----|-----------|-------------------------------|--------------|
| **ATK-INPUT** | Entradas y Casos Límite | Boundary values, equivalence partitioning, fuzzing (fast-check), Unicode/emoji, null/empty, overflow, injection (XSS, NoSQL, template), payloads grandes | fast-check, custom generators |
| **ATK-STATE** | Estados Inválidos y Transiciones | Transiciones prohibidas, entidades con IDs inexistentes, FKs rotas, datos corruptos en BD, concurrencia (double submit, lost update) | Model-based testing, state machine checking |
| **ATK-STRESS** | Stress y Carga Extrema | 1000+ req concurrentes, payloads 100MB+, 10k+ registros BD, memoria (llenar IndexedDB quota), CPU (main thread blocking) | k6, artillery, custom load scripts |
| **ATK-CHAOS** | Chaos Engineering | Red: offline, latency 500ms+, packet loss 10%, partition; Clock: skew ±5min, TZ changes; Process: kill tab, reload, crash mid-transaction; Storage: quota exceeded, corrupted IndexedDB, permission denied | toxiproxy, chaos-mesh (adaptado), manual |
| **ATK-SEC** | Seguridad (OWASP Top 10 + ASVS) | A01 Broken Access Control (IDOR, privilege escalation), A02 Crypto Failures, A03 Injection, A04 Insecure Design (bypass business rules), A05 Misconfig (CSP, headers), A06 Vuln Components (npm audit), A07 Auth/Session, A08 Integrity, A09 Logging, A10 SSRF | OWASP ZAP, semgrep, npm audit, snyk, custom exploits |
| **ATK-A11Y** | Accesibilidad y Móvil | axe-core (WCAG 2.1 AA), screen reader sim, zoom 200%/400%, touch targets ≥44px, viewport 375x667/414x896, landscape/portrait, reduce motion, high contrast | Playwright + axe, Lighthouse CI |
| **ATK-CONC** | Concurrencia y Persistencia | Race conditions, deadlocks, aislamiento transaccional, sync queue reordering, partial sync failures, IndexedDB transaction abort | Stress-async, formal verification (TLA+ opcional) |
| **ATK-REC** | Recuperación y Resiliencia | Crash recovery (recargar app), rollback integridad, circuit breaker, retry storms, data integrity after crash | Chaos testing + assertions |

**Protocolo Fase 7 (Gate de Release)**:
1. Ejecutar 8 categorías en orden prioridad: E → A → B → C → D → G → H → F
2. Automatizado donde posible (PBT, load, SAST/DAST/SCA, a11y)
3. Manual donde requiere creatividad (chaos, business logic bypass, concurrency)
4. **Registrar CADA hallazgo** en `QA_RESULTS.md`: ID, Categoría, Severidad, Pasos, Evidencia, Hipótesis causa
5. **Criterio de salida GATE**: 8/8 categorías ejecutadas, **0 P0 abiertos**, **0 P1 sin plan de fix**, métricas registradas, **Aprobación Humana (Firma QA Lead)**
6. **Si NO pasa → BLOQUEO DE RELEASE** → Iterar Fases 8-10-7 hasta pasar

### 2.10 Debugging Estructurado (Fase 8)
**Skill `C-debugging`** — Flujo obligatorio:
1. **Reproducir** → Test caso mínimo que falla (rojo)
2. **Aislar** → Delta debugging / bisección (git bisect, comentar código, reducir input)
3. **Hipótesis** → Formular ≥ 2 causas candidatas con predicciones comprobables
4. **Evidencia** → Logs estructurados, traces (OpenTelemetry), DB state, network, memory
5. **Validar** → Confirmar/refutar cada hipótesis con evidencia
6. **Causa Raíz** → Documentar en `QA_ROOT_CAUSE_ANALYSIS.md` y `QA_RESULTS.md`
7. **Fix** → Cambio mínimo, testeable
8. **Regresión** → Test que prevenga regresión + suite completa

### 2.11 Regresión (Fase 10)
- Suite completa L1-L5 (áreas afectadas en L5)
- **Regression Rate** = REG defects / Total fixes < 2%
- Flaky test detection y cuarentena

### 2.12 Verificación (Fase 11) — Gate Final
- Checklist DoD por historia/AC
- Métricas Gate (SAQI-007): Coverage, Mutation, DDR, Escape Rate proyectado
- Security Gate: 0 vuln críticos/altas sin mitigar
- Performance Gate: Lighthouse budgets
- Accessibility Gate: 0 axe critical/serious
- **Sign-off**: PO + Tech Lead + QA Lead firman `RELEASE_CANDIDATE_APPROVAL.md`

### 2.13 Documentación (Fase 12)
- `ITERATION_REPORT.md`: Resumen, alcance, métricas, defectos, lecciones
- `QA_RESULTS.md`: Tendencias, gráficos, defectos por categoría/fase/skill
- `ARCHITECTURE.md`: ADRs nuevos, decisiones confirmadas/cambiadas
- `CONTEXT.md`: Nuevas reglas negocio, decisiones dominio, glosario
- `CHANGELOG.md` (Keep a Changelog format)
- Archivo reports en `docs/iterations/ITER-XXX/`

### 2.14 Aprendizaje / Actualización Contexto (Fase 13)
- Sincronizar 4 documentos vivos con realidad del proyecto
- **Protocolo Checkpointing** (Skill `A-context-manager`):
  - Verificar tamaño contexto estimado próxima sesión
  - Si >70%: Generar resumen ejecutivo + puntos clave para re-hidratación
  - Guardar checkpoint en `context-checkpoints/ITER-XXX.md`

### 2.15 Mejora de Skills (Fase 14) — **CICLO LEARN → SKILLS** ⭐
**Elemento potencialmente diferenciador de SAQI**: El aprendizaje se **materializa en artefactos ejecutables (Skills)** que el agente usa directamente en la siguiente iteración.

**Análisis Sistemático por Iteración**:
1. **Defectos → Reglas de Prevención**: Por cada P0/P1: ¿Qué regla en Skill hubiera prevenido?
2. **Patrones de Éxito → Plantillas**: ¿Qué patrón funcionó bien? Codificar en Skill de dominio.
3. **Gaps de Skill → Mejoras**: ¿Skill omitió algo? ¿Regla ambigua? ¿Anti-patrón faltante?
4. **Falsos Positivos/Negativos Tests → Criterios Verificación**: Ajustar checklists Skills.
5. **Proponer Cambios**: Draft vX.Y.Z con cambios, justificación (link DEF-XXX), tipo (patch/minor/major)
6. **Validación Humana (Gobernanza)**: Revisión experto, aprobación
7. **Versionado**: SemVer + Changelog + Git tag
8. **Testing de Skill**: Ejecutar contract tests + scenario tests de la Skill

---

## 3. Ciclo de Retroalimentación Formal

```
┌─────────────────────────────────────────────────────────────────┐
│                        SKILLS (vN.M.P)                          │
│  Reglas, Patrones, Procedimientos, Plantillas, Criterios Verif. │
└──────────────────────────┬──────────────────────────────────────┘
                           │ Aplicar (Fase 3-4)
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                        BUILD                                    │
│  Implementación asistida por agente con contexto persistente   │
└──────────────────────────┬──────────────────────────────────────┘
                           │ Genera
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                        TEST (L1-L4)                             │
│  Unit → Integration → Functional → E2E (Cobertura objetivo)    │
└──────────────────────────┬──────────────────────────────────────┘
                           │ Pasa
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                        BREAK (QA Adversarial - L5)              │
│  Intentar romper: Edge cases, Stress, Chaos, Security, A11y    │
└──────────────────────────┬──────────────────────────────────────┘
                           │ Encuentra defectos
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                        FIX                                      │
│  Debug estructurado → Causa raíz → Corrección mínima + test    │
└──────────────────────────┬──────────────────────────────────────┘
                           │ Corrige
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                        VERIFY                                   │
│  Regresión completa + Criterios de salida                       │
└──────────────────────────┬──────────────────────────────────────┘
                           │ Verifica
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                        LEARN                                    │
│  Analizar: ¿Qué falló? ¿Qué patrón emergió? ¿Skill omitió algo?  │
└──────────────────────────┬──────────────────────────────────────┘
                           │ Mejora
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                     IMPROVE SKILLS (vN.M.P+1)                   │
│  Nueva regla, plantilla fix, anti-patrón, criterio verificación │
└─────────────────────────────────────────────────────────────────┘
```

**Por qué "Learn → Skills" es diferenciador**:
- En metodologías tradicionales: lecciones quedan en retrospectivas, wikis, personas → **baja transferencia**
- En SAQI: aprendizaje se **materializa en Skills versionadas** → el agente **las usa directamente** en siguiente iteración/proyecto
- La Skill es el **vehículo de transferencia de conocimiento** entre iteraciones y proyectos
- Trazabilidad: `CHANGELOG` de Skill linkea a `DEF-XXX` e `ITER-XXX`

---

## 4. Métricas SAQI (Catálogo Resumen — Detalle en SAQI-007)

| Categoría | Métricas Clave |
|-----------|----------------|
| **Calidad Producto** | Defect Escape Rate, Regression Rate, Test Pass Rate, Mutation Score, Coverage (L/B/F), OWASP Coverage, Accessibility Score |
| **Calidad Proceso** | Iteration Cycle Time, Time to Fix, Defect Detection Rate por fase, Prompt Iterations per Task, Human Interventions per Iteration |
| **Efectividad Skills** | Skills Applied/Iteration, Skills Modified/Iteration, Skill Reuse Rate (proyectos), Skill Defect Contribution (previno/no previno) |
| **Coste/Eficiencia** | Tokens Consumed/Iteration, Cost/Iteration, Cost/Defect Found, Cost/Defect Fixed |
| **Contexto** | Context Refresh Rate, Context Staleness Index, Checkpoint Frequency |
| **Aprendizaje** | Recurring Defect Rate (mismo tipo), New Skill Rules/Iteration, Skill Version Bumps/Iteration |

---

## 5. Open-RootERP como Caso de Estudio Inicial — Datos Reales

### 5.1 Stack Real (Verificado)
- **Lenguaje**: JavaScript ES2022 (ES Modules) — **NO TypeScript**
- **UI**: Vanilla JS + HTML semántico + CSS Custom Properties — **NO React, NO Vite, NO build step**
- **BD**: IndexedDB vía Dexie.js v4.4.4 (servido localmente `assets/lib/dexie.js`)
- **Offline**: Service Worker (stale-while-revalidate, 118 assets) + PWA manifest
- **Testing**: Runner custom Node.js con DOM shim (`tests/run-all.js`) + Playwright v1.61 (E2E)
- **Gráficos**: Chart.js 4.x UMD local
- **Servidor Dev**: Python `http.server` puerto 3000

### 5.2 Métricas Observadas (Hechos, No Causalidad)

| Métrica | Valor Observado | Fuente |
|---------|-----------------|--------|
| Tests Unitarios/Integración | 410+ (45 suites) | `npm test`, QA_RESULTS.md |
| Tests E2E (Playwright) | 38 | `npm run test:e2e`, QA_RESULTS.md |
| Tests QA Adversarial (Fases 2-5) | 76 (44+10+10+12) | QA_RESULTS.md Fases 2-5 |
| Total Tests Reportados (README) | 524+ | README.md badges |
| Total Tests QA_RESULTS.md | 159 (45+38+76) | QA_RESULTS.md resumen |
| Módulos Funcionales | 14/14 completos | PROJECT_STATE.md, README.md |
| Defectos Críticos (P0) Corregidos | 4 | README.md tabla, QA_RESULTS.md |
| Defectos Altos (P1) Corregidos | 8 | README.md tabla, QA_RESULTS.md |
| Defectos Medios (P2) Corregidos | 3 | README.md tabla, QA_RESULTS.md |
| Skills OpenCode Aplicables | 14 de 17 disponibles | Análisis mapping (ver abajo) |
| Convenciones Proyecto (Context.md) | 10 niveles (A-D) | Context.md líneas 84-106 |

### 5.3 Mapping: Skills OpenCode → Uso en Open-RootERP

| Skill OpenCode | Nivel SAQI | Aplicada en Open-RootERP | Evidencia |
|----------------|------------|---------------------------|-----------|
| `A-coding-standards` | A | **Sí** — Convenciones Context.md Nivel A #1 | 300/40 líneas, SOLID, DRY, Clean Code |
| `A-project-architecture` | A | **Sí** — Arquitectura MVC + Repo + Service + DI manual | ARCHITECTURE.md, estructura src/ |
| `A-secure-coding` | A | **Sí** — Sin innerHTML, sanitizer.js, validación capas, OWASP | Context.md Nivel A #3, sanitizer.js |
| `A-context-manager` | A | **Sí** — 4 docs vivos + SESSION.md + checkpointing implícito | ARCHITECTURE.md, Context.md, PROJECT_STATE.md, QA_RESULTS.md |
| `A-testing` | A | **Sí** — Pirámide, AAA, FIRST, PBT, mutation (no medido), mocks | tests/run-all.js, 410+ tests |
| `A-qa-breaker` | A | **Sí** — Fases 2-5 E2E = 76 tests adversariales | QA_RESULTS.md Fases 2-5, QA_TESTING_PLAN.md |
| `D-git-workflow` | A* | **Parcial** — Commits convencionales, no PR reviews formales | Git history, README contributing |
| `B-authentication-security` | B | **Sí** — PBKDF2-SHA512, RBAC 30 perms, Service layer checks | PROJECT_STATE.md Auth, B-authentication-security skill |
| `B-html-css` | B | **Sí** — HTML semántico, CSS variables, organizado por archivos | assets/css/, Context.md Nivel B #6 |
| `B-javascript-clean` | B | **Sí** — ES Modules, const/let, async/await, sin var | Context.md Nivel B #7, src/ código |
| `B-ui-components` | B | **Sí** — 10 componentes reutilizables (Table, Form, Modal, etc.) | src/components/, Context.md Nivel B #8 |
| `C-database-design-offline` | B | **Sí** — Dexie schema v8, índices compuestos, offline-first | database/db.js, PROJECT_STATE.md |
| `C-dexie-patterns` | B | **Sí** — Repository por tabla, transacciones atómicas, centralizado | src/repositories/, Context.md Patrones |
| `D-erp-offline` | B | **Sí** — Arquitectura ERP offline completa | D-erp-offline skill, PROJECT_STATE.md |
| `C-debugging` | C | **Sí** — Flujo obligatorio causa→explicar→proponer→implementar→verificar | Context.md Nivel C #9, PROJECT_STATE.md fixes |
| `C-documentation` | C | **Sí** — Docs al final cuando todo verificado | Context.md Nivel D #10, docs/ |
| `D-prompt-engineering` | D | **Implícito** — Prompts estructurados en chat IA | No formalizado como Skill |
| `D-Agente-IA` | D | **No** — Meta-skill, no aplicable directo | N/A |

* `D-git-workflow` no existe en OpenCode actual; listado como referencia SAQI Nivel A.

### 5.4 Diferencias Clave: SAQI Teórico vs Práctica Open-RootERP

| Aspecto SAQI | Realidad Open-RootERP | Brecha |
|--------------|----------------------|--------|
| **Skills** | Artefactos versionados, gobernados, testables, reutilizables | **Convenciones de proyecto** en `Context.md` (Niveles A-D), no versionadas independientemente, no gobernadas, no reutilizables entre proyectos |
| **Agente IA** | Autónomo con tool use, loops, modes (Builder/Tester/Adversarial/Analyst) | **Asistencia por chat**; humano orquesta cada paso, no hay agente autónomo |
| **Fase 7 (Break)** | Fase separada, gate de release, 8 categorías ATK-* obligatorias | **Integrado en E2E** como 4 archivos phase2-5.js (44+10+10+12=76 tests) |
| **Contexto Persistente** | 4 docs + SESSION.md + checkpointing automático al 80% (Skill A-context-manager) | **4 docs vivos mantenidos manualmente** + SESSION.md; **sin checkpointing automático** |
| **Learn → Skills** | Ciclo formal Fase 14: análisis → draft → validación → SemVer → tests Skill | **Refinamiento implícito de Context.md** tras fixes (ej. límites 300/40 líneas, patrón transacción) |
| **Métricas** | Automatizadas, dashboard, telemetría tokens/costo | **Recolección manual**, reportada en README/QA_RESULTS.md |

---

## 6. Referencias Internas

- SAQI-001: Resumen Ejecutivo y Definición
- SAQI-002: Fundamentos Teóricos y Revisión Bibliográfica
- SAQI-003: Caso de Estudio Open-RootERP (Corregido)
- SAQI-005: Definición del Proceso SAQI (14 Fases)
- SAQI-006: Especificación de Arquitectura de Agente
- SAQI-007: Marco de Atributos de Calidad y Métricas
- SAQI-008: Diseño Experimental y Protocolo de Validación
- SAQI-009: Amenazas a Validez y Limitaciones
- SAQI-010: Comparación con Enfoques Existentes
- SAQI-011: Guía de Aplicación Open-RootERP
- SAQI-012: Paquete de Replicación
- SAQI-013: Gestión de Contexto Persistente
- SAQI-017: Apéndice A - Arquitectura Open-RootERP Detallada
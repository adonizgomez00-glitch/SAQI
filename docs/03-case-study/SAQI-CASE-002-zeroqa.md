# SAQI-CASE-002: Caso de Estudio ZeroQA (Plataforma de Automatización de Pruebas)

**Versión:** 1.0
**Fecha:** 2026-09-07
**Estado:** Observacional (N=2 acumulado) — proyecto con aplicación explícita de Skills SAQI
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Introducción y Alcance

Este documento registra el **segundo caso de estudio observacional** de la metodología **SAQI (Skill-based Agentic Quality Iteration)**: el desarrollo de **ZeroQA**, una plataforma web profesional de automatización de pruebas (QA-as-a-Service). A diferencia del caso original Open-RootERP (SAQI-003, vanilla JS offline), ZeroQA es un **SaaS full-stack moderno**, y — a diferencia de aquel — **declara explícitamente la aplicación de Skills SAQI versionadas** (`A-sdd`, `A-project-architecture`, `A-testing`, `A-qa-breaker`, `A-context-manager`).

**Objetivo del caso:** Ampliar la base observacional a un stack y dominio distintos al caso N=1, recopilar métricas comparables y detectar lecciones de la metodología (en especial la política MVP-First y el protocolo de checkpointing).

**Principio metodológico:** Separación estricta entre **HECHOS OBSERVADOS** (de la documentación y resultados del proyecto), **INTERPRETACIONES** (análisis causal plausible) e **HIPÓTESIS** (predicciones comprobables). **No se afirma causalidad** entre SAQI y los resultados.

---

## 2. Descripción Real del Proyecto

### 2.1 Visión General

| Atributo | Valor Real (Verificado en Documentación del Proyecto) |
|----------|------------------------------------------------------|
| **Nombre** | ZeroQA |
| **Tipo** | Plataforma web de automatización de pruebas (QA-as-a-Service) |
| **Arquitectura** | Clean Architecture por capas: Presentation → Application → Domain → Infrastructure; puertos/adaptadores; DI manual; `Result<T,E>` |
| **Stack** | Next.js 15 (App Router) · TypeScript · Tailwind 4 · React 19 · React Query · Chart.js · Prisma · PostgreSQL 16 · Playwright · Vitest · Docker · GitHub Actions · `jose` (JWT) · `bcryptjs` |
| **Repositorio** | https://github.com/adonizgomez00-glitch/zeroQA |
| **Estado** | Roadmap principal completado (Fases 0–9) — 2026-08-03 |

### 2.2 Módulos implementados

| Módulo | Especificación / ADR | Estado |
|--------|----------------------|--------|
| Fundamentos + BD | Fase 0–1 (Prisma, 10 tablas, 4 migraciones, seed idempotente) | ✅ |
| Autenticación | SPEC-001 / ADR-001 (JWT + bcrypt + RBAC + rate limit) | ✅ |
| Core CRUD | SPEC-002/003/004 (Projects, Suites, TestCases + RBAC) | ✅ |
| Motor de Ejecución | SPEC-005 / ADR-005 (cola async + runner Playwright) | ✅ |
| Resultados y Evidencias | SPEC-006 / ADR-006 | ✅ |
| Historial y Reportes | SPEC-007 / ADR-007 (persistencia BD+FS, comparación) | ✅ |
| GitHub Integration | SPEC-008 / ADR-008 (cliente + RBAC + workflow trigger) | ✅ |
| Dashboard | SPEC-009 / ADR-009 (Chart.js, KPIs, tendencia) | ✅ |
| DevOps y Cierre | Dockerfile prod, compose.prod, CI/CD, docs | ✅ |

---

## 3. Hechos Observados

> Fuentes: `docs/PROJECT_STATE.md`, `docs/SESSION.md`, `docs/CONTEXT.md`, `docs/QA_RESULTS.md`, `README.md` y `context-checkpoints/ITER-*.md`.

### 3.1 Métricas de calidad

| Métrica | Valor Observado |
|---------|-----------------|
| Tests L1–L3 (Vitest) | **238** passed (al cierre) |
| Tests L4 (Playwright) | **51** passed (al cierre) |
| **Total tests** | **289** (README) — cierre Fase 9 registra 236 (196 Vitest + 40 Playwright) |
| Cobertura Lines / Branches / Functions | **95.42 / 78.81 / 94.94 %** |
| Migraciones aplicadas | 4 |
| Tablas / CHECK / Índices | 10 / 11 / 39 |
| Typecheck / Build / Lint | ✅ / ✅ / ✅ (0 errores) |
| Imagen Docker prod | build + smoke test ✅ |

> **Nota de precisión:** existen dos cifras de total de tests en la documentación del proyecto (README: 289; PROJECT_STATE cierre Fase 9: 236). La discrepancia parece deberse a registros tomados en momentos distintos de la iteración final. Se reporta **ambas** para transparencia y como lección de métricas (ver §6).

### 3.2 Defectos documentados y lecciones

| Defecto | Fase | Causa raíz | Prevención |
|---------|------|------------|-----------|
| DEF-001/002 | Auth | carrera cookie httpOnly + `noValidate` bloqueaba submit | E2E repetidos de login; payloads ATK-INPUT |
| DEF-003 | Fase 3 | Next 15 pasa `params` `undefined` en rutas API no-dinámicas → 500 | E2E contra API real |
| DEF-004 | Fase 3 | seed no idempotente violaba UNIQUE | `prisma db seed` upsert en smoke |
| DEF-005 | Fase 6 | race condition flaky E2E (refetch queries) | `await refetchQueries` async |
| DEF-006 | Fase 6 | race condition cancelación de run (stub runner) | `STUB_RUNNER_DELAY_MS` configurable |
| DEF-007 | Fase 7 | rate limiter login saturaba suite E2E creciente → 429 | `LOGIN_RATE_LIMIT_MAX` por env |
| DEF-008 | Fase 7/8 | `Headers.get` con nombre inválido → TypeError | tests unitarios `GitHubClient` |
| DEF-009 | Fase 9 | `next lint` sin config interactivo colgaba el gate CI | `eslint.config.mjs` flat + `eslint .` |

### 3.3 Aplicación de Skills SAQI y checkpointing

| Skill SAQI | Aplicación en ZeroQA |
|------------|----------------------|
| `A-sdd` | Specs en `docs/specs/SPEC-XXX.md`; ACs Given/When/Then; spec = contrato |
| `A-project-architecture` | MVC estricto, ADRs, `ARCHITECTURE.md`, contratos de puertos, DI manual |
| `A-testing` | Pirámide L1–L4; TDD; AAA; builders; gates de cobertura |
| `A-qa-breaker` | Fase adversarial, taxonomía ATK-*, `QA_RESULTS.md`, gate de release |
| `A-context-manager` | Documentos vivos + **12 checkpoints** `context-checkpoints/ITER-*.md` |
| `C-database-design-sql` | Esquema normalizado, restricciones, índices |

- **Checkpointing real observado:** 12 archivos `ITER-002`…`ITER-012` evidencian el protocolo de checkpoint en la práctica.
- **Retroalimentación Learn → Skills real:** lecciones de DEF-005/006/007 se materializaron en **`A-testing` v2.1.0** (reglas A-TST-105/106, A-TST-208 y procedimiento 7.4). Ejemplo concreto del ciclo SAQI Fase 14.

---

## 4. Interpretaciones

1. **ZeroQA es el primer caso con aplicación explícita de Skills SAQI versionadas** (a diferencia de Open-RootERP, donde las "skills" eran convenciones de proyecto). Esto reduce parcialmente la amenaza "Skill Maturity Confound" identificada en SAQI-009.
2. **El protocolo de checkpointing (SAQI-013) es funcional en un proyecto SaaS full-stack y complejo** (9 fases, 10 tablas), validando su viabilidad más allá del ERP offline vanilla.
3. **La retroalimentación Learn → Skills es observable y trazable** (DEF-005/006/007 → `A-testing` v2.1.0), proporcionando una instancia real de la Fase 14.
4. **La mayoría de defectos (DEF-003…009) emergieron en pruebas E2E de integración real o adversarial**, consistente con la hipótesis H2 del caso original (el QA adversarial / integración real detecta defectos no cubiertos por tests unitarios).
5. **Stack distinto, mismas lecciones de flakiness**: los defectos de carrera (DEF-005/006) en un proyecto TS/Next reproducen patrones ya vistos, sugiriendo que existen clases de defectos independientes del stack.

---

## 5. Hipótesis Comprobables

| ID | Hipótesis | Método de Validación |
|----|-----------|----------------------|
| Z-H1 | Aplicar Skills SAQI versionadas produce menor "Skill Maturity Confound" que el caso original | Comparar trazabilidad de reglas de Skills a defectos entre ZeroQA y Open-RootERP |
| Z-H2 | El checkpointing (≥12 checkpoints/9 fases) reduce la pérdida de contexto entre sesiones | Medir "re-hydration time" y defectos por reintroducción de contexto |
| Z-H3 | Las clases de defectos de flakiness (carreras) son invariantes al stack tecnológico | Análisis cruzado de DEF-005/006 con defectos del caso original |
| Z-H4 | Un proyecto SaaS con RBAC completo y QA adversarial (Fase 7) exhibe menor defect escape en integraciones externas (GitHub) | Seguimiento de defectos P0/P1 tras despliegue en CI/CD real |

---

## 6. Lecciones para la Metodología SAQI

1. **Discrepancia de métricas (README vs PROJECT_STATE):** dos totales de tests distintos en la misma documentación. → **Lección SAQI-007:** definir una única fuente de verdad para métricas (p. ej. `QA_RESULTS.md` generado) y un comando `saqi metrics collect` que lo unifique.
2. **Validación de la política MVP-First:** (introducida en SAQI-005 v1.2 por ITER-001) es coherente con el patrón de fases de ZeroQA (por módulo con gates). → Fortalece la decisión v1.2.
3. **ADRs por módulo funcionan** (9 ADRs específicos), abriendo la pregunta de si SAQI debería recomendar un ADR obligatorio por fase/módulo como patrón.
4. **Gates de release no técnicos:** sugieren ampliar `saqi-gate-enforcer` más allá de lo técnico.

---

## 7. Limitaciones

- Es una instancia observacional adicional (N=2 acumulado con Open-RootERP), **no** un experimento controlado.
- Un solo autor/desarrollador (riesgo de *researcher bias* — SAQI-009).
- Las métricas de coste/tokens no están instrumentadas; se infieren de la duración de la sesión documentada.
- La discrepancia del total de tests no está resuelta en el proyecto origen.

---

## 8. Referencias Internas SAQI

- SAQI-001: Resumen Ejecutivo (definición)
- SAQI-003: Caso de Estudio original Open-RootERP (comparación N=1 a N=2)
- SAQI-005: Proceso (política MVP-First, RACI)
- SAQI-007: Marco de Métricas (discrepancia de métricas)
- SAQI-009: Amenazas a la Validez (Skill Maturity Confound)
- SAQI-013: Gestión de Contexto (checkpointing verificado)
- SAQI-014: Trabajo Futuro (réplicas)

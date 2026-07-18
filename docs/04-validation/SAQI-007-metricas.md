# SAQI-007: Marco de Atributos de Calidad y Métricas

**Versión:** 1.1
**Fecha:** 2026-07-17
**Estado:** Corregido con métricas reales de Open-RootERP
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Introducción

Este documento define el **sistema de métricas SAQI** para evaluar la efectividad de la metodología. Las métricas se organizan en tres dimensiones: **Calidad de Producto** (ISO/IEC 25010), **Calidad de Proceso** (efectividad SAQI), y **Efectividad de Skills** (retroalimentación Learn→Skills).

**Principio**: Métricas para **aprendizaje y mejora**, no solo control. Separar **métricas observadas** (caso de estudio) de **métricas objetivo** (umbrales para validación experimental).

---

## 2. Modelo de Calidad Base: ISO/IEC 25010 Adaptado

| Característica ISO 25010 | Sub-características Relevantes SAQI | Métricas SAQI Asociadas |
|--------------------------|-------------------------------------|-------------------------|
| **Funcionalidad** | Adecuación, Exactitud, Interoperabilidad | Requirement Coverage, Defect Escape Rate, API Contract Compliance |
| **Fiabilidad** | Madurez, Disponibilidad, Tolerancia a fallos, Recuperabilidad | MTBF, MTTF, Regression Rate, Crash Recovery Time, Data Integrity Score |
| **Usabilidad** | Reconocibilidad, Aprendizaje, Operabilidad, Accesibilidad | Accessibility Score (axe), Mobile Usability Score, Task Success Rate, Time to Task |
| **Eficiencia** | Comportamiento temporal, Utilización recursos | LCP, TBT, CLS (Lighthouse), Bundle Size, DB Query Time, Memory Leak Rate |
| **Mantenibilidad** | Modularidad, Reutilizabilidad, Analizabilidad, Modificabilidad, Testabilidad | Cyclomatic Complexity, Cognitive Complexity, Technical Debt Ratio, Code Coverage, Mutation Score, Coupling (Afferent/Efferent) |
| **Portabilidad** | Adaptabilidad, Instalabilidad, Coexistencia | Offline-First Score, Cross-browser Score, PWA Installability, Dependency Freshness |
| **Seguridad** | Confidencialidad, Integridad, No repudio, Responsabilidad, Autenticidad | OWASP Top 10 Coverage, SAST Findings, Dependency Vulnerabilities, AuthZ Bypass Attempts Blocked |

---

## 3. Catálogo de Métricas SAQI

### 3.1 Métricas de Calidad de Producto (Product Quality Metrics)

| ID | Métrica | Fórmula / Definición | Umbral Objetivo SAQI | Open-RootERP Observado |
|----|---------|---------------------|----------------------|------------------------|
| **PQ-01** | **Defect Escape Rate (DER)** | Defectos en producción / Total defectos (pre + post release) | < 5% | N/A (no release producción) |
| **PQ-02** | **Regression Rate (RR)** | Defectos REG / Total fixes en iteración | < 2% | 4 regresiones / 157 fixes = **2.5%** |
| **PQ-03** | **Test Pass Rate (TPR)** | Tests passing / Total tests ejecutados | 100% (gate) | **100%** (524+ tests) |
| **PQ-04** | **Mutation Score (MS)** | Mutantes killed / Total mutantes (custom mutation testing) | ≥ 70% | **72%** (1,247 mutantes, 898 killed) |
| **PQ-05** | **Code Coverage (Lines/Branches/Functions)** | Standard coverage | L≥80% / B≥75% / F≥80% | **84.3% / 78.9% / 81.2%** |
| **PQ-06** | **OWASP Top 10 Coverage** | Categorías OWASP con tests adversariales / 10 | 100% (10/10) | **100%** (Phase 4 Security: 10 tests) |
| **PQ-07** | **Accessibility Score** | axe-core violations (critical+serious) | 0 critical, 0 serious | **0 critical, 0 serious** (Phase 5) |
| **PQ-08** | **Mobile Usability Score** | Viewports passing / Viewports tested | 100% (375×667, 414×896, 768×1024) | **100%** (Phase 5: R1, R2) |
| **PQ-09** | **Performance Budget Compliance** | Lighthouse budgets met (LCP, TBT, CLS) | LCP<2.5s, TBT<200ms, CLS<0.1 | Presupuestos definados, no métricas reportadas |
| **PQ-10** | **Cyclomatic Complexity (Avg/Max)** | Promedio y máximo por función | Avg ≤ 5, Max ≤ 15 | **Avg 3.1, Max 12** (PROJECT_STATE.md) |
| **PQ-11** | **Cognitive Complexity (Avg/Max)** | Promedio y máximo por función | Avg ≤ 7, Max ≤ 20 | **Avg 4.7, Max 18** |
| **PQ-12** | **Technical Debt Ratio** | Remediation cost / Development cost | < 5% | **2.1 días** (SonarCloud estimado) |
| **PQ-13** | **Duplication Rate** | Líneas duplicadas / Total líneas | < 3% | **2.3%** |
| **PQ-14** | **Coupling (Afferent/Efferent)** | Acoplamiento entre módulos | Efferent ≤ 10 por módulo | No medido explícitamente |

### 3.2 Métricas de Calidad de Proceso (Process Quality Metrics)

| ID | Métrica | Fórmula / Definición | Umbral Objetivo SAQI | Open-RootERP Observado |
|----|---------|---------------------|----------------------|------------------------|
| **PR-01** | **Iteration Cycle Time (ICT)** | Tiempo desde Plan (Fase 1) a Verify (Fase 11) | < 2 semanas/iteración | ~2 semanas/iteración (6 iteraciones ~12 semanas) |
| **PR-02** | **Time to Fix (TTF / MTTF)** | Tiempo promedio desde detección a fix verificado | P0 < 4h, P1 < 24h | **3.2 horas promedio** |
| **PR-03** | **Defect Detection Rate by Phase** | Defectos detectados en fase / Total defectos | Fase 7 (Adversarial) ≥ 25% | **27.4%** (43/157 en Fase 7) |
| **PR-04** | **Adversarial Detection Rate** | Defectos solo en adversarial / Total defectos | ≥ 15% | **12.1%** (19/157 solo adversarial) |
| **PR-05** | **Prompt Iterations per Task** | # iteraciones prompt-refinamiento por tarea completada | < 3 | No medido (chat asistido, no agente loop) |
| **PR-06** | **Human Interventions per Iteration** | # decisiones/intervenciones humanas / iteración | < 10 | **7.8 promedio** |
| **PR-07** | **Changes Reverted** | Commits revertidos / Total commits | < 2% | **6 reverts** (historial git) |
| **PR-08** | **Requirement Coverage** | ACs con tests / Total ACs | 100% | 100% (DoD gate) |
| **PR-09** | **Functional Coverage** | Módulos funcionales con E2E / Total módulos | 100% | **14/14 = 100%** |

### 3.3 Métricas de Efectividad de Skills (Skill Effectiveness Metrics)

| ID | Métrica | Fórmula / Definición | Umbral Objetivo SAQI | Open-RootERP Observado |
|----|---------|---------------------|----------------------|------------------------|
| **SK-01** | **Skills Applied per Iteration** | Skills activas / Iteración | Nivel A: 100%, Nivel B: según stack | **11.3 promedio** (14 Nivel A, 7 Nivel B aplicables) |
| **SK-02** | **Skills Modified per Iteration** | Skills con version bump / Iteración | ≥ 1 (mejora continua) | **1.3 promedio** (8 skills modificadas en 6 iteraciones) |
| **SK-03** | **New Rules Added per Iteration** | Reglas/patrones/plantillas nuevas en Skills / Iteración | ≥ 2 | **4.2 promedio** (~25 reglas en 6 iteraciones) |
| **SK-04** | **Skill Reuse Rate** | Proyectos usando skill / Proyectos aplicables | > 70% (proyectos futuros) | N/A (primer caso de estudio) |
| **SK-05** | **Defect Prevention Attribution** | Defectos tipo X prevenidos tras regla Y en Skill / Total defectos tipo X históricos | > 80% para reglas MUST | Datos preliminares: validación Zod previno 3 P1 en I3-I6 |
| **SK-06** | **Skill False Positive Rate** | Alertas skill incorrectas / Total alertas skill | < 10% | No medido (skills = convenciones proyecto, no automatizadas) |
| **SK-07** | **Skill Version Bumps per Iteration** | Major + Minor + Patch bumps / Iteración | ≥ 1 | **1.3 promedio** |
| **SK-08** | **Time to Skill Fix** | Tiempo desde gap detectado → Skill patcheada | < 2 iteraciones | ~1 iteración (convenciones Context.md actualizadas) |

### 3.4 Métricas de Coste y Eficiencia (Cost & Efficiency Metrics)

| ID | Métrica | Fórmula / Definición | Open-RootERP Observado (Estimado) |
|----|---------|---------------------|-----------------------------------|
| **CE-01** | **Tokens Consumed per Iteration** | Tokens LLM entrada+salida / Iteración | **~2.4M tokens/iteración** (estimado OpenCode) |
| **CE-02** | **Cost per Iteration** | $ tokens + tiempo humano / Iteración | No calculado exactamente |
| **CE-03** | **Cost per Defect Found** | Coste iteración / Defectos encontrados | No calculado exactamente |
| **CE-04** | **Cost per Defect Fixed** | Coste fix (tiempo humano+agente) / Defecto | ~3.2h × coste hora / fix |
| **CE-05** | **Context Refresh Rate** | Checkpoints / Iteración | **5.7 checkpoints/iteración** (34 total / 6 iteraciones) |
| **CE-06** | **Context Staleness Index** | Decisiones contradichas / Sesión | **0** (0 pérdidas contexto reportadas) |

### 3.5 Métricas de Aprendizaje (Learning Metrics)

| ID | Métrica | Fórmula / Definición | Open-RootERP Observado |
|----|---------|---------------------|------------------------|
| **LR-01** | **Recurring Defect Rate** | Defectos mismo tipo en iteración N vs N-1 | **"Validación entrada": 8 (I2) → 1 (I6) = -87.5%** |
| **LR-02** | **New Skill Rules per Iteration** | Ver SK-03 | **4.2 promedio** |
| **LR-03** | **Skill Version Bumps per Iteration** | Ver SK-07 | **1.3 promedio** |
| **LR-04** | **Context Drift Detected** | Inconsistencias entre docs vivos / Checkpoint | **0** (docs sincronizados) |

---

## 4. Dashboard SAQI (Vista Consolidada por Iteración)

```markdown
# ITERATION DASHBOARD — ITER-006 (Open-RootERP)

## Product Quality
| Metric | Target | Actual | Status |
|--------|--------|--------|--------|
| Test Pass Rate | 100% | 100% | ✅ |
| Coverage (L/B/F) | 80/75/80 | 84.3/78.9/81.2 | ✅ |
| Mutation Score | ≥70% | 72% | ✅ |
| DER | <5% | N/A | — |
| Regression Rate | <2% | 2.5% | ⚠️ |
| OWASP Coverage | 100% | 100% | ✅ |
| A11y (critical/serious) | 0/0 | 0/0 | ✅ |
| Cyclomatic (avg/max) | ≤5/≤15 | 3.1/12 | ✅ |

## Process Quality
| Metric | Target | Actual | Status |
|--------|--------|--------|--------|
| Cycle Time | <2 wks | ~2 wks | ✅ |
| MTTF | P0<4h, P1<24h | 3.2h avg | ✅ |
| Adversarial Detection | ≥25% | 27.4% | ✅ |
| Adversarial Unique | ≥15% | 12.1% | ⚠️ |
| Human Interventions | <10 | 7.8 | ✅ |
| Reverts | <2% | 6 total | ✅ |

## Skill Effectiveness
| Metric | Target | Actual | Status |
|--------|--------|--------|--------|
| Skills Applied | A:100% | 6/6 (A) | ✅ |
| Skills Modified | ≥1 | 1 (A-secure-coding) | ✅ |
| New Rules | ≥2 | 4 | ✅ |
| Recurring Defect Reduction | >50% | -87.5% (input validation) | ✅ |
```

---

## 5. Recolección Automatizada vs Manual

| Métrica | Automatización | Herramienta | Frecuencia |
|---------|----------------|-------------|------------|
| Coverage, Mutation, Test Count | ✅ Completa | Custom runner (`tests/run-all.js`), custom mutation | Cada Fase 5, 6, 10 |
| Lighthouse, Axe, Playwright | ✅ Completa | lighthouse-ci, playwright+axe | Cada Fase 6, 7 |
| SAST (semgrep), SCA (npm audit) | ✅ Completa | semgrep, npm audit, snyk | Cada Fase 7 (ATK-SEC) |
| Cyclomatic, Cognitive, Duplication | ✅ Completa | eslint, sonarjs, jscpd | Cada Fase 4, 9 |
| Tokens Consumidos | ⚠️ Parcial | OpenCode telemetry / estimación | Por sesión |
| Tiempo Humano | ❌ Manual | Time tracking | Por tarea |
| Defect Classification | ⚠️ Semi-auto | QA_RESULTS.md + tags | Por defecto |
| Skill Effectiveness | ❌ Manual | Análisis Fase 14 | Por iteración |

**Meta SAQI v2**: Automatizar ≥ 90% de métricas via `saqi metrics collect` command.

---

## 6. Métricas de Validación Experimental (Para SAQI-008)

Variables dependientes primarias para comparación Grupo A (ad-hoc) vs Grupo B (SAQI):

| Variable | Tipo | Medición | Análisis Estadístico |
|----------|------|----------|---------------------|
| **Defect Escape Rate** | Continua (ratio) | Defectos prod / Total defectos | Mann-Whitney U, Cliff's δ, IC 95% |
| **Regression Rate** | Continua (ratio) | REG fixes / Total fixes | Mann-Whitney U |
| **Cycle Time per Iteration** | Continua (tiempo) | Horas Plan→Verify | Mann-Whitney U |
| **Human Intervention Count** | Discreta (cuenta) | Intervenciones/iteración | Mann-Whitney U |
| **Cost per Iteration** | Continua ($) | Tokens + horas humanas | Mann-Whitney U |
| **Maintainability Index** | Continua (score) | Post-iteración (MI, complejidad, deuda) | Mann-Whitney U |
| **Adversarial Defects Found** | Discreta (cuenta) | Defectos solo Fase 7 | Mann-Whitney U |
| **Prompt Sensitivity Index** | Continua (ratio) | Var(calidad) / Var(prompt) | Comparación varianzas (Levene) |

**Tamaño de efecto mínimo de interés práctico**: Cliff's δ ≥ 0.33 (medio)
**Potencia objetivo**: 0.8
**Nivel significancia**: α = 0.05 (bilateral)

---

## 7. Referencias Internas

- SAQI-001: Resumen Ejecutivo (Tabla métricas resumen)
- SAQI-003: Caso Estudio Open-RootERP (Métricas observadas reales)
- SAQI-004: Marco QA (Métricas por fase)
- SAQI-005: Proceso SAQI (Métricas por fase)
- SAQI-008: Protocolo Experimental (Diseño validación)
- SAQI-019: Apéndice C - Plantillas Recolección Métricas

---

## 8. Referencias Externas

1. ISO/IEC 25010:2011 — Systems and software Quality Requirements and Evaluation (SQuaRE)
2. McCall, J. A., Richards, P. K., & Walters, G. F. (1977). *Factors in Software Quality*. NTIS.
3. Forsgren, N., Humble, J., & Kim, G. (2018). *Accelerate: The Science of Lean Software and DevOps*. IT Revolution Press. (DORA Metrics)
4. Jia, Y. & Harman, M. (2011). "An Analysis and Survey of the Development of Mutation Testing." *IEEE TSE*.
5. OWASP Foundation. (2023). *OWASP Top 10* / *ASVS 4.0.3*.
6. NIST. (2022). *SP 800-218: Secure Software Development Framework (SSDF)*.
7. Kitchenham, B., et al. (2004). "Evidence-Based Software Engineering." *ICSE*.
# SAQI-009: Amenazas a la Validez y Limitaciones

**Versión:** 1.1
**Fecha:** 2026-07-17
**Estado:** Corregido con análisis realista del caso de estudio
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Introducción

Este documento analiza sistemáticamente las **amenazas a la validez** (internal, external, construct, statistical conclusion) y **limitaciones inherentes** de la metodología SAQI y su caso de estudio inicial Open-RootERP. El objetivo es **transparencia científica**: identificar qué se sabe, qué no se sabe, y qué no se puede afirmar.

**Principio rector**: **"No afirmar causalidad sin experimento controlado."** El caso Open-RootERP es **observacional (N=1)**, no experimental.

---

## 2. Amenazas a la Validez Interna (Causalidad)

| Amenaza | Descripción | Impacto en SAQI | Mitigación Parcial | Residual |
|---------|-------------|-----------------|-------------------|----------|
| **Single Case Study (N=1)** | Open-RootERP es el único caso; no hay grupo control | **CRÍTICA**: Imposible inferir causalidad SAQI → resultados | Replicación en ≥3 proyectos (SAQI-021); Protocolo RCT (SAQI-008) | **Alta** — Requiere experimento |
| **Researcher Bias / Hawthorne** | Un solo investigador/desarrollador conoce hipótesis; puede influir resultados consciente/inconscientemente | **ALTA**: Métricas pueden inflarse, defectos subreportarse, gates pasados laxamente | Métricas automatizadas (no auto-reportes); Analista ciego en validación futura; Replicación independiente | **Media** — Parcialmente mitigable |
| **Confounding Variables** | Múltiples factores cambian simultáneamente: Skills, arquitectura, testing, contexto, debugging. ¿Cuál causó qué? | **ALTA**: No se puede aislar efecto de cada componente SAQI | Diseño factorial en experimento futuro (SAQI-008); Análisis de mediación | **Alta** — Requiere diseño experimental |
| **Skill Maturity Confound** | Skills usadas en Open-RootERP eran "convenciones de proyecto" (Context.md), no Skills SAQI versionadas/gobernadas. Madurez variable. | **MEDIA**: Resultados atribuidos a "Skills" pueden ser por convicciones personales | Skills SAQI v1.0 baseline en experimento; Medir madurez skill como covariable | **Media** |
| **LLM Model Variability** | Modelo LLM único (OpenCode default); resultados dependen de versión, temperatura, prompt interno | **MEDIA**: No generalizable a otros modelos (GPT-4o, Claude, Llama local) | Experimentar con múltiples modelos; Reportar versión exacta | **Media** |
| **Publication Bias / Selective Reporting** | Solo se documenta éxito (v1.0.0 release); iteraciones fallidas o abandonadas no visibles | **BAJA-MEDIA**: TODO.md muestra 371 líneas all ✅; pero path no documentado | Documentar decisiones descartadas, spikes fallidos, dead ends en PROJECT_STATE.md | **Baja** |
| **Testing Effect (Pre-test sensitization)** | Ejecutar tests adversariales (Fase 7) puede "enseñar" al agente/desarrollador patrones para iteraciones futuras | **BAJA**: Efecto deseado (aprendizaje), pero confunde "mejora real" vs "memorización tests" | Rotar/ocultar test suite adversarial "producción" (SAQI-008); Medir generalización | **Baja** |

---

## 3. Amenazas a la Validez Externa (Generalizabilidad)

| Amenaza | Descripción | Generalización Limitada A |
|---------|-------------|---------------------------|
| **Stack Tecnológico Único** | Vanilla JS ES Modules + Dexie/IndexedDB + Playwright + Chart.js. **NO**: TypeScript, React/Vue, SQL/PostgreSQL, Backend, Mobile nativo, Rust, Go, Python | Proyectos con stack idéntico o muy similar |
| **Dominio Único** | ERP offline-first para pequeños negocios (14 módulos estándar). **NO**: SaaS online, tiempo real, ML/AI, alta concurrencia, microservicios, embedded | ERP/CRM offline ligero similares |
| **Tamaño de Proyecto** | ~42K LOC, 14 módulos, 524 tests, 10 semanas. **NO**: Proyectos grandes (100K+ LOC), equipos 5+, legacy/brownfield, mantenimiento 5+ años | Proyectos greenfield medianos (30-60K LOC) |
| **Equipo de 1 Persona** | Un investigador/desarrollador. **NO**: Equipos distribuidos, múltiples desarrolladores, code reviews reales, pair programming, onboarding | Desarrollo individual o pair con IA |
| **Modelo LLM Único** | OpenCode (modelo por defecto, no especificado públicamente). **NO**: GPT-4o, Claude 3.5 Sonnet, Llama 3 70B, modelos locales 7B cuantizados | Cualquier modelo — requiere validación |
| **Duración Limitada** | ~10 semanas desarrollo + testing. **NO**: Efectos largo plazo (deuda técnica acumulada, rotación equipo, evolución requisitos 12+ meses) | Proyectos cortos/medianos |
| **Greenfield Only** | Proyecto desde cero. **NO**: Migración legacy, refactoring masivo, brownfield, technical debt rescue | Solo greenfield |
| **Cultura/Contexto** | Investigador senior con experiencia en arquitectura, testing, seguridad, offline-first. **NO**: Equipos junior, sin expertise arquitectura/testing | Contextos con expertise técnico senior |

---

## 4. Amenazas a la Validez de Constructo (Medición)

| Amenaza | Variable Afectada | Descripción |
|---------|-------------------|-------------|
| **Defect Escape Rate (DER)** | DER | No hay "producción real" en Open-RootERP. DER = 0 por definición (no release producción). Métrica teórica. |
| **Adversarial Detection Rate** | ADR | Taxonomía ATK-* definida por SAQI; no estándar de la industria. Cobertura ATK-* ≠ cobertura real de amenazas. |
| **Skill Effectiveness** | SK-01 a SK-08 | "Skills" en Open-RootERP = convenciones Context.md, no Skills SAQI versionadas/gobernadas. Métricas proxy. |
| **Context Staleness Index** | CE-06 | Métrica proxy (decisiones contradichas/sesión). No mide pérdida real de contexto semántico en LLM. |
| **Prompt Sensitivity Index** | PR-05 | No medido en Open-RootERP (chat asistido, no agente loop con prompts variados). |
| **Cost per Iteration** | CE-02 | Tokens estimados (~2.4M/iter), no medidos instrumentalmente. Coste humano no trackeado sistemáticamente. |
| **Maintainability Index** | PQ-14 | Métricas estáticas (complejidad, acoplamiento) ≠ mantenibilidad real (tiempo onboarding, fix bugs futuros). |
| **Regression Rate** | PR-02 | 4 regresiones / 157 fixes = 2.5%. Pero "fix" incluye mejoras, no solo correcciones defectos. Denominador inflado. |

---

## 5. Amenazas a la Validez de Conclusión Estadística

| Amenaza | Descripción | Relevancia Actual |
|---------|-------------|-------------------|
| **Bajo Poder Estadístico** | N=1 caso, n=6 iteraciones, n=157 defectos. Imposible tests inferenciales. | **Total** — Solo estadística descriptiva válida |
| **Violación Normalidad** | Métricas (defectos, tiempo, cobertura) no normales; tests paramétricos inválidos. | N/A — Solo descriptivo |
| **Múltiples Comparaciones** | 20+ métricas reportadas; mayor probabilidad hallazgos espurios. | N/A — No hay tests de significancia |
| **Tamaño Efecto No Reportado** | Diferencias observadas (ej. 87.5% reducción defectos validación) sin IC ni tamaño efecto estandarizado. | **Alta** — Reportar Cliff's δ, IC bootstrap en experimento |
| **Datos Faltantes No Aleatorios** | Métricas de coste/tokens no recopiladas sistemáticamente; sesgo hacia métricas fáciles (tests pass). | **Media** — Instrumentar en v2 |

---

## 6. Limitaciones Inherentes a SAQI (Metodología)

| Categoría | Limitación | Descripción | Impacto |
|-----------|------------|-------------|---------|
| **Dependencia LLM** | Calidad variable entre modelos | SAQI asume agente competente (tool use, planning, CoT). Modelos 3B-7B locales pueden fallar en fases complejas (Adversarial, Debugging). | **Alto** para modelos pequeños; **Bajo** para frontier models |
| | Alucinaciones | Agente puede inventar APIs, omitir validaciones, generar tests falsos positivos. Mitigado por gates (lint, typecheck, tests), pero no eliminado. | **Medio** |
| | Coste tokens / Latencia | Fases 4, 5, 6, 7, 8, 10 consumen tokens masivos. Proyectos grandes → coste $$$ significativo. | **Alto** en proyectos grandes / modelos caros |
| **Dependencia Agente** | Capacidad tool use imperfecta | Agente puede fallar en: glob/grep precisos, shell commands complejos, browser automation flaky, git merges. Requiere supervisión humana (H4, H6, H8). | **Medio** |
| | Loop recovery | Agente puede entrar en loops (reintentar acción fallida sin progreso). Requiere timeout + intervención humana. | **Medio** |
| **Skills** | Sobreajuste (Overfitting) | Skills muy específicas a stack/dominio pierden reutilización. Skills muy genéricas pierden utilidad. Balance difícil. | **Medio** |
| | Mantenimiento Skills | 17+ Skills requieren gobernanza continua. Skill obsoleta → propaga errores. Governance overhead real. | **Alto** en organizaciones sin expertise |
| | Deuda de Contexto | 4 docs vivos + SESSION.md + checkpoints = mantenimiento continuo. Si no se actualizan → **Context Staleness** (decisiones contradichas). | **Medio-Alto** |
| | Complejidad instrucciones | System prompt crece: 4 docs + skills + threat models + protocolos. Ventana contexto se llena rápido → más checkpoints → overhead. | **Medio** |
| **QA Adversarial** | Falsos positivos | ATK-* generan ruido (ej. chaos testing crashea app por diseño). Requiere triaje humano (H5). | **Medio** |
| | Coste tiempo | Fase 7 completa = horas (stress, chaos, security scans). Gate de release lento. | **Medio** |
| | Creatividad adversarial | Agente en modo atacante limitado por training data. Humanos mejores en "business logic bypass". Requiere H5 experto. | **Medio** |
| **Riesgo IA genera código Y tests** | Mismos puntos ciegos | Agente que genera código (Fase 4) también genera tests (Fase 4-5). Puede tener mismos sesgos/omisiones. **Mitigación**: QA Adversarial (Fase 7) por agente en modo atacante + Humano QA Lead. | **Alto** — Requiere separar roles (modo builder vs adversarial) |
| **General** | Curva de adopción | Requiere: conocimiento arquitectura, testing, seguridad, debugging, prompt engineering, governance. No "plug & play". | **Alto** para equipos sin expertise |
| | Tooling inmaduro | No existe "SAQI CLI" que automatice: checkpointing, skill loading, metric collection, gate enforcement. Actual: manual/semi-auto. | **Alto** — Requiere inversión tooling (SAQI-014) |

---

## 7. Análisis de Riesgo: Open-RootERP como Caso Único (N=1)

### Matriz de Riesgo de Generalización

| Afirmación | Soporte en Open-RootERP | Nivel de Evidencia | ¿Generalizable? |
|------------|------------------------|-------------------|-----------------|
| "SAQI produce código de alta calidad" | 524+ tests, 100% pass, 0 P0 en release | **Observacional fuerte** | **NO** — N=1, sin control |
| "QA Adversarial encuentra defectos críticos que unit tests no" | 4 P0 + 8 P1 solo en Fase 7 (27.4% total) | **Observacional fuerte** | **Hipótesis (H2)** — Requiere RCT |
| "Skills reducen variabilidad" | Convenciones Context.md → 300/40 líneas, 0 innerHTML, patrones uniformes | **Observacional correlacional** | **Hipótesis (H2, H6)** — Requiere RCT |
| "Contexto persistente evita pérdida decisiones" | 0 decisiones contradichas, 12 ADRs mantenidos, 34 checkpoints | **Observacional correlacional** | **Hipótesis (H3)** — Requiere cuasi-exp |
| "Learn→Skills reduce defectos recurrentes" | Validación entrada: 8 (I2) → 1 (I6) = -87.5% | **Serie temporal N=1** | **Hipótesis (H4)** — Requiere longitudinal |
| "SAQI es más eficiente que ad-hoc" | No medido (sin grupo control, sin tokens precisos, sin tiempo humano trackeado) | **Sin evidencia** | **Hipótesis (H1, H5)** — Requiere RCT |

---

## 8. Declaración de No-Afirmaciones (What We Do NOT Claim)

Basado en la evidencia actual (Open-RootERP N=1), **SAQI NO AFIRMA**:

1. ❌ **Superioridad** sobre desarrollo tradicional, Agile, TDD, DevOps, u otros enfoques asistidos por IA.
2. ❌ **Reducción probada** de defectos en producción (Defect Escape Rate).
3. ❌ **Aceleración probada** de velocidad de desarrollo (Cycle Time).
4. ❌ **Reducción probada** de coste total de desarrollo.
5. ❌ **Generalizabilidad** a otros stacks, dominios, tamaños de equipo, modelos LLM.
6. ❌ **Madurez** de las Skills SAQI (actualmente 17 Skills base OpenCode; gobernanza SAQI-003 es propuesta).
7. ❌ **Automatización completa** (requiere intervención humana en 8 gates críticos).
8. ❌ **Aplicabilidad inmediata** sin expertise en arquitectura, testing, seguridad, debugging.

**LO QUE SÍ AFIRMAMOS (con evidencia observacional)**:
- ✅ Open-RootERP **fue desarrollado** siguiendo **prácticas alineadas con principios SAQI** (convenciones obligatorias, QA adversarial integrado, docs vivos, debugging estructurado, retroalimentación a convenciones).
- ✅ **Se observaron** métricas de calidad altas (524+ tests, 100% pass, 15+ defectos críticos/altos corregidos, 0 regresiones en release).
- ✅ **Se formularon 7 hipótesis comprobables** (H1-H7) y **un protocolo experimental replicable** (SAQI-008) para validarlas.
- ✅ **Se documentó transparentemente** el caso de estudio separando **Hechos / Interpretaciones / Hipótesis**.

---

## 9. Plan de Mitigación a Futuro (Roadmap Validación)

| Prioridad | Acción | Documento SAQI | Objetivo |
|-----------|--------|----------------|----------|
| **P0** | Replicar SAQI en **3+ proyectos distintos** (stack, dominio, equipo) | SAQI-021 | Validez externa inicial |
| **P0** | Ejecutar **RCT piloto** (Grupo A ad-hoc vs Grupo B SAQI) con n≥10 tareas | SAQI-008 | Validez interna causal |
| **P1** | Desarrollar **SAQI CLI/Tooling**: checkpointing auto, skill loader, metric collector, gate enforcer | SAQI-014 | Reducir overhead, aumentar adherencia, instrumentar métricas |
| **P1** | Probar con **múltiples modelos LLM** (GPT-4o, Claude 3.5, Llama 3 70B, Qwen 7B local) | SAQI-014 | Robustez a modelo |
| **P2** | Estudio **longitudinal 6-12 meses**: deuda técnica, onboarding, rotación, evolución requisitos | SAQI-014 | Efectos largo plazo |
| **P2** | Caso **Brownfield**: migrar legacy a SAQI | SAQI-021 | Generalizabilidad beyond greenfield |
| **P2** | Análisis **coste-beneficio real**: tokens instrumentados + time tracking humano | SAQI-007, SAQI-008 | Viabilidad económica |

---

## 10. Referencias Internas

- SAQI-001: Resumen Ejecutivo (Declaración rigor)
- SAQI-003: Caso Estudio Open-RootERP (Limitaciones sección 8)
- SAQI-007: Marco Métricas (Definiciones operacionales)
- SAQI-008: Protocolo Experimental (Diseño RCT, amenazas validez)
- SAQI-014: Trabajo Futuro (Roadmap mitigación)
- SAQI-021: Apéndice E - Modelos de Amenaza (Para ATK-SEC validity)

---

## 11. Referencias Externas

1. Wohlin, C., et al. (2012). *Experimentation in Software Engineering*. Springer. (Cap. 5: Validity Threats)
2. Kitchenham, B., et al. (2002). "Preliminary guidelines for empirical research in software engineering." *IEEE TSE*.
3. Shadish, W. R., Cook, T. D., & Campbell, D. T. (2002). *Experimental and Quasi-Experimental Designs for Generalized Causal Inference*. Houghton Mifflin.
4. Easterbrook, S., et al. (2008). "Selecting empirical methods for software engineering research." *Guide to Advanced Empirical Software Engineering*.
5. Runeson, P., & Höst, M. (2009). "Guidelines for conducting and reporting case study research in software engineering." *Empirical Software Engineering*.
6. Mohagheghi, P., & Conradi, R. (2007). "An empirical investigation of software reuse benefits in a large telecom product." *ACM TOSEM*.
7. Basili, V. R., et al. (1999). "The Goal/Question/Metric paradigm." *Encyclopedia of Software Engineering*.
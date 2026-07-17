# SAQI-014: Trabajo Futuro y Hoja de Ruta de Investigación

**Versión:** 1.0
**Fecha:** 2026-07-17
**Estado:** Borrador inicial
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Visión General

SAQI es una **metodología experimental** en etapa temprana. Este documento traza la hoja de ruta para evolucionar desde un **caso de estudio único (N=1)** hacia un **framework de ingeniería de software agéntica validado empíricamente**, adoptable por la industria.

**Objetivo a 3 años**: SAQI v2.0 con validación experimental robusta, tooling maduro, y adopción temprana en 3+ organizaciones.

---

## 2. Prioridad Inmediata (0-6 meses): Validación Empírica

### 2.1 Réplica Múltiple de Casos de Estudio (P0)
| Acción | Detalle | Entregable |
|--------|---------|------------|
| **Proyecto 2** | Aplicar SAQI a **SaaS online** (React/TS + Node/Express + PostgreSQL + Playwright) | Caso estudio SAQI-CASE-002 |
| **Proyecto 3** | Aplicar SAQI a **Mobile offline-first** (React Native + SQLite/Expo + Detox) | Caso estudio SAQI-CASE-003 |
| **Proyecto 4** | Aplicar SAQI a **CLI/Backend** (Go + SQLite + gRPC + k6) | Caso estudio SAQI-CASE-004 |
| **Análisis cruzado** | Comparar métricas, adaptaciones Skills, gaps por stack | Paper: "SAQI Across Stacks" |

**Criterio de éxito**: ≥ 3 proyectos completados con métricas SAQI-007 comparables, Skills reutilizadas ≥ 70%.

### 2.2 RCT Piloto (P0)
| Acción | Detalle | Entregable |
|--------|---------|------------|
| **Ejecutar SAQI-008** | n=10/grupo (30 total), 10 iteraciones, stack JS/Dexie | Datos experimentales brutos |
| **Análisis estadístico** | Plan pre-registrado, análisis ciego | Paper: "RCT of SAQI vs Ad-hoc AI Development" |
| **Publicar replication package** | Zenodo/Figshare con DOI | Open science compliance |

### 2.3 Tooling Mínimo Viable (MVP) (P0)
| Herramienta | Función | Tecnología |
|-------------|---------|------------|
| `saqi-checkpoint` | Monitoreo tokens, checkpoint auto 70/80%, re-hidratación | Node.js + OpenCode hooks |
| `saqi-skill-loader` | Carga skills por fase, validación conflictos, version lock | Node.js + YAML/JSON schema |
| `saqi-metrics-collector` | Auto-recolección: coverage, mutation, test counts, tokens, time | Vitest/Playwright reporters + OpenCode telemetry |
| `saqi-gate-enforcer` | Gates H1-H8 en CI/CD (GitHub Actions/GitLab CI) | YAML pipeline + CLI |

**Stack**: TypeScript, Node.js, Commander.js, OpenCode SDK (cuando exista) o wrapper CLI.

---

## 3. Prioridad Alta (6-18 meses): Maduración Metodológica

### 3.1 Gobernanza de Skills Comunitaria
| Iniciativa | Descripción |
|------------|-------------|
| **SAQI Skill Registry** | Registro público (GitHub/GitLab) con Skills versionadas, valoradas, con evidencia de uso |
| **Skill Review Process** | Proceso tipo RFC para nuevas Skills: propuesta → revisión par → validación empírica → publicación |
| **Skill Deprecation Policy** | Ciclo de vida oficial: Active → Maintenance → Deprecated → Archived |
| **Cross-stack Skill Templates** | Parametrización: `B-database-design-{sql,offline,graph}`, `B-auth-{jwt,oauth,session}` |

### 3.2 Integración CI/CD Nativa
| Gate | Implementación |
|------|----------------|
| **Skill Validation Gate** | `saqi-skill-loader validate --phase 3` en PR |
| **Adversarial Test Gate** | `saqi-gate-enforcer phase7` — bloquea merge si P0/P1 abiertos |
| **Context Freshness Gate** | `saqi-checkpoint staleness --max 0` — falla si decisiones contradichas |
| **Metrics Dashboard** | Grafana/Prometheus + SAQI-007 dashboard por iteración/proyecto |

### 3.3 Soporte Multi-Modelo LLM
| Capacidad | Descripción |
|-----------|-------------|
| **Model Routing** | Tareas simples (lint, format) → modelo pequeño (7B local); tareas complejas (arch, security, adversarial) → frontier model (GPT-4o, Claude 3.5) |
| **Cost Optimization** | Presupuesto tokens/iteración; auto-switch si excede threshold |
| **Local-First Option** | SAQI operable 100% con modelos locales (Llama 3, Qwen, Phi) via Ollama/LM Studio |
| **Capability Matrix** | Matriz: Skill × Modelo → Nivel soporte (Full/Partial/Unsupported) |

### 3.4 Formalización de Skills (DSL)
- **SAQI Skill DSL**: Especificación formal (YAML/JSON Schema + Rego/OPA para rules)
- **Verificación estática**: Consistencia entre rules, anti-patterns, templates
- **Code generation**: De Skill DSL → ESLint rules, Vitest templates, scaffolding CLI

---

## 4. Prioridad Media (18-36 meses): Escalamiento y Ecosistema

### 4.1 SAQI para Equipos (Multi-Developer)
| Desafío | Enfoque |
|---------|---------|
| **Code review asistido por IA** | Agent mode "Reviewer" con Skills A-secure-coding, A-coding-standards |
| **Knowledge sharing** | Skills como "team memory" — onboarding nuevo dev = cargar Skills del proyecto |
| **Concurrent iterations** | Múltiples agentes en ramas paralelas + merge gate con regresión cross-branch |
| **Distributed context** | Sync de 4 docs vivos via Git + conflict resolution semántico |

### 4.2 SAQI para Mantenimiento / Brownfield
| Actividad | Adaptación SAQI |
|-----------|-----------------|
| **Legacy assessment** | Fase 0: "Archaeology" — agente analiza código existente → genera ARCHITECTURE.md + CONTEXT.md + debt map |
| **Strangler Fig pattern** | Skills para migración incremental: `B-migration-strangler-fig`, `B-legacy-wrapper` |
| **Regression-heavy** | Fase 7 (Break) continua en pipeline; mutation testing obligatorio en legacy touch points |

### 4.3 Métricas Avanzadas y Benchmarks
- **SAQI Benchmark Suite**: 50 tareas estandarizadas (auth, CRUD, reporting, sync, offline, security) con production test suites ocultas
- **Leaderboard público**: Equipos/modelos compiten en DER, CT, Cost, MI — estilo SWE-bench pero metodológico
- **Longitudinal tracking**: Métricas a 6/12/24 meses: technical debt evolution, onboarding time, incident rate

### 4.4 Integración con Estándares Industriales
| Estándar | Punto de Integración SAQI |
|----------|---------------------------|
| **ISO/IEC 25010** | Métricas PQ-01 a PQ-14 mapeadas directas |
| **NIST SSDF (SP 800-218)** | Skills A-secure-coding, B-authentication-security → PW, RV practices |
| **OWASP SAMM** | Nivel 2-3 alcanzable via SAQI gates + adversarial QA |
| **CMMI** | Nivel 2 (Managed) via proceso 14 fases + métricas; Nivel 3 (Defined) via Skills gobernadas |
| **SOC 2 / ISO 27001** | Evidence generation automática: QA_RESULTS.md, ARCHITECTURE.md, threat models |

---

## 5. Investigación Fundamental (36+ meses)

### 5.1 Preguntas de Investigación Abiertas

| Área | Preguntas |
|------|-----------|
| **Skill Transferability** | ¿Qué % de una Skill es transferible entre stacks/dominios? ¿Cómo medir "Skill distance"? |
| **Agent-Human Interaction** | ¿Cuál es la carga cognitiva óptima de gates humanos (H1-H8)? ¿Se pueden automatizar algunos? |
| **Adversarial AI vs Human** | ¿Un agente en modo adversarial supera a QA humano en detección business logic flaws? |
| **Context Compression** | ¿Cuál es la pérdida de información al comprimir contexto 80%→20%? ¿Qué se pierde crítico? |
| **Skill Evolution Dynamics** | ¿Las Skills convergen a un "core estable" o divergen por proyecto? Teoría de grafos de Skills. |
| **Economic Viability** | ¿Cuál es el ROI real de SAQI? Break-even: tokens + tooling + expertise vs defect cost reduction. |

### 5.2 Colaboración Académica/Industrial
- **PhD/Thesis topics**: Skill governance, Context engineering for SE, Adversarial QA automation
- **Industry partnerships**: Pilotos en empresas medianas (50-500 devs) con datos reales producción
- **Standards bodies**: Propuesta a IEEE/ISO para "AI-Assisted Software Engineering Process Standard"

---

## 6. Hoja de Ruta Resumida (Timeline)

```
2026 Q3-Q4    │ 2027 Q1-Q2      │ 2027 Q3-Q4      │ 2028 Q1-Q4      │ 2029+
──────────────┼─────────────────┼─────────────────┼─────────────────┼──────
VALIDACIÓN    │ MADURACIÓN      │ ESCALAMIENTO    │ ECOSISTEMA      │ INVESTIGACIÓN
──────────────┼─────────────────┼─────────────────┼─────────────────┼──────
✓ Case study  │ ✓ Tooling MVP   │ ✓ Multi-team    │ ✓ Skill Registry│ ✓ PhD topics
  réplicas 3+ │ ✓ RCT piloto    │ ✓ Brownfield    │ ✓ Benchmark     │ ✓ Industry
✓ RCT piloto  │ ✓ Skill Gov     │ ✓ Multi-model   │ ✓ Standards     │ ✓ Standards
✓ Tooling MVP │ ✓ Multi-model   │ ✓ CI/CD gates   │ ✓ Longitudinal  │ ✓ Theory
──────────────┼─────────────────┼─────────────────┼─────────────────┼──────
Papers: 2-3   │ Papers: 3-4     │ Papers: 2-3     │ Papers: 1-2     │ Papers: 2+
(Case, RCT,   │ (Tooling, Gov,  │ (Team, Brown,   │ (Benchmark,     │ (Theory,
 Tools)       │  Multi-model)   │  Metrics)       │  Standards)     │  Transfer)
```

---

## 7. Recursos Necesarios

| Recurso | 2026 | 2027 | 2028 |
|---------|------|------|------|
| **Investigador principal** | 1.0 FTE | 1.0 FTE | 0.5 FTE |
| **Desarrolladores tooling** | 0.5 FTE | 1.0 FTE | 1.0 FTE |
| **Participantes experimento** | 30 (estudiantes/voluntarios) | 50 (industria) | 100+ |
| **Compute (LLM APIs)** | $5K | $15K | $30K |
| **Publicación (OA fees)** | $3K | $5K | $5K |
| **Total estimado/año** | ~$50K | ~$150K | ~$250K |

---

## 8. Riesgos y Mitigaciones

| Riesgo | Probabilidad | Impacto | Mitigación |
|--------|--------------|---------|------------|
| **No se reclutan suficientes participantes RCT** | Media | Alto | Incentivos académicos + industriales; diseño within-subject alternativo |
| **Modelos LLM cambian rápido (skills obsoletas)** | Alta | Media | Skill versioning + capability matrix; actualización trimestral |
| **Tooling no adoptado (complejidad)** | Media | Alto | UX-first design; onboarding < 30 min; valor inmediato visible |
| **Resultados RCT nulos / negativos** | Media | Alto | Pre-registrar análisis; publicar null results; aprender para v2 |
| **Competencia: otras metodologías surgen** | Alta | Media | Open source, community-driven; interoperabilidad con otras |

---

## 9. Referencias Internas

- SAQI-001: Resumen Ejecutivo (Objetivos OE1-OE8)
- SAQI-003: Caso Estudio (Limitaciones sección 8)
- SAQI-007: Métricas (Dashboard, Skill metrics)
- SAQI-008: Protocolo Experimental (Diseño RCT)
- SAQI-009: Amenazas Validez (Plan mitigación)
- SAQI-012: Paquete Replicación (Guía práctica)

---

## 10. Referencias Externas (Inspiración Hoja de Ruta)

1. **SWE-bench** (Jimenez et al., 2023) — Benchmark agentes en repos reales
2. **AgentBench** (Liu et al., 2023) — Evaluación multi-tarea agentes
3. **Voyager** (Wang et al., 2023) — Skill library persistente para agentes
4. **GitHub Copilot / Cursor / Windsurf** — Tooling comercial referencia
5. **NIST SSDF v1.1** — Secure SDLC framework
6. **ISO/IEC 5055** — Software Quality Measurement
7. **ACM SIGSOFT Empirical Standards** — Replicación, open science
8. **DORA/Accelerate** — Métricas de delivery de alto rendimiento

---

*Documento vivo — Actualizar trimestralmente con progreso real*
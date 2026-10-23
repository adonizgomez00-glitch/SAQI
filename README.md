# SAQI: Skill-based Agentic Quality Iteration

> **Una metodología experimental de ingeniería de software para desarrollo asistido por agentes de IA**

[![Versión](https://img.shields.io/badge/versión-2.0--experimental-blue)](https://github.com/adonis/SAQI)
[![Estado](https://img.shields.io/badge/estado-experimental-orange)](https://github.com/adonis/SAQI)
[![Licencia](https://img.shields.io/badge/licencia-CC--BY--4.0-blue)](LICENSE)

---

## 📋 Resumen Ejecutivo

**SAQI v2 (Skill-based Agentic Quality Iteration)** es una metodología que estructura el desarrollo de software asistido por agentes de IA mediante:

1. **Desarrollo incremental por resultados** — Vertical slices pequeñas con outcome verificable, criterios de aceptación claros, alcance mínimo necesario
2. **Modo adaptativo de ejecución** — Tres niveles (Ligero/Estándar/Riguroso) con criterios concretos de selección según tamaño, complejidad y riesgo
3. **ODD + SDD ligero** — Outcome verificable como punto de partida; template ≤10 líneas por historia (resultado, ACs, constraints, tests, riesgos)
4. **TDD selectivo por riesgo** — Test-first obligatorio para reglas negocio, validaciones, permisos, algoritmos edge cases; test-after para resto
5. **Anti-sobreingeniería** — Checklist obligatorio: no abstracciones sin necesidad demostrada, no refactors ajenos, no deps innecesarias
6. **QA Dual con dos ciclos** — Ciclo rápido por historia (funcional + tests pertinentes + lint/typecheck) + Ciclo profundo por riesgo/hito (adversarial completo, seguridad, stress, regresión ampliada)
7. **Skills versionadas con aprendizaje v2** — Flujo 8 pasos (detectar→reproducir→causa raíz→clasificar→evaluar mecanismo→aplicar→validar→versionar), prioridad ejecutable > instruccional (lint/contract/check/pattern/rule/doc), carga selectiva
8. **Estrategia explícita de eficiencia** — 10 reglas operativas (TE-01 a TE-10), limitación declarada (no medición real tokens), 5 métricas nuevas (TE-11 a TE-15)
9. **Contexto persistente sin burocracia** — Única fuente de verdad por clase info, anti-duplicación, actualización solo en checkpoints significativos
10. **Validación experimental A vs B** — Grupo A (incremental baseline) vs Grupo B (+Skills, contexto, QA adaptativo, Learn→Skills), criterios éxito medibles

**Principio rector**: *No afirmamos superioridad. Definimos → Documentamos → Aplicamos → Medimos → Comparamos → Iteramos → Validamos.*

---

## 🎯 Objetivos de SAQI v2

| ID | Objetivo | Métrica de Evaluación |
|----|----------|----------------------|
| OE1 | **Desarrollo incremental por resultados** — Vertical slices con outcome verificable | Historias cerradas con ACs passing; 0 scope creep |
| OE2 | **Modo adaptativo de ejecución** — 3 niveles con criterios concretos | % tareas en modo correcto (auditoría); 0 bajadas indebidas de Riguroso |
| OE3 | **ODD + SDD ligero integrado** — Template ≤10 líneas por historia | 100% historias con template completo; 0 specs extensas innecesarias |
| OE4 | **TDD selectivo por riesgo** — Test-first donde aporte valor | Cobertura test-first en reglas críticas 100%; 0 TDD mecánico en bajo riesgo |
| OE5 | **Anti-sobreingeniería obligatoria** — Checklist en Fase 4 | 0 abstracciones/deps/refactors no justificados en code review |
| OE6 | **QA Dual con dos ciclos** — Rápido (historia) + Profundo (riesgo/hito) | 100% historias con ciclo rápido; ciclos profundos solo por triggers |
| OE7 | **Skills versionadas con aprendizaje v2** — Flujo 8 pasos, prioridad ejecutable | `defect_prevention_rate` > 80% reglas MUST; `false_positive_rate` < 10% |
| OE8 | **Estrategia eficiencia tokens** — 10 reglas + 5 métricas | TE-11 (tokens/story) ↓20% YoY; TE-12 (cycles/story) ≤1.5 |
| OE9 | **Contexto persistente sin burocracia** — Única fuente verdad, checkpoints significativos | `Context Staleness Index` = 0; 0 duplicación entre docs vivos |
| OE10 | **Validación experimental A vs B** — RCT con criterios medibles | DER ↓30% (Cliff's δ≥0.33); ADR ≥25%; CT no aumento >20% |

---

## 🏗️ Arquitectura Metodológica v2

```
┌─────────────────────────────────────────────────────────────────┐
│                    MODO ADAPTATIVO (Selección)                  │
│  Ligero (fixes ≤2 archivos) │ Estándar (features) │ Riguroso   │
│  (auth/security/schema/pre-release)                            │
└──────────────────────────┬──────────────────────────────────────┘
                           │
          ┌────────────────┼────────────────┐
          ▼                ▼                ▼
┌──────────────────┐ ┌───────────────┐ ┌──────────────────┐
│   HISTORIA       │ │   HISTORIA    │ │   HISTORIA       │
│   (Outcome +     │ │   (Outcome +  │ │   (Outcome +     │
│    ACs + Tests)  │ │    ACs + Tests)│ │    ACs + Tests)  │
└────────┬─────────┘ └───────┬───────┘ └────────┬─────────┘
         │                   │                   │
         ▼                   ▼                   ▼
┌──────────────────┐ ┌───────────────┐ ┌──────────────────┐
│  CICLO RÁPIDO    │ │ CICLO RÁPIDO  │ │ CICLO RÁPIDO +   │
│  (por historia)  │ │ (por historia)│ │ CICLO PROFUNDO   │
│  - Funcional     │ │ - Funcional   │ │ (obligatorio     │
│  - Tests áreas   │ │ - Tests áreas │ │  antes de merge) │
│  - Lint/Typecheck│ │ - Lint/Typeck │ │ - Adversarial 8  │
│  - Diff review   │ │ - Diff review │ │   categorías     │
│  - ACs confirm   │ │ - ACs confirm │ │ - Seguridad      │
└────────┬─────────┘ └───────┬───────┘ └────────┬─────────┘
         │                   │                   │
         └───────────────────┼───────────────────┘
                             ▼
              ┌─────────────────────────────┐
              │     HITO / RELEASE          │
              │   CICLO PROFUNDO (Gate)     │
              │  - Adversarial completo     │
              │  - Seguridad (SAST/DAST)    │
              │  - Stress/Chaos/Concurrencia│
              │  - Regresión completa L1-L5 │
              │  - Migraciones + Rollback   │
              └──────────────┬──────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                    LEARN → SKILLS v2                            │
│  Detectar→Reproducir→Causa raíz→Clasificar→Evaluar mecanismo→  │
│  Aplicar→Validar no contradicción→Versionar→Registrar impacto  │
│  Prioridad: Lint/Contract/Check > Pattern > Rule > Doc         │
└─────────────────────────────────────────────────────────────────┘
```

**Flujo por defecto**: Modo Estándar → Ciclo Rápido por historia → Ciclo Profundo en hito/release
**Modo Ligero**: Solo Ciclo Rápido
**Modo Riguroso**: Ciclo Rápido + Ciclo Profundo obligatorio antes de merge

---

## 📚 Documentación (Índice Completo)

### Fundamentos (01-foundations/)
| Doc | Título | Descripción |
|-----|--------|-------------|
| [SAQI-001](docs/01-foundations/SAQI-001-resumen-ejecutivo-definicion.md) | Resumen Ejecutivo y Definición | Visión general, principios, componentes, métricas, caso de estudio |
| [SAQI-002](docs/01-foundations/SAQI-002-fundamentos-teoricos-revision-bibliografica.md) | Fundamentos Teóricos y Revisión Bibliográfica | EBSE, Clean Architecture, Secure Coding, Testing, Debugging, Offline DB, Prompt Engineering, Agentic AI |
| [SAQI-010](docs/01-foundations/SAQI-010-comparacion-enfoques.md) | Comparación con Enfoques Existentes | Tabla comparativa: Agile, TDD, DevOps, DevSecOps, Agentic SE, Context Engineering, Harness Engineering |

### Metodología (02-methodology/)
| Doc | Título | Descripción |
|-----|--------|-------------|
| [SAQI-003](docs/02-methodology/SAQI-003-gobernanza-skills.md) | Gobernanza y Ciclo de Vida de Skills v2 | Estructura canónica, 4 niveles (A-D), ciclo vida, versionado SemVer, testing de Skills, **aprendizaje v2 (flujo 8 pasos, prioridad ejecutable), carga selectiva** |
| [SAQI-004](docs/04-validation/SAQI-004-qa-framework.md) | Marco de QA Funcional y Adversarial v2 | 6 niveles QA, taxonomía 8 categorías ATK-*, **dos ciclos QA (rápido/profundo con triggers)**, clasificación defectos, roles |
| [SAQI-005](docs/02-methodology/SAQI-005-proceso-saqi.md) | Definición del Proceso SAQI v2 (14 Fases + Políticas) | **§2.2 Modo adaptativo (3 niveles), §2.3 ODD+SDD ligero, §2.4 Anti-sobreingeniería, §2.5 TDD selectivo**, matriz RACI, métricas por fase |
| [SAQI-006](docs/02-methodology/SAQI-006-arquitectura-agente.md) | Especificación de Arquitectura de Agente | Componentes (Planner, Context Manager, Skill Registry, Tool Executor, Mode Controller), loop de ejecución, interfaz humano-agente, prompts por modo |
| [SAQI-013](docs/02-methodology/SAQI-013-gestion-contexto.md) | Gestión de Contexto Persistente v2 | **§8 Contexto sin burocracia (única fuente verdad, anti-duplicación, checkpoints significativos)**, 4+1 documentos vivos, protocolo checkpointing |

### Caso de Estudio (03-case-study/)
| Doc | Título | Descripción |
|-----|--------|-------------|
| [SAQI-003](docs/03-case-study/SAQI-003-caso-estudio-open-rooterp.md) | Caso de Estudio Open-RootERP (Baseline v1) | **Hechos observados** (524+ tests, 15+ defectos, 14 módulos), **Interpretaciones**, **Hipótesis** H1-H7, lecciones reales, limitaciones N=1 — **Base para SAQI v2** |
| [SAQI-CASE-002](docs/03-case-study/SAQI-CASE-002-zeroqa.md) | Caso de Estudio ZeroQA | SaaS full-stack (Next.js/TS/Prisma/PostgreSQL/Playwright). Primera aplicación explícita de Skills SAQI; 12 checkpoints; ciclo Learn→Skills trazable (A-testing v2.1.0); 289 tests |
| [SAQI-CASE-003](docs/03-case-study/SAQI-CASE-003-automixer.md) | Caso de Estudio AutoMixer | App de escritorio (Python/PySide6/FFmpeg/Qt). Fakes primero → adaptador real; umbrales numéricos D2; gate de release por licencias; lectura alternativa del acrónimo |
| [SAQI-CASE-004](docs/03-case-study/SAQI-CASE-004-discipline.md) | Caso de Estudio Discipline | Web Flask/SQLite (MVP pre-SAQI). Línea de base observacional: la entrada al ciclo SAQI desde un MVP validado |
| [SAQI-CASE-005](docs/03-case-study/SAQI-CASE-005-flight-mainframe-simulator.md) | Caso de Estudio FMS | Escritorio Rust/Tauri/COBOL (DDD multi-crate, MCP). Convenciones AGENTS.md como semilla de Skills; stack heterogéneo |
| [SAQI-011](docs/03-case-study/SAQI-011-guia-aplicacion-open-rooterp.md) | Guía de Aplicación Open-RootERP | 6 iteraciones detalladas, patrones clave (Repository+Dexie, Service Layer, Zod validation, Import auto-detect v2), checklist aplicación, plantillas docs |

### Validación (04-validation/)
| Doc | Título | Descripción |
|-----|--------|-------------|
| [SAQI-007](docs/04-validation/SAQI-007-metricas.md) | Marco de Atributos de Calidad y Métricas v2 | ISO 25010 adaptado, **§8 estrategia tokens (10 reglas TE + 5 métricas nuevas)**, 35+ métricas, dashboard iteración, recolección automatizada vs manual |
| [SAQI-008](docs/04-validation/SAQI-008-protocolo-experimental.md) | Diseño Experimental y Protocolo de Validación v2 | **§9 Experimento A vs B (Grupo A baseline incremental vs Grupo B + mecanismos SAQI)**, RCT 3 brazos, 10 tareas, variables PICOC, análisis estadístico planificado |
| [SAQI-009](docs/04-validation/SAQI-009-amenazas-validez-limitaciones.md) | Amenazas a la Validez y Limitaciones | Análisis exhaustivo: validez interna/externa/constructo/estadística, limitaciones inherentes SAQI, cartera multi-caso N=5, matriz evidencia/afirmación |

### Referencias (06-references/)
| Doc | Título |
|-----|--------|
| [SAQI-014](docs/06-references/SAQI-014-trabajo-futuro.md) | Trabajo Futuro y Hoja de Ruta |
| [SAQI-015](docs/06-references/SAQI-015-glosario.md) | Glosario y Terminología (50+ términos) |
| [SAQI-016](docs/06-references/SAQI-016-referencias.md) | Referencias Bibliográficas (115 fuentes) |

### Apéndices (05-appendices/)
| Doc | Título |
|-----|--------|
| [SAQI-017](docs/05-appendices/SAQI-017-apendice-a-arquitectura-open-rooterp.md) | Apéndice A: Arquitectura Detallada Open-RootERP |
| [SAQI-018](docs/05-appendices/SAQI-018-apendice-b-plantillas-agente.md) | Apéndice B: Plantillas Configuración Agente y Docs Vivos |
| [SAQI-019](docs/05-appendices/SAQI-019-apendice-c-plantillas-metricas.md) | Apéndice C: Plantillas Recolección Métricas (Dashboard, CSV, Collector Script) |
| [SAQI-020](docs/05-appendices/SAQI-020-apendice-d-protocolos-experimento.md) | Apéndice D: Protocolos de Experimento (RCT detallado, sub-estudio PSI, ética) |
| [SAQI-021](docs/05-appendices/SAQI-021-apendice-e-modelos-amenaza.md) | Apéndice E: Modelos de Amenaza (STRIDE Open-RootERP, plantillas otros stacks) |

---

## 🚀 Inicio Rápido

### 1. Clonar y Explorar
```bash
git clone https://github.com/adonis/SAQI.git
cd SAQI
```

### 2. Aplicar en un Proyecto Nuevo
```bash
# 1. Estructura base SAQI
mkdir -p src/{config,database,models,repositories,services,controllers,views,components,store,utils}
mkdir -p tests/{services,controllers,integration,utils,e2e}
mkdir -p docs

# 2. Crear documentos vivos (usar plantillas SAQI-018)
cp docs/05-appendices/SAQI-018-apendice-b-plantillas-agente.md#ARCHITECTURE.md ARCHITECTURE.md
cp docs/05-appendices/SAQI-018-apendice-b-plantillas-agente.md#CONTEXT.md CONTEXT.md
cp docs/05-appendices/SAQI-018-apendice-b-plantillas-agente.md#PROJECT_STATE.md PROJECT_STATE.md
cp docs/05-appendices/SAQI-018-apendice-b-plantillas-agente.md#QA_RESULTS.md QA_RESULTS.md

# 3. Cargar Skills OpenCode (mínimo Nivel A)
opencode skill enable A-coding-standards A-project-architecture A-secure-coding A-context-manager A-testing C-qa-breaker D-git-workflow

# 4. Seguir proceso SAQI-005: Fase 1 Plan → Fase 2 Architect → Fase 3 Skill Selection → ...
```

### 3. Replicar Caso de Estudio Open-RootERP
```bash
# Ver SAQI-011 para guía paso a paso
# Ver SAQI-012 para paquete de replicación completo
```

---

## 📊 Métricas Clave del Caso de Estudio (Open-RootERP)

| Métrica | Valor Observado | Nota |
|---------|-----------------|------|
| **Tests Unit/Int** | 410+ (45 suites) | 100% pass |
| **Tests E2E** | 38 (Playwright) | 100% pass |
| **Tests Adversariales** | 76 (4 fases) | 100% pass |
| **Total Tests** | 524+ | Reportado en README |
| **Módulos Funcionales** | 14/14 completos | ERP offline completo |
| **Defectos P0 Corregidos** | 4 | Race condition, XSS, Prototype pollution, CSV injection |
| **Defectos P1 Corregidos** | 8 | Double submit, Dark mode, Import fixes, etc. |
| **Cobertura (L/B/F)** | 84.3% / 78.9% / 81.2% | Custom runner + v8 |
| **Mutation Score** | 72% | Custom mutation testing (1,247 mutantes) |
| **Skills Aplicables/OpenCode** | 14/17 | Mapeo real en SAQI-003 |

> **⚠️ Importante**: Estos son **hechos observados en el caso original Open-RootERP (N=1)**. No constituyen evidencia causal de superioridad de SAQI. Desde 2026-09-07 la cartera observacional se amplió a **5 casos** (ver [tabla de casos de estudio](#-caso-de-estudio-03-case-study) y SAQI-CASE-002…005); aun así, sigue sin haber grupo control y todos comparten autor y modelo. Las métricas sirven para formular hipótesis (H1-H7 y las específicas de cada caso) que requieren validación experimental (SAQI-008).

---

## 🔬 Validación Experimental (Próximos Pasos)

| Hito | Descripción | Estado |
|------|-------------|--------|
| **Réplica 1** | Aplicar SAQI en proyecto distinto (stack, dominio, equipo) | ✅ zeroQA documentado (SAQI-CASE-002) + automixer (003), discipline (004), FMS (005) |
| **Réplica 2-3** | Mínimo 3 proyectos totales para validez externa inicial | 🔶 4 casos observacionales nuevos (N=5 acumulado) — réplicas independientes pendientes |
| **RCT Piloto** | Ejecutar SAQI-008: Grupo A (ad-hoc) vs Grupo B (SAQI) | 📋 Diseñado |
| **Multi-Modelo** | Probar con GPT-4o, Claude 3.5, Llama 3 70B, modelos locales 7B | 📋 Planificado |
| **Tooling SAQI** | CLI para checkpointing, skill loading, metric collection, gate enforcement | 📋 Diseñado (SAQI-014) |
| **Normalización métricas** | Fuente única de verdad por métrica; resolver discrepancias (lección ZeroQA) | 🔶 Nueva lección (SAQI-007 v1.2) |

---

## ⚠️ Limitaciones Conocidas (Resumen)

| Categoría | Limitación Principal |
|-----------|---------------------|
| **Evidencia** | Cartera observacional de **5 casos** (Open-RootERP, ZeroQA, AutoMixer, Discipline, FMS), auto-experimentación, **sin grupo control**; réplicas independientes pendientes |
| **Generalización** | Diversidad de stacks ampliada (Vanilla JS+Dexie, Next.js/TS/Prisma, Python/PySide6, Flask, Rust/COBOL/Tauri), pero **mismo autor y mismo modelo** en todos |
| **Métricas** | Coste/tokens estimados, no instrumentados; métricas skill = convenciones/AGENTS.md salvo ZeroQA (Skills SAQI declaradas); discrepancias de consolidación detectadas |
| **Metodología** | Fase 7 integrada en E2E en parte de los casos; gobernanza de Skills SAQI-003 sigue siendo propuesta |
| **Riesgo IA** | Mismo modelo genera código y tests; mitigado por QA adversarial modo atacante + humano |

> Ver [SAQI-009](docs/04-validation/SAQI-009-amenazas-validez-limitaciones.md) para análisis exhaustivo (actualizado a N=5).

---

## 🤝 Contribución

SAQI es **open source** y **community-driven**. Contribuciones bienvenidas:

1. **Issues**: Reportar bugs, proponer mejoras, discutir amenazas a validez
2. **PRs**: Mejorar documentación, añadir Skills, plantillas, métricas, protocolos
3. **Réplicas**: Compartir resultados de aplicar SAQI en otros proyectos
4. **Skills**: Contribuir Skills al registry (gobernanza SAQI-003)

```bash
# Flujo contribución
git checkout -b feature/nueva-skill-seguridad
# Editar .opencode/skill/B-nueva-skill/SKILL.md + tests
# Ejecutar tests skill (contract + scenario)
git commit -m "feat(skill): add B-nueva-skill v1.0.0"
git push origin feature/nueva-skill-seguridad
# PR → Revisión gobernanza → Merge → Tag v1.0.0
```

---

## 📄 Licencia

**Documentación y Metodología SAQI**: **CC-BY-4.0** — Ver [LICENSE](LICENSE)

> SAQI es una metodología documentada. La documentación (`.md`, guías, papers, plantillas de texto) es **CC-BY-4.0** (atribución requerida). Open-RootERP (caso de estudio) es un proyecto separado con su propia licencia **GPL-2.0-or-later** en [erp-ligero-offline](https://github.com/adonis/apps-locales/erp-ligero-offline).

---

## 📞 Contacto y Referencias

- **Autor**: Adónis Adonai Gómez Martínez
- **Proyecto Base**: [Open-RootERP](https://github.com/adonis/apps-locales/erp-ligero-offline) (caso de estudio)
- **Skills Base**: [OpenCode Skills](https://github.com/opencode-ai/opencode/tree/main/skills) (17 skills referencia)

### Skills de opencode aportadas — Consolidación v2

#### Skills Consolidadas SAQI (4 + 9 flujo + 1 condicional)

| Skill | Tipo | Descripción | Submódulos |
|-------|------|-------------|------------|
| **saqi-testing** | Consolidada | Testing unificado: unit, e2e, component, tui | `unit`, `e2e`, `component`, `tui` |
| **saqi-lean-code** | Consolidada | Código lean, anti-overengineering, auditoría, review | `principles`, `audit`, `review`, `standards` |
| **saqi-spec-arch** | Consolidada | SDD ligero, arquitectura, patrones Effect | `sdd`, `architecture`, `effect-patterns` |
| **saqi-qa-harness** | Consolidada | QA adversarial, testing standards, security gates | `adversarial`, `standards`, `security` |
| **saqi-adaptive-mode** | Flujo | Modo ligero/estándar/riguroso con criterios concretos | — |
| **saqi-vertical-slice** | Flujo | Historias pequeñas, criterios aceptación, checklist | — |
| **saqi-skills-learning** | Flujo | Aprendizaje: detectar→root cause→evaluar→aplicar→versionar | — |
| **saqi-token-optimizer** | Flujo | Recuperación selectiva, budgets, diagnóstico estancamiento | — |
| **saqi-harness** | Flujo | Comandos validación, exit criteria, budgets, logging | — |
| **saqi-context-manager** | Flujo | Contexto persistente (.saqi/), única fuente verdad | — |
| **saqi-metrics** | Flujo | Tokens, tiempo, ciclos, defectos pre/post, experimento A/B | — |
| **saqi-qa-cycles** | Flujo | Ciclo rápido (historia) vs profundo (riesgo/hito) | — |

#### Skill Condicional (Carga Bajo Demanda)

| Skill | Trigger | Descripción |
|-------|---------|-------------|
| **rtl-aware-development** | `detect-rtl` | RTL/LTR behavior — solo carga si detecta logical properties, `dir="rtl"`, locales RTL, portaled components |

#### Skills Legadas (Deprecated → Migradas)

| Skill Original | Estado | Migrada a |
|----------------|--------|-----------|
| app-e2e-tests | `deprecated` | `saqi-testing:e2e` |
| component-tests | `deprecated` | `saqi-testing:component` |
| effect | `deprecated` | `saqi-spec-arch:effect-patterns` |
| tui-tests | `deprecated` | `saqi-testing:tui` |
| unit-tests | `deprecated` | `saqi-testing:unit` |
| sample-skill | `archived` | Solo template de referencia |

#### Skills Globales Activas (Referenciadas)

| Skill | Fuente | Nivel | Descripción |
|-------|--------|-------|-------------|
| A-sdd | global | A | Spec-Driven Development (core) |
| A-testing | global | A | Testing standards |
| A-secure-coding | global | A | Secure coding practices |
| D-git-workflow | global | D | Git workflow practices |
| C-debugging | global | C | Debugging skills |
| B-database-design-sql | global | B | Database design SQL |
| ponytail / ponytail-audit / ponytail-review | global | — | Lean code philosophy |

**Registro completo en `.opencode/skill/registry.yaml`**. Fuente original: [anomalyco/opencode](https://github.com/anomalyco/opencode).
- **Metodología Relacionada**: [Agentic SE](https://arxiv.org/abs/2305.15294), [Context Engineering](https://arxiv.org/abs/2305.14225), [Harness Engineering](https://github.com/swe-bench/SWE-bench)

---

## 🏷️ Citas Sugeridas

```bibtex
@misc{saqi2026,
  title = {SAQI: Skill-based Agentic Quality Iteration},
  author = {Adónis Adonai Gómez Martínez},
  year = {2026},
  note = {Metodología experimental — v2.0},
  url = {https://github.com/adonis/SAQI}
}
```

---

> **SAQI no es una herramienta, es una disciplina.**  
> La calidad no emerge del azar ni del prompt perfecto.  
> Emerge de **conocimiento versionado**, **verificación adversarial**, **memoria persistente** y **retroalimentación cerrada**.  
> **DEFINIR → DOCUMENTAR → APLICAR → MEDIR → COMPARAR → ITERAR → VALIDAR.**
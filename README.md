# SAQI: Skill-based Agentic Quality Iteration

> **Una metodología experimental de ingeniería de software para desarrollo asistido por agentes de IA**

[![Versión](https://img.shields.io/badge/versión-1.0-blue)](https://github.com/adonis/SAQI)
[![Estado](https://img.shields.io/badge/estado-experimental-orange)](https://github.com/adonis/SAQI)
[![Licencia Docs](https://img.shields.io/badge/docs-CC--BY--4.0-blue)](LICENSE-DOCS)
[![Licencia Código](https://img.shields.io/badge/código-MIT-green)](LICENSE-CODE)

---

## 📋 Resumen Ejecutivo

**SAQI (Skill-based Agentic Quality Iteration)** es una metodología que estructura el desarrollo de software asistido por agentes de IA mediante:

1. **Skills versionadas y gobernadas** — Artefactos de conocimiento reutilizables (reglas, patrones, plantillas, criterios de verificación) en 4 niveles (A: Core, B: Dominio, C: Verificación, D: Soporte)
2. **Ciclo de vida de 14 fases** — Desde Plan hasta Mejora de Skills, con gates de calidad obligatorios y responsables humano/agente explícitos
3. **QA Dual** — Testing convencional (Unit → Integration → Functional → E2E) + **QA Adversarial obligatorio** (Fase 7: 8 categorías de ataque ATK-*)
4. **Contexto persistente** — 4+1 documentos vivos (`ARCHITECTURE.md`, `CONTEXT.md`, `PROJECT_STATE.md`, `QA_RESULTS.md`, `SESSION.md`) + protocolo de checkpointing automático
5. **Retroalimentación cerrada Learn → Skills** — Hallazgos de cada iteración materializan mejoras en Skills versionadas (SemVer + Changelog trazable a defectos)

**Principio rector**: *No afirmamos superioridad. Definimos → Documentamos → Aplicamos → Medimos → Comparamos → Iteramos → Validamos.*

---

## 🎯 Objetivos de SAQI

| ID | Objetivo | Métrica de Evaluación |
|----|----------|----------------------|
| OE1 | Definir ciclo de vida con fases, criterios y responsables explícitos | SAQI-005 documentado y aplicado en caso de estudio |
| OE2 | Codificar conocimiento de ingeniería en Skills versionadas y gobernadas | 17 Skills base, gobernanza SAQI-003 |
| OE3 | Integrar QA adversarial como fase obligatoria con taxonomía de ataques | Fase 7 (ATK-*) gate de release |
| OE4 | Implementar gestión de contexto persistente para agentes | 4 docs vivos + checkpointing (SAQI-013) |
| OE5 | Establecer ciclo de retroalimentación Skills ← Iteración | Fase 14: análisis defectos → reglas Skills |
| OE6 | Definir métricas para evaluar y comparar la metodología | Marco SAQI-007 (6 categorías) |
| OE7 | Documentar caso de estudio separando hechos/interpretaciones/hipótesis | SAQI-003 (Open-RootERP) |
| OE8 | Diseñar protocolo experimental para validación controlada | SAQI-008 (RCT replicable) |

---

## 🏗️ Arquitectura Metodológica

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
│                        BREAK (QA Adversarial - L5) ⭐           │
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
│  Regresión completa + Gates de calidad                         │
└──────────────────────────┬──────────────────────────────────────┘
                           │ Verifica
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                        LEARN                                    │
│  Analizar: ¿Qué falló? ¿Qué patrón emergió? ¿Skill omitió algo? │
└──────────────────────────┬──────────────────────────────────────┘
                           │ Mejora
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                     IMPROVE SKILLS (vN.M.P+1)                   │
│  Nueva regla, plantilla fix, anti-patrón, criterio verificación │
└─────────────────────────────────────────────────────────────────┘
```

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
| [SAQI-003](docs/02-methodology/SAQI-003-gobernanza-skills.md) | Gobernanza y Ciclo de Vida de Skills | Estructura canónica, 4 niveles (A-D), ciclo vida, versionado SemVer, testing de Skills, prevención propagación errores |
| [SAQI-004](docs/04-validation/SAQI-004-qa-framework.md) | Marco de QA Funcional y Adversarial | 6 niveles QA, taxonomía 8 categorías ATK-*, protocolo Fase 7, clasificación defectos, roles |
| [SAQI-005](docs/02-methodology/SAQI-005-proceso-saqi.md) | Definición del Proceso SAQI (14 Fases) | Entrada/Actividades/Responsable/Artefactos/Salida por fase, matriz RACI, métricas por fase |
| [SAQI-006](docs/02-methodology/SAQI-006-arquitectura-agente.md) | Especificación de Arquitectura de Agente | Componentes (Planner, Context Manager, Skill Registry, Tool Executor, Mode Controller), loop de ejecución, interfaz humano-agente, prompts por modo |
| [SAQI-013](docs/02-methodology/SAQI-013-gestion-contexto.md) | Gestión de Contexto Persistente | 4+1 documentos vivos, protocolo checkpointing (70%/80%), re-hidratación, métricas contexto |

### Caso de Estudio (03-case-study/)
| Doc | Título | Descripción |
|-----|--------|-------------|
| [SAQI-003](docs/03-case-study/SAQI-003-caso-estudio-open-rooterp.md) | Caso de Estudio Open-RootERP (Corregido) | **Hechos observados** (524+ tests, 15+ defectos, 14 módulos), **Interpretaciones**, **Hipótesis** H1-H7, lecciones reales, limitaciones N=1 |
| [SAQI-011](docs/03-case-study/SAQI-011-guia-aplicacion-open-rooterp.md) | Guía de Aplicación Open-RootERP | 6 iteraciones detalladas, patrones clave (Repository+Dexie, Service Layer, Zod validation, Import auto-detect v2), checklist aplicación, plantillas docs |

### Validación (04-validation/)
| Doc | Título | Descripción |
|-----|--------|-------------|
| [SAQI-007](docs/04-validation/SAQI-007-metricas.md) | Marco de Atributos de Calidad y Métricas | ISO 25010 adaptado, 35+ métricas (Producto, Proceso, Skills, Coste, Contexto, Aprendizaje), dashboard iteración, recolección automatizada vs manual |
| [SAQI-008](docs/04-validation/SAQI-008-protocolo-experimental.md) | Diseño Experimental y Protocolo de Validación | RCT 3 brazos (Ad-hoc vs SAQI vs SAQI-Light), 10 tareas, 30+ equipos, variables PICOC, análisis estadístico planificado |
| [SAQI-009](docs/04-validation/SAQI-009-amenazas-validez-limitaciones.md) | Amenazas a la Validez y Limitaciones | Análisis exhaustivo: validez interna/externa/constructo/estadística, limitaciones inherentes SAQI, limitaciones caso N=1, matriz evidencia/afirmación |

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
opencode skill enable A-coding-standards A-project-architecture A-secure-coding A-context-manager A-testing A-qa-breaker

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
| **Cobertura (L/B/F)** | 84.3% / 78.9% / 81.2% | Vitest + v8 |
| **Mutation Score** | 72% | Stryker (1,247 mutantes) |
| **Skills Aplicables/OpenCode** | 14/17 | Mapeo real en SAQI-003 |

> **⚠️ Importante**: Estos son **hechos observados en un único caso (N=1)**. No constituyen evidencia causal de superioridad de SAQI. Sirven para formular hipótesis (H1-H7) que requieren validación experimental (SAQI-008).

---

## 🔬 Validación Experimental (Próximos Pasos)

| Hito | Descripción | Estado |
|------|-------------|--------|
| **Réplica 1** | Aplicar SAQI en 3I SAQI en proyecto distinto (stack, dominio, equipo) | 📋 Planificado |
| **Réplica 2-3** | Mínimo 3 proyectos totales para validez externa inicial | 📋 Planificado |
| **RCT Piloto** | Ejecutar SAQI-008: Grupo A (ad-hoc) vs Grupo B (SAQI) | 📋 Diseñado |
| **Multi-Modelo** | Probar con GPT-4o, Claude 3.5, Llama 3 70B, modelos locales 7B | 📋 Planificado |
| **Tooling SAQI** | CLI para checkpointing, skill loading, metric collection, gate enforcement | 📋 Diseñado (SAQI-014) |

---

## ⚠️ Limitaciones Conocidas (Resumen)

| Categoría | Limitación Principal |
|-----------|---------------------|
| **Evidencia** | Un solo caso de estudio (N=1), auto-experimentación, sin grupo control |
| **Generalización** | Stack específico (Vanilla JS + Dexie + IndexedDB), greenfield, 1 desarrollador |
| **Métricas** | Coste/tokens estimados, no instrumentados; métricas skill = convenciones proyecto, no Skills SAQI gobernadas |
| **Metodología** | Fase 7 integrada en E2E (no separada); Skills = convenciones proyecto, no artefactos versionados/gobernados SAQI |
| **Riesgo IA** | Mismo modelo genera código y tests; mitigado por QA adversarial modo atacante + humano |

> Ver [SAQI-009](docs/04-validation/SAQI-009-amenazas-validez-limitaciones.md) para análisis exhaustivo.

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

Este repositorio tiene licenciamiento dual:

| Componente | Licencia | Archivo |
|------------|----------|---------|
| **Documentación / Metodología / Plantillas de texto** (`.md`, guías, papers) | **CC-BY-4.0** | [LICENSE-DOCS](LICENSE-DOCS) |
| **Código / Scripts / Plantillas ejecutables** (`.js`, `.sh`, `.yaml`, collectors) | **MIT** | [LICENSE-CODE](LICENSE-CODE) |
| **Caso de estudio Open-RootERP** (referencia) | **GPL-2.0-or-later** | Ver [erp-ligero-offline](https://github.com/adonis/apps-locales/erp-ligero-offline) |

> **Nota**: SAQI es una metodología documentada. El código de ejemplo y scripts de recolección son MIT. La documentación es CC-BY-4.0 (atribución requerida). Open-RootERP (caso de estudio) mantiene su licencia GPL-2.0-or-later original.

---

## 📞 Contacto y Referencias

- **Autor**: Adónis Adonai Gómez Martínez
- **Proyecto Base**: [Open-RootERP](https://github.com/adonis/apps-locales/erp-ligero-offline) (caso de estudio)
- **Skills Base**: [OpenCode Skills](https://github.com/opencode-ai/opencode/tree/main/skills) (17 skills referencia)
- **Metodología Relacionada**: [Agentic SE](https://arxiv.org/abs/2305.15294), [Context Engineering](https://arxiv.org/abs/2305.14225), [Harness Engineering](https://github.com/swe-bench/SWE-bench)

---

## 🏷️ Citas Sugeridas

```bibtex
@misc{saqi2026,
  title = {SAQI: Skill-based Agentic Quality Iteration},
  author = {Adónis Adonai Gómez Martínez},
  year = {2026},
  note = {Metodología experimental — v1.0},
  url = {https://github.com/adonis/SAQI}
}
```

---

> **SAQI no es una herramienta, es una disciplina.**  
> La calidad no emerge del azar ni del prompt perfecto.  
> Emerge de **conocimiento versionado**, **verificación adversarial**, **memoria persistente** y **retroalimentación cerrada**.  
> **DEFINIR → DOCUMENTAR → APLICAR → MEDIR → COMPARAR → ITERAR → VALIDAR.**
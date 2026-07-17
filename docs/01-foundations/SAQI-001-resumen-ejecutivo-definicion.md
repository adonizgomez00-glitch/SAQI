# SAQI-001: Resumen Ejecutivo y Definición de la Metodología SAQI

**Versión:** 1.0
**Fecha:** 2026-07-17
**Estado:** Borrador inicial
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Resumen Ejecutivo

### 1.1 Problema

El desarrollo de software asistido por agentes de Inteligencia Artificial (IA) ha demostrado capacidades significativas para la generación de código, pero presenta limitaciones críticas cuando se aplica sin un marco metodológico estructurado:

- **Ausencia de arquitectura deliberada:** Los agentes generan código funcional localmente sin garantizar coherencia arquitectónica global.
- **Alucinaciones y errores funcionales:** Los modelos de lenguaje grande (LLM) producen código sintácticamente correcto pero semánticamente incorrecto.
- **Regresiones no detectadas:** La falta de pruebas de regresión sistemáticas permite que correcciones introduzcan nuevos defectos.
- **Vulnerabilidades de seguridad:** El código generado no sigue automáticamente prácticas de seguridad (OWASP, CERT, NIST SSDF).
- **Pérdida de contexto entre sesiones:** Los agentes no mantienen memoria persistente del estado del proyecto, decisiones arquitectónicas, o lecciones aprendidas.
- **Inconsistencia en la calidad:** La calidad del resultado depende excesivamente de la habilidad del humano para escribir prompts efectivos.
- **Pruebas superficiales:** Los tests generados automáticamente tienden a validar "paths felices" sin explorar casos límite, condiciones de error, o escenarios adversariales.

### 1.2 Propuesta: SAQI (Skill-based Agentic Quality Iteration)

SAQI es una **metodología experimental de ingeniería de software agéntica** que estructura el desarrollo asistido por IA mediante:

1. **Skills especializadas:** Artefactos de conocimiento reutilizable que codifican reglas, procedimientos, patrones y criterios de calidad para dominios específicos de la ingeniería de software (arquitectura, seguridad, testing, debugging, bases de datos, etc.).
2. **Ciclo de vida iterativo estructurado:** 14 fases bien definidas desde requerimientos hasta mejora de Skills.
3. **QA adversarial integrado:** Pruebas deliberadas de ruptura (breaking tests) como fase obligatoria, no opcional.
4. **Gestión de contexto persistente:** Documentos vivos (`ARCHITECTURE.md`, `CONTEXT.md`, `PROJECT_STATE.md`, `QA_RESULTS.md`) que funcionan como memoria externa para los agentes.
5. **Retroalimentación cerrada Skills → Build → Test → Break → Fix → Verify → Learn → Skills ↻:** Los hallazgos de cada iteración mejoran no solo el código, sino las propias Skills y el contexto proporcionado a los agentes.

### 1.3 Funcionamiento General

```
Requerimientos
    ↓
Arquitectura (documentada en ARCHITECTURE.md)
    ↓
Selección y aplicación de Skills
    ↓
Implementación asistida por agentes con contexto persistente
    ↓
Pruebas unitarias e integración
    ↓
Pruebas funcionales y E2E
    ↓
QA Adversarial / Pruebas Destructivas
    ↓
Detección y clasificación de defectos
    ↓
Debugging
    ↓
Corrección
    ↓
Pruebas de regresión
    ↓
Verificación
    ↓
Documentación de resultados
    ↓
Actualización del contexto del proyecto
    ↓
Mejora de prompts y Skills
    ↓
Nueva iteración
```

**Ciclo de retroalimentación fundamental:**

```
Skills → Build → Test → Break → Fix → Verify → Learn → Skills ↻
```

### 1.4 Papel de las Skills

Las **Skills** son el componente diferenciador de SAQI. No son simples prompts; son **artefactos de ingeniería de conocimiento** que contienen:

- **Reglas obligatorias** (MUST/SHALL) basadas en estándares (OWASP, CERT, ISO, Clean Code).
- **Procedimientos paso a paso** para tareas recurrentes (ej. "cómo implementar autenticación offline", "cómo diseñar schema Dexie").
- **Patrones aprobados** y **anti-patrones prohibidos** con justificación técnica.
- **Criterios de verificación** automatizables (checklists, linters, tests de contrato).
- **Plantillas de código** con placeholders tipados.
- **Referencias a fuentes autoritativas** (RFC, especificaciones, papers).

Clasificación en 4 niveles:
- **Nivel A (Fundamentales/Obligatorias):** Aplicables a todo proyecto (coding standards, arquitectura, seguridad básica, git workflow, gestión de contexto).
- **Nivel B (Dominio/Tecnología):** Específicas al stack (Dexie/IndexedDB, Playwright, autenticación, UI components, ERP offline).
- **Nivel C (Verificación/Debugging):** Testing adversarial, debugging estructurado, regression testing, security testing.
- **Nivel D (Soporte/Mantenimiento):** Documentación, prompt engineering, métricas, depuración de Skills.

### 1.5 Papel del QA Adversarial

El QA adversarial en SAQI **no es una fase de testing opcional ni complementaria**. Es una **fase obligatoria y deliberada** donde el sistema (humano + agente) actúa como un atacante racional que intenta romper el software mediante:

- Casos límite y entradas inesperadas (nulos, vacíos, overflow, unicode, inyección)
- Estados inválidos (transiciones prohibidas, datos corruptos, inconsistencias BD)
- Stress testing (carga extrema, concurrencia alta, agotamiento recursos)
- Chaos testing (fallos de red, latencia, particiones, crashes simulados)
- Seguridad (OWASP Top 10, inyección, auth roto, exposición datos)
- Accesibilidad y dispositivos móviles (screen readers, zoom, touch, offline-first)
- Concurrencia y persistencia (race conditions, deadlocks, transacciones parciales)
- Recuperación ante errores (rollback, retry, circuit breaker, integridad datos)

### 1.6 Retroalimentación y Mejora Continua

El ciclo **Learn → Skills** es el elemento potencialmente diferenciador de SAQI. En metodologías tradicionales, las lecciones aprendidas quedan en retrospectivas o wikis que rara vez se consultan. En SAQI:

1. Cada defecto encontrado genera una **regla de prevención** candidata para la Skill correspondiente.
2. Cada patrón de éxito se **codifica como plantilla** en la Skill de dominio.
3. Cada fallo de una Skill (falso positivo, omisión, instrucción ambigua) se **documenta y corrige** en la propia Skill.
4. Las Skills se **versionan semánticamente** (MAJOR.MINOR.PATCH) con changelog trazable a defectos/iteraciones.
5. Existe un **proceso de gobernanza** para validar, probar y deprecar Skills.

### 1.7 Caso de Estudio Inicial: Open-RootERP

Open-RootERP es el **primer caso de estudio experimental** de SAQI, desarrollado mediante un flujo de ingeniería asistido por IA y agentes utilizando OpenCode con las 17 Skills disponibles.

**Métricas observadas (datos del caso de estudio, no evidencia causal):**

| Métrica | Valor Observado |
|---------|-----------------|
| Pruebas unitarias/integración | 410+ |
| Pruebas E2E (Playwright) | 38 |
| Pruebas QA adversariales | 76 |
| Total pruebas | 524+ |
| Suites unitarias/integración | 45 |
| Módulos funcionales | 14 |
| Defectos relevantes encontrados y corregidos | 15+ |
| Skills aplicadas / disponibles | 14 / 17 |

**Nota importante:** Estas métricas son **hechos observados en un único caso de estudio**. No constituyen evidencia científica de que SAQI sea superior a otras metodologías. Se presentan como datos base para formular hipótesis que requieren validación experimental controlada.

---

## 2. Definición Formal de SAQI

> **SAQI (Skill-based Agentic Quality Iteration)** es una metodología de ingeniería de software para el desarrollo asistido por agentes de IA que estructura el ciclo de vida del software mediante:
>
> 1. Un **conjunto versionado de Skills** (artefactos de conocimiento reutilizable con reglas, procedimientos, patrones y criterios de verificación para dominios específicos de ingeniería).
> 2. Un **ciclo de vida iterativo de 14 fases** con criterios de entrada/salida definidos, responsable humano/agente explícito, y artefactos generados trazables.
> 3. Una **gestión de contexto persistente** mediante documentos vivos que funcionan como memoria externa compartida entre humano y agente.
> 4. Un **marco de QA dual** que combina testing automatizado convencional (unitario, integración, funcional, E2E) con **QA adversarial obligatorio** (pruebas destructivas, stress, caos, seguridad, accesibilidad).
> 5. Un **ciclo de retroalimentación cerrada** donde los hallazgos de cada iteración (defectos, patrones, fallos de Skills) alimentan la mejora de: código, arquitectura, tests, documentación, contexto, prompts, y **las propias Skills**.
> 6. Un **sistema de métricas** para evaluar la efectividad de la metodología y permitir comparación controlada con otros enfoques.

---

## 3. Objetivos de SAQI

### 3.1 Objetivo General

Establecer un marco metodológico reproducible para el desarrollo de software asistido por agentes de IA que **reduzca la variabilidad en la calidad del resultado** y **permita la acumulación sistemática de conocimiento de ingeniería** reutilizable entre proyectos.

### 3.2 Objetivos Específicos

| ID | Objetivo | Indicador de Logro |
|----|----------|-------------------|
| OE1 | Definir un ciclo de vida con fases, criterios y responsables explícitos | SAQI-005 documentado y aplicado en caso de estudio |
| OE2 | Codificar conocimiento de ingeniería en Skills versionadas y gobernadas | 17 Skills creadas, versionadas, con gobernanza (SAQI-003) |
| OE3 | Integrar QA adversarial como fase obligatoria con taxonomía de ataques | 76+ pruebas adversariales ejecutadas en caso de estudio |
| OE4 | Implementar gestión de contexto persistente para agentes | 4 documentos vivos mantenidos (SAQI-013) |
| OE5 | Establecer ciclo de retroalimentación Skills ← Iteración | Proceso de mejora de Skills documentado (SAQI-003) |
| OE6 | Definir métricas para evaluar y comparar la metodología | Marco de métricas SAQI-007 |
| OE7 | Documentar caso de estudio Open-RootERP separando hechos/interpretaciones | SAQI-003 completado |
| OE8 | Diseñar protocolo experimental para validación controlada | SAQI-008 completado |

---

## 4. Principios Fundamentales

| # | Principio | Descripción |
|---|-----------|-------------|
| P1 | **Arquitectura deliberada antes que código** | No se genera código sin arquitectura documentada y validada (ADRs en ARCHITECTURE.md). |
| P2 | **Conocimiento explícito y versionado (Skills)** | Lo que el agente "sabe" hacer está en Skills, no en prompts efímeros. |
| P3 | **Contexto persistente como memoria externa** | El estado del proyecto vive en documentos, no en la ventana de contexto del LLM. |
| P4 | **Verificación obligatoria en múltiples niveles** | Unit → Integration → Functional → E2E → Adversarial. Sin saltos. |
| P5 | **QA adversarial como mentalidad, no fase opcional** | El sistema debe intentar romperse a sí mismo deliberadamente. |
| P6 | **Debugging estructurado y trazable** | Hipótesis → Evidencia → Causa raíz → Fix → Test de regresión. |
| P7 | **Retroalimentación cerrada al conocimiento (Skills)** | Cada iteración mejora las Skills; las Skills mejoran las siguientes iteraciones. |
| P8 | **Humano como orquestador y validador final** | El agente ejecuta; el humano define objetivos, valida arquitectura, aprueba releases. |
| P9 | **Métricas para aprendizaje, no solo control** | Medir para mejorar la metodología, no solo para reportar. |
| P10 | **Rigor científico en afirmaciones** | Separar hechos observados, interpretaciones, e hipótesis. No causalidad sin experimento. |

---

## 5. Componentes de SAQI

| Componente | Descripción | Artefacto Principal |
|------------|-------------|---------------------|
| **Humano / Orquestador** | Define objetivos, valida arquitectura, aprueba releases, gobierna Skills | Decisiones, aprobaciones, governance |
| **Agente de IA** | Ejecuta tareas técnicas guiado por Skills y contexto | Código, tests, docs, análisis |
| **Skills** | Conocimiento de ingeniería reutilizable, versionado, gobernado | Carpeta `.opencode/skill/` |
| **Contexto Persistente** | Memoria externa estructurada del proyecto | `ARCHITECTURE.md`, `CONTEXT.md`, `PROJECT_STATE.md`, `QA_RESULTS.md` |
| **Arquitectura** | Decisiones de alto nivel, ADRs, diagramas, contratos | `ARCHITECTURE.md` |
| **Implementación** | Código fuente generado/asistido | `src/` |
| **Testing Automatizado** | Unit, Integration, Functional, E2E | `tests/unit`, `tests/integration`, `tests/e2e` |
| **QA Funcional** | Validación de requerimientos, casos de uso | `tests/functional`, `QA_RESULTS.md` |
| **QA Adversarial** | Pruebas destructivas, seguridad, stress, caos, a11y | `tests/adversarial`, `QA_RESULTS.md` |
| **Debugging** | Diagnóstico estructurado de defectos | Issues, fixes, root cause analysis |
| **Regresión** | Suite completa + tests por defecto | CI/CD pipeline |
| **Documentación** | Docs técnicas, usuario, estado, lecciones | `/docs`, `QA_RESULTS.md` |
| **Aprendizaje/Mejora** | Actualización Skills, prompts, contexto | Skills versionadas, changelogs |

---

## 6. Taxonomía de Skills (Resumen)

Ver **SAQI-003: Gobernanza de Skills** para detalle completo.

| Nivel | Nombre | Skills Ejemplo | Obligatoriedad |
|-------|--------|----------------|----------------|
| **A** | Fundamentales | coding-standards, project-architecture, secure-coding, context-manager, git-workflow, testing | **Obligatorias** todo proyecto |
| **B** | Dominio/Tecnología | database-design-sql, database-design-offline, dexie-patterns, authentication-security, html-css, ui-components, javascript-clean, erp-offline | Según stack/dominio |
| **C** | Verificación/Debugging | qa-breaker, debugging, documentation | **Obligatorias** en fases C |
| **D** | Soporte/Mantenimiento | prompt-engineering, agent-ia | Mejora continua |

---

## 7. Ciclo de Vida SAQI (14 Fases - Resumen)

Ver **SAQI-005: Definición del Proceso SAQI** para detalle completo.

| Fase | Nombre | Responsable | Entrada Clave | Salida Clave | Criterio Salida |
|------|--------|-------------|---------------|--------------|-----------------|
| 1 | **Plan** | Humano | Requerimientos | Plan de iteración, backlog | Backlog priorizado, criterios aceptación |
| 2 | **Architect** | Humano + Agente | Plan, Contexto | ADRs, ARCHITECTURE.md actualizado | Arquitectura validada, ADRs aprobados |
| 3 | **Skill Selection** | Humano | Arquitectura, Stack | Lista Skills aplicables + versiones | Skills seleccionadas, versiones registradas |
| 4 | **Build** | Agente (+ Humano) | Skills, Contexto, Arquitectura | Código, Tests unitarios | Code review pasada, tests unitarios ≥ 80% |
| 5 | **Test (Unit/Integration)** | Agente | Código | Suite unit/integration passing | 100% pass, coverage ≥ 80%, mutation ≥ 70% |
| 6 | **Test (Functional/E2E)** | Agente + Humano | Build deployable | Suite functional/E2E passing | 100% pass, escenarios críticos cubiertos |
| 7 | **Break (QA Adversarial)** | Humano + Agente (modo atacante) | Sistema testeable | Defectos clasificados, QA_RESULTS.md | Taxonomía de ataques ejecutada, 0 críticos abiertos |
| 8 | **Diagnose (Debugging)** | Agente (+ Humano) | Defectos | Root cause analysis por defecto | Causa raíz documentada, hipótesis validada |
| 9 | **Fix** | Agente | Root causes | Código corregido, tests regresión | Fix mínimo, test que fallaba ahora pasa |
| 10 | **Regression** | Agente | Fix + Suite completa | Suite completa passing | 100% pass, 0 regresiones |
| 11 | **Verify** | Humano | Todo anterior | Release candidate validado | Criterios Done cumplidos, sign-off |
| 12 | **Document** | Agente + Humano | Iteración completa | Docs actualizadas, QA_RESULTS.md | Artefactos al día, lecciones registradas |
| 13 | **Learn (Context Update)** | Humano + Agente | Resultados iteración | CONTEXT.md, PROJECT_STATE.md, ARCHITECTURE.md actualizados | Contexto refleja estado real |
| 14 | **Improve Skills** | Humano (gobernanza) | Lecciones, defectos, patrones | Skills actualizadas (versionadas) | Skills mejoradas, changelog, validación |

---

## 8. Ciclo de Retroalimentación

```
┌─────────────────────────────────────────────────────────────────┐
│                        SKILLS (v1.0)                            │
│  Reglas, Patrones, Procedimientos, Plantillas, Criterios       │
└──────────────────────────┬──────────────────────────────────────┘
                           │ Aplicar
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                        BUILD                                    │
│  Implementación asistida por agente con contexto persistente   │
└──────────────────────────┬──────────────────────────────────────┘
                           │ Genera
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                        TEST                                     │
│  Unit → Integration → Functional → E2E (Cobertura objetivo)    │
└──────────────────────────┬──────────────────────────────────────┘
                           │ Pasa
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                        BREAK (QA Adversarial)                   │
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
│                     IMPROVE SKILLS (v1.1)                       │
│  Nueva regla, plantilla fix, anti-patrón, criterio verificación │
└─────────────────────────────────────────────────────────────────┘
```

**¿Por qué "Learn → Skills" es diferenciador?**
En metodologías tradicionales, el aprendizaje queda en personas o wikis. En SAQI, el aprendizaje se **materializa en artefactos ejecutables (Skills)** que el agente *usa directamente* en la siguiente iteración. La Skill es el vehículo de transferencia de conocimiento entre iteraciones y proyectos.

---

## 9. QA Adversarial en SAQI (Resumen)

Ver **SAQI-004: Marco de QA Funcional y Adversarial** para detalle.

| Categoría de Ataque | Técnicas | Herramientas |
|---------------------|----------|--------------|
| **Casos límite / Entradas** | Boundary values, null/empty, overflow, unicode, injection, fuzzing | fast-check, custom generators |
| **Estados inválidos** | Transiciones prohibidas, datos corruptos, violación invariantes | Property-based testing, state model checking |
| **Stress / Carga** | High concurrency, large payloads, memory pressure | k6, artillery, custom load scripts |
| **Chaos** | Network partition, latency injection, process kill, clock skew | chaos-mesh, toxiproxy, custom |
| **Seguridad** | OWASP Top 10, SAST/DAST, dependency scan, authz bypass | OWASP ZAP, semgrep, npm audit, snyk |
| **Accesibilidad / Móvil** | axe-core, screen reader simulation, touch targets, viewport | Playwright + axe, Lighthouse CI |
| **Concurrencia / Persistencia** | Race conditions, deadlocks, transaction isolation, corrupt DB | Stress testing, formal verification (TLA+) |
| **Recuperación** | Crash recovery, rollback integrity, circuit breaker, retry storms | Chaos testing + assertions |

---

## 10. Gestión del Contexto (Resumen)

Ver **SAQI-013: Gestión de Contexto** para protocolo completo.

| Documento | Propósito | Actualización |
|-----------|-----------|---------------|
| `ARCHITECTURE.md` | Decisiones arquitectónicas, ADRs, diagramas, contratos | Fase 2, 13 |
| `CONTEXT.md` | Reglas de negocio, dominio, decisiones de dominio, glosario | Fase 1, 13 |
| `PROJECT_STATE.md` | Tareas, estado, bloqueos, próximos pasos, métricas | Continuo, Fase 13 |
| `QA_RESULTS.md` | Historial QA: defectos, fixes, métricas, lecciones, tendencias | Fase 7, 10, 12, 13 |
| `SESSION.md` (opcional) | Log de sesión actual: comandos, decisiones, hallazgos | Por sesión |

**Protocolo de Checkpointing (Skill A-context-manager):**
- Monitoreo de uso de contexto (target < 80%)
- Auto-checkpoint al 80%: guardar estado, resumir, limpiar contexto
- Re-hidratación al inicio de sesión: leer 4 documentos → resumir → continuar

---

## 11. Gobernanza de Skills (Resumen)

Ver **SAQI-003: Gobernanza y Ciclo de Vida de Skills** para detalle.

| Etapa | Actividad | Responsable | Artefacto |
|-------|-----------|-------------|-----------|
| **Crear** | Identificar necesidad → Draft Skill → Revisión técnica | Humano (experto dominio) | Skill v0.1 (draft) |
| **Validar** | Prueba en caso real → Métricas → Aprobación | Humano + Agente | Skill v1.0 (released) |
| **Versionar** | SemVer (MAJOR.MINOR.PATCH) + Changelog | Humano (gobernanza) | Git tags, CHANGELOG.md |
| **Probar** | Tests de Skill (contract tests, scenario tests) | Agente (CI) | Skill test results |
| **Mejorar** | Defecto → Regla prevención → Patch Skill | Humano | Skill v1.x / v2.0 |
| **Deprecar** | Obsoleta, reemplazada, dañina → Mark deprecated | Humano (gobernanza) | Skill deprecated, migration guide |

**Principio:** Una Skill incorrecta no debe propagar errores a múltiples proyectos. Gobernanza evita deriva.

---

## 12. Métricas SAQI (Resumen)

Ver **SAQI-007: Marco de Métricas** para catálogo completo.

| Categoría | Métricas Clave |
|-----------|----------------|
| **Calidad de Producto** | Defect Escape Rate, Regression Rate, Test Pass Rate, Mutation Score, Coverage (line/branch/func), OWASP Coverage, Accessibility Score |
| **Calidad de Proceso** | Iteration Cycle Time, Time to Fix, Defect Detection Rate por fase, Prompt Iterations per Task, Human Interventions per Iteration |
| **Efectividad Skills** | Skills Applied per Iteration, Skills Modified per Iteration, Skill Reuse Rate (proyectos), Skill Defect Contribution (defectos que Skill previno/no previno) |
| **Coste/Eficiencia** | Tokens Consumed per Iteration, Cost per Iteration, Cost per Defect Found, Cost per Defect Fixed |
| **Contexto** | Context Refresh Rate, Context Staleness Index, Checkpoint Frequency |
| **Aprendizaje** | Recurring Defect Rate (mismo tipo), New Skill Rules per Iteration, Skill Version Bumps per Iteration |

---

## 13. Caso de Estudio: Open-RootERP

Ver **SAQI-003: Documentación del Caso de Estudio Open-RootERP** para detalle completo.

**Separación estricta:**

| Categoría | Contenido |
|-----------|-----------|
| **HECHOS OBSERVADOS** | 524+ tests ejecutados, 15+ defectos corregidos, 14 skills aplicadas, arquitectura documentada, 14 módulos funcionales, métricas de cobertura registradas |
| **INTERPRETACIONES** | "El QA adversarial detectó defectos que unit tests no cubrían", "Las Skills redujeron variabilidad en patrones de BD", "El contexto persistente evitó pérdida de decisiones arquitectónicas" |
| **HIPÓTESIS** | "SAQI reduce Defect Escape Rate vs prompting ad-hoc", "Skills versionadas permiten transferencia entre proyectos", "Checkpointing de contexto reduce tokens/sesión" |

**No se afirma causalidad.** Se registran observaciones para diseñar experimentos (SAQI-008).

---

## 14. Comparación Conceptual (Resumen)

Ver **SAQI-010: Comparación con Enfoques Existentes** para tabla detallada.

| Enfoque | Complementa SAQI | SAQI Aporta Adicional |
|---------|------------------|----------------------|
| **Agile/Scrum** | Gestión de proyecto, iteraciones | Skills técnicas, QA adversarial, agente como actor, contexto persistente |
| **TDD** | Tests first | Skills guían implementación, QA adversarial obligatorio, arquitectura deliberada |
| **DevOps** | CI/CD, automatización | Skills como infraestructura de conocimiento, governance de Skills |
| **DevSecOps** | Seguridad en pipeline | Security Skills (Nivel A), QA adversarial seguridad, secure coding rules en Skills |
| **Continuous Testing** | Testing continuo | QA adversarial como fase distinta y obligatoria, taxonomía de ataques |
| **Agentic SE** | Agentes autónomos | Metodología **sobre** el agente: ciclo 14 fases, Skills, contexto, QA adversarial |
| **Context Engineering** | Contexto para LLM | Documentos vivos estructurados, checkpointing, re-hidratación, gobernanza |
| **Harness Engineering** | Andamiaje de pruebas | Skills como harness de conocimiento, QA adversarial como harness de calidad |

---

## 15. Novedad Potencial

| Aspecto | Estado en Literatura | Aporte SAQI |
|---------|---------------------|-------------|
| Skills como artefactos versionados de ingeniería | Parcial (prompt libraries, agent skills) | Gobernanza, versionado SemVer, trazabilidad a defectos, testing de Skills |
| Ciclo 14 fases con QA adversarial obligatorio | No integrado así | Integración en ciclo único con criterios entrada/salida |
| Contexto persistente 4 documentos + checkpointing | Parcial (RAG, memory) | Estructura fija, protocolo operativo, re-hidratación |
| Retroalimentación Learn → Skills (materializada) | Raro (generalmente humano) | Skill como vehículo de transferencia inter-iteración/proyecto |
| Taxonomía QA adversarial integrada | Separada (security testing, chaos engineering) | Unificada en fase "Break" con criterios obligatorios |

**No se afirma novedad absoluta.** Ver SAQI-002 y SAQI-016 para antecedentes bibliográficos.

---

## 16. Limitaciones (Resumen)

Ver **SAQI-009: Amenazas a Validez y Limitaciones** para análisis completo.

| Categoría | Limitación |
|-----------|------------|
| **Dependencia LLM** | Calidad variable entre modelos; alucinaciones; costo tokens |
| **Agente** | Capacidad variable; tool use imperfecto; loop recovery |
| **Skills** | Sobreajuste a dominio; mantenimiento; gobernanza requiere humano experto |
| **Contexto** | Deuda de contexto; documentos desactualizados; checkpoint overhead |
| **QA Adversarial** | Falsos positivos; costo tiempo; requiere creatividad adversarial |
| **Generalización** | Un solo caso de estudio (Open-RootERP); stack específico (TS/React/Dexie/Playwright) |
| **Riesgo IA genera código y tests** | Mismo modelo puede tener mismos puntos ciegos; mitigación: QA adversarial humano + agente en modo atacante |

---

## 17. Amenazas a la Validez (Resumen)

Ver **SAQI-009** para análisis completo.

| Amenaza | Tipo | Mitigación Parcial |
|---------|------|-------------------|
| **Single Case Study** | Validez externa | Replicar en ≥3 proyectos distintos (SAQI-021) |
| **Researcher Bias** | Validez interna | Protocolo ciego, métricas automáticas, replicación independiente |
| **Confounding Variables** | Validez interna | Controlar: experiencia dev, modelo LLM, complejidad tarea |
| **Hawthorne Effect** | Validez constructo | Métricas objetivas, no auto-reportes |
| **Skill Maturity Confound** | Validez interna | Versionar Skills, medir madurez, comparar versiones |

---

## 18. Propuesta Experimental (Resumen)

Ver **SAQI-008: Protocolo Experimental** para diseño completo.

**Diseño:** Ensayo controlado aleatorizado (RCT) o cuasi-experimental.

| Grupo | Metodología | N |
|-------|-------------|---|
| **A (Control)** | Desarrollo asistido por IA con prompting ad-hoc (sin Skills, sin QA adversarial obligatorio, sin contexto persistente estructurado) | ≥ 10 tareas/proyecto |
| **B (Tratamiento)** | Desarrollo mediante SAQI (Skills, 14 fases, QA adversarial, contexto persistente) | ≥ 10 tareas/proyecto |

**Variables Dependientes Principales:**
- Defect Escape Rate (producción)
- Regression Rate
- Cycle Time per Iteration
- Human Intervention Count
- Cost per Iteration (tokens + tiempo)
- Maintainability Index (post-iteración)

**Análisis:** Mann-Whitney U (no paramétrico), tamaño efecto (Cliff's δ), intervalos confianza 95%.

---

## 19. Trabajo Futuro

| Área | Acción |
|------|--------|
| **Replicación** | Aplicar SAQI en ≥ 3 proyectos distintos (dominio, stack, equipo) |
| **Validación Experimental** | Ejecutar protocolo SAQI-008 con grupos control/tratamiento |
| **Automatización** | Tooling para: checkpointing contexto, Skill testing, métricas auto-recolección |
| **Skill Marketplace** | Repositorio de Skills versionadas, valoradas, con evidencia de uso |
| **Integración CI/CD** | Gates de calidad SAQI en pipeline (Skill validation, adversarial test gate) |
| **Modelos Locales** | Optimizar SAQI para modelos 3B-7B (quantization, routing, skill selection) |
| **Formalización** | Especificación formal de Skills (DSL), verificación de consistencia Skills |

---

## 20. Conclusiones

SAQI propone una **metodología estructurada, reproducible y medible** para el desarrollo de software asistido por agentes de IA. Sus elementos diferenciadores son:

1. **Skills como infraestructura de conocimiento** versionada y gobernada, no prompts efímeros.
2. **Ciclo de vida de 14 fases** con QA adversarial obligatorio integrado.
3. **Contexto persistente** operativo mediante 4 documentos vivos y protocolo de checkpointing.
4. **Retroalimentación cerrada Learn → Skills** que materializa el aprendizaje en artefactos ejecutables.
5. **Métricas y protocolo experimental** para validación empírica, no solo anecdótica.

**SAQI no afirma superioridad.** Presenta una propuesta metodológica con un caso de estudio inicial (Open-RootERP) y un marco para su evaluación científica rigurosa. El camino es: **DEFINIR → DOCUMENTAR → APLICAR → MEDIR → COMPARAR → ITERAR → VALIDAR.**

---

## 21. Glosario (Resumen)

Ver **SAQI-015: Glosario y Terminología** para versión completa.

| Término | Definición |
|---------|------------|
| **SAQI** | Skill-based Agentic Quality Iteration |
| **Skill** | Artefacto de conocimiento de ingeniería reutilizable, versionado, con reglas, procedimientos, patrones, plantillas y criterios de verificación |
| **Agente de IA** | Sistema de software basado en LLM que usa herramientas para ejecutar tareas de ingeniería guiado por Skills y contexto |
| **Contexto Persistente** | Memoria externa estructurada (ARCHITECTURE.md, CONTEXT.md, PROJECT_STATE.md, QA_RESULTS.md) |
| **QA Adversarial** | Fase obligatoria de pruebas destructivas deliberadas (edge cases, stress, chaos, security, a11y) |
| **Ciclo Learn → Skills** | Retroalimentación donde hallazgos de iteración mejoran Skills versionadas |
| **Checkpointing** | Guardado automático de estado al 80% uso de contexto con re-hidratación al inicio de sesión |
| **Defect Escape Rate** | Defectos encontrados en producción / Total defectos |
| **Regression Rate** | Defectos reintroducidos / Total fixes |
| **Skill Governance** | Proceso de crear, validar, versionar, probar, mejorar, deprecrar Skills |

---

## 22. Referencias Bibliográficas (Resumen)

Ver **SAQI-016: Referencias Bibliográficas Completas**.

Principales: Kitchenham (EBM SE), Martin (Clean Architecture), Kleppmann (DDIA), Zeller (Debugging), OWASP/ASVS, ISO 25010, Liu et al. (Prompting), Yao et al. (ReAct), Forsgren et al. (Accelerate/DORA).

---

## 23. Documentos Relacionados (Índice SAQI)

| Doc | Título | Ubicación |
|-----|--------|-----------|
| SAQI-001 | Resumen Ejecutivo y Definición | `docs/01-foundations/SAQI-001-resumen-ejecutivo-definicion.md` |
| SAQI-002 | Fundamentos Teóricos y Revisión Bibliográfica | `docs/01-foundations/SAQI-002-fundamentos-teoricos-revision-bibliografica.md` |
| SAQI-003 | Gobernanza y Ciclo de Vida de Skills | `docs/02-methodology/SAQI-003-gobernanza-skills.md` |
| SAQI-004 | Marco de QA Funcional y Adversarial | `docs/02-methodology/SAQI-004-qa-framework.md` |
| SAQI-005 | Definición del Proceso SAQI (14 Fases) | `docs/02-methodology/SAQI-005-proceso-saqi.md` |
| SAQI-006 | Especificación de Arquitectura de Agente | `docs/02-methodology/SAQI-006-agent-architecture.md` |
| SAQI-007 | Marco de Atributos de Calidad y Métricas | `docs/04-validation/SAQI-007-metricas.md` |
| SAQI-008 | Diseño Experimental y Protocolo de Validación | `docs/04-validation/SAQI-008-protocolo-experimental.md` |
| SAQI-009 | Amenazas a Validez y Limitaciones | `docs/04-validation/SAQI-009-amenazas-validez-limitaciones.md` |
| SAQI-010 | Comparación con Enfoques Existentes | `docs/01-foundations/SAQI-010-comparacion-enfoques.md` |
| SAQI-011 | Guía de Aplicación: Caso Open-RootERP | `docs/03-case-study/SAQI-011-guia-aplicacion-open-rooterp.md` |
| SAQI-012 | Paquete de Replicación y Guía | `docs/03-case-study/SAQI-012-paquete-replicacion.md` |
| SAQI-013 | Gestión de Contexto Persistente | `docs/02-methodology/SAQI-013-gestion-contexto.md` |
| SAQI-014 | Trabajo Futuro y Hoja de Ruta | `docs/06-references/SAQI-014-trabajo-futuro.md` |
| SAQI-015 | Glosario y Terminología | `docs/06-references/SAQI-015-glosario.md` |
| SAQI-016 | Referencias Bibliográficas Completas | `docs/06-references/SAQI-016-referencias.md` |
| SAQI-017 | Apéndice A: Arquitectura Open-RootERP | `docs/05-appendices/SAQI-017-apendice-a-arquitectura-open-rooterp.md` |
| SAQI-018 | Apéndice B: Plantillas Configuración Agente | `docs/05-appendices/SAQI-018-apendice-b-plantillas-agente.md` |
| SAQI-019 | Apéndice C: Plantillas Recolección Métricas | `docs/05-appendices/SAQI-019-apendice-c-plantillas-metricas.md` |
| SAQI-020 | Apéndice D: Protocolos de Experimento | `docs/05-appendices/SAQI-020-apendice-d-protocolos-experimento.md` |
| SAQI-021 | Apéndice E: Modelos de Amenaza | `docs/05-appendices/SAQI-021-apendice-e-modelos-amenaza.md` |
| README | Presentación Pública y Resumen SAQI | `README.md` |

---
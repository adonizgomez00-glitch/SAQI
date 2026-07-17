# SAQI-010: Comparación Conceptual con Enfoques Existentes

**Versión:** 1.0
**Fecha:** 2026-07-17
**Estado:** Borrador inicial
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Posicionamiento Metodológico

SAQI **no pretende reemplazar** metodologías establecidas (Agile, TDD, DevOps, etc.). Se posiciona como un **marco metodológico específico para desarrollo asistido por agentes de IA** que **complementa y extiende** prácticas existentes, abordando vacíos que emergen cuando un agente de IA participa activamente en el ciclo de vida del software.

**Principio**: SAQI = Ingeniería de Software Clásica + Agentic AI + Knowledge Management (Skills) + Adversarial QA + Context Persistence.

---

## 2. Tabla Comparativa Detallada

| Dimensión | **Agile / Scrum** | **TDD** | **DevOps / CI/CD** | **DevSecOps** | **Continuous Testing** | **Agentic SE** (AutoGPT, CrewAI, LangGraph) | **Context Engineering** | **Harness Engineering** | **SAQI** |
|-----------|-------------------|---------|-------------------|---------------|------------------------|---------------------------------------------|------------------------|------------------------|----------|
| **Origen/Foco** | Gestión proyecto, entrega incremental | Diseño guiado por tests | Automatización entrega, cultura | Seguridad integrada en pipeline | Testing continuo en pipeline | Agentes autónomos multi-paso | Optimización contexto LLM | Andamiaje para evaluación agentes | **Metodología completa para dev asistido por IA** |
| **Ciclo de vida** | Sprints (1-4 sem) | Red-Green-Refactor (minutos) | Pipeline continuo (commit→prod) | Pipeline + security gates | Test pyramid en CI | Loop: Plan→Act→Observe→Reflect | Prompt + RAG + Memory | Test harness + evaluación | **14 fases iterativas con gates** |
| **Artefacto central** | User Story, Backlog, Incremento | Test (unitario) | Pipeline, Artifact, Deploy | Policy, Scan, Gate | Test Suite, Coverage | Agent, Tool, Prompt, Memory | Context Window, RAG, Checkpoint | Benchmark, Scenario, Metric | **Skills (versionadas, gobernadas)** |
| **Rol Humano** | PO, SM, Dev Team | Developer (escribe test+code) | Dev + Ops (shared responsibility) | Sec Engineer + Dev | QA + Dev | **Orquestador / Supervisor** | Prompt Engineer | Harness Designer | **Orquestador + Gobernador Skills + QA Lead** |
| **Rol Agente IA** | No definido | No definido | No definido | No definido | No definido | **Autónomo (loop completo)** | Asistente pasivo (RAG) | Sujeto de evaluación | **Ejecutor en modos (Builder, Tester, Adversarial, Analyst, Documenter)** |
| **Gestión Conocimiento** | Retrospectivas, Wikis, Tribales | Código + Tests como docs | Docs as code, Runbooks | Threat models, Policies | Test reports, Dashboards | **Prompt libraries, Memory, RAG** | **Context docs, Checkpoints** | Benchmark suites | **Skills versionadas + 4 docs vivos + Checkpointing** |
| **QA / Testing** | Sprint review, DoD | Unit tests obligatorios | Pipeline gates (unit, int, e2e) | SAST/DAST/SCA en pipeline | Test pyramid continua | Ad-hoc, emergente | No definido | Benchmarking agents | **Dual QA: Convencional (L1-L4) + Adversarial obligatorio (L5/Fase 7)** |
| **Seguridad** | No prescriptiva | No prescriptiva | Gates en pipeline | **Integral (Shift-left)** | Security tests en pyramid | Ad-hoc | No definido | Red-teaming agents | **Nivel A Skill + ATK-SEC obligatorio Fase 7** |
| **Arquitectura** | Emergente / Evolutiva | Emergente (refactor) | Arquitectura para despliegue | Arquitectura segura | Testable architecture | Emergente | No definido | No definido | **Clean/Hexagonal obligatoria (Skill A-project-architecture + ADRs)** |
| **Métricas** | Velocity, Burndown, Lead time | Coverage, Defect rate | DORA (Lead time, Deploy freq, MTTR, CFR) | Vuln count, MTTR sec, Policy compliance | Coverage, Mutation, Flakiness | Task success, Tool use, Loops | Token usage, Context retention | Pass rate, Latency, Cost | **SAQI-007: 6 categorías (Producto, Proceso, Skills, Coste, Contexto, Aprendizaje)** |
| **Retroalimentación** | Retrospectiva sprint | Refactor continuo | Post-mortem, Monitoring | Incident response | Test analytics | Self-reflection (prompt) | Checkpointing | Benchmark comparison | **Ciclo cerrado Learn→Skills (Fase 14) + Context Update (Fase 13)** |
| **Escalabilidad** | Equipos 5-9, SAFe/LeSS para escala | Equipo | Organizacional | Organizacional | Pipeline | Multi-agente | Sesión individual | Benchmark suite | **Proyecto → Programa (Skills reutilizables)** |

---

## 3. Análisis de Complementariedad

### 3.1 SAQI + Agile/Scrum
| Agile Proporciona | SAQI Aporta Adicional |
|-------------------|----------------------|
| Gestión de backlog, sprints, roles, ceremonias | **Estructura técnica dentro del sprint**: 14 fases mapeables a sprint (1 iteración ≈ 1 sprint) |
| Priorización negocio, stakeholder engagement | **Skills técnicas** que reducen variabilidad implementación |
| Retrospectiva de proceso | **Retroalimentación técnica materializada en Skills** (Fase 14) |
| Definition of Done | **DoD técnica cuantitativa** (coverage, mutation, adversarial gate, security gate) |

**Integración**: Un Sprint Agile = 1-2 Iteraciones SAQI. PO define backlog (Fase 1), Equipo ejecuta Fases 2-14, Retrospectiva = Fase 13+14.

### 3.2 SAQI + TDD
| TDD Proporciona | SAQI Aporta Adicional |
|-----------------|----------------------|
| Ciclo Red-Green-Refactor a nivel unidad | **TDD asistido por agente** (Fase 4: agente genera código + test simultáneo) |
| Tests unitarios como especificación | **Testing multi-nivel obligatorio**: Unit → Integration → Functional → E2E → **Adversarial** |
| Diseño emergente por refactor | **Arquitectura deliberada previa** (Fase 2) + **Patrones aprobados en Skills** |
| Cobertura como métrica | **Mutation testing + Property-based testing + Contract tests** obligatorios |

**Integración**: SAQI **incorpora TDD en Fase 4-5** pero lo extiende a niveles superiores y añade QA adversarial.

### 3.3 SAQI + DevOps / CI/CD
| DevOps Proporciona | SAQI Aporta Adicional |
|-------------------|----------------------|
| Pipeline automatizado, infraestructura como código | **Gates de calidad metodológicos** (no solo técnicos): H1-H8 human gates |
| Métricas DORA | **Métricas SAQI-007** (incluye DORA + Skills + Contexto + Aprendizaje) |
| Despliegue continuo | **Release gate Fase 11** (Adversarial pass + Human sign-off) |
| Cultura "you build it you run it" | **Agente construye, Humano gobierna** (responsabilidad compartida clara) |

**Integración**: Pipeline CI/CD ejecuta Fases 5, 6, 7, 10 automatizadas. Gates H1-H8 son approvals manuales en pipeline.

### 3.4 SAQI + DevSecOps
| DevSecOps Proporciona | SAQI Aporta Adicional |
|----------------------|----------------------|
| Security shift-left en pipeline | **Security by design en Skills Nivel A** (A-secure-coding obligatoria desde Fase 3) |
| SAST/DAST/SCA automatizados | **ATK-SEC categoría obligatoria en Fase 7** (más allá de scans: business logic bypass, authz, chaos) |
| Compliance, policy as code | **Threat models por módulo (SAQI-021) + Security requirements en ACs** |

**Integración**: SAQI **internaliza seguridad** como skill transversal, no solo gate de pipeline.

### 3.5 SAQI + Continuous Testing
| Continuous Testing Proporciona | SAQI Aporta Adicional |
|-------------------------------|----------------------|
| Test pyramid en CI | **Test pyramid + Adversarial layer (L5) obligatorio** |
| Coverage, mutation, flakiness metrics | **Adversarial metrics (ADR, ATK coverage) + Skill effectiveness metrics** |
| Test automation | **Agent-generated tests + Human-designed adversarial scenarios** |

**Integración**: SAQI **extiende la pirámide** con capa L5 adversarial y métricas de efectividad de testing.

### 3.6 SAQI + Agentic Software Engineering
| Agentic SE Proporciona | SAQI Aporta Adicional |
|------------------------|----------------------|
| Agentes autónomos (planning, tool use, memory) | **Metodología SOBRE el agente**: 14 fases, gates humanos, Skills gobernadas |
| Prompt engineering, RAG, memory | **Context management sistemático** (4 docs + checkpointing protocol) |
| Multi-agent orchestration | **Single agent con modos especializados** (Builder, Tester, Adversarial, Analyst, Documenter) |
| Benchmarks (SWE-bench, etc.) | **Validación empírica controlada** (RCT SAQI-008) vs solo benchmarks |

**Diferencia fundamental**: Agentic SE = **cómo construir agentes**. SAQI = **cómo usar agentes para ingeniería de software con rigor**.

### 3.7 SAQI + Context Engineering
| Context Engineering Proporciona | SAQI Aporta Adicional |
|--------------------------------|----------------------|
| RAG, prompt optimization, context window mgmt | **4 documentos vivos estandarizados** (Architecture, Context, Project State, QA Results) |
| Checkpointing, summarization | **Protocolo operativo** (70% resumen, 80% checkpoint, re-hidratación) |
| Long-context strategies | **Contexto persistente entre sesiones** + **Context Staleness Index** métrica |

**Integración**: SAQI **operacionaliza Context Engineering** para proyectos de ingeniería (no solo chat).

### 3.8 SAQI + Harness Engineering
| Harness Engineering Proporciona | SAQI Aporta Adicional |
|--------------------------------|----------------------|
| Test harnesses, evaluation frameworks | **Skills como "harness de conocimiento"** (reglas, patrones, verificables) |
| Benchmarking agents | **Benchmarking metodología** (RCT con métricas SAQI-007) |
| Scenario-based evaluation | **Threat models + ATK taxonomy** como escenarios adversariales obligatorios |

---

## 4. Novedad Potencial de SAQI (Gap Analysis)

| Elemento | Estado en Literatura/Práctica | Aporte SAQI |
|----------|------------------------------|-------------|
| **Skills como artefactos versionados, gobernados, testables de ingeniería** | Parcial: Prompt libraries, agent skills (LangChain, OpenCode), pero sin gobernanza formal, versionado SemVer, contract tests, trazabilidad a defectos | **Formalización completa**: ciclo vida, gobernanza, testing, métricas efectividad, registry |
| **Ciclo 14 fases con QA adversarial obligatorio integrado** | No integrado así: QA adversarial existe (chaos eng, security testing) pero separado, opcional, final | **Integración en gate de release (Fase 7)** con taxonomía 8 categorías ATK-* obligatorias |
| **Contexto persistente 4-docs + checkpointing protocol** | Parcial: RAG, memory, context windows; pero no estructura fija 4-docs + protocolo operativo | **Estandarización operativa** para proyectos ingeniería |
| **Retroalimentación cerrada Learn → Skills (materializada)** | Raro: Lecciones → wiki/personas; no → artefactos ejecutables que agente usa | **Skills como vehículo de transferencia** inter-iteración/proyecto con trazabilidad |
| **Métricas de efectividad de Skills** | No existe | **Skill Defect Contribution, Prevention Rate, Reuse Rate, Version Bumps** |
| **Protocolo experimental RCT para metodologías IA** | Pocos: estudios observacionales, benchmarks agentes | **Diseño RCT replicable** con variables dependientes definidas operacionalmente |

---

## 5. Conclusión de Comparación

**SAQI no compite** con Agile, TDD, DevOps, DevSecOps, Continuous Testing. **Los complementa** proporcionando:

1. **Estructura metodológica** para el "cómo" usar agentes de IA dentro de sprints/pipelines
2. **Artefactos de conocimiento gobernados (Skills)** que reducen dependencia de prompt engineering experto
3. **QA Adversarial obligatorio** como práctica de ingeniería, no opcional
4. **Gestión de contexto persistente estandarizada** para continuidad entre sesiones
5. **Ciclo de mejora del conocimiento (Learn→Skills)** materializado en artefactos versionados
6. **Marco de validación empírica** para medir si el enfoque realmente funciona

**Riesgo**: Complejidad añadida. **Mitigación**: Adopción incremental (empezar Nivel A Skills + Fases 1-7-11), tooling automatizado (SAQI-014).

---

## 6. Referencias Internas

- SAQI-001: Resumen Ejecutivo (Tabla comparativa resumen)
- SAQI-002: Fundamentos Teóricos (Literatura base)
- SAQI-004: Marco QA (Fase 7 vs otros testing)
- SAQI-005: Proceso SAQI (Fases mapeables a sprints)
- SAQI-007: Marco Métricas (vs DORA, ISO 25010)
- SAQI-008: Protocolo Experimental (vs benchmarks agentes)

---

## 7. Referencias Externas Seleccionadas

1. **Agile/Scrum**: Schwaber, K. & Sutherland, J. (2020). *The Scrum Guide*.
2. **TDD**: Beck, K. (2003). *Test-Driven Development: By Example*. Addison-Wesley.
3. **DevOps**: Humble, J. & Farley, D. (2010). *Continuous Delivery*. Addison-Wesley. Kim, G., et al. (2016). *The DevOps Handbook*.
4. **DevSecOps**: OWASP DevSecOps Guideline. NIST SP 800-218 (SSDF).
5. **Continuous Testing**: Clapp, J. & Ducharme, L. (2016). *Continuous Testing for DevOps Professionals*.
6. **Agentic SE**: Wang, G., et al. (2023). "Voyager." *arXiv*. Zhou, A., et al. (2023). "Agents." *arXiv*. SWE-bench (Jimenez et al., 2023).
7. **Context Engineering**: Liu, P., et al. (2023). "Pre-train, Prompt, and Predict." *ACM Computing Surveys*. Lewis, P., et al. (2020). "RAG." *NeurIPS*.
8. **Harness Engineering**: Benchmarking frameworks: SWE-bench, AgentBench, ToolBench.
9. **Chaos Engineering**: Basiri, A., et al. (2016). "Chaos Engineering." *IEEE Software*.
10. **Security Testing**: OWASP Testing Guide v4.2. OWASP ASVS 4.0.3.
# SAQI-015: Glosario y Terminología

**Versión:** 1.0
**Fecha:** 2026-07-17
**Estado:** Borrador inicial
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Términos Fundamentales SAQI

| Término | Definición |
|---------|------------|
| **SAQI** | **Skill-based Agentic Quality Iteration** — Metodología experimental de ingeniería de software para desarrollo asistido por agentes de IA, basada en Skills versionadas, ciclo de 14 fases, QA adversarial obligatorio, contexto persistente y retroalimentación cerrada Learn→Skills. |
| **Skill** | Artefacto de conocimiento de ingeniería **reutilizable, versionado (SemVer), gobernado y comprobable**, que codifica reglas (MUST/SHALL), procedimientos, patrones, plantillas y criterios de verificación para un dominio específico. No es un prompt; es infraestructura de conocimiento. |
| **Agente de IA (SAQI Agent)** | Sistema de software basado en LLM con capacidades de **tool use** (fs, shell, git, browser, test runners), **planificación**, **gestión de estado**, y **modos de operación especializados** (Builder, TestEngineer, Adversarial, Analyst, Documenter). Ejecuta fases SAQI bajo supervisión humana. |
| **Orquestador Humano (Human Orchestrator)** | Persona responsable de: definir objetivos, validar arquitectura, aprobar gates críticos (H1-H8), gobernar Skills, firmar releases. **No** escribe código rutinario; **sí** toma decisiones de diseño, riesgo y calidad. |
| **Contexto Persistente (Persistent Context)** | Memoria externa estructurada del proyecto, materializada en **4+1 documentos vivos** (`ARCHITECTURE.md`, `CONTEXT.md`, `PROJECT_STATE.md`, `QA_RESULTS.md`, `SESSION.md`) que sobreviven entre sesiones y son leídos/escritos por humano y agente. |
| **Checkpointing** | Protocolo (Skill `A-context-manager`) para **guardar estado** cuando la ventana de contexto del LLM supera umbrales (70% resumen preventivo, 80% checkpoint obligatorio), permitiendo **re-hidratación** precisa en la siguiente sesión. |
| **QA Adversarial (Adversarial QA)** | Fase obligatoria (Fase 7: BREAK) donde el sistema (humano + agente en modo atacante) **intenta romper deliberadamente** el software mediante taxonomía de 8 categorías ATK-* (Inputs, Estados, Stress, Chaos, Seguridad, A11y, Concurrencia, Recuperación). |
| **Ciclo Learn → Skills** | Retroalimentación cerrada donde hallazgos de cada iteración (defectos, patrones, gaps) se **materializan en mejoras de Skills versionadas** (nuevas reglas, plantillas, anti-patrones), que el agente usa en la siguiente iteración/proyecto. |
| **Iteración SAQI** | Una pasada completa por las **14 fases** (Plan → Architect → Skill Selection → Build → Test L1-L2 → Test L3-L4 → Break → Diagnose → Fix → Regression → Verify → Document → Learn → Improve Skills). |

---

## 2. Fases del Proceso SAQI (14 Fases)

| Fase | Nombre Corto | Descripción |
|------|--------------|-------------|
| **1** | **Plan** | Definir alcance, ACs testables, DoD, riesgos |
| **2** | **Architect** | ADRs, diagramas C4, contratos puertos, threat models |
| **3** | **Skill Selection** | Seleccionar, versionar, configurar Skills Nivel A-D |
| **4** | **Build** | Implementación asistida (código + tests unitarios simultáneos) |
| **5** | **Test (Unit/Int)** | L1-L2: Unit + Integration, coverage ≥80/75/80, mutation ≥70% |
| **6** | **Test (Func/E2E)** | L3-L4: Functional + E2E (Playwright), CUJs, a11y, perf, offline |
| **7** | **Break (Adversarial)** | **L5**: QA adversarial 8 categorías ATK-*, gate de release |
| **8** | **Diagnose** | Debugging estructurado: reproducir → aislar → hipotetizar → evidenciar → causa raíz |
| **9** | **Fix** | Cambio mínimo + test prevención + commit convencional |
| **10** | **Regression** | Suite completa L1-L5 (áreas afectadas), regression rate <2% |
| **11** | **Verify** | Gates: DoD, métricas, security, perf, a11y, docs, sign-off PO+TL+QA |
| **12** | **Document** | ITERATION_REPORT, QA_RESULTS, ARCHITECTURE, CONTEXT, CHANGELOG |
| **13** | **Learn (Context Update)** | Sincronizar 4 docs vivos, checkpoint contexto |
| **14** | **Improve Skills** | Análisis defectos→reglas, patrones→plantillas, gaps→mejoras, versionado SemVer |

---

## 3. Taxonomía de Skills (Niveles A-D)

| Nivel | Nombre | Skills Ejemplo | Obligatoriedad |
|-------|--------|----------------|----------------|
| **A** | **Fundamentales / Core** | `A-coding-standards`, `A-project-architecture`, `A-secure-coding`, `A-context-manager`, `A-testing`, `C-qa-breaker`, `D-git-workflow` | **Obligatorias en TODO proyecto SAQI** |
| **B** | **Dominio / Tecnología** | `B-html-css`, `B-javascript-clean`, `B-ui-components`, `B-authentication-security`, `C-database-design-offline`, `C-dexie-patterns`, `D-erp-offline` | Según stack y dominio |
| **C** | **Verificación / Debugging** | `C-debugging`, `C-documentation` | Obligatorias en fases verificación (5,6,7,8,10,12) |
| **D** | **Soporte / Mejora Continua** | `D-prompt-engineering`, `D-Agente-IA` | Transversales, opcionales pero recomendadas |

---

## 4. Taxonomía de Ataques QA Adversarial (ATK-*)

| ID | Categoría | Descripción |
|----|-----------|-------------|
| **ATK-INPUT** | Entradas y Casos Límite | Boundary values, fuzzing, Unicode, null/empty, overflow, injection (XSS, NoSQL, template), payloads grandes |
| **ATK-STATE** | Estados Inválidos y Transiciones | Transiciones prohibidas, IDs inexistentes, FKs rotas, datos corruptos, doble submit, lost update |
| **ATK-STRESS** | Stress y Carga Extrema | 1000+ req concurrentes, payloads 100MB+, 10k+ registros, quota IndexedDB, CPU main thread |
| **ATK-CHAOS** | Chaos Engineering | Network: offline, latency, partition; Clock: skew, TZ; Process: kill tab, crash mid-transaction; Storage: quota, corruption |
| **ATK-SEC** | Seguridad (OWASP Top 10 + ASVS) | A01-A10: Access control, crypto, injection, insecure design, misconfig, vulnerable components, auth/session, integrity, logging, SSRF |
| **ATK-A11Y** | Accesibilidad y Móvil | axe-core WCAG 2.1 AA, screen reader, zoom 200/400%, touch targets 44px, viewport 375×667, landscape/portrait, reduce motion |
| **ATK-CONC** | Concurrencia y Persistencia | Race conditions, deadlocks, aislamiento transaccional, sync queue reordering, partial sync, IndexedDB abort |
| **ATK-REC** | Recuperación y Resiliencia | Crash recovery, rollback integridad, circuit breaker, retry storms, data integrity after crash, graceful degradation |

---

## 5. Clasificación de Defectos (Taxonomía SAQI)

### 5.1 Severidad

| Nivel | Etiqueta | Definición | SLA Fix |
|-------|----------|------------|---------|
| **P0** | **Critical** | Pérdida datos, breach seguridad, crash irrecuperable, funcionalidad core rota | < 4h (hotfix) |
| **P1** | **High** | Funcionalidad importante rota, degradación severa, vulnerabilidad media | < 24h |
| **P2** | **Medium** | Bug funcional menor, UX degradada, performance bajo presupuesto | < 1 iteración |
| **P3** | **Low** | Cosmético, typo, mejora menor, deuda técnica documentada | Backlog |

### 5.2 Origen (Fase Detección)

| Código | Fase | Descripción |
|--------|------|-------------|
| **UT** | 5 (Unit/Integration) | Test unitario/integración falló |
| **FT** | 5 (Functional) | Test funcional falló |
| **E2E** | 6 (E2E) | Test E2E falló |
| **ADV** | **7 (Adversarial)** | QA adversarial encontró |
| **PROD** | Post-release | Usuario reportó (escape) |
| **REG** | 10 (Regression) | Regresión detectada |

### 5.3 Tipo Técnico

| Código | Tipo | Ejemplos |
|--------|------|----------|
| **LOGIC** | Lógica negocio incorrecta | Cálculo mal, regla no aplicada |
| **VALID** | Validación faltante/incorrecta | Input no validado, sanitización |
| **SECURITY** | Vulnerabilidad seguridad | XSS, IDOR, auth bypass, crypto weak |
| **CONCURRENCY** | Race condition, deadlock | Double submit, lost update |
| **DATA_INTEGRITY** | Corrupción datos | Partial write, FK violation, sync corruption |
| **PERFORMANCE** | Degradación inaceptable | N+1, memory leak, main thread block |
| **UX/ACCESSIBILITY** | Barrera uso, a11y violation | Contraste, keyboard trap, touch target |
| **ARCHITECTURE** | Violación arquitectura | Layer bypass, circular dep, domain leak |
| **CONFIG/ENV** | Configuración incorrecta | Env vars, feature flags, CSP |

---

## 6. Métricas Clave (Resumen SAQI-007)

| Categoría | Métricas Principales |
|-----------|---------------------|
| **Producto** | Defect Escape Rate, Regression Rate, Test Pass Rate, Mutation Score, Coverage (L/B/F), OWASP Coverage, A11y Score |
| **Proceso** | Iteration Cycle Time, Time to Fix, Defect Detection Rate/fase, Prompt Iterations/Task, Human Interventions/Iteration |
| **Skills** | Skills Applied/Iter, Skills Modified/Iter, Skill Reuse Rate, Skill Defect Contribution |
| **Coste** | Tokens/Iteration, Cost/Iteration, Cost/Defect Found, Cost/Defect Fixed |
| **Contexto** | Context Refresh Rate, Context Staleness Index, Checkpoint Frequency |
| **Aprendizaje** | Recurring Defect Rate, New Skill Rules/Iter, Skill Version Bumps/Iter |

---

## 7. Documentos Vivos (4+1)

| Documento | Propósito | Frecuencia Actualización |
|-----------|-----------|-------------------------|
| `ARCHITECTURE.md` | Decisiones arquitectura, ADRs, diagramas, contratos | Fases 2, 13 |
| `CONTEXT.md` | Reglas negocio, dominio, convenciones, stack, fixes | Fases 1, 13 |
| `PROJECT_STATE.md` | Tareas done/WIP/bloqueadas, métricas, deuda, próximos pasos | Continuo, Fase 13 |
| `QA_RESULTS.md` | Historial QA: defectos, fixes, métricas, tendencias, lessons | Fases 7, 10, 12, 13 |
| `SESSION.md` | Log sesión actual: comandos, decisiones, hallazgos | Por sesión |

---

## 8. Gates Humanos Obligatorios (H1-H8)

| Gate | Fase | Decisión | Firmantes |
|------|------|----------|-----------|
| **H1** | 1 (Plan) | Aprobar ITERATION_PLAN.md | PO / Tech Lead |
| **H2** | 2 (Architect) | Aprobar ADRs, decisiones arquitectura | Architect / TL |
| **H3** | 3 (Skill Selection) | Aprobar Skills + versiones | TL / Orquestador |
| **H4** | 4 (Build) | Code review código crítico (sec, arch, domain) | TL / Senior |
| **H5** | 7 (Break) | **Firma gate release** — Aprobar/Rechazar basado en QA adversarial | **QA Lead (obligatorio)** |
| **H6** | 8 (Diagnose) | Validar causa raíz, aprobar fix strategy | TL / QA |
| **H7** | 11 (Verify) | **Sign-off final release** | PO + TL + QA Lead |
| **H8** | 14 (Improve Skills) | Aprobar cambios Skills, versionado, deprecación | Gobernanza Skills |

---

## 9. Términos de Investigación Experimental

| Término | Definición |
|---------|------------|
| **RCT** | Randomized Controlled Trial — Ensayo controlado aleatorizado (diseño experimental oro) |
| **PICOC** | Population, Intervention, Comparison, Outcome, Context — Marco para preguntas de investigación |
| **Cliff's δ** | Tamaño de efecto no paramétrico (|δ|>0.147 pequeño, >0.33 medio, >0.474 grande) |
| **Mann-Whitney U** | Test no paramétrico para comparar dos grupos independientes |
| **TOST** | Two One-Sided Tests — Test de equivalencia |
| **Holm-Bonferroni** | Corrección paso-a-paso para comparaciones múltiples |
| **ITT** | Intention-To-Treat — Análisis por intención de tratar (incluye dropouts) |
| **CONSORT** | Consolidated Standards of Reporting Trials — Estándar reporte ensayos |
| **Replication Package** | Conjunto completo de artefactos para replicar estudio (protocolos, datos, código, análisis) |

---

## 10. Acrónimos Comunes

| Acrónimo | Expansión |
|----------|-----------|
| **SAQI** | Skill-based Agentic Quality Iteration |
| **LLM** | Large Language Model |
| **RAG** | Retrieval-Augmented Generation |
| **CoT** | Chain-of-Thought |
| **PBT** | Property-Based Testing |
| **AST** | Abstract Syntax Tree |
| **ADR** | Architecture Decision Record |
| **C4** | Context, Containers, Components, Code (modelo arquitectura) |
| **STRIDE** | Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege |
| **OWASP** | Open Web Application Security Project |
| **ASVS** | Application Security Verification Standard |
| **SSDF** | Secure Software Development Framework (NIST) |
| **CERT** | Computer Emergency Response Team (secure coding standards) |
| **DORA** | DevOps Research and Assessment |
| **MTBF** | Mean Time Between Failures |
| **MTTF** | Mean Time To Fix |
| **DER** | Defect Escape Rate |
| **RR** | Regression Rate |
| **ADR** | Adversarial Detection Rate |
| **MI** | Maintainability Index |
| **SemVer** | Semantic Versioning (MAJOR.MINOR.PATCH) |

---

## 11. Referencias Internas

- SAQI-001: Resumen Ejecutivo (Tabla glosario resumen)
- SAQI-003: Gobernanza Skills (Taxonomía A-D, Ciclo vida)
- SAQI-004: Marco QA (ATK-* taxonomía, clasificación defectos)
- SAQI-005: Proceso SAQI (14 fases, Gates H1-H8)
- SAQI-007: Métricas (Catálogo completo)
- SAQI-008: Protocolo Experimental (RCT, PICOC, estadística)
- SAQI-013: Gestión Contexto (4+1 docs, checkpointing)

---

*Glosario vivo — Actualizar con cada nueva versión de SAQI*
# SAQI-002: Fundamentos Teóricos y Revisión Bibliográfica

**Versión:** 1.0
**Fecha:** 2026-07-17
**Estado:** Borrador inicial
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Introducción

Este documento establece los fundamentos teóricos sobre los que se construye SAQI (Skill-based Agentic Quality Iteration). No es una revisión exhaustiva de la literatura, sino una **selección crítica** de teorías, modelos y evidencias empíricas que fundamentan las decisiones de diseño de SAQI.

**Posición epistemológica:** SAQI se posiciona en la **ingeniería de software basada en evidencia (Evidence-Based Software Engineering - EBSE)**. Las afirmaciones sobre efectividad requieren validación experimental; este documento establece la base teórica para formular hipótesis comprobables.

---

## 2. Fundamentos Teóricos Principales

### 2.1 Ingeniería del Conocimiento y Sistemas Basados en Conocimiento

**Referencia clásica:** Feigenbaum, E. A. (1977). "The Art of Artificial Intelligence: Themes and Case Studies of Knowledge Engineering."

**Concepto central:** Los sistemas expertos clásicos separaban **base de conocimiento** (hechos, reglas) del **motor de inferencia**. SAQI adapta esta separación:
- **Skills** ≈ Base de conocimiento (reglas, patrones, procedimientos, criterios) versionada y mejorable.
- **Agente LLM + orquestador** ≈ Motor de inferencia (razonamiento, planificación, uso de herramientas).

**Diferencia clave:** En sistemas expertos clásicos, el conocimiento es **explícito y simbólico** (reglas lógicas). En SAQI, el conocimiento en Skills es **semi-estructurado** (texto estructurado, plantillas, checklists, patrones) y el motor de inferencia es un **LLM probabilístico**. Esto introduce **incertidumbre** y **variabilidad** que SAQI aborda mediante:
- Verificación obligatoria (tests, linters, checklists) en cada fase.
- QA adversarial para detectar alucinaciones.
- Ciclo de retroalimentación que corrige tanto código como Skills.

**Referencias clave:**
- Russell, S. & Norvig, P. (2020). *Artificial Intelligence: A Modern Approach* (4th ed.). Cap. 7-8.
- Studer, R., Benjamins, V. R., & Fensel, D. (1998). "Knowledge Engineering: Principles and Methods." *Data & Knowledge Engineering*.

---

### 2.2 Ingeniería de Software Basada en Evidencia (EBSE)

**Referencia fundacional:** Kitchenham, B., et al. (2004). "Evidence-Based Software Engineering." *ICSE 2004*.

**Principios EBSE aplicados en SAQI:**

| Principio EBSE | Aplicación en SAQI |
|----------------|-------------------|
| Decisiones basadas en evidencia | Skills basadas en estándares (OWASP, CERT, ISO), no opiniones |
| Preguntas PICOC (Population, Intervention, Comparison, Outcome, Context) | Preguntas de investigación explícitas en SAQI-008 |
| Revisiones sistemáticas de literatura | Base para cada Skill (referencias en cada Skill) |
| Replicabilidad | SAQI-012 (Replication Package) |
| Evaluación de calidad de evidencia | SAQI-009 (Amenazas a validez) |

**Crítica a EBSE clásica:** Las revisiones sistemáticas tradicionales tardan años. SAQI propone **evidencia incremental iterativa**: cada iteración genera datos que alimentan la base de evidencia del proyecto y de las Skills.

---

### 2.3 Arquitectura Limpia y Arquitectura Hexagonal

**Referencias:** Martin, R. C. (2017). *Clean Architecture*. Alistair Cockburn (2005). "Hexagonal Architecture."

**Principios aplicados en SAQI (Skill A-project-architecture):**

| Principio | Implementación en SAQI |
|-----------|------------------------|
| Independencia de frameworks | Skills de arquitectura prohíben acoplamiento a frameworks en dominio |
| Testabilidad | Arquitectura hexagonal obligatoria en Skill A-project-architecture |
| Independencia de UI/DB | Puertos y adaptadores obligatorios |
| Regla de dependencia | Hacia adentro (dominio no conoce infraestructura) |

**Evidencia empírica:** Estudios de caso (Martin, 2017; cockburn) muestran reducción de acoplamiento y facilidad de testing. SAQI lo **codifica como reglas obligatorias en Skills** con verificaciones automatizadas (linting arquitectónico, tests de arquitectura).

---

### 2.4 Clean Code y Estándares de Codificación

**Referencias:** Martin, R. C. (2008). *Clean Code*. McConnell, S. (2004). *Code Complete* (2nd ed.).

**Codificación en SAQI (Skill A-coding-standards):**

| Principio | Regla en Skill (MUST/SHALL) |
|-----------|----------------------------|
| Nombrado significativo | Nombres que revelan intención; sin abreviaturas crípticas |
| Funciones pequeñas | Una responsabilidad; < 20 líneas preferiblemente |
| DRY / YAGNI / KISS | Prohibido código duplicado; no anticipar necesidades |
| Manejo explícito de errores | Result types / Result pattern; no excepciones para control de flujo |
| Tipado estricto | TypeScript strict mode; no `any` sin justificación documentada |

---

### 2.5 Programación Segura (Secure Coding)

**Referencias normativas:**

| Estándar | Ámbito | Aplicación en SAQI (Skill A-secure-coding, B-authentication-security) |
|----------|--------|---------------------------------------------------------------|
| OWASP Top 10 (2021/2023) | Web App Security | Reglas MUST para cada categoría (A01-A10) |
| OWASP ASVS 4.0 | Application Security Verification | Nivel 2 como baseline; Nivel 3 para módulos críticos |
| CERT Secure Coding (C/C++/Java/JS) | Language-specific | Reglas aplicables a TypeScript/JavaScript |
| NIST SSDF (SP 800-218) | Secure SDLC | Fases PW (Protect), RV (Respond), RV (Recover) mapeadas a fases SAQI |
| ISO 27001/27002 | InfoSec Management | Controles aplicables mapeados a Skills B-authentication-security, A-secure-coding |

**Principio SAQI:** La seguridad **no es una fase final**, es un requisito transversal codificado en Skills de Nivel A y B con verificaciones en **cada fase** (linting, SAST, dependency scanning, security testing en QA adversarial).

---

### 2.6 Testing y Quality Assurance

#### 2.6.1 Pirámide de Testing y Testing Cuadrantes

**Referencias:** Cohn, M. (2009). *Succeeding with Agile* (Test Pyramid). Crispin, L. & Gregory, J. (2009). *Agile Testing* (Quadrants).

| Cuadrante / Nivel | Enfoque | Implementación SAQI |
|-------------------|---------|---------------------|
| Q1: Unit / Component | Técnico, automatizado | Fase 5: Jest/Vitest, ≥80% cobertura, tests de contrato |
| Q2: Integration / API | Negocio, automatizado | Fase 5: Tests de integración DB, API, servicios |
| Q3: Exploratory / UX | Negocio, manual | Fase 6: Playwright E2E, user journeys reales |
| Q4: Performance / Security / Chaos | Técnico, especializado | **Fase 7: QA Adversarial (obligatorio)** |

**Diferencia clave SAQI:** El Cuadrante 4 **no es opcional ni final**. Es una **fase obligatoria con criterios de salida** antes de release. Incluye: security testing, chaos testing, accessibility, stress, fuzzing, adversarial inputs.

#### 2.6.2 Mutation Testing

**Referencia:** Jia, Y. & Harman, M. (2011). "An Analysis and Survey of the Development of Mutation Testing." *IEEE TSE*.

**Aplicación SAQI:** Mutation testing (Stryker para JS/TS) como **métrica de calidad de tests** en Fase 5. Umbral objetivo: Mutation Score ≥ 70%.

#### 2.6.3 Property-Based Testing

**Referencia:** Claessen, K. & Hughes, J. (2000). "QuickCheck: A Lightweight Tool for Random Testing of Haskell Programs." *ICFP*.

**Aplicación SAQI:** Fast-check (TypeScript) para testing de propiedades en dominios con invariantes matemáticos (ej. transacciones financieras, inventarios). Incluido en Skill A-testing y C-database-design-offline.

---

### 2.7 Debugging Estructurado

**Referencia:** Zeller, A. (2009). *Why Programs Fail: A Guide to Systematic Debugging* (2nd ed.).

**Metodología aplicada en SAQI (Skill C-debugging, Fase 8):**

1. **Reproducir** → Test caso mínimo que falla
2. **Aislar** → Delta debugging / bisección
3. **Hipótesis** → Formular causas candidatas
4. **Evidencia** → Logs, traces, memoria, DB, red
5. **Causa raíz** → Validar hipótesis
6. **Fix** → Cambio mínimo, testeable
7. **Regresión** → Test que prevenga regresión + suite completa

**Herramientas:** rr (record/replay), Delve/Node inspector, logging estructurado (pino/winston), OpenTelemetry.

---

### 2.8 Bases de Datos Offline-First y Sincronización

**Referencias:**

- Melnik, S., et al. (2000). "Algebras for Data Synchronization." *VLDB*.
- Kleppmann, M. (2017). *Designing Data-Intensive Applications*. Cap. 5, 9.
- Kleppmann, M., & Beresford, A. (2017). "CRDTs: An Overview." *ACM SIGMOD*.
- Dexie.js / Dexie Cloud documentation. RxDB documentation. Yjs documentation.

**Skill C-database-design-offline, C-dexie-patterns:**

| Patrón | Descripción | Aplicación Open-RootERP |
|--------|-------------|------------------------|
| Offline-first / Local-first | Datos primarios en cliente; sync background | Dexie.js + IndexedDB |
| CRDT (Conflict-free Replicated Data Types) | Merge automático sin coordinación | Yjs para colaborativo; Dexie sync para ERP |
| Event Sourcing + CQRS | Log de eventos como source of truth | Skill C-database-design-sql para sync backend |
| Optimistic UI / Latency Compensation | UI responde inmediato; sync después | React Query / TanStack Query + Dexie |

**Evidencia:** Kleppmann (2017) demuestra que offline-first reduce latencia percibida y aumenta disponibilidad. SAQI lo codifica como **arquitectura obligatoria** para apps offline (Skill A-project-architecture + C-database-design-offline).

---

### 2.9 Ingeniería de Prompts y Agentes LLM

#### 2.9.1 Prompt Engineering

**Referencias:**
- Liu, P., et al. (2023). "Pre-train, Prompt, and Predict: A Systematic Survey of Prompting Methods in Natural Language Processing." *ACM Computing Surveys*.
- Wei, J., et al. (2022). "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models." *NeurIPS*.
- Kojima, T., et al. (2022). "Large Language Models are Zero-Shot Reasoners." *NeurIPS*.

**Aplicación SAQI (Skill D-prompt-engineering):**
- **Chain-of-Thought obligatorio** para tareas de razonamiento (debugging, arquitectura, debugging).
- **Few-shot con ejemplos validados** extraídos de iteraciones previas (retrieval desde Skills).
- **Structured output** (JSON Schema) para salidas parseables.
- **Self-consistency / Self-critique** para tareas críticas (security review, architecture decisions).

#### 2.9.2 Arquitecturas de Agentes LLM

**Referencias:**
- Yao, S., et al. (2022). "ReAct: Synergizing Reasoning and Acting in Language Models." *ICLR*.
- Wang, G., et al. (2023). "Voyager: An Open-Ended Embodied Agent with Large Language Models." *arXiv*.
- Zhou, A., et al. (2023). "Agents: An Open-Source Framework for Language Agents." *arXiv*.
- OpenCode agent architecture (referencia implementativa).

**Arquitectura SAQI (ver SAQI-006):**
- **Orquestador** (planning, tool selection, context management)
- **Skills** como conocimiento externo recuperable (RAG sobre Skills)
- **Contexto persistente** (ARCHITECTURE.md, CONTEXT.md, PROJECT_STATE.md)
- **Loop de verificación** obligatorio tras cada acción de generación

---

### 2.10 Gestión de Contexto y Memoria Externa

**Problema:** Los LLM tienen ventana de contexto finita y **no tienen memoria persistente entre sesiones**.

**Soluciones en literatura:**
- **Memoria externa / RAG:** Lewis, P., et al. (2020). "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks." *NeurIPS*.
- **Compresión de contexto:** Ge, T., et al. (2023). "Long Context Compression for LLMs." *arXiv*.
- **Checkpointing / State persistence:** Wang, G., et al. (2023). "Voyager." (Skill library persistente).

**Solución SAQI (Skill A-context-manager):**
- **4 documentos vivos** como memoria externa estructurada:
  1. `ARCHITECTURE.md` - Decisiones arquitectónicas, ADRs, diagramas
  2. `CONTEXT.md` - Estado del dominio, reglas de negocio, decisiones de dominio
  3. `PROJECT_STATE.md` - Estado actual: tareas, completadas, bloqueos, próximos pasos
  4. `QA_RESULTS.md` - Historial de QA: defectos, fixes, métricas, lecciones
- **Checkpointing automático** al 80% de contexto (protocolo en Skill A-context-manager)
- **Re-hidratación** de contexto al inicio de cada sesión

---

### 2.11 Métricas de Ingeniería de Software

**Referencias:**
- ISO/IEC 25010:2011 - Systems and software Quality Requirements and Evaluation (SQuaRE).
- McCall, J. A., Richards, P. K., & Walters, G. F. (1977). *Factors in Software Quality*.
- DORA Metrics (Forsgren, N., Humble, J., & Kim, G. 2018. *Accelerate*).

**Aplicación SAQI (SAQI-007):**

| Categoría ISO 25010 | Métricas SAQI (Ejemplos) |
|---------------------|--------------------------|
| Funcionalidad | Defect Escape Rate, Requirement Coverage |
| Fiabilidad | MTBF, MTTF, Regression Rate, Availability |
| Usabilidad | Accessibility Score (axe-core), Mobile Usability Score |
| Eficiencia | Performance (Lighthouse), Bundle Size, DB Query Time |
| Mantenibilidad | Cyclomatic Complexity, Cognitive Complexity, Technical Debt Ratio, Code Coverage, Mutation Score |
| Portabilidad | Offline-First Score, Cross-browser Score |
| Seguridad | OWASP Top 10 Coverage, SAST Findings, Dependency Vulnerabilities |

**Métricas de proceso SAQI:**
- Iteration Cycle Time
- Defect Detection Rate por fase
- Skill Application Rate
- Context Refresh Rate
- Prompt Iteration Count per Task

---

## 3. Posicionamiento de SAQI Frente a Enfoques Relacionados

| Enfoque | Similitud con SAQI | Diferencia Clave |
|---------|-------------------|------------------|
| **Traditional SDLC (Waterfall, V-Model)** | Fases definidas, QA tardía | SAQI: Iterativo, QA adversarial integrado, Skills versionadas |
| **Agile / Scrum** | Iterativo, incremental | SAQI: Skills como artefactos de conocimiento reutilizables, QA adversarial obligatorio, agente como actor |
| **DevOps / CI/CD** | Automatización, feedback rápido | SAQI: Skills codifican conocimiento de ingeniería; agente ejecuta; contexto persistente |
| **Test-Driven Development (TDD)** | Tests first | SAQI: Tests obligatorios + QA adversarial + Skills guían implementación |
| **Model-Driven Engineering (MDE)** | Modelos como artefactos centrales | SAQI: Skills = conocimiento semi-estructurado; LLM = generador; verificación obligatoria |
| **AI-Assisted Coding (Copilot, Cursor, etc.)** | IA genera código | SAQI: Marco metodológico completo con Skills, QA adversarial, contexto persistente, retroalimentación a Skills |
| **Agentic AI Frameworks (AutoGPT, BabyAGI, LangGraph, CrewAI, OpenCode)** | Agentes autónomos | SAQI: Metodología de ingeniería **sobre** el agente; Skills = conocimiento de ingeniería versionado; QA adversarial obligatorio; ciclo de vida 14 fases |

---

## 4. Hipótesis de Investigación Derivadas

Basado en los fundamentos anteriores, SAQI formula las siguientes **hipótesis comprobables** (ver SAQI-008 para protocolos experimentales):

| ID | Hipótesis | Tipo |
|----|-----------|------|
| H1 | El uso de Skills versionadas reduce la variabilidad en calidad de código entre desarrolladores de distinta experiencia. | Comparativa controlada |
| H2 | El QA adversarial obligatorio detecta ≥ 30% más defectos de seguridad y casos límite que testing convencional solo. | Comparativa controlada |
| H3 | La gestión de contexto persistente (4 documentos vivos) reduce la pérdida de contexto entre sesiones ≥ 80%. | Cuasi-experimental |
| H4 | El ciclo de retroalimentación Skills ← Iteración reduce defectos recurrentes del mismo tipo ≥ 50% en 5 iteraciones. | Longitudinal |
| H5 | SAQI reduce Prompt Sensitivity Index (variabilidad de calidad por variación de prompt) vs. prompting ad-hoc. | Experimental controlado |

---

## 5. Referencias Seleccionadas (Subconjunto)

> Ver SAQI-016 para bibliografía completa.

1. Kitchenham, B., et al. (2004). Evidence-Based Software Engineering. *ICSE*.
2. Martin, R. C. (2017). *Clean Architecture*. Prentice Hall.
3. Cockburn, A. (2005). Hexagonal Architecture. *alistaircockburn.com*.
4. Kleppmann, M. (2017). *Designing Data-Intensive Applications*. O'Reilly.
5. Zeller, A. (2009). *Why Programs Fail*. Morgan Kaufmann.
6. OWASP Foundation. (2021/2023). OWASP Top 10 / ASVS 4.0.
7. NIST. (2022). SP 800-218: Secure Software Development Framework (SSDF).
8. ISO/IEC 25010:2011. Systems and software Quality Requirements and Evaluation.
9. Liu, P., et al. (2023). Pre-train, Prompt, and Predict. *ACM Computing Surveys*.
10. Yao, S., et al. (2022). ReAct: Synergizing Reasoning and Acting. *ICLR*.
11. Forsgren, N., Humble, J., & Kim, G. (2018). *Accelerate*. IT Revolution Press.
12. Crispin, L. & Gregory, J. (2009). *Agile Testing*. Addison-Wesley.

---

## 6. Referencias Internas

- SAQI-001: Resumen Ejecutivo y Definición
- SAQI-004: Definición del Marco Metodológico SAQI
- SAQI-005: Definición del Proceso SAQI
- SAQI-006: Especificación de Arquitectura de Agente
- SAQI-007: Marco de Atributos de Calidad y Métricas
- SAQI-008: Diseño Experimental y Marco de Validación
- SAQI-009: Amenazas a Validez y Limitaciones
- SAQI-015: Glosario y Terminología
- SAQI-016: Referencias Bibliográficas Completas
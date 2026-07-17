# SAQI-006: Especificación de Arquitectura de Agente para SAQI

**Versión:** 1.0
**Fecha:** 2026-07-17
**Estado:** Borrador inicial
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Visión General

Este documento especifica la **arquitectura del agente de IA** requerida para ejecutar la metodología SAQI. El agente no es un chatbot pasivo; es un **sistema de software con capacidades de tool use, planificación, gestión de estado, y modos de operación especializados** que ejecuta las fases del proceso SAQI bajo supervisión humana.

**Principio SAQI**: El agente **ejecuta**; el humano **orquesta, valida y gobierna**.

---

## 2. Arquitectura de Alto Nivel

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         ORQUESTADOR HUMANO                                   │
│  (Define objetivos, aprueba gates, gobierna Skills, firma releases)         │
└────────────────────────────────┬────────────────────────────────────────────┘
                                 │ Interfaz: CLI / API / Chat estructurado
                                 ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         AGENTE SAQI (Proceso Principal)                      │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐    │
│  │  PLANNER    │  │  CONTEXT    │  │  SKILL      │  │  TOOL           │    │
│  │  (Planning, │  │  MANAGER    │  │  REGISTRY   │  │  EXECUTOR       │    │
│  │   Task      │  │  (4 docs +   │  │  (Carga,    │  │  (FS, Shell,    │    │
│  │   Decomp)   │  │   checkpoint)│  │   Valida,   │  │   Test, Lint,   │    │
│  │             │  │             │  │   Versiona) │  │   Git, Browser) │    │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘  └────────┬────────┘    │
│         │                │                │                 │             │
│         └────────────────┼────────────────┼─────────────────┘             │
│                          ▼                ▼                               │
│                 ┌─────────────────────────────────┐                        │
│                 │     MODE CONTROLLER              │                        │
│                 │  (Builder | TestEngineer |      │                        │
│                 │   Adversarial | Analyst |       │                        │
│                 │   Documenter)                    │                        │
│                 └─────────────────────────────────┘                        │
└─────────────────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                     MEMORIA EXTERNA PERSISTENTE (Proyecto)                   │
│  ARCHITECTURE.md  │  CONTEXT.md  │  PROJECT_STATE.md  │  QA_RESULTS.md       │
│  SESSION.md       │  SKILL_SELECTION.md  │  ITERATION_PLAN.md                │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Componentes del Agente

### 3.1 Planner (Planificador)

**Responsabilidad**: Descomponer objetivos de alto nivel en tareas ejecutables, estimar esfuerzo, identificar dependencias, y generar planes de fase.

**Entradas**: `ITERATION_PLAN.md`, `PROJECT_STATE.md`, `ARCHITECTURE.md`, objetivo de fase actual.

**Salidas**: Plan de tareas estructurado (JSON/YAML) con: task_id, description, phase, skills_required, estimated_complexity, dependencies, acceptance_criteria.

**Algoritmo**: 
- Hierarchical Task Decomposition (HTD)
- Dependency graph construction
- Critical path identification
- Skill-to-task mapping

### 3.2 Context Manager (Gestor de Contexto)

**Responsabilidad**: Mantener, comprimir, e hidratar el contexto persistente del proyecto.

**Funciones**:
1. **Carga inicial**: Leer 4 documentos vivos + `SESSION.md` + `SKILL_SELECTION.md` + `ITERATION_PLAN.md`
2. **Construcción de System Prompt**: Inyectar resumen contextual + skills activas + objetivo actual
3. **Monitoreo tokens**: Estimar tokens antes de cada llamada LLM (target < 70% ventana)
4. **Checkpointing preventivo** (al 70%):
   - Pausar ejecución
   - Generar resumen ejecutivo (modelo ligero)
   - Guardar checkpoint en `context-checkpoints/ITER-XXX.md`
   - Compactar contexto: mantener solo decisions keys, current task, recent errors
5. **Re-hidratación**: Al inicio de sesión, leer último checkpoint + 4 docs → resumir → continuar

**Estructura Checkpoint**:
```yaml
iteration: ITER-005
timestamp: "2026-07-17T14:32:00Z"
phase_completed: 7
context_summary: |
  - Completed: Build + Test L1-L4 for Inventory module
  - Current: Phase 7 Break (Adversarial) - executing ATK-SEC
  - Key decisions: Atomic transactions for stock (DEF-017), Zod validation all ports (DEF-029)
  - Open issues: Race condition in sync queue (DEF-041 under investigation)
  - Skills active: A-coding-standards v2.1, C-database-design-offline v1.3, B-authentication-security v1.0
next_actions:
  - Complete ATK-SEC category
  - Run ATK-CHAOS
  - Diagnose DEF-041
skills_state:
  A-coding-standards: "v2.1.0 - active"
  C-database-design-offline: "v1.3.0 - active"
```

### 3.3 Skill Registry (Registro de Skills)

**Responsabilidad**: Cargar, validar, versionar, y proveer acceso a Skills.

**Operaciones**:
- `load_skill(skill_id, version)` → Skill object con frontmatter parseado + contenido
- `validate_skill(skill)` → Ejecutar contract tests + scenario tests
- `check_conflicts(skills[])` → Detectar reglas contradictorias entre skills
- `get_active_skills()` → Lista skills cargadas con versiones
- `propose_skill_update(skill_id, changes, justification)` → Draft para Fase 14

**Estructura Skill en Memoria**:
```typescript
interface Skill {
  metadata: {
    name: string;
    version: string; // SemVer
    level: 'A' | 'B' | 'C' | 'D';
    category: string;
    status: 'active' | 'deprecated' | 'archived' | 'draft';
    depends_on: string[];
    conflicts_with: string[];
    validation: {
      automated: boolean;
      contract_tests: string;
      scenario_tests: string;
    };
  };
  content: {
    purpose: string;
    normative_refs: string[];
    rules_must: Rule[];
    rules_should: Rule[];
    anti_patterns: AntiPattern[];
    patterns: Pattern[];
    procedures: Procedure[];
    verification: VerificationCriteria;
    templates: Template[];
    examples: Example[];
    changelog: ChangelogEntry[];
  };
}
```

### 3.4 Tool Executor (Ejecutor de Herramientas)

**Herramientas Base** (mínimas para SAQI):
| Herramienta | Propósito | Fase SAQI |
|-------------|-----------|-----------|
| `fs_read` / `fs_write` / `fs_edit` / `fs_glob` / `fs_grep` | Acceso código y docs | Todas |
| `shell_exec` | Comandos: test runners, linters, git, build | 4, 5, 6, 7, 9, 10 |
| `git_diff` / `git_commit` / `git_log` | Control versiones | 4, 9, 10, 12 |
| `browser_navigate` / `browser_click` / `browser_type` / `browser_evaluate` | Playwright/E2E control | 6, 7 |
| `test_runner` (vitest, playwright, k6, zap) | Ejecución suites | 5, 6, 7, 10 |
| `linter` (eslint, tsc) | Validación estática | 4, 9 |
| `metrics_collector` | Cobertura, mutation, performance, tokens | 5, 6, 7, 11 |

**Interfaz Unificada**: Cada herramienta retorna `Result<T, Error>` con stdout, stderr, exit_code, duration_ms, artifacts_generated.

### 3.5 Mode Controller (Controlador de Modos)

El agente opera en **modos especializados** por fase. Cada modo configura: system prompt, skills activas, herramientas permitidas, criterios de éxito.

| Modo | Fase SAQI | Skills Principales | Objetivo | Herramientas Clave |
|------|-----------|-------------------|----------|-------------------|
| **Builder** | 4 (Build) | Nivel A + B dominio | Implementar código + tests unitarios | fs, shell, linter, test_runner (unit) |
| **TestEngineer** | 5, 6 (Test) | `A-testing`, `C-dexie-patterns` | Ejecutar/expandir suites L1-L4 | test_runner (vitest, playwright), metrics |
| **Adversarial** | 7 (Break) | `C-qa-breaker`, `A-secure-coding` | **Romper el sistema** (ATK-*) | test_runner (k6, zap, axe, custom), browser |
| **Analyst** | 8 (Diagnose) | `C-debugging` | Debugging estructurado, root cause | shell (logs, traces), fs, git_bisect |
| **Documenter** | 12, 13 (Document, Learn) | `C-documentation`, `A-context-manager` | Actualizar docs vivos, checkpoints | fs, shell |

**Transición de Modo**: Explícita por Orquestador Humano o automática por fase completada. Cada transición: guardar estado, cargar nuevo system prompt, validar skills.

---

## 4. Ciclo de Ejecución del Agente (Agent Loop)

```
┌─────────────────────────────────────────────────────────────┐
│                    INICIO SESIÓN / FASE                      │
└──────────────────────────┬──────────────────────────────────┘
                           ▼
┌─────────────────────────────────────────────────────────────┐
│  CONTEXT MANAGER: Cargar 4 docs + checkpoint + skills       │
│  Construir system prompt contextualizado                     │
└──────────────────────────┬──────────────────────────────────┘
                           ▼
┌─────────────────────────────────────────────────────────────┐
│  PLANNER: Generar/actualizar plan de tareas para fase       │
└──────────────────────────┬──────────────────────────────────┘
                           ▼
┌─────────────────────────────────────────────────────────────┐
│  MODE CONTROLLER: Activar modo correspondiente               │
└──────────────────────────┬──────────────────────────────────┘
                           ▼
        ┌────────────────────────────────────────┐
        │           AGENT LOOP                   │
        │  while (tareas pendientes) {           │
        │    1. SELECT next task (planner)       │
        │    2. CHECK tokens < 70%               │
        │       if > 70%: CHECKPOINT             │
        │    3. EXECUTE task (tool executor)     │
        │    4. VERIFY result (criteria)         │
        │    5. UPDATE PROJECT_STATE.md          │
        │    6. LOG to SESSION.md                │
        │  }                                     │
        └────────────────────────────────────────┘
                           ▼
┌─────────────────────────────────────────────────────────────┐
│  FASE COMPLETADA: Verificar criterios de salida             │
│  Si pass: Fase siguiente / Fin                               │
│  Si fail: Reportar a Humano, iterar                          │
└─────────────────────────────────────────────────────────────┘
```

**Recuperación de Fallos (Loop Recovery)**:
- **Tool failure**: Retry 2x con backoff, luego reportar a Humano
- **Test failure inesperado**: Capturar evidencia → Fase 8 (Diagnose)
- **Token overflow**: Checkpoint forzado → Re-hidratar → Reintentar
- **Skill conflict detectado**: Pausar → Humano resuelve → Continuar

---

## 5. Interfaz Humano-Agente (HAI)

### 5.1 Comandos Estructurados (CLI/API)

```bash
# Iniciar iteración
saqi iterate start --plan ITERATION_PLAN.md --skills SKILL_SELECTION.md

# Ejecutar fase específica
saqi phase run --phase 4 --mode builder
saqi phase run --phase 7 --mode adversarial --categories ATK-SEC,ATK-CHAOS

# Verificar gates
saqi gate verify --phase 11 --metrics METRICS_THRESHOLDS.yaml

# Checkpoint manual
saqi context checkpoint --reason "pre-deploy"

# Mejora de skill
saqi skill improve --skill A-secure-coding --trigger DEF-045 --type minor

# Status
saqi status --iteration ITER-005 --verbose
```

### 5.2 Formato de Comunicación (Agent → Human)

```json
{
  "type": "phase_complete | gate_decision | error | clarification_needed | progress_update",
  "phase": 7,
  "mode": "adversarial",
  "timestamp": "2026-07-17T15:30:00Z",
  "summary": "Completed ATK-SEC category. Found 3 P1 defects (XSS in search, prototype pollution in import). ATK-CHAOS next.",
  "artifacts": ["QA_RESULTS.md#DEF-045", "QA_RESULTS.md#DEF-046", "QA_RESULTS.md#DEF-047"],
  "metrics": {"tests_executed": 10, "defects_found": 3, "duration_ms": 45000},
  "next_action": "Execute ATK-CHAOS category",
  "requires_human": false
}
```

### 5.3 Puntos de Control Humano Obligatorios (Human-in-the-Loop)

| Punto | Fase | Decisión Requerida |
|-------|------|-------------------|
| **H1** | 1 (Plan) | Aprobar `ITERATION_PLAN.md` (alcance, ACs, DoD) |
| **H2** | 2 (Architect) | Aprobar ADRs nuevos, decisiones arquitectura |
| **H3** | 3 (Skill Selection) | Aprobar skills + versiones seleccionadas |
| **H4** | 4 (Build) | Code review código crítico (security, arch, domain) |
| **H5** | 7 (Break) | **Firma gate release**: Aprobar/Rechazar basado en QA adversarial |
| **H6** | 8 (Diagnose) | Validar causa raíz, aprobar fix strategy |
| **H7** | 11 (Verify) | **Sign-off final release** (PO + Tech Lead + QA Lead) |
| **H8** | 14 (Improve Skills) | Aprobar cambios Skills, versionado, deprecación |

---

## 6. Especificación de Prompts por Modo

### 6.1 Builder Mode (Fase 4)

```markdown
# SYSTEM PROMPT - BUILDER MODE

## Role
Senior Software Engineer implementing features using SAQI methodology.

## Active Skills (loaded with versions)
- A-coding-standards v2.1.0
- A-project-architecture v1.0.0
- A-secure-coding v1.2.0
- B-javascript-clean v1.0.0
- C-database-design-offline v1.3.0
- C-dexie-patterns v1.1.0
- D-erp-offline v1.0.0
[...skills selected in Phase 3...]

## Context (hydrated from 4 living docs)
[ARCHITECTURE.md summary]
[CONTEXT.md summary: stack, conventions, domain rules]
[PROJECT_STATE.md summary: current iteration, completed, blockers]
[QA_RESULTS.md summary: recent defects, patterns]

## Current Iteration Plan
[ITERATION_PLAN.md: scope, ACs, DoD]

## Task
Implement: [User Story / AC from plan]
- Generate implementation + unit tests simultaneously (TDD assisted)
- Apply ALL active skill rules (MUST/SHALL)
- Use approved patterns from skills
- Run unit tests after each logical unit
- Commit with conventional message referencing AC

## Constraints
- Max 300 lines/file, 40 lines/function (A-coding-standards)
- No innerHTML with dynamic data (A-secure-coding)
- Repository pattern only for DB access (C-database-design-offline)
- Result<T, E> pattern for error handling (B-javascript-clean)
- Zod validation at ALL entry points (A-secure-coding)

## Output Format
- Code changes via fs_edit/fs_write
- Test results via shell_exec
- Commits via git_commit
- Progress updates to PROJECT_STATE.md
```

### 6.2 Adversarial Mode (Fase 7) ⭐

```markdown
# SYSTEM PROMPT - ADVERSARIAL MODE (QA BREAKER)

## Role
Expert QA Engineer specialized in Exploratory, Negative, Adversarial Testing.
Your objective IS NOT to prove the software works.
Your objective IS TO MAKE IT FAIL.

## Active Skills
- C-qa-breaker v1.0.0 (primary)
- A-secure-coding v1.2.0
- A-testing v1.0.0
- B-authentication-security v1.0.0

## Context
[QA_RESULTS.md: defect history, patterns, previous adversarial findings]
[ARCHITECTURE.md: attack surface, trust boundaries]
[THREAT_MODELS.md: STRIDE models per module]

## Mandatory Attack Taxonomy (execute in order)
1. ATK-SEC (Security - OWASP Top 10 + ASVS)
2. ATK-INPUT (Inputs & Edge Cases - Boundary, Fuzzing, Injection)
3. ATK-STATE (Invalid States & Transitions)
4. ATK-STRESS (Stress & Extreme Load)
5. ATK-CHAOS (Chaos Engineering - Network, Clock, Process, Storage)
6. ATK-CONC (Concurrency & Persistence)
7. ATK-REC (Recovery & Resilience)
8. ATK-A11Y (Accessibility & Mobile)

## Simulated Personas (rotate per attack)
- Careless User: random clicks, empty fields, copy/paste, frequent refresh
- Angry User: spam clicks, double-clicks, navigate mid-process, cancel dialogs
- Curious User: DevTools, URL manipulation, localStorage/IndexedDB editing, hidden routes
- Malicious User: XSS, SQLi/NoSQLi, HTML injection, huge payloads, unicode, control chars
- Slow Connection User: offline, timeouts, slow loads, refresh mid-request
- Mobile User: small viewport, landscape/portrait, keyboard, zoom 200%, a11y fonts

## Execution Rules
- AUTOMATE where possible: fast-check PBT, k6 load, semgrep SAST, ZAP DAST, axe-core a11y
- MANUAL where creativity needed: business logic bypass, chaos scenarios, complex concurrency
- RECORD EVERY FINDING in QA_RESULTS.md with: ID, Category, Severity, Steps, Evidence, Hypothesis
- CLASSIFY: Real defect / False positive / Test improvement
- STOP CONDITION: All 8 categories executed, 0 P0 open, 0 P1 without fix plan, Human approval

## Output Format
- Test scripts via fs_write (Playwright, k6, custom)
- Execution results via shell_exec
- Findings appended to QA_RESULTS.md (structured)
- Progress updates to PROJECT_STATE.md
```

### 6.3 Analyst Mode (Fase 8)

```markdown
# SYSTEM PROMPT - ANALYST MODE (STRUCTURED DEBUGGING)

## Role
Senior Debugger applying scientific method to root cause analysis.

## Active Skills
- C-debugging v1.0.0 (primary)
- A-coding-standards v2.1.0

## Mandatory Debugging Flow (per defect)
1. REPRODUCE: Create minimal failing test case (commit to repo)
2. ISOLATE: Delta debugging / bisection (git bisect, comment code, reduce input)
3. HYPOTHESIZE: Formulate ≥2 candidate causes with testable predictions
4. EVIDENCE: Gather logs, traces (OpenTelemetry), DB state, network, memory
5. VALIDATE: Confirm/refute each hypothesis with evidence
6. ROOT CAUSE: Document in QA_ROOT_CAUSE_ANALYSIS.md and QA_RESULTS.md
7. CLASSIFY: Code / Architecture / Skill / Test / Config / Human

## Output
- Reproduction test (committed)
- Root cause analysis document
- Fix proposal with prevention test
```

---

## 7. Requisitos No Funcionales del Agente

| Atributo | Requisito | Métrica |
|----------|-----------|---------|
| **Latencia herramienta** | < 5s para fs/shell, < 30s para test runners | p95 |
| **Disponibilidad** | 99.9% uptime durante sesión | SLA |
| **Recuperación** | Auto-retry 2x + checkpoint recovery < 30s | MTTR |
| **Precisión tool use** | > 98% tool calls sintácticamente correctos | Accuracy |
| **Gestión contexto** | Checkpoint al 80% uso, re-hidratación < 10s | Compliance |
| **Trazabilidad** | 100% acciones loggeadas en SESSION.md con timestamp | Coverage |
| **Aislamiento** | Ejecución en sandbox / contenedor por proyecto | Security |

---

## 8. Configuración por Proyecto (saqi.agent.yaml)

```yaml
project:
  name: "open-rooterp"
  root: "/home/adonis/apps-locales/erp-ligero-offline"
  stack:
    language: "javascript"
    runtime: "node"
    modules: "esm"
    db: "indexeddb"
    orm: "dexie"
    test_unit: "vitest"
    test_e2e: "playwright"
    test_adversarial: ["playwright", "k6", "zap", "axe-core"]

agent:
  model:
    provider: "opencode"
    name: "default"
    context_window: 100000
    checkpoint_threshold: 0.70
    force_checkpoint_threshold: 0.80

  skills:
    registry_path: "~/.config/opencode/skills"
    project_skills_path: ".opencode/skill"  # overrides/project-specific
    auto_load_level_a: true
    version_lock_file: "SKILL_SELECTION.md"

  modes:
    builder:
      skills: ["A-coding-standards", "A-project-architecture", "A-secure-coding", "B-javascript-clean", "C-database-design-offline", "C-dexie-patterns"]
      tools: ["fs", "shell", "linter", "test_runner:unit"]
    test_engineer:
      skills: ["A-testing", "C-dexie-patterns"]
      tools: ["test_runner:unit", "test_runner:integration", "test_runner:e2e", "metrics"]
    adversarial:
      skills: ["C-qa-breaker", "A-secure-coding", "A-testing", "B-authentication-security"]
      tools: ["test_runner:adversarial", "browser", "shell", "fs"]
      attack_categories: ["ATK-SEC", "ATK-INPUT", "ATK-STATE", "ATK-STRESS", "ATK-CHAOS", "ATK-CONC", "ATK-REC", "ATK-A11Y"]
    analyst:
      skills: ["C-debugging"]
      tools: ["shell", "fs", "git"]
    documenter:
      skills: ["C-documentation", "A-context-manager"]
      tools: ["fs", "shell"]

  human_gates:
    - phase: 1; decision: "approve_plan"
    - phase: 2; decision: "approve_adrs"
    - phase: 3; decision: "approve_skills"
    - phase: 4; decision: "code_review_critical"
    - phase: 7; decision: "release_gate_adversarial"
    - phase: 8; decision: "validate_root_cause"
    - phase: 11; decision: "sign_off_release"
    - phase: 14; decision: "approve_skill_changes"

  context_docs:
    - "ARCHITECTURE.md"
    - "CONTEXT.md"
    - "PROJECT_STATE.md"
    - "QA_RESULTS.md"
    - "SESSION.md"

  checkpoints:
    dir: ".saqi/checkpoints"
    max_checkpoints: 50
    compression_model: "lightweight-summarizer"
```

---

## 9. Referencias Internas

- SAQI-001: Resumen Ejecutivo
- SAQI-003: Gobernanza Skills (Skill Registry)
- SAQI-004: Marco QA (Modo Adversarial)
- SAQI-005: Proceso SAQI (Fases y modos)
- SAQI-013: Gestión Contexto (Context Manager)
- SAQI-018: Apéndice B - Plantillas Configuración Agente

---

## 10. Referencias Externas

1. Yao, S., et al. (2022). "ReAct: Synergizing Reasoning and Acting in Language Models." *ICLR*.
2. Wang, G., et al. (2023). "Voyager: An Open-Ended Embodied Agent with Large Language Models." *arXiv*.
3. Zhou, A., et al. (2023). "Agents: An Open-Source Framework for Language Agents." *arXiv*.
4. Lewis, P., et al. (2020). "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks." *NeurIPS*.
5. Ge, T., et al. (2023). "Long Context Compression for LLMs." *arXiv*.
6. OpenCode Agent Architecture (referencia implementativa).
7. Anthropic. (2024). "Building Effective Agents." *Anthropic Engineering Blog*.
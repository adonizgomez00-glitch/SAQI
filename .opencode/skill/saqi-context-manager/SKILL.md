---
name: saqi-context-manager
description: "Contexto persistente sin burocracia para SAQI: única fuente de verdad, actualización en checkpoints, prioriza arquitectura real, comandos, decisiones, estado historias, defectos, riesgos, validaciones."
---

# SAQI Context Manager

Contexto persistente sin burocracia. **Evita duplicar información entre archivos. Define una única fuente de verdad para cada clase de información.**

## Principios (SAQI §12)

| Principio | Acción |
|-----------|--------|
| Única fuente verdad | Cada clase info = 1 archivo autoritativo |
| Actualiza en checkpoints | No tras cada operación trivial |
| Prioriza lo accionable | Arquitectura, comandos, decisiones, estado |
| No docs obligatorias inútiles | Solo si facilita ejecución/recuperación/mantenimiento |

---

## Estructura de Archivos de Contexto

```
.saqi/
├── CONTEXT.md          # Estado actual del proyecto (fuente verdad principal)
├── ARCHITECTURE.md     # Decisiones arquitectónicas + ADRs
├── DECISIONS.md        # Log de decisiones importantes (inmutables)
├── STORIES.md          # Estado de historias (STORY-XXX: status, outcome, metrics)
├── DEFECTS.md          # Defectos conocidos + root causes + prevenciones
├── RISKS.md            # Riesgos y limitaciones pendientes
├── VALIDATIONS.md      # Resultados validaciones relevantes
├── HARNESS_LOG.jsonl   # Log validaciones harness (append-only)
├── SKILLS_LEARNING.md  # Registro aprendizaje skills
└── METRICS.jsonl       # Métricas por historia (append-only)
```

---

## CONTEXT.md - Fuente Verdad Principal

### Estructura Obligatoria
```markdown
# Project Context - [Project Name]

## Estado Actual
- **Fase:** [Plan | Architect | Build | Test | Deploy | Done]
- **Modo:** [ligero | estándar | riguroso]
- **Historia activa:** STORY-XXX / TECH-XXX / DEFECT-XXX
- **Sprint/Iteración:** ITER-XXX

## Arquitectura Real (no aspiracional)
- **Stack:** [Runtime, Framework, DB, Infra]
- **Boundaries:** [AuthZ, APIs públicas, Data integrity]
- **Constraints:** [Tech, Business, Security, Compliance]

## Comandos Clave
```bash
# Dev
bun dev              # Start dev
bun test             # Run tests
bun typecheck        # Type check
bun lint             # Lint

# CI Validation
bun run validate     # Full validation suite
```

## Decisiones Importantes (Últimas 5)
| Fecha | Decisión | ADR | Impacto |
|-------|----------|-----|---------|
| 2026-XX-XX | [Qué] | ADR-XXX | [Por qué] |

## Estado Historias
| Story | Status | Outcome | Métricas |
|-------|--------|---------|----------|
| STORY-042 | Done | User profile API | 2h, 3 ACs, 0 defects |
| STORY-043 | In Progress | Payment integration | 1/5 ACs done |

## Defectos Conocidos (Activos)
| Defect | Root Cause | Prevención | Status |
|--------|------------|------------|--------|
| DEF-012 | Race cache | Mutex + test | Monitoring |

## Riesgos Pendientes
| Riesgo | Likelihood | Impact | Mitigación | Owner |
|--------|------------|--------|------------|-------|
| Migration v2 | Medium | High | Rollback plan + test | Team |

## Validaciones Recientes
| Fecha | Tipo | Resultado | Detalles |
|-------|------|-----------|----------|
| 2026-XX-XX | Acceptance | PASS | 100% ACs |
| 2026-XX-XX | Security | PASS | 0 high/critical |
```

---

## ARCHITECTURE.md - Decisiones Estructurales

### Contenido
- **System Context Diagram** (C4 Level 1)
- **Container Diagram** (C4 Level 2) 
- **Component Diagram** (C4 Level 3) - solo áreas críticas
- **ADR Index** con links
- **Security Boundaries** (trust zones, data flow)
- **Data Integrity Rules** (invariants, transactions)
- **Public Contracts** (API schemas, event schemas)

### Formato ADR
```markdown
# ADR-XXX: [Título]

**Status:** Accepted / Superseded / Proposed
**Date:** 2026-XX-XX
**Deciders:** [Roles/Nombres]

## Context
[Qué fuerzas impulsan la decisión]

## Decision
[Qué se decide]

## Consequences
### Positive
- ...
### Negative  
- ...
### Risks
- ...

## Related
- SPEC-XXX
- ADR-YYY
```

---

## STORIES.md - Estado Historias

### Formato por Historia
```markdown
## STORY-XXX: [Título]

**Status:** [Planned | In Progress | In Review | Done | Blocked]
**Mode:** [ligero | estándar | riguroso]
**Started:** 2026-XX-XX
**Completed:** 2026-XX-XX (si Done)
**Outcome:** [Resultado verificable 1 frase]

**ACs:**
- [x] AC-01: ...
- [x] AC-02: ...
- [ ] AC-03: ...

**Métricas:**
- Tiempo: Xh
- Tokens: Y (si medible)
- Ciclos corrección: Z
- Defectos durante: N
- Defectos escapados: M
- Tests: Unit N, Int N, Acceptance N, E2E N

**Rollback:** [Git commit / Feature flag / Migration down]
**Known Issues:** [Ninguno / DEF-XXX]
```

---

## DECISIONS.md - Log Inmutable

### Formato
```markdown
| Fecha | Tipo | Decisión | Contexto | Alternativas | Impacto |
|-------|------|----------|----------|--------------|---------|
| 2026-XX-XX | Architecture | [Qué] | [Por qué] | [Otras] | [Impacto] |
| 2026-XX-XX | Tech Stack | [Qué] | [Por qué] | [Otras] | [Impacto] |
| 2026-XX-XX | Process | [Qué] | [Por qué] | [Otras] | [Impacto] |
```

---

## Actualización de Contexto

### Cuándo Actualizar (Checkpoints)
| Evento | Archivos a Actualizar |
|--------|----------------------|
| Historia completada | CONTEXT.md, STORIES.md, METRICS.jsonl |
| Decisión arquitectónica | ARCHITECTURE.md, DECISIONS.md, ADR-XXX.md |
| Defecto escapado | DEFECTS.md, SKILLS_LEARNING.md |
| Riesgo identificado | RISKS.md |
| Validación completada | VALIDATIONS.md, HARNESS_LOG.jsonl |
| Skill aprendida | SKILLS_LEARNING.md |

### NO Actualizar
- ❌ Tras cada commit
- ❌ Tras cada test run
- ❌ Tras cada operación trivial

---

## Recuperación de Contexto (Agent Startup)

### Carga Mínima Obligatoria
```typescript
function loadContext(): Context {
  return {
    current: read('.saqi/CONTEXT.md'),
    architecture: read('.saqi/ARCHITECTURE.md'),
    activeStory: getActiveStory(read('.saqi/STORIES.md')),
    knownDefects: read('.saqi/DEFECTS.md'),
    risks: read('.saqi/RISKS.md'),
    // NO cargar: HARNESS_LOG completo, METRICS completo, git history
  };
}
```

### Referencias en Lugar de Copias
```markdown
# En specs/tasks:
Ver CONTEXT.md sección "Estado Historias"
Ver ARCHITECTURE.md ADR-003 para auth boundary
Ver DEFECTS.md DEF-012 para cache race condition
```

---

## Anti-Patrones (MUST NOT)

| Anti-Patrón | Correcto |
|-------------|----------|
| Duplicar info en múltiples archivos | 1 fuente verdad + referencias |
| Actualizar tras cada commit | Actualizar en checkpoints |
| Docs obligatorias sin utilidad | Solo si facilita ejecución |
| Contexto completo en cada task | Carga selectiva mínima |
| Historial git como documentación | DECISIONS.md + ADRs |
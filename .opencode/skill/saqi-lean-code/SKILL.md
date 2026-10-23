---
name: saqi-lean-code
description: "Código lean y anti-overengineering para SAQI. Consolida: ponytail (lazy senior dev), ponytail-audit (auditoría repo), ponytail-review (review cambios), A-coding-standards, A-project-architecture. Submódulos: principles, audit, review, standards."
submodules:
  - principles: "Principios ponytail: smallest change, no abstraction sin necesidad, stdlib first"
  - audit: "Auditoría repo completa: bugs, risk, scale, missing tests, speed, lean"
  - review: "Review de cambios: correct, safe, scale, tested, fast, lean"
  - standards: "Coding standards y arquitectura: naming, structure, patterns"
---

# SAQI Lean Code

Consolida la filosofía "ponytail" (lazy senior dev) con estándares de coding y arquitectura. **El mejor código es el que no se escribe.**

## Submódulo: `principles` (Ponytail Core)

### Regla de Oro
> La solución más pequeña que resuelve completamente el problema. Una respuesta que un humano ocupado entiende en una lectura.

### Checklist Antes de Escribir
- [ ] **¿Necesita existir?** Salta features, opciones, flexibilidad que nadie pidió
- [ ] **¿Ya está en el codebase?** Helper, componente, servicio, pattern → úsalo
- [ ] **¿Stdlib o platform feature?** Úsalo, salvo que el proyecto tenga el suyo
- [ ] **¿Dependencia instalada?** Úsala. Nunca añadas dependencia para pocas líneas
- [ ] **¿Una línea legible?** Una línea. Si no, el mínimo código que funciona

### Niveles Ponytail
| Nivel | Comportamiento |
|-------|----------------|
| **lite** | Build what was asked. Nombra opción menor en una línea. |
| **full** | Default. Reglas arriba + finish every part (callers, tests, fixtures). |
| **ultra** | Cuestiona el request: push back en partes que la necesidad no justifica. |

### Never Cut (líneas rojas)
- Validación en trust boundaries
- Error handling que previene data loss
- Security, accessibility
- Calibración hardware real
- Lo que el user pidió explícitamente

### Shortcut Comments
```typescript
// shortcut: <el límite>, <cuándo actualizar>
// Ejemplo:
const MAX_RETRIES = 3; // shortcut: 3 retries covers 99% cases, increase if p99 latency > 5s
```

---

## Submódulo: `audit` (Ponytail-Audit)

### Auditoría Completa - Una sola pasada, cambia nada

**Orden de importancia:** correct → safe → holds under load → tested → fast → lean

#### 1. Map First
- Lee: README, deploy/build config, deps, entry points (main, routes, handlers, jobs, CLI), tests
- Traza flujos main end-to-end: data in → stored → out
- Big repo: go deep donde un error cuesta más

#### 2. Look For (Hallazgos)

| Categoría | Qué Buscar |
|-----------|------------|
| **Bug** | Wrong result, crash, edge case (empty, zero, last, rounding, tz), callers que discrepan, misma regla distinta en 2 lugares |
| **Risk** | Security holes (injection, weak randomness, secrets in code, missing input checks), data loss (swallowed errors, wrong write order, no transaction) |
| **Scale** | Check-then-write races, mismo work por cada proceso, memory/lists que solo crecen, query per item, O(n²), per-process state que debe shared |
| **Missing Test** | Risky logic (branch, parser, money, security, data writes) sin test que falle al romperse. **Un buen test, no coverage.** |
| **Speed** | Big slowdowns = problems. Small wins = suggestions. |
| **Lean** | Código que no debería existir o ser menor: delete (dead code, unused options, speculative features), reuse (2 helpers same thing), stdlib/native (platform ya lo hace), yagni (interface 1 impl, factory 1 product, wrapper pass-through), merge (near-copies), split (1 función varios jobs unrelated) |

#### 3. Output Format
```
What this repo does: [2-3 sentences] + load assumed

Must fix:
1. **Finding** (`file:line`)
   - What: ...
   - Problem: ...
   - Fix: ...
   - If skip: ...

Should fix:
2. ...

Nice to have:
3. ...

Verdict: healthy / what to fix first
Lean: -N lines, -M deps possible
Not checked: [partes no leídas]
```

---

## Submódulo: `review` (Ponytail-Review)

### Review de Cambio - Como senior que será paged cuando rompa

**Lee:** diff → código tocado (callers, callees, tests, README) → traza flujo real

#### Hallazgos (mismo orden: correct → safe → scale → tested → fast → lean)

| Grupo | Qué Buscar |
|-------|------------|
| **Must fix** | Bug, security, data loss, breaks at expected load |
| **Should fix** | Risky code sin test, real slowness, duplication, función mixea jobs, código que no debería existir |
| **Nice to have** | Small speed-ups, shorter forms |

#### Cada Hallazgo = 4 Partes (1-2 oraciones c/u)
```
N. **Título** (`file:Lstart-Lend`)
   - **What this is:** ...
   - **Problem:** ...
   - **Fix:** ...
   - **If we skip it:** ...
```

#### Output
```
What this change does: [2-3 sentences]

Must fix:
1. ...

Should fix:
2. ...

Nice to have:
3. ...

Verdict: Ship. / fix 1 and 3 first.
Lean: -N lines possible.
Not checked: [one line if something mattered]
```

---

## Submódulo: `standards` (Coding Standards + Architecture)

### Naming
- **Archivos:** kebab-case (`user-service.ts`, `user-service.test.ts`)
- **Types/Interfaces:** PascalCase (`UserService`, `UserConfig`)
- **Funciones/Variables:** camelCase (`getUser`, `userConfig`)
- **Constantes:** UPPER_SNAKE_CASE (`MAX_RETRIES`, `DEFAULT_TIMEOUT`)
- **Tests:** `describe('Feature', () => { it('should do X when Y', ...) })`

### Estructura
- **Colocation:** Test junto a código (`*.test.ts` al lado de `*.ts`)
- **Barrel exports:** `index.ts` re-exporta public API
- **Internal:** `_` prefix o carpeta `internal/` para no-público
- **Una responsabilidad por archivo/módulo**

### Patterns SAQI
- **Effect-first:** `Effect.gen`, `Effect.fn`, `Schema` para APIs, `Schema.TaggedErrorClass` para errores
- **Thin handlers:** decode → context → service → map errors
- **Explicit layers:** no hidden provisioning
- **Test layers:** explicit dependency provisioning en test file

### Architecture Decisions (ADRs)
- Documenta decisiones estructurales reales: security boundaries, public contracts, data integrity, tech constraints
- No diseñes toda la arquitectura por anticipado
- Permite evolución con evidencia real

### Anti-Patterns Prohibidos
| Anti-Pattern | Correcto |
|--------------|----------|
| Abstracción sin necesidad demostrada | Código concreto, inline |
| Interface/factory/capa/pattern innecesario | Función directa |
| Funcionalidad no solicitada | YAGNI |
| Refactor módulos no relacionados | Solo lo que toca la tarea |
| Reemplazar tech sin justificación | Mantener existing |
| Reescribir archivo completo para cambio local | Edit quirúrgico |
| Añadir dependencia pudiendo usar existente | Stdlib/stdlib primero |

---

## Integración SAQI

### En Modo Ligero
- Aplica `principles` nivel lite
- No generes planes extensos, ADRs, specs independientes

### En Modo Estándar
- `principles` full + `review` del diff
- `audit` solo si cambio toca área crítica

### En Modo Riguroso
- `audit` completo del área afectada
- `review` exhaustivo + `standards` compliance
- ADR si decisión arquitectónica nueva

### Gates de Calidad
```bash
# Antes de commit
bun typecheck && bun lint && bun test -- --only-failures

# Review automatizado (si configurado)
ponytail-review  # o saqi-lean-code:review
```

---

## Referencias Globales
- `~/.config/opencode/skills/ponytail/SKILL.md` (principles)
- `~/.config/opencode/skills/ponytail/skills/ponytail-audit/SKILL.md` (audit)
- `~/.config/opencode/skills/ponytail/skills/ponytail-review/SKILL.md` (review)
- `~/.config/opencode/skills/generic/A-coding-standards/SKILL.md` (standards)
- `~/.config/opencode/skills/generic/A-project-architecture/SKILL.md` (architecture)
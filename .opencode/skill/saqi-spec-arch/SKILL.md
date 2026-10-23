---
name: saqi-spec-arch
description: "SDD ligero, decisiones de arquitectura, patrones Effect para SAQI. Consolida: A-sdd (Spec-Driven Development), A-project-architecture, effect (Effect TS patterns). Submódulos: sdd, architecture, effect-patterns."
submodules:
  - sdd: "Spec-Driven Development: spec como contrato único, AC=tests, trazabilidad"
  - architecture: "Decisiones estructurales: security boundaries, public contracts, data integrity"
  - effect-patterns: "Effect v4: Effect.gen, Effect.fn, Schema, thin handlers, explicit layers"
---

# SAQI Spec & Architecture

Consolida SDD ligero, arquitectura evolutiva y patrones Effect. **La spec no es documentación, es contrato ejecutable.**

## Submódulo: `sdd` (A-SDD - Spec-Driven Development)

### Principio Rector SDD+SAQI
> **La spec es el contrato único de verdad** — anterior al código, fuente de verdad para humanos y agentes IA, criterio de validación final. Si no se puede testear, no está lista.

### Ciclo SDD Integrado en SAQI (REGLAS MUST)

| ID | Regla |
|----|-------|
| **A-SDD-001** | **MUST**: Fase 1 (Plan) no inicia sin spec madura — Checklist SDD completado. Gate: `SPEC_READY = true` |
| **A-SDD-002** | **MUST**: **Spec = Contrato único** — Código, tests, ADRs, docs derivados de spec. Spec versionada en `docs/specs/SPEC-XXX.md` (SemVer) |
| **A-SDD-003** | **MUST**: **Criterios de aceptación = Tests L3 Functional** — Cada AC (Given/When/Then) → test automatizado en `tests/functional/`. 1:1 mapping |
| **A-SDD-004** | **MUST**: **Exclusiones explícitas** — Sección `No incluye` obligatoria. Sin exclusiones → spec rechazada |
| **A-SDD-005** | **MUST**: **Spec antes que código** — TDD asistido: test falla (Red) desde AC → implementa (Green) → refactor. Agente IA usa spec como contexto primario |
| **A-SDD-006** | **MUST**: **Validación contra spec, no opiniones** — Gate Fase 10/11: `npm run test:acceptance` (tests derivados de spec) = 100% pass |

### Estructura Canónica de Spec (MUST)

| Sección | Requerida | Descripción |
|---------|-----------|-------------|
| Título, Problema, Contexto, Objetivo | ✅ | Contexto acotado: usuario real, situación concreta, limitaciones reales |
| Alcance: Incluye / No incluye | ✅ | Ambos no vacíos. "No incluye" evita scope creep |
| Comportamiento: Flujo principal + alternativos + edge cases | ✅ | Mínimo 1 ejemplo ejecutable por flujo crítico |
| Criterios Aceptación (Given/When/Then) | ✅ | Binarios, verificables, 1:1 con tests |
| Restricciones: Técnicas + Negocio + Seguridad | ✅ | Explícitas, no implícitas |

### Objetivo Falsable (MUST)
- ✅ "El sistema crea usuario con estado pending_verification"
- ❌ "Mejora la experiencia de registro", "Optimiza performance", "Facilita el uso"

### Integración con Agentes IA (MUST)
- **Spec = Contexto primario** en `saqi.agent.yaml` mode `builder`: `context_docs: ["specs/SPEC-XXX.md", "ARCHITECTURE.md", "CONTEXT.md"]`
- **Agente NO decide fuera de spec** — Si ambigüedad → para y pregunta humano. No "inventa" comportamiento
- **Feedback loop:** Si test falla por ambigüedad → actualiza spec → regenera. Ciclo cerrado

### Trazabilidad Bidireccional (MUST)
```
SPEC-001-AC-01 → test_functional_SPEC001_AC01 → src/service/UserService.ts:45
```
- Cambio en spec = Nueva versión + Impact analysis → `ITERATION_PLAN.md` actualizado
- Living documentation: `jsdoc`/`typedoc` genera docs desde código + spec. Spec NO duplicada en comentarios código

### Anti-Patrones Prohibidos (MUST NOT)
| Anti-Patrón | Correcto |
|-------------|----------|
| Empezar Build sin spec madura | Fase 1 gate: `SPEC_READY = true` |
| Spec como "wishlist" sin criterios falsables | Objetivo binario + AC Given/When/Then |
| Sin sección "No incluye" | Exclusiones explícitas obligatorias |
| Contexto vago ("usuario final", "uso general") | Usuario concreto, escenario real, límites |
| Agente IA "inventa" comportamiento | Agente para y pregunta humano |
| Tests escritos antes que AC | AC → Test (1:1) |
| Código sin trazabilidad a spec | Spec ID en test + código (comentario o metadata) |
| Cambiar código sin actualizar spec | Spec versionada + impact analysis |

### Plantilla Spec Canónica
```markdown
---
spec_id: "SPEC-001"
version: "1.0.0"
status: "active"
title: "Registro de Usuario (Backend)"
authors: ["PM", "Tech Lead"]
created: "2026-07-18"
related_specs: ["SPEC-002"]
related_adrs: ["ADR-003"]
tags: ["auth", "backend"]
---

# SPEC-001: Registro de Usuario (Backend)

## Problema
Permitir creación segura y consistente de cuentas, evitando duplicados.

## Contexto
Backend recibe peticiones de registro desde web, móvil, API externa. Única fuente verdad.

## Objetivo
Dado email y password válidos → crea usuario persistido en estado pending_verification.

## Alcance
### Incluye
- Endpoint registro
- Validación email único
- Validación password policy
- Persistencia + hash Argon2id
- Evento "usuario registrado"

### No incluye
- Render formularios
- Login/sesión
- Envío email verificación (SPEC-005)

## Comportamiento
### Flujo Principal
1. Recibe solicitud con email + password
2. Verifica email no existe
3. Valida password policy
4. Hash password (Argon2id, 100k iter)
5. Crea usuario status="pending_verification"
6. Publica evento dominio

### Edge Cases
- Email duplicado → Error conflicto
- Password inválido → Error validación
- BD unavailable → Error 503 (retry policy)

## Criterios de Aceptación
- **AC-01**: Dado email no registrado + password válido → crea usuario pending_verification
- **AC-02**: Dado email existente → no crea usuario, error conflicto
- **AC-03**: Dado password inválido → no crea usuario, error validación
- **AC-04**: Dado BD falla → error 503, no usuario parcial

## Restricciones
- Técnicas: Hash Argon2id (mín 100k), endpoint idempotente
- Negocio: RGPD email, auditoría
- Seguridad: Rate limit 5 req/min/IP, logging sin PII
```

### Test Derivado (1:1 Mapping)
```typescript
// tests/functional/SPEC-001-user-registration.test.ts
describe('SPEC-001: User Registration', () => {
  it('AC-01: Creates user with pending_verification on valid input', async () => { /* ... */ });
  it('AC-02: Rejects duplicate email with conflict error', async () => { /* ... */ });
  it('AC-03: Rejects invalid password with validation error', async () => { /* ... */ });
  it('AC-04: Handles DB failure gracefully', async () => { /* ... */ });
});
```

### Spec Validation Checklist (Gate Fase 1)
```bash
# Validación automatizada
specmark validate SPEC-001.md  # 0 errors

# Review obligatorio (30 min máx)
# Firmas: PM + Tech Lead + QA Lead
```

---

## Submódulo: `architecture` (Decisiones Estructurales)

### Qué Documentar (Desde Inicio)
- **Security boundaries:** AuthZ, AuthN, data encryption, trust boundaries
- **Public contracts:** API schemas, event schemas, interfaces externas
- **Data integrity:** Transactions, invariants, consistency models
- **Tech constraints:** Runtime, language, framework, infra decisions

### Qué NO Documentar Por Anticipado
- Internal module structure
- Helper functions
- Implementation patterns (evolucionan con evidencia)

### ADR Template
```markdown
# ADR-XXX: [Título Decisión]

## Status
Proposed / Accepted / Superseded

## Context
[Qué fuerzas impulsan la decisión - técnico, negocio, riesgo]

## Decision
[Qué se decide, conciso]

## Consequences
### Positive
- ...
### Negative
- ...
### Risks
- ...

## Alternatives Considered
1. [Opción] - [Por qué no]
2. [Opción] - [Por qué no]

## Related
- SPEC-XXX
- ADR-YYY
```

### Evolución Arquitectónica
- Permite evolución con evidencia real
- Documenta decisiones importantes cuando afecten futuras tareas
- No obligues arquitectura completa upfront

---

## Submódulo: `effect-patterns` (Effect TS v4)

### Source of Truth
- Usa `.opencode/references/effect` (clone de `https://github.com/Effect-TS/effect`)
- Busca APIs exactas, ejemplos, tests, naming patterns ANTES de implementar
- Inspecciona código repo existente para house style local

### Guidelines (MUST)

| Pattern | Uso |
|---------|-----|
| `Effect.gen(function* () { ... })` | Multi-step workflows |
| `Effect.fn("Name")` / `Effect.fnUntraced(...)` | Named effects para service methods / workflows importantes |
| `Schema` | API y domain data shapes. Branded schemas para IDs. `Schema.TaggedErrorClass` para typed domain errors |
| Thin handlers | Decode input → read context → call services → map transport errors. Business rules en services |
| Effect-aware deps | Prefer Effect platform abstractions over ad hoc promises |
| Explicit layers | Avoid broad hidden provisioning |
| Test helpers | `testEffect(...)`, `it.live(...)` para filesystem, git, HTTP, sockets, child processes, locks, real time |

### Testing Patterns
```typescript
// packages/core/test/lib/effect.ts
testEffect(layer) // adds your layer
it.effect(...) // runs on TestClock
it.live(...) // filesystem, git, HTTP, sockets, child processes, locks, real time
```

### Anti-Patterns (MUST NOT)
- ❌ `any`, non-null assertions, unchecked casts
- ❌ Older Effect APIs para satisfacer types
- ❌ Responder de memoria - verifica contra `.opencode/references/effect` o código cercano
- ❌ `Effect.runPromise` bare (usa default real clock) - captura context con `yield* Effect.context()`

### Clock Control
```typescript
// Fork, then adjust
Effect.forkChild({ startImmediately: true })
// or await Deferred then Effect.yieldNow()

// Debounced streams
advance(condition) + drain() // alternates 1ms real + 500ms adjusts
```

---

## Integración SAQI

### Modo Ligero
- Solo `sdd`: spec ligera (título, objetivo, 1-2 ACs), no plantilla completa
- `architecture`: solo si toca security boundary o public contract
- `effect-patterns`: solo si código tocado ya usa Effect

### Modo Estándar
- `sdd`: spec completa con plantilla, ACs Given/When/Then
- `architecture`: ADR si decisión nueva afecta boundaries/contracts
- `effect-patterns`: aplica guidelines a código nuevo/modificado

### Modo Riguroso
- `sdd`: spec completa + validación automatizada + review PM+Tech+QA
- `architecture`: ADR obligatorio + threat model si security
- `effect-patterns`: explicit test layers + it.live para I/O real

### Gates
```bash
# Spec validation
specmark validate SPEC-XXX.md
# Test acceptance (derivados de spec)
npm run test:acceptance  # 100% pass
# Trazabilidad
check-traceability  # 100% ACs mapped
```
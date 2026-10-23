---
name: saqi-vertical-slice
description: "Historias pequeñas y vertical slices para SAQI. Cada historia: resultado útil verificable, criterios aceptación claros, alcance mínimo, componentes necesarios, funcionalidad demostrable, tests según riesgo, reversible independientemente."
---

# SAQI Vertical Slice

**Desarrollo incremental por resultados** — flujo predeterminado mediante historias pequeñas y funcionalidades verticales completas.

## Definición de Vertical Slice

> Una vertical slice atraviesa las capas necesarias para entregar **valor real**. No obligues al agente a terminar toda la BD, todas las API o todo el frontend antes de conseguir una funcionalidad operativa.

### Características Obligatorias (MUST)
| Criterio | Descripción |
|----------|-------------|
| **Resultado útil** | Funcionalidad que un usuario real puede usar/verificar |
| **Verificable** | Criterios de aceptación binarios (pass/fail) |
| **Alcance mínimo** | Solo lo necesario para el resultado |
| **Componentes necesarios** | Modifica únicamente lo que toca la historia |
| **Demostrable** | Funcionalidad operable al cerrar la historia |
| **Tests según riesgo** | TDD selectivo (§7) - no coverage por coverage |
| **Reversible** | Git revert / feature flag / rollback plan |

---

## Plantilla de Historia (Story Template)

```markdown
# STORY-XXX: [Título Descriptivo]

## Resultado Esperado (Outcome)
[Qué puede hacer el usuario al terminar - verificable binariamente]

## Criterios de Aceptación (Given/When/Then)
- **AC-01**: Dado [contexto] Cuando [acción] Entonces [resultado verificable]
- **AC-02**: Dado [contexto] Cuando [acción] Entonces [resultado verificable]
- **AC-03**: ...

## Restricciones
- Técnicas: [ej. latency < 200ms, Argon2id 100k iter]
- Negocio: [ej. RGPD, auditoría]
- Seguridad: [ej. rate limit, no PII logs]

## Componentes Afectados
- [ ] Frontend: [componentes específicos]
- [ ] Backend: [servicios/endpoints específicos]
- [ ] Database: [migración sí/no, tablas]
- [ ] Infra: [config, deploy, secrets]

## Dependencias
- Bloquea: [STORY-YYY]
- Bloqueada por: [STORY-ZZZ]
- Contratos: [API schemas, events]

## Tests Requeridos (Según Riesgo)
- [ ] Unit: [qué lógica pura]
- [ ] Integration: [qué interacciones]
- [ ] Acceptance: 1:1 desde ACs
- [ ] E2E: [solo si flujo crítico user-facing]
- [ ] Adversarial: [solo si riesgo alto]

## Definición de Terminado (DoD)
- [ ] Código implementado + tests pasando
- [ ] Criterios aceptación verificados (manual/auto)
- [ ] Typecheck + lint passing
- [ ] Documentación actualizada (CONTEXT.md, CHANGELOG.md)
- [ ] Reversible: git revert / feature flag probado
- [ ] No rompe tests existentes

## Estimación
- Modo: [ligero | estándar | riguroso] — ver saqi-adaptive-mode
- Esfuerzo: [horas/días]
- Riesgo: [bajo | medio | alto]
```

---

## Checklist Vertical Slice (Por Historia)

### Antes de Empezar
- [ ] Historia cabe en una pantalla (≤ 5 ACs)
- [ ] Outcome es **binario** (no "mejora", "optimiza")
- [ ] "No incluye" explícito en spec relacionada
- [ ] Dependencias identificadas y disponibles
- [ ] Modo seleccionado con `saqi-adaptive-mode`

### Durante Implementación
- [ ] Una vertical slice a la vez (no horizontal layers)
- [ ] Tests escritos **antes o junto** con código (TDD selectivo)
- [ ] Commits pequeños, atómicos, con mensaje convencional
- [ ] No refactor código no relacionado
- [ ] No añadas abstracciones sin necesidad demostrada

### Al Cerrar Historia
- [ ] Todos los ACs verificados (pass/fail binario)
- [ ] Tests requeridos pasando (100%)
- [ ] Typecheck + lint clean
- [ ] `CONTEXT.md` actualizado: estado historia, decisiones, known issues
- [ ] `CHANGELOG.md` entry
- [ ] Rollback probado (git revert o feature flag off)
- [ ] Métricas registradas (tokens, tiempo, ciclos corrección)

---

## Anti-Patrones (MUST NOT)

| Anti-Patrón | Correcto |
|-------------|----------|
| Horizontal layer: "primero toda la BD, luego API, luego UI" | Vertical slice: DB + API + UI para UN feature |
| Historia = "refactor module X" | Historia = "usuario puede hacer Y" (refactor es tarea técnica separada) |
| ACs vagos: "mejora performance" | AC binario: "latency p99 < 200ms en endpoint X" |
| Historia depende de 5 otras para valor | Historias independientes, value incremental |
| Tests escritos al final | Tests derivados de ACs (1:1) |
| Cambio esquemático sin historia | Migración = tarea técnica con outcome verificable |

---

## Tareas Técnicas Puras (Excepción Vertical Slice)

Cuando vertical slice no es apropiado (migraciones, refactors, infra):
- **Sigue flujo adaptativo** (`saqi-adaptive-mode`)
- **Exige outcome verificable** (ej. "migración aplica sin data loss, rollback < 5min")
- **Documenta en `TECH-XXX`** en lugar de `STORY-XXX`
- **Mismo DoD** + rollback testado

---

## Ejemplos

### ✅ Buena Vertical Slice
```
STORY-042: Usuario ve su perfil
Outcome: GET /api/profile retorna {id, email, name, avatar} del usuario autenticado
ACs: 3 (success, not found, unauthorized)
Componentes: 1 endpoint + 1 service + 1 repo
Tests: unit (service) + acceptance (3 ACs)
DoD: Verificado en staging, rollback probado
```

### ❌ Mala (Horizontal Layer)
```
STORY-042: Implementar UserRepository
Outcome: Repository pattern completo con CRUD
ACs: 10 (create, read, update, delete, list, filters...)
Componentes: Solo repo (sin endpoint, sin UI)
Tests: Solo unit del repo
Problema: No entrega valor usuario hasta que se añada API + UI
```

---

## Métricas por Historia
| Métrica | Target |
|---------|--------|
| ACs por historia | 1-5 (ideal 3) |
| Archivos tocados | < 10 (estándar), < 3 (ligero) |
| Tiempo cycle time | < 2 días (estándar) |
| Tests por AC | 1 acceptance + unit según riesgo |
| Defectos escapados | 0 |
| Rollback time | < 5 min |
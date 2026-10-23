---
name: saqi-skills-learning
description: "Aprendizaje de skills para SAQI: detectar→root cause→evaluar prevención→aplicar→versionar. Evita reglas automáticas por cada error. Prioriza prevenciones ejecutables y comprobables."
---

# SAQI Skills Learning

Sistema de aprendizaje de skills versionadas. **No cada error genera regla permanente.** Prioriza prevenciones ejecutables sobre instrucciones escritas.

## Flujo de Aprendizaje (8 Pasos - SAQI §9)

```
1. DETECTAR    → Reproducir defecto en aislamiento
2. ROOT CAUSE  → 5 Whys / Fishbone / Debug sistemático
3. CLASIFICAR  → Error aislado vs Patrón recurrente
4. EVALUAR     → ¿Prevención viable? (Skill, Test, Lint, Contract, Tool)
5. APLICAR     → Mecanismo más simple y eficaz
6. VALIDAR     → No contradice otras rules
7. VERSIONAR   → Si corresponde (SemVer skill)
8. REGISTRAR   → Impacto en futuras tareas
```

---

## Paso 1: Detectar y Reproducir

### Requisitos
- [ ] Defecto reproducido en entorno controlado
- [ ] Pasos mínimos para reproducir documentados
- [ ] Contexto: input, estado, ambiente, versión

### Template Defecto
```markdown
# DEFECT-XXX: [Título]

## Reproducción
1. [Paso 1]
2. [Paso 2]
3. [Resultado observado vs esperado]

## Contexto
- Input: ...
- Estado previo: ...
- Ambiente: OS, runtime, deps
- Versión: commit/tag

## Impacto
- Usuario afectado: ...
- Severidad: [baja | media | alta | crítica]
- Frecuencia: [siempre | intermitente | rara]
```

---

## Paso 2: Root Cause Analysis

### Técnicas
| Técnica | Cuándo |
|---------|--------|
| **5 Whys** | Defecto simple, cadena causal lineal |
| **Fishbone (Ishikawa)** | Múltiples factores (people, process, tech, env) |
| **Debug sistemático** | Binary search en código, logs, traces |
| **Change analysis** | Qué cambió entre versión OK y fallando |

### Output Root Cause
```markdown
## Root Cause
**Causa raíz:** [Descripción concisa - 1 frase]

**Evidencia:** [Logs, traces, test que falla, code path]

**Categoría:**
- [ ] Bug en lógica de negocio
- [ ] Race condition / concurrencia
- [ ] Boundary condition no manejada
- [ ] Config/environment issue
- [ ] Dependency behavior change
- [ ] Spec ambigua / incompleta
- [ ] Test faltante / incorrecto
- [ ] Tooling / CI issue
```

---

## Paso 3: Clasificar - Aislado vs Recurrente

### Criterios Patrón Recurrente
- [ ] Mismo root cause en ≥ 2 defectos previos (últimos 30 días)
- [ ] Mismo módulo/patrón afectado repetidamente
- [ ] Equipo reporta "siempre pasa lo mismo"
- [ ] Fix previo no previno recurrencia

### Si Aislado → Fix Puntual
- Fix mínimo en código
- Test de regresión específico
- **No** crear skill/regla

### Si Recurrente → Evaluar Prevención (Paso 4)

---

## Paso 4: Evaluar Mecanismo de Prevención

### Jerarquía de Prevención (Mejor → Peor)
| Nivel | Mecanismo | Ejemplo | Costo Mantenimiento |
|-------|-----------|---------|---------------------|
| **1** | **Test automatizado** | Unit/integration test que falle al romperse | Bajo |
| **2** | **Lint rule / Type constraint** | ESLint rule, TypeScript strict, schema validation | Bajo |
| **3** | **Contract / Schema** | OpenAPI spec, JSON Schema, Protobuf | Medio |
| **4** | **Tool improvement** | Better compiler warning, IDE integration | Medio |
| **5** | **Skill / Documentation** | Skill rule, checklist, runbook | Alto (requiere lectura humana) |

### Decision Matrix
```
¿Puede prevenirse con test automatizado?
  SÍ → Escribe test (nivel 1) → DONE
  NO → ¿Lint rule / type constraint?
    SÍ → Añade rule (nivel 2) → DONE
    NO → ¿Contract / schema?
      SÍ → Define contract (nivel 3) → DONE
      NO → ¿Tool improvement?
        SÍ → Mejora tooling (nivel 4) → DONE
        NO → Skill/Documentación (nivel 5) → SOLO SI justificado
```

### Justificación Requerida para Nivel 5 (Skill)
- [ ] Prevención ejecutable NO viable (por qué)
- [ ] Requiere juicio humano contextual
- [ ] Patrón cognitivo, no técnico
- [ ] Documentación de "por qué no hacer X"

---

## Paso 5: Aplicar - El Más Simple y Eficaz

### Principios
- **Una prevención por root cause** (no capas)
- **Ejecutable > Documental** (test/lint/contract > skill/doc)
- **Específico > General** (test para X > regla genérica)
- **Automatizado en CI** (no manual)

### Ejemplos Aplicación

| Root Cause | Prevención Aplicada | Por Qué |
|------------|---------------------|---------|
| Null pointer en user.email | TypeScript `NonNullable<User['email']>` + test | Nivel 2 - compile-time |
| Race condition cache | Test property-based (fast-check) + mutex | Nivel 1 - executable |
| SQL injection risk | Parameterized query enforcement via lint | Nivel 2 - compile-time |
| Spec ambigua en AC-03 | Spec validator (specmark) en CI | Nivel 3 - contract |
| Secret en commit | TruffleHog + pre-commit hook | Nivel 4 - tooling |
| "Olvidamos validar X" | Checklist en PR template | Nivel 5 - SOLO si no automatizable |

---

## Paso 6: Validar - No Contradecir

### Checks Obligatorios
- [ ] No rompe tests existentes (`bun test`)
- [ ] No contradice skills activas (`saqi-lean-code`, `saqi-spec-arch`, etc.)
- [ ] No introduce over-engineering (ver `saqi-lean-code:principles`)
- [ ] Performance impact aceptable
- [ ] DX impact: ¿hace la vida más fácil o más difícil?

### Conflict Resolution
```
Si nueva prevención contradice existente:
  1. ¿Es la existente obsoleta? → Deprecate old, adopt new
  2. ¿Es la nueva más específica? → Scope new, keep general
  3. ¿Son incompatibles? → Human decision, document rationale
```

---

## Paso 7: Versionar Skill (Si Corresponde)

### Cuándo Versionar
- Nueva prevención nivel 5 (skill/doc) que aporta valor generalizable
- Cambio significativo en skill existente
- Consolidación de múltiples rules en una

### Versionado SemVer Skills
| Cambio | Version Bump |
|--------|--------------|
| Bug fix en skill (typo, claridad) | PATCH (1.0.1) |
| Nueva rule/pattern en skill existente | MINOR (1.1.0) |
| Breaking change (rule removida/cambiada semántica) | MAJOR (2.0.0) |

### Changelog Skill
```markdown
## [1.1.0] - 2026-XX-XX
### Added
- Rule: "Valida email único en registro" (previene DEF-042, DEF-055)
### Changed
- Updated: "Password policy" para Argon2id 100k (was bcrypt)
### Deprecated
- Rule: "Manual email check" (replaced by schema validation)
```

---

## Paso 8: Registrar Impacto

### Métricas de Aprendizaje
| Métrica | Target |
|---------|--------|
| Defectos recurrentes / total | < 20% |
| Tiempo detectar → prevención aplicada | < 2 iteraciones |
| Prevenciones nivel 1-4 / total | > 80% |
| Skills activas con ≥ 1 prevención | 100% |
| False positives (skill flagga OK) | < 5% |

### Registro en `SKILLS_LEARNING_LOG.md`
```markdown
| Date | Defect | Root Cause | Prevention | Level | Skill Version | Impact |
|------|--------|------------|------------|-------|---------------|--------|
| 2026-XX-XX | DEF-042 | Null email | TS NonNullable + test | 1-2 | saqi-lean-code 1.2.0 | 0 recurrences |
```

---

## Carga Selectiva de Skills (Evita Bloat)

### Principio
> **Carga solo skills aplicables a la tarea actual.**

### Selector Automático
```typescript
function selectSkills(task: Task): Skill[] {
  const skills = ['saqi-lean-code:principles']; // Siempre
  
  if (task.type === 'testing') skills.push('saqi-testing');
  if (task.type === 'spec') skills.push('saqi-spec-arch:sdd');
  if (task.securityRisk >= 'medium') skills.push('saqi-qa-harness:security');
  if (task.requiresArchitecture) skills.push('saqi-spec-arch:architecture');
  if (task.isRefactor) skills.push('saqi-lean-code:audit');
  
  // Skills learning: solo si task involucra defect analysis
  if (task.type === 'defect-analysis') skills.push('saqi-skills-learning');
  
  return skills;
}
```

### Manual Override
- Agent puede añadir/quitar skills basado en contexto
- Documentar por qué en `CONTEXT.md`

---

## Anti-Patrones Learning (MUST NOT)

| Anti-Patrón | Correcto |
|-------------|----------|
| Cada bug → nueva skill rule | Fix + test de regresión |
| Skill crece sin límite | Podar rules obsoletas cada quarter |
| Rules genéricas ("valida inputs") | Rules específicas ("valida email único en registro") |
| Skill sin tests que la validen | Skill con test que falle si rule rota |
| Aprendizaje solo documental | Prevención ejecutable primero |

---

## Integración SAQI

### En Modo Ligero
- Solo fix + test regresión
- No root cause formal si obvio

### En Modo Estándar
- Flujo completo 1-8
- Skill update si recurrente

### En Modo Riguroso
- Root cause formal obligatorio
- Prevención nivel 1-3 preferida
- Skill versionada + changelog
- Métricas registradas
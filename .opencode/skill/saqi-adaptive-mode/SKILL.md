---
name: saqi-adaptive-mode
description: "Modo adaptativo de ejecución para SAQI: ligero/estándar/riguroso según tamaño, complejidad y riesgo. Define criterios concretos para elegir modo y permite elevar nivel ante nuevos riesgos."
---

# SAQI Adaptive Mode

Política explícita para seleccionar profundidad del proceso según **tamaño**, **complejidad** y **riesgo** de la tarea.

## Matriz de Decisión

| Factor | Ligero | Estándar | Riguroso |
|--------|--------|----------|----------|
| **Archivos tocados** | 1-2 | 3-10 | 10+ |
| **Componentes afectados** | 1 (local) | 2-3 | 4+ / cross-system |
| **Riesgo seguridad** | Ninguno | Bajo | Alto / AuthZ, AuthN, datos sensibles |
| **Datos** | No persistentes | Persistentes no críticos | Sensibles / PII / Financieros |
| **Concurrencia** | No | Baja | Alta / Race conditions |
| **Esquema BD** | No cambia | Cambia (migración safe) | Cambia con riesgo / Data loss possible |
| **Arquitectura** | No toca | Toca boundaries | Crítica / Pre-release |
| **Reversibilidad** | Trivial (git revert) | Feature flag / rollback plan | Requiere plan migración + rollback testado |
| **Tiempo estimado** | < 2h | 2h - 2d | 2d+ |

## Criterios Concretos por Modo

### 🟢 MODO LIGERO
**Para:** Correcciones pequeñas, cambios locales, scripts sencillos, tareas de bajo riesgo

**Flujo (6 pasos):**
1. Comprender comportamiento esperado
2. Inspeccionar código relevante
3. Implementar cambio mínimo
4. Ejecutar pruebas pertinentes
5. Review del diff
6. Registrar resultado

**No generes:** Planes extensos, diagramas, ADR, specs independientes

**Skills activas:** `saqi-lean-code:principles` (lite), `saqi-testing:unit`

---

### 🟡 MODO ESTÁNDAR
**Para:** Funcionalidades que afectan varios archivos/componentes

**Flujo (7 pasos):**
1. Definir resultado y criterios de aceptación
2. Crear historia pequeña + plan breve
3. Identificar dependencias y contratos
4. Implementar vertical slice
5. Ejecutar tests unit/integration/E2E según corresponda
6. Review cambios + regresiones
7. Consolidar + actualizar estado proyecto

**Skills activas:** `saqi-vertical-slice`, `saqi-testing` (relevant submodule), `saqi-lean-code:principles` (full), `saqi-spec-arch:sdd`

---

### 🔴 MODO RIGUROSO
**Para:** Auth, AuthZ, seguridad, datos sensibles, concurrencia, esquema BD riesgo, arquitectura crítica, pre-release

**Flujo (8 pasos):**
1. Specs y contratos explícitos (`saqi-spec-arch:sdd`)
2. Análisis impacto + amenazas (`saqi-qa-harness:security` threat model)
3. Tests aceptación + regresión (`saqi-qa-harness:standards`)
4. TDD en reglas críticas (`saqi-spec-arch:sdd` + `saqi-testing`)
5. Análisis estático + verificaciones auto (`saqi-qa-harness:security` SAST)
6. QA adversarial proporcional (`saqi-qa-harness:adversarial` ciclo profundo)
7. Migraciones, recuperación, compatibilidad, rollback testados
8. Documentar riesgos residuales + resultados

**Skills activas:** Todas las relevantes + `saqi-qa-harness` completo

---

## Reglas de Transición

### ✅ PERMITIDO: Elevar Nivel
- Aparece nuevo riesgo durante ejecución → **eleva inmediatamente**
- Descubres que afecta security boundary → **riguroso**
- Encuentras race condition → **riguroso**
- Cambio toca más archivos de lo estimado → **estándar/riguroso**

### ❌ PROHIBIDO: Rebajar Nivel
- **Nunca** rebajes automáticamente si existen requisitos security/compliance obligatorios
- Si empezaste en riguroso por security, terminas en riguroso
- Compliance requirements son floor, no ceiling

### 🔄 Re-evaluación
En cada checkpoint significativo:
- ¿Cambió el riesgo?
- ¿Afecta más componentes de lo planeado?
- ¿Surgen unknowns que elevan riesgo?

---

## Selector Automático (Heurística)

```typescript
function selectMode(task: Task): Mode {
  const riskFactors = {
    files: task.estimatedFiles,
    components: task.affectedComponents,
    security: task.securityRisk,      // none | low | medium | high
    data: task.dataSensitivity,       // none | internal | confidential | restricted
    concurrency: task.concurrency,    // none | low | high
    schema: task.schemaChange,        // none | safe | risky
    architecture: task.archImpact,    // none | boundary | critical
  };

  // Riguroso triggers (cualquiera = riguroso)
  if (riskFactors.security === 'high') return 'riguroso';
  if (riskFactors.data === 'restricted') return 'riguroso';
  if (riskFactors.concurrency === 'high') return 'riguroso';
  if (riskFactors.schema === 'risky') return 'riguroso';
  if (riskFactors.architecture === 'critical') return 'riguroso';
  if (task.isPreRelease) return 'riguroso';

  // Estándar triggers
  if (riskFactors.files > 10) return 'estándar';
  if (riskFactors.components >= 2) return 'estándar';
  if (riskFactors.security === 'medium') return 'estándar';
  if (riskFactors.data === 'confidential') return 'estándar';
  if (riskFactors.schema === 'safe') return 'estándar';

  // Default: Ligero
  return 'ligero';
}
```

---

## Documentación por Modo

| Artefacto | Ligero | Estándar | Riguroso |
|-----------|--------|----------|----------|
| Spec | Título + objetivo + 1-2 ACs | Completa (plantilla) | Completa + validación auto |
| Plan | Mental / 3 bullets | `ITERATION_PLAN.md` breve | `ITERATION_PLAN.md` + ADR + threat model |
| Tests | Unit relevantes | Unit + integration + acceptance | Todos + adversarial |
| Review | Diff review | Diff + `saqi-lean-code:review` | Exhaustivo + security review |
| Docs | Inline comments | Update `CONTEXT.md` | Update + ADR + risk register |

---

## Integración con SAQI

### En `saqi.agent.yaml`
```yaml
agent:
  modes:
    builder:
      skills:
        - saqi-adaptive-mode  # SIEMPRE activo - selecciona modo
        - saqi-lean-code      # Principios siempre
      # Otras skills se activan según modo seleccionado
```

### En `ITERATION_PLAN.md`
```markdown
## Mode: estándar
## Justificación: 5 archivos, 2 componentes, data internal

## Steps:
1. [ ] Spec: SPEC-042 (3 ACs)
2. [ ] Vertical slice: user-profile API
3. [ ] Tests: unit + integration + acceptance
4. [ ] Review: saqi-lean-code:review
5. [ ] Update CONTEXT.md
```

---

## Ejemplos de Clasificación

| Tarea | Modo | Justificación |
|-------|------|---------------|
| Fix typo en mensaje error | Ligero | 1 archivo, sin riesgo |
| Añadir campo opcional a API | Estándar | 3 archivos, 1 componente, data internal |
| Cambiar password hashing | Riguroso | Security high, data restricted, schema risky |
| Refactor componente UI interno | Ligero | 2 archivos, sin riesgo externo |
| Nueva integración payment | Riguroso | Security high, PII, external contract |
| Fix race condition en cache | Riguroso | Concurrencia high, data integrity |
| Actualizar dependencia menor | Ligero | 1 archivo (lockfile), sin riesgo |
| Migración BD con data loss risk | Riguroso | Schema risky, data restricted |

---

## Métricas de Efectividad
- **Mode accuracy:** % tareas donde modo inicial = modo final (target > 85%)
- **Escalations:** % tareas que elevan modo (target < 20%)
- **Over-engineering:** % tareas en riguroso que podrían ser estándar (target < 15%)
- **Security incidents:** 0 en tareas clasificadas correctamente
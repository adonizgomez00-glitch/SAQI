---
name: saqi-token-optimizer
description: "Optimización de tokens para SAQI: recuperación selectiva, reutilización instrucciones, resúmenes estado, budgets configurables, límites iteración, diagnóstico estancamiento."
---

# SAQI Token Optimizer

Estrategia explícita de eficiencia. **No confundas menos tokens con mayor eficiencia** — una solución que consume menos tokens pero introduce defectos es más costosa.

## Principios (SAQI §10)

| Principio | Acción |
|-----------|--------|
| Recupera solo lo relevante | `grep`/`glob` dirigido, no `cat` todo el repo |
| Reutiliza instrucciones estables | Skills, AGENTS.md, CONTEXT.md = contexto primario |
| Evita re-explicar documentado | Referencia docs, no copies contenido |
| Resúmenes breves y actualizados | `CONTEXT.md` estado = fuente verdad |
| Limita tarea al cambio necesario | Vertical slice, no horizontal layers |
| Reduce salida a resultados relevantes | `--only-failures`, `grep` output, summary |
| Evita ciclos corrección sin límite | Budget intentos, diagnosticar si estanca |
| Budgets configurables | Intentos, tokens, tiempo si medibles |
| Diagnostica estancamiento | Para, analiza, pide ayuda si repite errores |
| No docs duplicadas ni planes extensos | Para tareas pequeñas: solo lo necesario |

---

## Estrategias por Fase

### 1. Planificación (Pre-ejecución)
```bash
# En lugar de leer todo el repo:
grep -r "pattern" --include="*.ts" packages/<relevant>/
glob "packages/**/specific-file.ts"

# Referencia docs existentes:
# "Ver AGENTS.md sección Testing" en lugar de copiar contenido
# "Spec SPEC-042 define ACs" en lugar de repetirlos
```

### 2. Ejecución (Durante tarea)
```bash
# Comandos con output controlado:
bun test -- --only-failures  # Solo fallos
bun typecheck 2>&1 | tail -20  # Últimas líneas
grep -A5 -B5 "error" output.log  # Contexto relevante

# Evita:
bun test  # Output completo verbose
cat large-file.ts  # Lee completo
```

### 3. Verificación (Post-ejecución)
```bash
# Resumen resultados:
echo "Tests: $(bun test -- --reporter=minimal 2>&1 | grep -E 'pass|fail')"
echo "Typecheck: $(bun typecheck 2>&1 | grep -c 'error' || echo 0) errors"
```

---

## Budget Configuración

### Por Tipo de Tarea

| Tipo Tarea | Max Intentos | Max Tokens (est.) | Timeout | Escalation |
|------------|--------------|-------------------|---------|------------|
| **Ligero** (fix typo, config) | 3 | 5k | 10 min | Preguntar humano |
| **Estándar** (feature pequeña) | 5 | 20k | 1 hora | Subdividir / replanificar |
| **Riguroso** (security, arch) | 8 | 50k | 4 horas | Arquitecto / security review |

### Implementación Budget
```typescript
interface TokenBudget {
  maxAttempts: number;
  maxTokens: number;
  maxTimeMs: number;
  currentAttempt: number;
  tokensUsed: number;
  startTime: number;
}

function checkBudget(budget: TokenBudget): { continue: boolean; reason?: string } {
  if (budget.currentAttempt >= budget.maxAttempts) {
    return { continue: false, reason: `Max attempts (${budget.maxAttempts}) reached` };
  }
  if (budget.tokensUsed >= budget.maxTokens) {
    return { continue: false, reason: `Token budget (${budget.maxTokens}) exceeded` };
  }
  if (Date.now() - budget.startTime >= budget.maxTimeMs) {
    return { continue: false, reason: `Time budget (${budget.maxTimeMs}ms) exceeded` };
  }
  return { continue: true };
}
```

---

## Diagnóstico Estancamiento

### Señales de Alerta
- [ ] Mismo error ≥ 3 intentos consecutivos
- [ ] Cambios sin progreso medible (tests siguen fallando igual)
- [ ] Tokens creciendo sin resolución
- [ ] Agente repite mismos patrones

### Protocolo Diagnóstico
```markdown
## Estancamiento Detectado - Intento N

### Qué intenté
1. [Acción 1] → [Resultado]
2. [Acción 2] → [Resultado]
3. [Acción 3] → [Resultado]

### Evidencia obtenida
- Logs: ...
- Tests: ...
- Code paths: ...

### Qué necesita para continuar
- [ ] Clarificación requisito
- [ ] Acceso a recurso/ambiente
- [ ] Expertise específico (security, arch, etc.)
- [ ] Subdividir tarea
- [ ] Replanificar desde cero
```

### Acción Requerida
**STOP** — No continúes ciegamente. Ejecuta diagnóstico y:
1. Resume arriba
2. Pide ayuda / clarificación
3. Subdivide o replanifica

---

## Recuperación Selectiva de Contexto

### Jerarquía de Fuentes (Prioridad)
| Prioridad | Fuente | Cuándo |
|-----------|--------|--------|
| 1 | **Task-specific**: spec, ACs, story, defect | Siempre |
| 2 | **Project-stable**: AGENTS.md, CONTEXT.md, skills activos | Siempre |
| 3 | **Code-relevant**: archivos tocados + tests + deps directas | Según tarea |
| 4 | **Architecture**: ARCHITECTURE.md, ADRs relevantes | Si toca boundaries |
| 5 | **History**: git log reciente, CHANGELOG | Si regression/debug |

### Lo que NO Recuperar
- ❌ Docs completas no referenciadas
- ❌ Código no tocado por la tarea
- ❌ Tests no relacionados con ACs
- ❌ Historial git completo (solo relevante)

### Template Contexto Mínimo
```markdown
## Contexto Mínimo para [TASK-XXX]

### Objetivo
[1 frase - outcome verificable]

### Referencias Clave
- Spec: SPEC-XXX (ACs: 1, 3)
- Skill: saqi-testing:unit
- Files: packages/core/src/user/service.ts, .test.ts

### Estado Actual
- Tests pasando: 47/50
- Known issues: DEF-012 (unrelated)
- Último commit: abc1234 "fix: user validation"
```

---

## Medición (Si Herramientas Permiten)

### Métricas Reales (No Estimaciones)
| Métrica | Cómo Medir | Fallback |
|---------|------------|----------|
| Tokens/historia | API provider si disponible | Declarar "no medible" |
| Tiempo/historia | Wall clock start→done | Manual log |
| Intentos/historia | Contador interno | Manual log |
| Ciclos corrección | Test fail → fix → pass | Manual log |

### Declaración Limitación
> **Si el agente no puede medir directamente el consumo real de tokens, debe declarar esa limitación y no inventar estimaciones presentadas como mediciones.**

---

## Integración SAQI

### En `saqi.agent.yaml`
```yaml
agent:
  budgets:
    default:
      maxAttempts: 5
      maxTokens: 20000
      maxTimeMs: 3600000
    riguroso:
      maxAttempts: 8
      maxTokens: 50000
      maxTimeMs: 14400000
  
  tokenOptimization:
    enableSelectiveRecovery: true
    enableStagnationDetection: true
    stagnationThreshold: 3  # mismo error N veces
```

### En Skills
Cada skill debe declarar su `tokenCost` estimado:
```yaml
skill:
  name: saqi-testing
  tokenCost: 2000  # tokens típicos al cargar
  submodules:
    unit: 500
    e2e: 3000
    component: 2000
    tui: 1500
```

---

## Anti-Patrones (MUST NOT)

| Anti-Patrón | Correcto |
|-------------|----------|
| Leer repo entero "por si acaso" | Grep/glob dirigido |
| Copiar AGENTS.md en contexto | Referenciar "ver AGENTS.md §Testing" |
| Plan extensivo para tarea ligera | 3 bullets mentales |
| Continuar tras 3 errores iguales | STOP → diagnosticar → escalar |
| Inventar métricas tokens | Declarar "no medible" |
| Output verbose sin filtrar | `--only-failures`, `grep`, `tail` |
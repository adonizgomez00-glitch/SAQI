---
name: saqi-metrics
description: "Métricas para SAQI: tokens/historia, tiempo, ciclos corrección, defectos durante/después, regresiones, cumplimiento ACs, coste validación profunda. Distingue defectos pre/post cierre."
---

# SAQI Metrics

Métricas mínimas para medir efectividad de SAQI. **Registra resultados reales, incluidos los negativos.**

## Métricas Mínimas Obligatorias (SAQI §13)

| Métrica | Definición | Cómo Registrar | Target |
|---------|------------|----------------|--------|
| **Tokens/historia** | Tokens consumidos por historia aceptada | API provider / declarada limitación | Baseline |
| **Tiempo/historia** | Wall clock start → Done | Manual log / timestamp | < 2 días (estándar) |
| **Ciclos corrección** | Test fail → fix → pass cycles | Contador por historia | < 3 |
| **Defectos durante** | Defectos encontrados ANTES de cerrar historia | Contador en STORY DoD | Target 0 escapados |
| **Defectos escapados** | Defectos detectados DESPUÉS de cerrar historia | Post-release tracking | 0 |
| **Regresiones** | Tests que pasaban y fallan en historia posterior | CI regression detection | 0 |
| **Cumplimiento ACs** | % ACs passing al cerrar historia | 100% = Done | 100% |
| **Coste validación profunda** | Tiempo/recursos ciclo profundo QA | Wall clock + recursos | Baseline |

---

## Distinción Crítica: Pre vs Post Cierre

```
┌─────────────────────────────────────────────────────────────┐
│                    HISTORY LIFECYCLE                        │
├─────────────────────────────────────────────────────────────┤
│  START → DEV → TEST → REVIEW → DONE → RELEASE → PRODUCTION │
│                    ↑                    ↑                   │
│            DEFECTOS DURANTE        DEFECTOS ESCAPADOS      │
│            (internal quality)      (external quality)      │
└─────────────────────────────────────────────────────────────┘
```

### Defectos Durante (Internal)
- Encontrados en: unit tests, integration, acceptance, review, manual QA
- **Registrar en:** `STORIES.md` métricas + `DEFECTS.md`
- **Target:** 0 al cerrar (DoD requiere 100% ACs passing)

### Defectos Escapados (External)
- Encontrados en: staging, production, user reports, post-release
- **Registrar en:** `DEFECTS.md` con tag `escaped`
- **Target:** 0 (zero escape policy)

---

## Registro por Historia (METRICS.jsonl)

### Formato Append-Only
```json
{
  "timestamp": "2026-XX-XXTXX:XX:XXZ",
  "storyId": "STORY-042",
  "mode": "estándar",
  "tokens": 15420,
  "timeMs": 7200000,
  "correctionCycles": 2,
  "defectsDuring": 1,
  "defectsEscaped": 0,
  "regressions": 0,
  "acCompliance": 1.0,
  "deepValidationCostMs": 1800000,
  "outcome": "User profile API",
  "acCount": 3,
  "tests": { "unit": 5, "integration": 3, "acceptance": 3, "e2e": 1 }
}
```

---

## Agregación y Análisis

### Dashboard Semanal (Automatizable)
```bash
# Promedio tokens/historia (últimas 10)
jq 'select(.timestamp > "'$(date -d '7 days ago' -Iseconds)'") | .tokens' .saqi/METRICS.jsonl | awk '{sum+=$1} END {print sum/NR}'

# Tiempo medio
jq '.timeMs' .saqi/METRICS.jsonl | awk '{sum+=$1} END {print sum/NR/3600000 "h"}'

# Defectos escapados rate
ESCAPED=$(jq 'select(.defectsEscaped > 0) | length' .saqi/METRICS.jsonl)
TOTAL=$(jq 'length' .saqi/METRICS.jsonl)
echo "Escape rate: $ESCAPED/$TOTAL = $(echo "scale=2; $ESCAPED*100/$TOTAL" | bc)%"
```

### Alertas
| Señal | Acción |
|-------|--------|
| Tokens/historia +50% vs baseline | Investigar over-engineering / context bloat |
| Tiempo/historia +100% vs baseline | Revisar scope, dependencies, skill gaps |
| Defectos escapados > 0 | Root cause obligatorio + prevención |
| Regresiones > 0 | Revisar test coverage + CI |
| AC compliance < 100% | Historia no Done - revisar DoD |

---

## Experimento Comparativo (SAQI §13)

### Diseño Experimento A vs B

| Variable | A: Baseline | B: SAQI Enhanced |
|----------|-------------|------------------|
| Flujo | Incremental + AI Pair + tests + commits | A + Skills + Contexto persistente + QA adaptativo + Learning |
| Tareas | Comparables (misma complejidad) | Mismas tareas |
| Condiciones | Documentadas | Mismas + SAQI mechanisms |
| Repeticiones | Múltiples (viable) | Mismas |
| Constantes | Modelo, env, complejidad | Mantenidas |

### Métricas Comparativas
| Métrica | A (Baseline) | B (SAQI) | Delta | Significancia |
|---------|--------------|----------|-------|---------------|
| Tokens/historia | X | Y | Y-X | p-value |
| Tiempo/historia | X | Y | Y-X | p-value |
| Defectos escapados | X | Y | Y-X | p-value |
| Regresiones | X | Y | Y-X | p-value |
| AC compliance | X | Y | Y-X | p-value |

### Registro Experimento
```markdown
# EXPERIMENT-001: SAQI vs Baseline

## Configuración
- Modelo: [model name]
- Env: [CI/local]
- Tareas: [lista task IDs]
- Repeticiones: N

## Resultados
| Métrica | Baseline (A) | SAQI (B) | Delta | p-value |
|---------|--------------|----------|-------|---------|
| Tokens | 18000 | 16500 | -8.3% | 0.03 |
| Tiempo (h) | 4.2 | 3.8 | -9.5% | 0.08 |
| Escaped defects | 2 | 0 | -100% | 0.15 |
| Regressions | 1 | 0 | -100% | 0.31 |

## Conclusión
[Evidencia real - no declarar mejora sin evidencia suficiente]
```

---

## Métricas de Skills Learning (SAQI §9)

| Métrica | Definición | Target |
|---------|------------|--------|
| **Defect recurrence rate** | % defectos con mismo root cause | < 20% |
| **Time to prevention** | Detect → prevention applied | < 2 iteraciones |
| **Executable prevention ratio** | Nivel 1-4 / Total prevenciones | > 80% |
| **Skill effectiveness** | Prevenciones skill / defects prevenidos | > 70% |
| **False positive rate** | Skill flags OK code / Total flags | < 5% |

---

## Integración

### En `saqi.agent.yaml`
```yaml
agent:
  metrics:
    enabled: true
    output: ".saqi/METRICS.jsonl"
    autoRecord: true
    fields:
      - tokens
      - timeMs
      - correctionCycles
      - defectsDuring
      - defectsEscaped
      - regressions
      - acCompliance
      - deepValidationCostMs
```

### En Skills
- `saqi-vertical-slice` → registra métricas al DoD
- `saqi-skills-learning` → registra learning metrics
- `saqi-harness` → registra validation metrics
- `saqi-adaptive-mode` → registra mode selection accuracy

---

## Anti-Patrones (MUST NOT)

| Anti-Patrón | Correcto |
|-------------|----------|
| Inventar métricas tokens | Declarar "no medible" |
| Mezclar defectos durante/escapados | Distinguir explícitamente |
| Declarar mejora sin evidencia | Registrar resultados reales + negativos |
| Solo métricas positivas | Incluir negativas y null results |
| Métricas sin actionability | Cada métrica → alerta + acción |
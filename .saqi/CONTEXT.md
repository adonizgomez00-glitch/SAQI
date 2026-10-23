# Project Context - SAQI

## Estado Actual
- **Fase:** Build
- **Modo:** estándar
- **Historia activa:** MIGRATION-SKILLS-001
- **Iteración:** ITER-001

## Arquitectura Real
- **Stack:** TypeScript, Effect TS, Bun, React/Solid, Electron, Playwright, Storybook
- **Boundaries:** AuthZ (saqi-qa-harness:security), APIs públicas (saqi-spec-arch:sdd), Data integrity (saqi-qa-harness:standards)
- **Constraints:** TypeScript strict, Effect patterns, Zero runtime deps donde posible, Windows/Linux CI parity

## Comandos Clave
```bash
# Dev
bun dev              # Start dev (opencode-dev skill)
bun test             # Run tests (saqi-testing)
bun typecheck        # Type check
bun lint             # Lint

# CI Validation
bun run validate     # Full validation suite (saqi-harness)
```

## Decisiones Importantes
| Fecha | Decisión | ADR | Impacto |
|-------|----------|-----|---------|
| 2026-10-23 | Consolidar skills en saqi-* | ADR-001 | Reduce bloat, mejora activación selectiva |
| 2026-10-23 | Modo adaptativo ligero/estándar/riguroso | ADR-002 | Proceso proporcional al riesgo |
| 2026-10-23 | Vertical slices como flujo default | ADR-003 | Entrega valor incremental |
| 2026-10-23 | Skills learning sin auto-rules | ADR-004 | Prevención ejecutable > docs |

## Estado Historias
| Story | Status | Outcome | Métricas |
|-------|--------|---------|----------|
| MIGRATION-SKILLS-001 | In Progress | Skills consolidadas + nuevas SAQI | 13 skills creadas |

## Defectos Conocidos
| Defect | Root Cause | Prevención | Status |
|--------|------------|------------|--------|

## Riesgos Pendientes
| Riesgo | Likelihood | Impact | Mitigación | Owner |
|--------|------------|--------|------------|-------|
| Migración skills rompe CI | Low | High | Tests paralelos, rollback git | Team |
| Over-engineering en skills nuevas | Medium | Medium | saqi-lean-code:audit cada skill | Team |

## Validaciones Recientes
| Fecha | Tipo | Resultado | Detalles |
|-------|------|-----------|----------|
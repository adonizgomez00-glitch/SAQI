# SAQI Architecture

## System Context (C4 Level 1)

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   User      │────▶│   SAQI      │────▶│   External  │
│   (Dev)     │     │   Agent     │     │   Systems   │
└─────────────┘     └─────────────┘     └─────────────┘
                           │
                    ┌──────┴──────┐
                    ▼           ▼
              ┌─────────┐ ┌─────────┐
              │ Skills  │ │ Context │
              │ Engine  │ │ Store   │
              └─────────┘ └─────────┘
```

## Container Diagram (C4 Level 2)

| Container | Technology | Responsibility |
|-----------|------------|----------------|
| **Skill Engine** | TypeScript/Effect | Carga, ejecuta, gestiona skills |
| **Context Store** | Markdown/JSONL | Estado persistente (.saqi/) |
| **Harness** | Bun/Shell | Validación reproducible |
| **Metrics Collector** | JSONL | Métricas por historia |
| **Git Integration** | CLI | Version control, diffs |

## Security Boundaries

| Boundary | Protection | Skill |
|----------|------------|-------|
| **Skill Loading** | Sandboxed, solo skills registradas | saqi-adaptive-mode |
| **Context Access** | Read-only para historia, write en checkpoints | saqi-context-manager |
| **Command Execution** | Allowlisted commands, budgets | saqi-harness |
| **Token Budget** | Hard limits por modo | saqi-token-optimizer |
| **External Calls** | Solo allowlisted origins | saqi-qa-harness:security |

## Data Integrity Rules

| Invariant | Enforcement |
|-----------|-------------|
| CONTEXT.md = única fuente verdad | saqi-context-manager |
| METRICS.jsonl append-only | saqi-metrics |
| HARNESS_LOG.jsonl append-only | saqi-harness |
| STORIES.md DoD = 100% ACs | saqi-vertical-slice |
| Skills versionadas SemVer | saqi-skills-learning |

## Public Contracts

### Skill Interface
```typescript
interface Skill {
  name: string;
  description: string;
  submodules?: string[];
  tokenCost?: number;
  trigger?: string; // conditional load
}
```

### Agent Config (saqi.agent.yaml)
```yaml
agent:
  modes:
    builder:
      skills: [saqi-adaptive-mode, saqi-lean-code:principles]
  budgets:
    ligero: { maxAttempts: 3, maxTokens: 5000, maxTimeMs: 600000 }
    estándar: { maxAttempts: 5, maxTokens: 20000, maxTimeMs: 3600000 }
    riguroso: { maxAttempts: 8, maxTokens: 50000, maxTimeMs: 14400000 }
```

## ADR Index

| ADR | Título | Status | Fecha |
|-----|--------|--------|-------|
| ADR-001 | Consolidar skills en saqi-* | Accepted | 2026-10-23 |
| ADR-002 | Modo adaptativo ligero/estándar/riguroso | Accepted | 2026-10-23 |
| ADR-003 | Vertical slices como flujo default | Accepted | 2026-10-23 |
| ADR-004 | Skills learning sin auto-rules | Accepted | 2026-10-23 |
| ADR-005 | RTL skill condicional | Accepted | 2026-10-23 |
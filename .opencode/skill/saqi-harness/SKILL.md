---
name: saqi-harness
description: "Harness Engineering para SAQI: comandos reproducibles validación, exit criteria, budgets, límites iteración, registro resultados/fallos."
---

# SAQI Harness Engineering

Verificación automatizada y controles objetivos. **Siempre que el proyecto lo permita, usa comandos reproducibles.**

## Comandos de Validación Reproducibles

### Matrix de Comandos por Proyecto
| Validación | Comando | Proyecto | Exit Code |
|------------|---------|----------|-----------|
| **Tests** | `bun test` | Todos | 0=pass |
| **Typecheck** | `bun typecheck` | Todos | 0=pass |
| **Lint** | `bun lint` | Todos | 0=pass |
| **Build** | `bun build` | Libs/Apps | 0=pass |
| **Contracts** | `npm run test:acceptance` | Spec-driven | 0=pass 100% |
| **Security** | `semgrep --config=auto .` | Todos | 0=pass |
| **Deps audit** | `bun audit --audit-level=high` | Todos | 0=pass |

### Ejecución en CI (`.github/workflows/test.yml`)
```yaml
jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: oven-sh/setup-bun@v1
      - run: bun install --frozen-lockfile
      - run: bun typecheck
      - run: bun lint
      - run: bun test -- --only-failures
      - run: bun run test:acceptance
      - run: semgrep --config=auto .
      - run: bun audit --audit-level=high
```

---

## Exit Criteria para Ciclos Autónomos

### Definición de Done por Modo

| Criterio | Ligero | Estándar | Riguroso |
|----------|--------|----------|----------|
| **Tests** | Unit relevantes passing | Unit + Int + Acceptance 100% | Todos + Adversarial threshold |
| **Typecheck** | 0 errors | 0 errors | 0 errors + strict mode |
| **Lint** | 0 errors | 0 errors | 0 errors + security rules |
| **Build** | - | Pass | Pass + artifacts verified |
| **Contracts** | - | Spec-derived 100% | Spec-derived 100% + mutation 80% |
| **Security** | Secrets grep | Deps audit + config | SAST + DAST + threat model |
| **Rollback** | Git revert tested | Feature flag tested | Migration rollback < 5min tested |

### Budget Límites
```typescript
interface HarnessBudget {
  maxAttempts: number;
  maxNoProgress: number;
  maxTokens: number;
  maxTimeMs: number;
  minTestsPassing: number;
}

const BUDGETS = {
  ligero: { maxAttempts: 3, maxNoProgress: 2, maxTokens: 5000, maxTimeMs: 600000, minTestsPassing: 1 },
  estándar: { maxAttempts: 5, maxNoProgress: 3, maxTokens: 20000, maxTimeMs: 3600000, minTestsPassing: 10 },
  riguroso: { maxAttempts: 8, maxNoProgress: 4, maxTokens: 50000, maxTimeMs: 14400000, minTestsPassing: 50 },
};
```

### Límite Alcanzado → Protocolo
```markdown
## Harness Limit Reached - [Mode]

### Resumen Intentos
- Intentos totales: N
- Intentos fallidos consecutivos: M
- Progreso medible: [sí/no]
- Tokens usados: X / Y
- Tiempo: T / Tmax

### Qué falló
- [Check 1]: [Error/Timeout]
- [Check 2]: [Error/Timeout]

### Evidencia
- Logs: ...
- Tests failing: ...
- Type errors: ...

### Qué necesita para continuar
- [ ] Clarificación requisito
- [ ] Subdividir tarea
- [ ] Expertise externo
- [ ] Replanificar
```

---

## Registro de Resultados y Fallos

### Estructura Log Estándar
```json
{
  "timestamp": "2026-XX-XXTXX:XX:XXZ",
  "taskId": "STORY-XXX",
  "mode": "estándar",
  "phase": "validate",
  "command": "bun test -- --only-failures",
  "exitCode": 0,
  "durationMs": 45000,
  "results": {
    "testsTotal": 142,
    "testsPassed": 142,
    "testsFailed": 0,
    "testsSkipped": 3
  },
  "budget": {
    "attemptsUsed": 1,
    "tokensUsed": 12000,
    "timeUsedMs": 45000
  }
}
```

### Agregación en `HARNESS_LOG.jsonl`
```bash
# Append each validation run
echo "$JSON" >> .saqi/harness.log

# Query fallos recientes
jq 'select(.exitCode != 0)' .saqi/harness.log | tail -20
```

---

## Verificación de Cambios Inesperados

### Git Diff Check
```bash
# Antes de validar: captura estado
git diff --name-only > .saqi/changed-files.txt

# Después: verifica solo archivos tocados cambiaron
comm -23 <(git diff --name-only | sort) <(sort .saqi/changed-files.txt)
# Si output no vacío → cambios inesperados
```

### Snapshots para Validación
```bash
# Database schema
bun run db:schema:dump > .saqi/schema-before.sql
# ... changes ...
bun run db:schema:dump > .saqi/schema-after.sql
diff .saqi/schema-before.sql .saqi/schema-after.sql

# Config files
cp config.json .saqi/config-before.json
# ... changes ...
diff .saqi/config-before.json config.json
```

---

## Integración con SAQI

### En `saqi.agent.yaml`
```yaml
agent:
  harness:
    validateOnPhaseEnd: true
    budgets:
      ligero: { maxAttempts: 3, maxNoProgress: 2, maxTokens: 5000, maxTimeMs: 600000 }
      estándar: { maxAttempts: 5, maxNoProgress: 3, maxTokens: 20000, maxTimeMs: 3600000 }
      riguroso: { maxAttempts: 8, maxNoProgress: 4, maxTokens: 50000, maxTimeMs: 14400000 }
    commands:
      - bun typecheck
      - bun lint
      - bun test -- --only-failures
    securityCommands:
      - semgrep --config=auto .
      - bun audit --audit-level=high
    contractCommand: "npm run test:acceptance"
```

### Skills que Usan Harness
- `saqi-adaptive-mode` → selecciona budget según modo
- `saqi-testing` → ejecuta tests según submódulo
- `saqi-qa-harness` → adversarial commands
- `saqi-spec-arch:sdd` → contract validation

---

## Anti-Patrones (MUST NOT)

| Anti-Patrón | Correcto |
|-------------|----------|
| Validar solo "funciona en mi máquina" | Comandos reproducibles en CI |
| Sin exit criteria → loop infinito | Budgets + protocolo límite |
| No registrar fallos | `HARNESS_LOG.jsonl` append-only |
| Ignorar cambios inesperados | Git diff check pre/post |
| Declarar done sin verificar criteria | Checklist modo obligatorio |
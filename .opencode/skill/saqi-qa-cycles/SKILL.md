---
name: saqi-qa-cycles
description: "Ciclos QA para SAQI: ciclo rápido (por historia) vs ciclo profundo (por riesgo/hito) con triggers definidos. Separa verificación funcional de QA adversarial."
---

# SAQI QA Cycles

Dos ciclos QA separados. **No afirmes que una aplicación es segura solo porque pasan las pruebas disponibles.**

## Ciclo Rápido (Por Historia) - SAQI §8

**Ejecuta en:** Cada historia completada (gate DoD)

### Checklist Obligatorio
- [ ] **Verificación funcional** - Outcome verificable binariamente
- [ ] **Pruebas pertinentes** - Según riesgo (TDD selectivo §7)
  - Riesgo bajo: Unit tests
  - Riesgo medio: Unit + Integration + Acceptance
  - Riesgo alto: Todos + Adversarial sampling
- [ ] **Comprobaciones tipos + estático** - `bun typecheck` + `bun lint` = 0 errors
- [ ] **Review del diff** - `saqi-lean-code:review` (principles + standards)
- [ ] **Confirmación criterios aceptación** - 100% ACs passing

### Tiempo Objetivo
- **Ligero:** < 30 min
- **Estándar:** < 2 horas
- **Riguroso:** < 4 horas (incluye adversarial sampling)

### Automatización CI
```yaml
# En .github/workflows/test.yml - por PR
- name: Fast QA Cycle
  run: |
    bun typecheck
    bun lint
    bun test -- --only-failures
    npm run test:acceptance  # Spec-derived tests
```

---

## Ciclo Profundo (Por Riesgo / Hito) - SAQI §8

**Triggers (cualquiera activa):**
| Trigger | Descripción |
|---------|-------------|
| **Riesgo elevado** | Modo riguroso, security high, data restricted |
| **Cambios sensibles** | AuthZ/AuthN, crypto, PII, payments, schema risky |
| **Defectos recurrentes** | ≥ 2 defectos mismo root cause (últimos 30 días) |
| **Cambios arquitectónicos** | Nuevos boundaries, contracts, infraestructura |
| **Preparación versión** | Pre-release, release candidate, hotfix crítico |

### Checklist Ciclo Profundo
- [ ] **QA Adversarial** (`saqi-qa-harness:adversarial`)
  - Property-based testing (10k+ iteraciones)
  - Mutation testing (threshold 80%)
  - Fuzzing (parsers, APIs, boundaries)
  - Chaos engineering (si distribuido)
- [ ] **Security Testing** (`saqi-qa-harness:security`)
  - SAST (semgrep, CodeQL)
  - DAST (OWASP ZAP) si web
  - Dependency audit (trivy, npm audit)
  - Secrets scan (truffleHog)
  - Threat model review (si modo riguroso)
- [ ] **Concurrencia y Estrés** (si procede)
  - Load testing (k6, artillery) - SLAs
  - Race condition detection (concurrent tests)
  - Resource exhaustion (memory, connections, disk)
- [ ] **Accesibilidad** (si hay UI)
  - axe-core automated (CI)
  - Manual keyboard/screen reader (sampling)
  - WCAG 2.1 AA checklist
- [ ] **Regresión Ampliada**
  - Full test suite (no solo affected)
  - E2E critical paths
  - Contract testing (Pact)
- [ ] **Validación Migraciones** (si schema change)
  - Migration up + down testado
  - Data integrity verification
  - Rollback < 5 min probado
  - Zero-downtime verificado
- [ ] **Despliegue, Recuperación, Rollback**
  - Deploy staging → smoke tests
  - Disaster recovery drill (si crítico)
  - Rollback procedure testado
  - Runbook actualizado

### Tiempo Objetivo
- **Estándar:** 4-8 horas
- **Riguroso:** 1-2 días (incluye security review externo si compliance)

---

## Integración con Modos Adaptativos

| Modo | Ciclo Rápido | Ciclo Profundo |
|------|--------------|----------------|
| **Ligero** | ✅ Obligatorio | Solo si trigger durante ejecución |
| **Estándar** | ✅ Obligatorio | ✅ Si trigger activado |
| **Riguroso** | ✅ Obligatorio | ✅ Obligatorio (parte del flujo) |

### Flujo Combinado (Estándar con Trigger)
```
Historia START
    ↓
Ciclo Rápido (obligatorio)
    ↓
¿Trigger activado?
    ├─ NO → DONE
    └─ SÍ → Ciclo Profundo → DONE
```

---

## QA Adversarial - Técnicas por Riesgo

| Riesgo | Técnicas | Budget |
|--------|----------|--------|
| **Bajo** | Unit tests + typecheck | 10 min |
| **Medio** | + Integration + Acceptance + Property-based (1k) | 1 hora |
| **Alto** | + Mutation (80%) + Fuzzing + Security scan | 4 horas |
| **Crítico** | + Chaos + Load + Penetration test + External audit | 1-2 días |

### Property-Based Testing (fast-check)
```typescript
// tests/adversarial/user-profile.property.test.ts
import { test, expect } from 'fast-check';
import { UserProfileService } from '@/services/UserProfileService';

test.prop('profile update idempotent', [fc.string(), fc.nat()], async (email, version) => {
  const service = new UserProfileService();
  await service.update(email, { version });
  await service.update(email, { version });
  const profile = await service.get(email);
  expect(profile.version).toBe(version);
});
```

### Mutation Testing (Stryker)
```bash
# Config: stryker.conf.json
{
  "thresholds": { "high": 80, "low": 60, "break": 50 },
  "mutator": "typescript",
  "testRunner": "vitest"
}

# Ejecutar
npx stryker run
```

---

## Métricas QA (Registrar en METRICS.jsonl)

| Métrica | Ciclo Rápido | Ciclo Profundo |
|---------|--------------|----------------|
| Tiempo ejecución | < 30m / 2h / 4h | 4h - 2d |
| Defectos encontrados | N | M |
| Defectos severidad alta | 0 | 0 target |
| Cobertura adversarial | N/A | Property: 10k, Mutation: 80% |
| False positives | < 5% | < 10% |

---

## Anti-Patrones (MUST NOT)

| Anti-Patrón | Correcto |
|-------------|----------|
| Solo ciclo rápido siempre | Ciclo profundo en triggers |
| Ciclo profundo sin triggers definidos | Triggers explícitos obligatorios |
| "Seguro porque tests pasan" | Tests ≠ seguridad completa |
| QA adversarial como afterthought | Integrado en flujo riguroso |
| Accesibilidad solo si tiempo | Obligatorio si UI en ciclo profundo |
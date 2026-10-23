---
name: saqi-qa-harness
description: "QA Harness para SAQI: QA adversarial, testing standards, security gates. Consolida: C-qa-breaker (adversarial QA), A-testing (standards), A-secure-coding."
submodules:
  - adversarial: "QA adversarial: break → fix → regression, root cause analysis, property-based, mutation, chaos"
  - standards: "Testing standards: unit, integration, acceptance, E2E, regression, security, adversarial"
  - security: "Security gates: authZ, authN, data protection, secrets, input validation, rate limiting"
---

# SAQI QA Harness

Consolida QA adversarial, estándares de testing y security gates. **No afirmes que una aplicación es segura solo porque pasan las pruebas disponibles.**

## Submódulo: `adversarial` (C-QA-Breaker)

### Principio: Break → Fix → Regression
El QA no es "verificar que funciona", es **intentar romperlo sistemáticamente**.

### Dos Ciclos QA (SAQI §8)

#### Ciclo Rápido (Por Historia)
- [ ] Verificación funcional
- [ ] Pruebas pertinentes (según riesgo)
- [ ] Comprobaciones tipos + análisis estático
- [ ] Review del diff
- [ ] Confirmación criterios de aceptación

#### Ciclo Profundo (Por Riesgo / Hito)
**Triggers:** Riesgo elevado, cambios sensibles, defectos recurrentes, cambios arquitectónicos, preparación versión

- [ ] QA Adversarial
- [ ] Security testing
- [ ] Concurrencia y estrés (si procede)
- [ ] Accesibilidad (si hay UI)
- [ ] Regresión ampliada
- [ ] Validación migraciones
- [ ] Despliegue, recuperación, rollback

### Técnicas Adversariales

| Técnica | Cuándo | Herramientas |
|---------|--------|--------------|
| **Property-based testing** | Reglas negocio, serialización, parsing | `fast-check`, `jqwik` |
| **Mutation testing** | Lógica crítica, cálculos | `stryker`, `mutation-testing` |
| **Chaos engineering** | Sistemas distribuidos, resilience | `chaos-mesh`, `litmus` |
| **Fuzzing** | Parsers, APIs públicas, input boundaries | `libfuzzer`, `go-fuzz` |
| **Contract testing** | Integraciones, APIs | `pact`, `spring-cloud-contract` |
| **Load/stress** | Performance SLAs | `k6`, `artillery`, `locust` |
| **Security scanning** | Pre-deploy, CI | `semgrep`, `bandit`, `trivy`, `owasp-zap` |
| **Accessibility** | UI components | `axe-core`, `storybook-addon-a11y` |

### Root Cause Analysis (Obligatorio tras defecto escapado)
1. **Reproducir** el defecto en aislamiento
2. **Identificar** causa raíz (5 Whys / Fishbone)
3. **Clasificar:** Error aislado vs patrón recurrente
4. **Prevenir:** Skill, test automatizado, lint rule, contract, tool improvement
5. **Validar:** Que el fix no contradiga otras reglas
6. **Versionar** aprendizaje si corresponde
7. **Registrar** impacto en futuras tareas

### Priorización Prevención
1. **Ejecutable y comprobable** (test, lint, contract) > Instrucción escrita
2. **Automatizado** > Manual
3. **En CI** > Local only
3. **Modular** - skills relevantes, sin contradicciones
4. **Carga selectiva** - solo skills aplicables a la tarea

---

## Submódulo: `standards` (A-Testing)

### Tipos de Prueba - Distingue, No Dupliques

| Tipo | Qué Verifica | Cuándo (SAQI §7) |
|------|--------------|------------------|
| **Unit** | Función/módulo aislado, lógica pura | Siempre - base |
| **Integration** | Interacción entre módulos, DB, APIs | Reglas negocio, cálculos, validaciones, permisos |
| **Acceptance** | Comportamiento user-facing desde spec | Cada AC (Given/When/Then) → 1 test functional |
| **E2E** | Flujos críticos cross-system | Vertical slices, user journeys principales |
| **Regression** | No rompimos lo que funcionaba | Automático en CI, selectivo en local |
| **Security** | AuthZ, AuthN, input validation, secrets | Modo riguroso, cambios sensibles |
| **Adversarial** | Intentar romper el sistema | Ciclo profundo, risk triggers |

### Cobertura - Evita Duplicación Innecesaria
- **Una prueba = un comportamiento/riesgo identificable**
- No tests solo para aumentar coverage number
- TDD selectivo: **primero** para reglas negocio, cálculos, validaciones, permisos, bug fixes reproducibles, algoritmos con edge cases
- **Otros:** implementa porción pequeña → añade tests apropiados

### Criterios de Aceptación Independientes
- **NUNCA** modifiques tests para hacerlos pasar ocultando defecto real
- ACs son independientes de implementación
- Si test falla por ambigüedad en spec → actualiza spec primero

### Test Organization
```
tests/
├── unit/           # Unit tests (co-located with src)
├── integration/    # Integration tests
├── functional/     # Acceptance tests (1:1 from spec ACs)
├── e2e/            # E2E tests (Playwright)
├── adversarial/    # Property-based, mutation, chaos, fuzzing
├── security/       # Security tests
└── performance/    # Load/stress (separate CI)
```

### CI Gates
```yaml
# Unit + Integration (fast)
- bun typecheck
- bun lint
- bun test -- --only-failures

# Acceptance (from spec)
- npm run test:acceptance  # 100% pass

# E2E (sharded)
- Playwright shards per OS

# Adversarial (on triggers)
- Property-based: 10k iterations
- Mutation: threshold 80%
- Security scan: 0 high/critical
```

---

## Submódulo: `security` (A-Secure-Coding)

### Security Gates por Modo

| Check | Ligero | Estándar | Riguroso |
|-------|--------|----------|----------|
| Secrets en código | ✅ grep | ✅ + semgrep | ✅ + truffleHog |
| Input validation | ✅ | ✅ + fuzzing | ✅ + property-based |
| AuthZ/AuthN | - | ✅ review | ✅ + threat model |
| Rate limiting | - | config review | ✅ load test |
| Dependencies audit | ✅ audit | ✅ + SAST | ✅ + DAST |
| Secrets management | - | ✅ | ✅ rotation test |

### OWASP Top 10 - Prevención Mínima
| Risk | Prevención |
|------|------------|
| A01 Broken Access Control | AuthZ checks en cada handler, deny by default |
| A02 Cryptographic Failures | TLS 1.3, Argon2id/bcrypt, key rotation |
| A03 Injection | Parameterized queries, input validation, output encoding |
| A04 Insecure Design | Threat modeling en modo riguroso, secure defaults |
| A05 Security Misconfiguration | Hardened configs, automated scans |
| A06 Vulnerable Components | `npm audit` / `bun audit` en CI, dependabot |
| A07 Auth Failures | MFA, rate limit, account lockout, secure reset |
| A08 Software Integrity | Signed releases, SBOM, provenance |
| A09 Logging Failures | Structured logs, no PII, alerting on anomalies |
| A10 SSRF | Allowlist URLs, no user-controlled fetch targets |

### Secure Coding Rules (MUST)
- **Nunca** hardcode secrets, API keys, passwords
- **Siempre** valida input en trust boundaries
- **Usa** parameterized queries / ORM safe methods
- **Implementa** rate limiting en endpoints públicos
- **Loggea** eventos seguridad sin PII
- **Rota** secrets regularmente, automatiza

### Threat Modeling (Modo Riguroso)
1. **Identify assets** - Qué proteges (data, keys, availability)
2. **Identify threats** - STRIDE: Spoofing, Tampering, Repudiation, Info Disclosure, DoS, Elevation
3. **Assess risk** - Likelihood × Impact
4. **Define mitigations** - Code, config, process
5. **Validate** - Tests, reviews, penetration testing

---

## Integración SAQI

### Modo Ligero
- `standards`: Unit tests relevantes, typecheck, lint
- `security`: grep secrets, `npm audit`
- `adversarial`: No requerido

### Modo Estándar
- `standards`: Unit + integration + acceptance (from spec)
- `security`: Input validation review, deps audit, config review
- `adversarial`: Property-based en lógica crítica

### Modo Riguroso
- `standards`: Todos los tipos + regression ampliada
- `security`: Threat model, fuzzing, SAST/DAST, penetration test
- `adversarial`: Ciclo profundo completo (mutation, chaos, load, a11y)

### Gates Automatizados
```bash
# Pre-commit
bun lint && bun typecheck

# CI - Unit/Integration
bun test -- --only-failures

# CI - Acceptance (spec-derived)
npm run test:acceptance

# CI - Security
semgrep --config=auto .
npm audit --audit-level=high
trivy fs .

# On-demand (adversarial triggers)
fast-check --num-runs=10000
stryker run --threshold=80
k6 run load-test.js
```

---

## Referencias Globales
- `~/.config/opencode/skills/generic/A-qa-breaker/SKILL.md` (adversarial)
- `~/.config/opencode/skills/generic/A-testing/SKILL.md` (standards)
- `~/.config/opencode/skills/security/A-secure-coding/SKILL.md` (security)
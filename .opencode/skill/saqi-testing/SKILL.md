---
name: saqi-testing
description: "Testing unificado para SAQI: unit, e2e, component, tui. Cubre control de tiempo, aislamiento, CI flakes, Windows timing, budgets de frames, mocks. Usa submódulo según tipo de test."
submodules:
  - unit: "Fast, reliable Bun unit tests (excluye OpenTUI renderer)"
  - e2e: "Playwright e2e tests en packages/app/e2e con mocked opencode server"
  - component: "Storybook-based Playwright component tests"
  - tui: "OpenTUI renderer tests en packages/tui y packages/cli"
---

# SAQI Testing Unificado

Este skill consolida las mejores prácticas de testing de SAQI. **Carga el submódulo relevante** según el tipo de test.

## Submódulos

### `unit` - Unit Tests
- Controla tiempo con `TestClock` / `jest.useFakeTimers()` - no esperas reales
- Budgets por test: 5s default, 30s para packages con spawns (core, cli, etc.)
- Spawne menos: usa fixtures compartidas, `isolatedEnv`, `tmpdir()`
- Windows: cuenta spawns (~40ms c/u), no wall time local
- CI flakes: `--rerun-each 10`, reproduce bajo carga
- No asserts de wall-clock speed; cuenta operaciones

### `e2e` - E2E Tests
- Mock server in-memory por page (`mockOpenCodeServer`)
- Navega una vez; reusa UI/events para estados posteriores
- Service workers bloqueados suite-wide
- Match routes con RegExp/glob, no funciones
- Stress fixture solo cuando escala es el contrato
- CI: built mode, shards, Windows timing rehearsal con delayed assets

### `component` - Component Tests
- Storybook/Vite dev server compartido por suite
- `mount` = navegación iframe completa
- Vite pre-bundling: zero reloads goal
- Fixtures por URL con `sourceURL()` literal
- Mobile: `hasTouch`, `isMobile`, `viewport` en `story.use`
- Fonts: `await document.fonts.ready` antes de medir

### `tui` - TUI Tests
- Frame budgets: `waitForFrame`/`waitForVisualIdle` (20 passes default)
- Renderer modes: started (30fps test) vs not started (await signal)
- `waitForHighlightedFrame` para markdown/code text
- `ManualClock` para time-driven renderables
- `kittyKeyboard: true` para input correcto
- Terminal sizes: siempre pasa `width`/`height`

## Principios Comunes (SAQI)

1. **Test = contrato de comportamiento**, no cobertura
2. **Un test por riesgo identificable** - no tests por aumentar números
3. **Criterios de aceptación independientes** de implementación
4. **TDD selectivo**: rules, cálculos, validaciones, permisos, bug fixes, algoritmos
5. **Harness Engineering**: comandos reproducibles, exit criteria, budgets

## Antes de Push (Checklist Unificado)

```bash
# Unit
bun test <file> --rerun-each 10 --randomize

# E2E (desde packages/app)
bun run test:e2e:built -- e2e/regression/<file>.spec.ts
PLAYWRIGHT_FULLY_PARALLEL=1 --repeat-each=10 --workers=5 --retries=0

# Component (desde package owner)
bun run test:components -- component-tests/<file>.spec.ts --workers=2 --repeat-each=10

# TUI (desde packages/tui)
bun test --timeout 30000 test/<file> --rerun-each 10
```

## CI Flakes - Diagnóstico Común

| Síntoma | Causa Típica | Fix |
|---------|--------------|-----|
| Timeout 5000ms Windows | Spawns git/bun excesivos | Fixtures compartidas, menos spawns |
| "Execution context destroyed" | Vite reload mid-test | Fix pre-bundling entries |
| Frame budget exceeded | Waiting on wrong signal | Wait on actual signal, not frames |
| Flaky solo Windows | Timer resolution 15ms | Fix wait, no bigger budget |

## Referencias

- Root `AGENTS.md` Testing section
- `packages/app/AGENTS.md` Tests
- `packages/app/e2e/AGENTS.md` Test Hygiene
- `.github/workflows/test.yml` CI config
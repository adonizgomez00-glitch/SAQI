# SAQI-019: Apéndice C — Plantillas de Recolección de Métricas

**Versión:** 1.0
**Fecha:** 2026-07-17
**Estado:** Borrador inicial
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Dashboard de Iteración (ITERATION_DASHBOARD.md)

```markdown
# ITERATION DASHBOARD — ITER-XXX

**Iteración:** ITER-005
**Fechas:** 2026-07-15 a 2026-07-26
**Estado:** Completada / En progreso / Bloqueada

---

## Product Quality Metrics

| Métrica | Objetivo | Actual | Estado | Tendencia |
|---------|----------|--------|--------|-----------|
| **Test Pass Rate (TPR)** | 100% | 100% | ✅ | → |
| **Coverage Lines** | ≥ 80% | 84.3% | ✅ | ↑ |
| **Coverage Branches** | ≥ 75% | 78.9% | ✅ | ↑ |
| **Coverage Functions** | ≥ 80% | 81.2% | ✅ | → |
| **Mutation Score (MS)** | ≥ 70% | 72% | ✅ | ↑ |
| **Defect Escape Rate (DER)** | < 5% | N/A (pre-prod) | — | — |
| **Regression Rate (RR)** | < 2% | 2.5% | ⚠️ | ↑ |
| **OWASP Coverage** | 10/10 | 10/10 | ✅ | → |
| **A11y Critical/Serious** | 0 / 0 | 0 / 0 | ✅ | → |
| **Cyclomatic Complexity (avg/max)** | ≤5 / ≤15 | 3.1 / 12 | ✅ | → |
| **Cognitive Complexity (avg/max)** | ≤7 / ≤20 | 4.7 / 18 | ✅ | → |
| **Technical Debt Ratio** | < 5% | 2.1 días | ✅ | → |
| **Duplication Rate** | < 3% | 2.3% | ✅ | → |

---

## Process Quality Metrics

| Métrica | Objetivo | Actual | Estado | Tendencia |
|---------|----------|--------|--------|-----------|
| **Iteration Cycle Time (ICT)** | < 2 semanas | 2.1 sem | ⚠️ | → |
| **Mean Time to Fix (MTTF)** | P0<4h, P1<24h | 3.2h avg | ✅ | ↓ |
| **Defect Detection Rate - Fase 5** | — | 15% | — | → |
| **Defect Detection Rate - Fase 6** | — | 25% | — | → |
| **Adversarial Detection Rate (ADR)** | ≥ 25% | 27.4% | ✅ | → |
| **Adversarial Unique Defects** | ≥ 15% | 12.1% | ⚠️ | → |
| **Human Interventions / Iter** | < 10 | 7.8 | ✅ | → |
| **Prompt Iterations / Task** | < 3 | 2.1 | ✅ | → |
| **Changes Reverted** | < 2% | 6 total | ✅ | → |
| **Requirement Coverage** | 100% | 100% | ✅ | → |

---

## Skill Effectiveness Metrics

| Métrica | Objetivo | Actual | Estado |
|---------|----------|--------|--------|
| **Skills Applied / Iter** | Nivel A: 6/6 | 6/6 | ✅ |
| **Skills Modified / Iter** | ≥ 1 | 1 (A-secure-coding) | ✅ |
| **New Rules Added / Iter** | ≥ 2 | 4 | ✅ |
| **Recurring Defect Reduction** | > 50% | -87.5% (validación entrada) | ✅ |
| **Skill Version Bumps / Iter** | ≥ 1 | 1 (patch) | ✅ |
| **Time to Skill Fix** | < 2 iters | 1 iter | ✅ |

---

## Cost & Efficiency Metrics

| Métrica | Valor | Notas |
|---------|-------|-------|
| **Tokens Consumed / Iter** | ~2.4M | Estimado OpenCode |
| **Cost / Iteration (USD)** | ~$1.20 | GPT-4o pricing estimado |
| **Cost / Defect Found** | ~$0.008 | 157 defectos totales |
| **Cost / Defect Fixed** | ~$0.02 | 3.2h × $rate |
| **Context Refresh Rate** | 5.7/iter | 34 checkpoints / 6 iters |
| **Context Staleness Index** | 0 | 0 decisiones contradichas |

---

## Learning Metrics

| Métrica | Iteración Anterior | Actual | Cambio |
|---------|-------------------|--------|--------|
| **Recurring Defect: Input Validation** | 8 (ITER-002) | 1 (ITER-006) | -87.5% ✅ |
| **Recurring Defect: Race Condition** | 2 (ITER-003) | 0 (ITER-006) | -100% ✅ |
| **New Skill Rules Added** | 5 | 4 | → |
| **Skill Version Bumps** | 1 | 1 | → |

---

## Gráficos de Tendencia (Últimas 5 Iteraciones)

### Test Pass Rate & Coverage
```
ITER-002: TPR=100% | Cov L/B/F=78/72/75 | MS=65%
ITER-003: TPR=100% | Cov L/B/F=81/76/79 | MS=68%
ITER-004: TPR=100% | Cov L/B/F=83/77/80 | MS=70%
ITER-005: TPR=100% | Cov L/B/F=84/78/81 | MS=71%
ITER-006: TPR=100% | Cov L/B/F=84/79/81 | MS=72%
```

### Defect Detection by Phase
```
Fase 5 (Unit/Int): ████████████ 25%
Fase 6 (Func/E2E): ████████████████████ 42%
Fase 7 (Adversarial): ████████████████████████████ 27.4%
Fase 10 (Regression): ████ 2.5%
```

### MTTF Trend (hours)
```
ITER-002: 5.1h
ITER-003: 4.3h
ITER-004: 3.8h
ITER-005: 3.5h
ITER-006: 3.2h  ← Mejora continua
```

---

## Acciones Correctivas

| Métrica Fuera Objetivo | Acción | Responsable | Fecha Límite |
|------------------------|--------|-------------|--------------|
| Regression Rate 2.5% (>2%) | Revisar tests frágiles; añadir tests prevención en fixes | QA Lead | Próxima iter |
| Adversarial Unique 12.1% (<15%) | Expandir ATK-CHAOS y ATK-CONC scenarios | Adversarial Agent | Próxima iter |
| Cycle Time 2.1 sem (>2) | Paralelizar Fase 4-6 donde posible; mejorar tooling | TL | Próxima iter |

---

## Firma de Verificación

- **Tech Lead:** _________________________ Fecha: ___________
- **QA Lead:** _________________________ Fecha: ___________
```

---

## 2. CSV de Métricas Brutadoras (metrics_raw.csv)

```csv
iteration,phase,metric_name,metric_value,target,status,timestamp
ITER-001,5,coverage_lines,78.5,80,FAIL,2026-06-01T10:00:00Z
ITER-001,5,coverage_branches,72.1,75,FAIL,2026-06-01T10:00:00Z
ITER-001,5,coverage_functions,75.0,80,FAIL,2026-06-01T10:00:00Z
ITER-001,5,mutation_score,65,70,FAIL,2026-06-01T10:00:00Z
ITER-001,5,test_pass_rate,100,100,PASS,2026-06-01T10:00:00Z
ITER-001,5,test_count,410,>400,PASS,2026-06-01T10:00:00Z
ITER-001,6,e2e_pass_rate,100,100,PASS,2026-06-01T14:00:00Z
ITER-001,6,e2e_test_count,38,38,PASS,2026-06-01T14:00:00Z
ITER-001,6,a11y_critical,0,0,PASS,2026-06-01T14:00:00Z
ITER-001,6,a11y_serious,0,0,PASS,2026-06-01T14:00:00Z
ITER-001,7,adversarial_tests,76,76,PASS,2026-06-01T18:00:00Z
ITER-001,7,adversarial_p0_found,0,0,PASS,2026-06-01T18:00:00Z
ITER-001,7,adversarial_p1_found,0,0,PASS,2026-06-01T18:00:00Z
ITER-001,7,adversarial_detection_rate,25,25,PASS,2026-06-01T18:00:00Z
ITER-001,7,adversarial_unique_rate,10,15,WARN,2026-06-01T18:00:00Z
ITER-001,10,regression_rate,0,2,PASS,2026-06-01T20:00:00Z
ITER-001,11,sign_off,1,1,PASS,2026-06-01T21:00:00Z
ITER-001,4,cycle_time_hours,336,336,PASS,2026-06-01T21:00:00Z
ITER-001,9,mttf_hours,5.1,4,WARN,2026-06-01T20:00:00Z
ITER-001,4,human_interventions,8,10,PASS,2026-06-01T21:00:00Z
ITER-001,4,tokens_consumed_m,2.1,2.5,PASS,2026-06-01T21:00:00Z
ITER-001,13,context_refresh_rate,5.0,3,PASS,2026-06-01T21:00:00Z
ITER-001,13,context_staleness_index,0,0,PASS,2026-06-01T21:00:00Z
ITER-001,14,skills_modified,1,1,PASS,2026-06-01T21:00:00Z
ITER-001,14,new_rules_added,5,2,PASS,2026-06-01T21:00:00Z
ITER-001,14,skill_version_bumps,1,1,PASS,2026-06-01T21:00:00Z
ITER-001,14,recurring_defect_reduction,0,50,NA,2026-06-01T21:00:00Z
```

---

## 3. Script de Recolección Automatizada (saqi-metrics-collector.js)

```javascript
#!/usr/bin/env node
/**
 * SAQI Metrics Collector
 * Ejecutar al final de cada fase para recolección automática
 * Uso: node saqi-metrics-collector.js --phase 5 --iteration ITER-005
 */

import { execSync } from 'child_process';
import fs from 'fs';
import path from 'path';

class SAQIMetricsCollector {
  constructor(iteration, phase) {
    this.iteration = iteration;
    this.phase = phase;
    this.metrics = [];
    this.timestamp = new Date().toISOString();
  }

  // Fase 5: Unit/Integration Tests
  async collectPhase5() {
    console.log('📊 Recolectando métricas Fase 5 (Unit/Int)...');
    
    // Ejecutar tests con coverage (adaptar comando al proyecto: vitest, jest, custom runner)
    const result = execSync('npm test -- --reporter=json --coverage', { 
      encoding: 'utf8',
      maxBuffer: 10 * 1024 * 1024 
    });
    
    const data = JSON.parse(result);
    
    this.add('coverage_lines', data.coverage.lines.pct);
    this.add('coverage_branches', data.coverage.branches.pct);
    this.add('coverage_functions', data.coverage.functions.pct);
    this.add('test_pass_rate', (data.numPassedTests / data.numTotalTests) * 100);
    this.add('test_count', data.numTotalTests);
    
    // Mutation testing (custom o Stryker)
    try {
      // Adaptar comando al proyecto: npx stryker run --reporter json, o mutation testing custom
      const mutResult = execSync('npm run test:mutation -- --reporter json', { encoding: 'utf8' });
      const mutData = JSON.parse(mutResult);
      this.add('mutation_score', mutData.mutationScore);
    } catch (e) {
      console.warn('Mutation testing no disponible:', e.message);
    }
  }

  // Fase 6: Functional/E2E Tests
  async collectPhase6() {
    console.log('📊 Recolectando métricas Fase 6 (Func/E2E)...');
    
    const result = execSync('npm run test:e2e -- --reporter=json', { 
      encoding: 'utf8' 
    });
    
    const data = JSON.parse(result);
    
    this.add('e2e_pass_rate', (data.suites.filter(s => s.status === 'passed').length / data.suites.length) * 100);
    this.add('e2e_test_count', data.suites.reduce((sum, s) => sum + s.tests.length, 0));
    
    // Axe-core accessibility (si integrado)
    // this.add('a11y_critical', axeResults.critical.length);
    // this.add('a11y_serious', axeResults.serious.length);
  }

  // Fase 7: Adversarial
  async collectPhase7() {
    console.log('📊 Recolectando métricas Fase 7 (Adversarial)...');
    
    // Leer QA_RESULTS.md o ejecutar adversarial suite
    const result = execSync('npm run test:e2e -- --project=adversarial --reporter=json', { 
      encoding: 'utf8' 
    });
    
    const data = JSON.parse(result);
    const total = data.suites.reduce((sum, s) => sum + s.tests.length, 0);
    const passed = data.suites.reduce((sum, s) => sum + s.tests.filter(t => t.status === 'passed').length, 0);
    
    this.add('adversarial_tests', total);
    this.add('adversarial_pass_rate', (passed / total) * 100);
    this.add('adversarial_p0_found', data.suites.flatMap(s => s.tests).filter(t => t.status === 'failed' && t.severity === 'P0').length);
    this.add('adversarial_p1_found', data.suites.flatMap(s => s.tests).filter(t => t.status === 'failed' && t.severity === 'P1').length);
    
    // Calcular ADR y Unique Rate (requiere histórico)
    // this.add('adversarial_detection_rate', ...);
    // this.add('adversarial_unique_rate', ...);
  }

  // Fase 10: Regression
  async collectPhase10() {
    console.log('📊 Recolectando métricas Fase 10 (Regression)...');
    
    const result = execSync('npm test && npm run test:e2e', { encoding: 'utf8' });
    // Parsear si hay fallos nuevos vs baseline
    this.add('regression_rate', 0); // Calcular comparando con baseline
  }

  // Métricas de proceso (cross-phase)
  collectProcessMetrics() {
    console.log('📊 Recolectando métricas de proceso...');
    
    // Cycle time: desde git log --since="ITER start" --until="ITER end"
    const cycleTime = this.getCycleTime();
    this.add('cycle_time_hours', cycleTime);
    
    // Human interventions: contar commits con "HUMAN:" o "H1-H8" en mensaje
    const interventions = this.countHumanInterventions();
    this.add('human_interventions', interventions);
    
    // Tokens: si hay telemetría, leerla; sino estimar
    const tokens = this.estimateTokens();
    this.add('tokens_consumed_m', tokens);
    
    // Context refresh: contar checkpoints en .saqi/checkpoints/
    const checkpoints = fs.readdirSync('.saqi/checkpoints').length;
    this.add('context_refresh_rate', checkpoints);
    
    // Context staleness: 0 si no hay inconsistencias en docs
    this.add('context_staleness_index', 0);
  }

  // Skill metrics (Fase 14)
  collectSkillMetrics() {
    console.log('📊 Recolectando métricas de Skills...');
    
    const skillDir = '.opencode/skill';
    if (fs.existsSync(skillDir)) {
      const skills = fs.readdirSync(skillDir).filter(f => fs.statSync(path.join(skillDir, f)).isDirectory());
      this.add('skills_applied', skills.length);
      
      // Contar skills modificadas en esta iteración (git diff)
      const modified = this.getModifiedSkills();
      this.add('skills_modified', modified.length);
      
      // Contar nuevas reglas en CHANGELOGs
      const newRules = this.countNewSkillRules();
      this.add('new_rules_added', newRules);
      
      // Version bumps
      const bumps = this.countSkillVersionBumps();
      this.add('skill_version_bumps', bumps);
    }
  }

  add(name, value) {
    this.metrics.push({
      iteration: this.iteration,
      phase: this.phase,
      metric_name: name,
      metric_value: value,
      target: this.getTarget(name),
      status: this.evaluateStatus(name, value),
      timestamp: this.timestamp
    });
  }

  getTarget(name) {
    const targets = {
      'coverage_lines': '>=80',
      'coverage_branches': '>=75',
      'coverage_functions': '>=80',
      'mutation_score': '>=70',
      'test_pass_rate': '100',
      'e2e_pass_rate': '100',
      'adversarial_pass_rate': '100',
      'adversarial_p0_found': '0',
      'adversarial_p1_found': '0',
      'regression_rate': '<2',
      'cycle_time_hours': '<=336', // 2 weeks
      'mttf_hours': 'P0<4,P1<24',
      'human_interventions': '<10',
      'tokens_consumed_m': '<2.5',
      'context_refresh_rate': '>=3',
      'context_staleness_index': '0',
      'skills_modified': '>=1',
      'new_rules_added': '>=2',
      'skill_version_bumps': '>=1',
      'recurring_defect_reduction': '>50%'
    };
    return targets[name] || 'N/A';
  }

  evaluateStatus(name, value) {
    const target = this.getTarget(name);
    if (target === 'N/A') return 'NA';
    // Lógica simplificada - en producción usar evaluación numérica
    return 'PASS'; // placeholder
  }

  getCycleTime() {
    // Calcular desde git log o PROJECT_STATE.md
    return 336; // placeholder 2 semanas en horas
  }

  countHumanInterventions() {
    try {
      const log = execSync('git log --since="2 weeks ago" --oneline --grep="HUMAN\\|H[1-8]:"', { encoding: 'utf8' });
      return log.trim().split('\n').filter(l => l).length;
    } catch { return 0; }
  }

  estimateTokens() {
    // Placeholder - integrar con telemetría real del agente
    return 2.4; // millones
  }

  getModifiedSkills() {
    try {
      const diff = execSync('git diff --name-only HEAD~10 -- .opencode/skill/', { encoding: 'utf8' });
      return [...new Set(diff.trim().split('\n').map(f => f.split('/')[2]).filter(Boolean))];
    } catch { return []; }
  }

  countNewSkillRules() {
    // Parsear CHANGELOGs de skills modificadas
    return 4; // placeholder
  }

  countSkillVersionBumps() {
    // Contar git tags de skills en último commit
    return 1; // placeholder
  }

  async save() {
    const outputDir = 'docs/iterations/' + this.iteration;
    fs.mkdirSync(outputDir, { recursive: true });
    
    // CSV
    const csvHeader = 'iteration,phase,metric_name,metric_value,target,status,timestamp\n';
    const csvRows = this.metrics.map(m => 
      `${m.iteration},${m.phase},${m.metric_name},${m.metric_value},${m.target},${m.status},${m.timestamp}`
    ).join('\n');
    
    fs.writeFileSync(path.join(outputDir, 'metrics.csv'), csvHeader + csvRows);
    
    // JSON
    fs.writeFileSync(
      path.join(outputDir, 'metrics.json'), 
      JSON.stringify(this.metrics, null, 2)
    );
    
    // Markdown Dashboard
    this.generateDashboard(path.join(outputDir, 'DASHBOARD.md'));
    
    console.log(`✅ Métricas guardadas en ${outputDir}`);
  }

  generateDashboard(outputPath) {
    // Generar markdown similar a plantilla 1
    const md = `# ITERATION DASHBOARD — ${this.iteration}\n\n...`;
    fs.writeFileSync(outputPath, md);
  }
}

// CLI
const args = process.argv.slice(2);
const iteration = args.find(a => a.startsWith('--iteration='))?.split('=')[1] || 'ITER-XXX';
const phase = parseInt(args.find(a => a.startsWith('--phase='))?.split('=')[1] || '0');

const collector = new SAQIMetricsCollector(iteration, phase);

async function main() {
  switch (phase) {
    case 5: await collector.collectPhase5(); break;
    case 6: await collector.collectPhase6(); break;
    case 7: await collector.collectPhase7(); break;
    case 10: await collector.collectPhase10(); break;
    case 0: // Todas las fases + proceso + skills
      await collector.collectPhase5();
      await collector.collectPhase6();
      await collector.collectPhase7();
      await collector.collectPhase10();
      collector.collectProcessMetrics();
      collector.collectSkillMetrics();
      break;
  }
  await collector.save();
}

main().catch(console.error);
```

---

## 4. Referencias Internas

- SAQI-007: Marco de Métricas (Definiciones completas)
- SAQI-005: Proceso SAQI (Métricas por fase)
- SAQI-008: Protocolo Experimental (Variables dependientes)
- SAQI-011: Guía Aplicación (Dashboard ejemplo Open-RootERP)
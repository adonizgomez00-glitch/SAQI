# SAQI-012: Paquete de Replicación y Guía de Replicación

**Versión:** 1.0
**Fecha:** 2026-07-17
**Estado:** Borrador inicial
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Propósito

Este documento define el **Paquete de Replicación (Replication Package)** completo para que terceros puedan replicar:
1. El **caso de estudio Open-RootERP** (validación observacional)
2. El **protocolo experimental SAQI** (validación causal controlada)

Siguiendo estándares de replicación en ingeniería de software empírica (ACM SIGSOFT, IEEE TSE, INFORMS).

---

## 2. Estructura del Paquete de Replicación

```
saqi-replication-package/
├── README.md                           # Este archivo
├── LICENSE                             # MIT
├── case-study/                         # Réplica Open-RootERP
│   ├── template-repo/                  # Repo base clonable
│   ├── open-rooterp-final/             # Estado final verificado (tag v1.0.0)
│   ├── documentation/                  # PDFs/MDs de todos los docs SAQI
│   └── metrics/                        # CSVs con métricas brutas
├── experiment/                         # Protocolo experimental SAQI-008
│   ├── protocol/
│   │   ├── EXPERIMENT_PROTOCOL.md      # SAQI-008 completo
│   │   ├── CONSENT_FORM.md
│   │   ├── TRAINING_MATERIALS/
│   │   │   ├── group_a_adhoc.md
│   │   │   ├── group_b_saqi_full.md
│   │   │   └── group_c_saqi_light.md
│   │   └── BACKLOG.md                  # 10 historias + ACs idénticas
│   ├── template-repo/                  # Repo base para experimento
│   ├── skills-registry/                # 17 Skills SAQI v1.0
│   ├── production-test-suite/          # Tests ocultos para DER
│   └── analysis/
│       ├── statistical_analysis_plan.md
│       ├── analysis.R / analysis.py
│       └── data/                       # Datos anonimizados (post-experimento)
├── tooling/                            # SAQI CLI (futuro)
│   ├── saqi-checkpoint/
│   ├── saqi-skill-loader/
│   ├── saqi-metrics-collector/
│   └── saqi-gate-enforcer/
└── appendix/
    ├── SAQI-017_Arquitectura_OpenRootERP.md
    ├── SAQI-018_Plantillas_Agente.md
    ├── SAQI-019_Plantillas_Metricas.md
    ├── SAQI-020_Protocolos_Experimento.md
    └── SAQI-021_Modelos_Amenaza.md
```

---

## 3. Replicación del Caso de Estudio Open-RootERP

### 3.1 Objetivo
Reproducir el desarrollo de Open-RootERP siguiendo las **prácticas alineadas con SAQI** documentadas en SAQI-011, verificando que se alcanzan métricas comparables.

### 3.2 Prerrequisitos

| Requisito | Especificación |
|-----------|----------------|
| **Entorno** | Node.js ≥ 18, Python 3.8+, Git |
| **Editor** | VS Code recomendado (extensiones: ESLint, Prettier, Playwright) |
| **Conocimientos** | JavaScript ES Modules, IndexedDB/Dexie, MVC, Testing, OWASP básico |
| **Herramientas IA** | OpenCode (o equivalente con tool use: fs, shell, git, browser) |
| **Tiempo estimado** | 8-12 semanas (1 desarrollador) |

### 3.3 Pasos de Replicación

#### Paso 1: Preparación (Día 1)
```bash
# 1. Clonar template
git clone https://github.com/saqi/replication-package.git
cd replication-package/case-study/template-repo

# 2. Instalar dependencias
npm install

# 3. Copiar Dexie local (requerido para offline)
cp node_modules/dexie/dist/dexie.mjs assets/lib/dexie.js

# 4. Verificar tests base
npm test
# Debe pasar: 0 suites (repo vacío), 0 fallos

# 5. Iniciar documentos vivos (copiar plantillas SAQI-018)
cp ../documentation/templates/ARCHITECTURE.md.template ARCHITECTURE.md
cp ../documentation/templates/CONTEXT.md.template CONTEXT.md
cp ../documentation/templates/PROJECT_STATE.md.template PROJECT_STATE.md
cp ../documentation/templates/QA_RESULTS.md.template QA_RESULTS.md
cp ../documentation/templates/SESSION.md.template SESSION.md

# 6. Cargar Skills OpenCode (mínimo Nivel A)
opencode skill enable A-coding-standards A-project-architecture A-secure-coding A-context-manager A-testing C-qa-breaker D-git-workflow
opencode skill enable B-html-css B-javascript-clean B-ui-components B-authentication-security C-database-design-offline C-dexie-patterns D-erp-offline
opencode skill enable C-debugging C-documentation D-prompt-engineering
```

#### Paso 2: Iteración 1 - Fundación + Auth (Semanas 1-2)
Seguir [SAQI-011 Sección 3, Iteración 1](docs/03-case-study/SAQI-011-guia-aplicacion-open-rooterp.md#iteración-1-fundación--auth-semanas-1-2)

**Entregables esperados:**
- Módulo Auth funcional (PBKDF2-SHA512, Session, Permission, RBAC 30 perms, Setup/Login)
- 7 suites auth, 100% pass
- 2 tests E2E (Setup + Login)
- Fase 2 adversarial (subset auth attacks)
- Fixes documentados: setup redirect, findByKey→get, rol Vendedor auto-create
- Convenciones base en CONTEXT.md (Niveles A-D)

#### Paso 3: Iteración 2 - Catálogo + Inventario (Semanas 3-4)
Seguir SAQI-011 Iteración 2. Módulos: Products, Customers, Inventory, Suppliers (4 módulos CRUD completos).

#### Paso 4: Iteración 3 - Ventas + Compras + Transacciones (Semanas 5-6)
Seguir SAQI-011 Iteración 3. Transacciones atómicas, stock validation, contabilidad auto.

#### Paso 5: Iteración 4 - Reportes + Dashboard + Config (Semanas 7-8)
Seguir SAQI-011 Iteración 4. Chart.js, dark mode, auto-gen reports.

#### Paso 6: Iteración 5 - Export/Import + Contabilidad + Usuarios (Semanas 9-10)
Seguir SAQI-011 Iteración 5. Import auto-detect v2, prototype pollution guard, contabilidad completa.

#### Paso 7: Iteración 6 - Hardening + QA Final + Release (Semanas 11-12)
Seguir SAQI-011 Iteración 6. 100% tests pass, v1.0.0.

### 3.4 Criterios de Éxito de Replicación

| Métrica | Objetivo Réplica | Tolerancia |
|---------|------------------|------------|
| Tests Unit/Int pass | 100% | Obligatorio |
| Tests E2E pass | 100% | Obligatorio |
| Tests Adversariales pass | 100% | Obligatorio |
| Módulos completos | 14/14 | Obligatorio |
| Coverage L/B/F | ≥ 80/75/80 | ±5% |
| Mutation Score | ≥ 70% | ±10% |
| Defectos P0/P1 corregidos | ≥ 10 | Informativo |
| Tiempo total | 8-12 semanas | ±4 semanas |

### 3.5 Verificación Final
```bash
# Ejecutar suite completa
npm test                    # 45 suites, 410+ tests, 0 fallos
npm run test:e2e           # 38 E2E + 76 adversarial, 0 fallos

# Verificar métricas objetivo
# - Coverage: node tests/run-all.js --coverage
# - Mutation: custom mutation testing
# - Comparar con métricas SAQI-003 (Open-RootERP original)
```

---

## 4. Replicación del Experimento Controlado (SAQI-008)

### 4.1 Objetivo
Ejecutar el **RCT piloto** comparando Grupo A (ad-hoc) vs Grupo B (SAQI completo) vs Grupo C (SAQI Light).

### 4.2 Prerrequisitos Adicionales

| Requisito | Detalle |
|-----------|---------|
| **Participantes** | Mínimo 30 (10/grupo), devs 1-3, experiencia mixta |
| **Entorno controlado** | Mismo hardware, mismo modelo LLM (versión fija), mismo stack |
| **Tiempo** | 10-12 semanas experimento + 4 análisis |
| **Ética** | Consentimiento informado, datos anonimizados, IRB si aplica |

### 4.3 Pasos de Ejecución

#### Fase 0: Preparación (2 semanas antes)
```bash
# 1. Publicar replication package
# 2. Verificar template-repo
cd experiment/template-repo
npm install && npm test && npm run test:e2e

# 3. Preparar production-test-suite (oculta)
# - 100 tests adversariales no vistos
# - Inyecta defectos conocidos en código base

# 4. Configurar telemetría
# - Instrumentar OpenCode para tokens, tiempo, tool calls
# - Configurar saqi-metrics-collector en CI

# 5. Reclutar y consentir
# - 30+ participantes, consentimiento firmado
# - Aleatorización estratificada (Jr/Sr, team size, ERP exp)

# 6. Entrenar (2h c/u)
# Grupo A: "IA ad-hoc: prompting, testing básico"
# Grupo B: "SAQI completo: 14 fases, Skills, QA adversarial, contexto"
# Grupo C: "SAQI Light: Fases 1-7, 11-14 (sin F8, F9, F10, F14)"

# 7. Calibrar con ejercicio piloto 1h (módulo Auth) → datos descartados
```

#### Fase 1: Ejecución Principal (10-12 semanas)
```bash
# PARA CADA EQUIPO EN PARALELO:
for module in {1..10}; do
  # Grupo A (Ad-hoc):
  #   Prompt → Code → Test básico → Fix → Release
  #   SIN Skills, SIN Fase 7 separada, SIN docs vivos
  
  # Grupo B (SAQI Completo):
  #   Fases 1-14 COMPLETAS con gates H1-H8 obligatorios
  #   Skills versionadas, 4 docs vivos, Fase 7 gate, Fase 14 mejora skills
  
  # Grupo C (SAQI Light):
  #   Fases 1-7, 11-14 (SALTAR 8, 9, 10, 14)
  #   Skills, 4 docs, Fase 7 gate, PERO sin debugging estructurado, sin regresión completa, sin mejora skills
  
  # AL FINAL DE CADA MÓDULO:
  #   1. Ejecutar production-test-suite (oculta) → DER
  #   2. Ejecutar suite regresión completa → RR
  #   3. Medir MI (herramientas estáticas)
  #   4. Archivar: código, tests, docs, métricas, logs
done
```

#### Fase 2: Medición Post-Iteración
```bash
# production-test-suite (oculta) → DER real
# Suite regresión completa → RR real
# MI via herramientas estáticas (ESLint, sonarjs, jscpd)
```

#### Fase 2: Análisis (4 semanas)
```bash
# Ejecutar analysis.R / analysis.py
# Generar: statistical_output.txt, figures/, raw_metrics.csv
# Redactar reporte final
```

### 4.4 Criterios de Validación Experimental

| Hipótesis | Test | Criterio Éxito |
|-----------|------|----------------|
| H1: DER Grupo B < Grupo A | Mann-Whitney U | p < 0.05, Cliff's δ ≥ 0.33 |
| H2: ADR Grupo B > Grupo A | Mann-Whitney U | p < 0.05, δ ≥ 0.33 |
| H3: CT Grupo B ≈ Grupo A | TOST equivalencia ±20% | p < 0.05 |
| H4: HIC Grupo B < Grupo A | Mann-Whitney U | p < 0.05 |
| H5: MI Grupo B > Grupo A | Mann-Whitney U | p < 0.05 |
| H6: SRR transferencia | Comparativa proyecto nuevo | Exploratorio |
| H7: PSI SAQI < Ad-hoc | Sub-estudio prompts variados | Exploratorio |

**Corrección múltiple**: Holm-Bonferroni para H1-H5 (familia primaria).

---

## 5. Datos y Artefactos a Publicar (Open Science)

Tras replicación exitosa, publicar en repositorio abierto (Zenodo, Figshare, GitHub):

```
saqi-replication-results-YYYYMMDD/
├── case-study-replication/
│   ├── metrics_final.csv
│   ├── iteration_dashboards/
│   ├── final_code_snapshot/
│   └── lessons_learned.md
├── experiment/
│   ├── raw_data_anonymized.csv
│   ├── statistical_analysis_output.txt
│   ├── figures/
│   ├── CONSORT_flow_diagram.png
│   └── final_report.pdf
├── skills_evolution/
│   ├── skill_versions_timeline.csv
│   ├── skill_effectiveness_metrics.csv
│   └── skill_changelogs/
└── replication_notes.md
```

---

## 6. Guía de Solución de Problemas (Troubleshooting)

| Problema | Causa Probable | Solución |
|----------|----------------|----------|
| `npm test` falla "Cannot find module dexie" | Dexie no copiado a assets/lib/ | `cp node_modules/dexie/dist/dexie.mjs assets/lib/dexie.js` |
| Playwright tests timeout | Puerto 3099 ocupado / servidor no inicia | Verificar `python3 -m http.server 3000` en background; matar procesos previos |
| Mutation testing custom lento | 1000+ mutantes | Configurar mutation testing custom: `mutate: ['src/**/*.js']`, `testRunner: 'custom'`, `concurrency: 4` |
| Coverage < 80% | Tests faltantes en utils, components | Añadir tests para sanitizer, validators, helpers, ThemeManager, components |
| Phase 7 adversarial tests flaky | Timing, async, race conditions | Aumentar timeouts, usar `waitFor` Playwright, aislar tests |
| OpenCode skill no carga | Path incorrecto / frontmatter inválido | Verificar `~/.config/opencode/skills/generic/<skill>/SKILL.md` existe y YAML válido |
| Contexto LLM > 80% en sesión larga | Checkpointing no activado | Forzar checkpoint manual: `saqi context checkpoint` (cuando tooling exista) |
| Grupo A (control) usa prácticas SAQI | Contaminación / formación previa | Reforzar entrenamiento: "NO uses convenciones formales, NO docs vivos, NO Fase 7" |
| Dropout participante | Carga trabajo, frustración | Incentivos claros, checkpoints semanales, soporte técnico |

---

## 7. Contacto y Soporte

- **Repositorio principal**: `https://github.com/adonis/SAQI`
- **Issues**: Para bugs en replicación, protocolo, tooling
- **Discussions**: Para preguntas metodológicas, adaptaciones
- **Email**: adonizgomez00@gmail.com (autor principal)

---

## 8. Licencia

Este paquete de replicación se publica bajo **MIT License** (código) y **CC-BY-4.0** (documentación), permitiendo uso, modificación y distribución con atribución.

---

## 9. Referencias Internas

- SAQI-003: Caso Estudio Open-RootERP (Datos base)
- SAQI-005: Proceso SAQI (14 fases)
- SAQI-007: Marco Métricas (Definiciones)
- SAQI-008: Protocolo Experimental (Diseño RCT)
- SAQI-011: Guía Aplicación Open-RootERP (Pasos detallados)
- SAQI-017 a SAQI-021: Apéndices (Arquitectura, Plantillas, Métricas, Protocolos, Threat Models)

---

*Paquete de replicación vivo — Actualizar con cada replicación exitosa*
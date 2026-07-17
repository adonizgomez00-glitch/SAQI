# SAQI-008: Diseño Experimental y Protocolo de Validación

**Versión:** 1.0
**Fecha:** 2026-07-17
**Estado:** Borrador inicial
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Objetivo de la Validación

Establecer un **protocolo experimental riguroso y replicable** para evaluar la efectividad de la metodología SAQI frente a enfoques alternativos de desarrollo asistido por IA, midiendo impacto en calidad, eficiencia, y aprendizaje organizacional.

**Preguntas de Investigación (PICOC)**:

| Componente | Definición |
|------------|------------|
| **Population** | Equipos de desarrollo (1-3 personas) construyendo aplicaciones web offline-first de complejidad media (ERP, CRM, inventario) |
| **Intervention** | Metodología SAQI completa (Skills versionadas, 14 fases, QA adversarial obligatorio, contexto persistente, ciclo Learn→Skills) |
| **Comparison** | Desarrollo asistido por IA *ad-hoc* (prompting libre, sin Skills gobernadas, sin QA adversarial obligatorio, sin contexto persistente estructurado) |
| **Outcome** | Defect Escape Rate, Regression Rate, Cycle Time, Human Intervention Rate, Cost/Iteration, Maintainability Index, Skill Reuse Rate |
| **Context** | Proyectos greenfield, stack JS/TS + IndexedDB/Dexie, 8-12 semanas, 14 módulos funcionales objetivo |

---

## 2. Diseño Experimental

### 2.1 Diseño Principal: Ensayo Controlado Aleatorizado (RCT) por Tareas

```
                    ┌─────────────────────────────────────┐
                    │     RECLUTAMIENTO (N ≥ 20 equipos)  │
                    │  Equipos de 1-3 devs, experiencia   │
                    │  mixta (junior/senior), mismo stack │
                    └──────────────────┬──────────────────┘
                                       │
                    ┌──────────────────▼──────────────────┐
                    │      ALEATORIZACIÓN ESTRATIFICADA    │
                    │  Estratos: Experiencia (Jr/Sr),      │
                    │  Tamaño equipo (1 vs 2-3),           │
                    │  Dominio previo (ERP sí/no)          │
                    └──────────────────┬──────────────────┘
                                       │
            ┌──────────────────────────┼──────────────────────────┐
            │                          │                          │
            ▼                          ▼                          ▼
    ┌───────────────┐          ┌───────────────┐          ┌───────────────┐
    │   GRUPO A     │          │   GRUPO B     │          │   GRUPO C     │
    │  (CONTROL)    │          │  (TRATAMIENTO)│          │  (VARIANTE)   │
    │  IA Ad-hoc    │          │    SAQI       │          │  SAQI Light   │
    │  Prompting    │          │  Completo     │          │  (Fases 1-7,  │
    │  Libre        │          │  (14 fases)   │          │   11-14)      │
    └───────┬───────┘          └───────┬───────┘          └───────┬───────┘
            │                          │                          │
            ▼                          ▼                          ▼
    ┌───────────────┐          ┌───────────────┐          ┌───────────────┐
    │ 10 tareas     │          │ 10 tareas     │          │ 10 tareas     │
    │ por equipo    │          │ por equipo    │          │ por equipo    │
    │ (módulos)     │          │ (mismos       │          │ (mismos       │
    │               │          │  módulos que A)│         │  módulos)     │
    └───────────────┘          └───────────────┘          └───────────────┘
```

### 2.2 Variables

**Variables Independientes**:
- `Metodología`: {Ad-hoc, SAQI-Completo, SAQI-Light} — factor entre-sujetos
- `Tarea/Módulo`: 10 módulos estándar (Auth, Products, Customers, Sales, Purchases, Inventory, Accounting, Reports, Settings, Import/Export) — factor dentro-sujetos (cada equipo hace todos)

**Variables Dependientes Principales**:

| Variable | Definición Operacional | Medición |
|----------|------------------------|----------|
| **Defect Escape Rate (DER)** | Defectos encontrados en "producción" (simulada: test suite oculta post-release) / Total defectos inyectados | % (0-100) |
| **Regression Rate (RR)** | Defectos REG (Fase 10) / Total fixes aplicados | % (0-100) |
| **Cycle Time (CT)** | Tiempo pared-reloj desde inicio tarea (Fase 1) a Verify pass (Fase 11) | Horas |
| **Human Intervention Count (HIC)** | Número de decisiones humanas requeridas (gates H1-H8) por iteración | Conteo |
| **Cost per Iteration (CPI)** | Tokens LLM × costo/token + Horas humano × costo/hora | USD |
| **Test Pass Rate (TPR)** | Tests passing / Total tests en Verify gate | % (target 100%) |
| **Mutation Score (MS)** | Mutation score en Fase 5 | % (target ≥70%) |
| **Adversarial Detection Rate (ADR)** | Defectos P0+P1 encontrados en Fase 7 / Total defectos P0+P1 | % |
| **Maintainability Index (MI)** | ISO/IEC 25010: complejidad ciclomática, acoplamiento, deuda técnica post-iteración | Score 0-100 |
| **Skill Reuse Rate (SRR)** | Skills reutilizadas de iteraciones previas / Skills aplicables | % |

**Variables Dependientes Secundarias**:
- `Context Staleness Index`: Decisiones contradichas / Sesión
- `Prompt Sensitivity Index`: Varianza calidad output / Varianza prompt (sub-estudio)
- `Time to Fix (TTF)`: Tiempo medio detección → fix verificado
- `Skill Version Bumps`: Cambios en Skills por iteración

### 2.3 Control de Variables Extrañas

| Variable | Control |
|----------|---------|
| **Modelo LLM** | Mismo modelo (ej. GPT-4o, Claude 3.5) para todos los grupos; versionado fijo |
| **Stack tecnológico** | Idéntico: JS/TS, Dexie, Playwright, mismo repo base (template) |
| **Requerimientos** | Mismas 10 historias de usuario con ACs idénticas, mismo orden |
| **Entorno** | Mismo hardware, mismo IDE, misma red |
| **Experiencia** | Estratificación en aleatorización; covarianza en análisis |
| **Efecto Hawthorne** | Métricas objetivas automatizadas (no auto-reportes); observadores ciegos a grupo para análisis de código |
| **Madurez Skills** | Grupo B usa Skills v1.0 (baseline); cambios solo en Grupo B tras iteración 3 (longitudinal) |

---

## 3. Protocolo de Ejecución

### 3.1 Preparación (Pre-Experimento)

1. **Template Repo**: Crear repositorio base con:
   - `package.json` con deps fijas
   - `ARCHITECTURE.md` template (Clean Architecture)
   - `CONTEXT.md` template (stack, convenciones base)
   - `PROJECT_STATE.md` template
   - `QA_RESULTS.md` template
   - `SKILL_SELECTION.md` template
   - `ITERATION_PLAN.md` template
   - 10 historias de usuario con ACs en `BACKLOG.md`
   - Test suite oculta "producción" (100 tests adversariales no vistos por equipos)

2. **Skills SAQI Baseline**: Publicar 17 Skills v1.0 en registry (las de OpenCode + gobernanza SAQI-003)

3. **Entrenamiento**: 2 horas por equipo:
   - Grupo A: "Desarrollo asistido por IA: prompting efectivo, testing básico"
   - Grupo B: "Metodología SAQI: 14 fases, Skills, QA adversarial, contexto persistente"
   - Grupo C: "SAQI Light: Fases 1-7, 11-14 (sin debugging estructurado, sin mejora Skills)"

4. **Calibración**: Ejercicio piloto 1 hora (módulo Auth) — datos descartados

### 3.2 Ejecución por Iteración (Cada Tarea/Módulo)

```
PARA cada equipo EN paralelo:
  PARA cada módulo EN secuencia (1..10):
    SI Grupo A:
      Ejecutar: Prompt → Code → Test (ad-hoc) → Fix → Release
      Métricas: Auto-recolectadas por instrumentación
    SI Grupo B:
      Ejecutar Fases 1-14 SAQI completas
      Gates humanos H1-H8 obligatorios
      Métricas: Auto-recolectadas + logs agente
    SI Grupo C:
      Ejecutar Fases 1-7, 11-14 (saltar 8, 9, 10)
      Métricas: Auto-recolectadas
    
    AL FINAL DE CADA MÓDULO:
      - Ejecutar "Production Test Suite" (oculta) → DER
      - Ejecutar suite regresión completa → RR
      - Medir MI (herramientas estáticas)
      - Archivar: código, tests, docs, métricas, logs
```

### 3.3 Duración Estimada

| Actividad | Duración |
|-----------|----------|
| Preparación + Reclutamiento | 4 semanas |
| Entrenamiento + Piloto | 1 semana |
| Ejecución (10 iteraciones × 3 grupos × ~1 semana) | 10-12 semanas |
| Análisis + Redacción | 4 semanas |
| **Total** | **~20-22 semanas** |

---

## 4. Análisis Estadístico Planificado

### 4.1 Análisis Principal (Confirmatorio)

**Hipótesis Primaria (H1)**: SAQI-Completo reduce DER ≥ 50% vs Ad-hoc.

- **Test**: Mann-Whitney U (no paramétrico, datos no normales esperados)
- **Tamaño efecto**: Cliff's δ (interpretación: |δ|>0.47 grande)
- **IC 95%**: Bootstrap (10,000 resamples)
- **Significancia**: α = 0.05 (bilateral)
- **Potencia**: 80% → requiere n ≥ 10 por grupo (estimado)

**Hipótesis Secundarias**:

| Hipótesis | Variable | Test | Corrección Múltiple |
|-----------|----------|------|---------------------|
| H2: SAQI detecta más defectos seguridad/edge | ADR | Mann-Whitney | Holm-Bonferroni |
| H3: SAQI reduce RR | RR | Mann-Whitney | Holm-Bonferroni |
| H4: SAQI no aumenta CT significativamente | CT | Equivalencia (TOST) ±20% | Holm-Bonferroni |
| H5: SAQI reduce HIC | HIC | Mann-Whitney | Holm-Bonferroni |
| H6: SAQI mejora MI | MI | Mann-Whitney | Holm-Bonferroni |
| H7: SAQI Light ≈ SAQI Completo en DER | DER (B vs C) | Equivalencia TOST | Exploratorio |

### 4.2 Análisis Exploratorio

- **Modelos mixtos**: `Outcome ~ Metodología + Iteración + Metodología×Iteración + (1|Equipo) + Experiencia`
- **Análisis de mediación**: ¿El efecto de SAQI en DER se media por ADR y Mutation Score?
- **Análisis longitudinal**: Tendencias de SRR, Skill Version Bumps, Context Staleness a lo largo de iteraciones
- **Análisis cualitativo**: Entrevistas post-experimento (cognitivas, usabilidad Skills, carga cognitiva)

### 4.3 Manejo de Datos Faltantes / Atipicos

- **Dropout equipo**: Análisis intención-de-tratar (última observación carried forward) + análisis per-protocolo
- **Outliers**: Winsorización 5% + análisis de sensibilidad sin outliers
- **Datos censurados** (timeouts): Análisis de supervivencia para CT

---

## 5. Validez Interna y Externa

### 5.1 Amenazas a Validez Interna (y Mitigaciones)

| Amenaza | Mitigación |
|---------|------------|
| **Sesgo de selección** | Aleatorización estratificada + covarianza experiencia |
| **Sesgo de ejecución** | Mismo modelo LLM, mismo stack, mismos requerimientos |
| **Efecto Hawthorne** | Métricas objetivas automatizadas; analistas ciegos a grupo |
| **Contaminación** | Equipos aislados (repos separados, sin comunicación cruzada) |
| **Madurez Skills** | Skills v1.0 fijas para Grupo B en iteraciones 1-3; evolutivas 4-10 |
| **Fatiga / Aprendizaje** | Orden tareas aleatorizado por equipo; modelo mixto controla iteración |
| **Expectativa investigador** | Análisis ciego (grupos codificados A/B/C sin etiqueta metodología) |

### 5.2 Amenazas a Validez Externa (Generalizabilidad)

| Limitación | Mitigación Futura |
|------------|-------------------|
| **Stack único** (JS/Dexie/Playwright) | Réplicas: Python/FastAPI/SQLite, Go/SQLite, Rust/Tauri |
| **Dominio único** (ERP offline) | Réplicas: SaaS online, Mobile, Embedded |
| **Tamaño equipo** (1-3) | Réplicas: equipos 5-8 (escalado) |
| **Duración** (10 iteraciones) | Estudios longitudinales 6-12 meses |
| **Modelo LLM único** | Réplicas: GPT-4o, Claude 3.5, Llama 3 70B, modelos locales 7B |
| **Greenfield only** | Caso brownfield: legacy migration |

---

## 6. Métricas de Calidad Experimental (CONSORT-adaptado)

| Criterio | Métrica | Objetivo |
|----------|---------|----------|
| **Reclutamiento** | Equipos elegibles / contactados | > 60% |
| **Adherencia protocolo** | % fases completadas según grupo | > 95% Grupo B/C, > 90% Grupo A |
| **Completitud datos** | % variables dependientes capturadas | 100% (automatizado) |
| **Ceguera análisis** | Analistas ciegos a asignación grupo | 100% |
| **Reporte** | CONSORT flow diagram + datos brutos | Público (repositorio) |

---

## 7. Paquete de Replicación (Replication Package)

Para cada experimento, publicar en repositorio abierto:

```
replication-package/
├── protocol/
│   ├── EXPERIMENT_PROTOCOL.md (este documento)
│   ├── CONSENT_FORM.md
│   ├── TRAINING_MATERIALS/
│   └── BACKLOG.md (10 historias + ACs)
├── template-repo/ (repo base clonable)
├── skills-registry/ (17 Skills v1.0 SAQI)
├── production-test-suite/ (tests ocultos para DER)
├── analysis/
│   ├── statistical_analysis_plan.md
│   ├── analysis.R / analysis.py (scripts reproducibles)
│   └── data/ (datos anonimizados CSV)
├── results/
│   ├── raw_metrics.csv
│   ├── statistical_output.txt
│   └── figures/
└── README.md (instrucciones replicación)
```

---

## 8. Referencias Internas

- SAQI-001: Resumen Ejecutivo (Hipótesis H1-H7)
- SAQI-003: Caso Estudio Open-RootERP (Datos baseline)
- SAQI-004: Marco QA (Fase 7 diseño)
- SAQI-005: Proceso SAQI (Fases 1-14)
- SAQI-007: Marco Métricas (Definiciones variables dependientes)
- SAQI-009: Amenazas a Validez (Análisis detallado)
- SAQI-012: Paquete de Replicación (Guía práctica)

---

## 9. Referencias Externas

1. Wohlin, C., et al. (2012). *Experimentation in Software Engineering*. Springer.
2. Kitchenham, B., et al. (2002). "Preliminary guidelines for empirical research in software engineering." *IEEE TSE*.
3. CONSORT 2010 Statement: *Consolidated Standards of Reporting Trials*.
4. Arcuri, A. & Briand, L. (2014). "A practical guide for using statistical tests to assess randomized algorithms in software engineering." *ICSE*.
5. Madeira, F., et al. (2020). "Guidelines for Reporting Empirical Studies in Software Engineering." *arXiv*.
6. ISO/IEC 25010:2011 — Systems and software Quality Requirements and Evaluation (SQuaRE).
7. Forsgren, N., Humble, J., & Kim, G. (2018). *Accelerate: The Science of Lean Software and DevOps*. IT Revolution Press.
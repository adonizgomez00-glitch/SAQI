# SAQI-020: Apéndice D — Protocolos de Experimento

**Versión:** 1.0
**Fecha:** 2026-07-17
**Estado:** Borrador inicial
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Protocolo Experimental Principal (SAQI-008 Compliant)

### 1.1 Diseño: RCT Estratificado Tres Brazos

```
                          ┌─────────────────────────────────────┐
                          │     RECLUTAMIENTO (N ≥ 30)          │
                          │  Devs 1-3, exp. mixta, mismo stack  │
                          └──────────────────┬──────────────────┘
                                             │
                              ┌──────────────┼──────────────┐
                              ▼              ▼              ▼
                        ┌───────────┐  ┌───────────┐  ┌───────────┐
                        │  GRUPO A  │  │  GRUPO B  │  │  GRUPO C  │
                        │ (CONTROL) │  │ (TRATAM.) │  │ (VARIANT) │
                        │ IA Ad-hoc │  │  SAQI     │  │ SAQI Light│
                        │ Prompting │  │ Completo  │  │ (F1-7,11-14)│
                        └─────┬─────┘  └─────┬─────┘  └─────┬─────┘
                              │              │              │
                        10 tareas       10 tareas       10 tareas
                        (mismos mods)   (mismos mods)   (mismos mods)
```

### 1.2 Variables

**Independientes:**
- `Metodología`: {Ad-hoc, SAQI-Completo, SAQI-Light} — entre-sujetos
- `Tarea/Módulo`: 10 módulos estándar — dentro-sujetos (cada equipo hace todos)

**Dependientes Principales:**
| Variable | Definición Operacional | Medición |
|----------|------------------------|----------|
| **DER** | Defectos en "producción" (suite oculta) / Total defectos inyectados | % |
| **RR** | Defectos REG (Fase 10) / Total fixes aplicados | % |
| **CT** | Tiempo pared-reloj Plan (F1) → Verify pass (F11) | Horas |
| **HIC** | Decisiones humanas requeridas (gates H1-H8) / iteración | Conteo |
| **CPI** | Tokens LLM × $/token + Horas humano × $/hora | USD |
| **TPR** | Tests passing / Total tests en Verify gate | % (target 100%) |
| **MS** | Mutation Score (Stryker) en Fase 5 | % (target ≥70%) |
| **ADR** | Defectos P0+P1 en Fase 7 / Total defectos P0+P1 | % |
| **MI** | Maintainability Index post-iteración (complejidad, acoplamiento, deuda) | Score 0-100 |
| **SRR** | Skills reutilizadas iteraciones previas / Skills aplicables | % |

**Dependientes Secundarias:**
- `CSI`: Context Staleness Index (decisiones contradichas/sesión)
- `PSI`: Prompt Sensitivity Index (Var(calidad)/Var(prompt)) — sub-estudio
- `TTF`: Time to Fix (detección → fix verificado)
- `Skill_VB`: Skill Version Bumps / iteración

### 1.3 Control de Variables Extrañas

| Variable | Control |
|----------|---------|
| **Modelo LLM** | Versión fija (ej. GPT-4o-2024-05-13) para TODOS los grupos |
| **Stack** | Repo template idéntico: React/TS/Vite + Dexie/IndexedDB + Playwright |
| **Requerimientos** | 10 historias con ACs idénticas, mismo orden, mismo `BACKLOG.md` |
| **Entorno** | Mismo hardware (CI runners), misma red, mismo IDE (VS Code + extensiones) |
| **Experiencia** | Estratificación: Jr/Sr, tamaño equipo (1 vs 2-3), dominio ERP (sí/no) |
| **Efecto Hawthorne** | Métricas objetivas automatizadas (no auto-reportes); analistas ciegos a grupo |
| **Madurez Skills** | Grupo B usa Skills v1.0 baseline iteraciones 1-3; evolutivas 4-10 |
| **Fatiga/Aprendizaje** | Orden tareas aleatorizado por equipo; modelo mixto controla iteración |

---

## 2. Protocolo de Ejecución Detallado

### 2.1 Fase 0: Preparación (2 semanas antes)

```bash
# 1. Publicar replication package
git clone https://github.com/saqi/replication-package.git
cd replication-package

# 2. Verificar template-repo
cd experiment/template-repo
npm install && npm test && npm run test:e2e
# Debe pasar: 0 suites base, 0 fallos

# 3. Preparar production-test-suite (oculta)
# - 100 tests adversariales no vistos por participantes
# - Inyecta defectos conocidos en código base
# - Mide DER real

# 4. Configurar telemetría
# - Instrumentar OpenCode para tokens, tiempo, tool calls
# - Configurar saqi-metrics-collector en CI

# 5. Reclutar y consentir
# - 30+ participantes, consentimiento informado firmado
# - Asignación aleatoria estratificada a A/B/C
```

### 2.2 Fase 1: Entrenamiento (1 semana)

| Grupo | Contenido (2h) | Práctica (1h) |
|-------|----------------|---------------|
| **A (Ad-hoc)** | "Desarrollo asistido por IA: prompting efectivo, testing básico, debugging" | Ejercicio: módulo Auth con IA chat |
| **B (SAQI)** | "Metodología SAQI: 14 fases, Skills, QA adversarial, contexto persistente, gates" | Ejercicio: iteración completa SAQI (módulo Auth) |
| **C (Light)** | "SAQI Light: Fases 1-7, 11-14 (sin debugging estructurado F8, sin regresión completa F10, sin mejora skills F14)" | Ejercicio: iteración Light (módulo Auth) |

**Calibración**: Todos hacen módulo Auth (1h) → datos descartados → verificar que tooling funciona.

### 2.3 Fase 2: Ejecución Principal (10-12 semanas)

```bash
# PARA CADA EQUIPO EN PARALELO:
for module in {1..10}; do
  # Grupo A: Ad-hoc
  #   Prompt → Code → Test básico → Fix → Release (sin Skills, sin Fase 7 separada, sin docs vivos)
  #   Métricas: auto-recolectadas por instrumentación
  
  # Grupo B: SAQI Completo
  #   Fases 1-14 completas con gates H1-H8 obligatorios
  #   Skills versionadas, 4 docs vivos, Fase 7 gate, Fase 14 mejora skills
  
  # Grupo C: SAQI Light
  #   Fases 1-7, 11-14 (saltar 8, 9, 10, 14)
  #   Skills, 4 docs, Fase 7 gate, PERO sin debugging estructurado, sin regresión completa
  
  # AL FINAL DE CADA MÓDULO:
  #   1. Ejecutar production-test-suite (oculta) → DER
  #   2. Ejecutar suite regresión completa → RR
  #   3. Medir MI (herramientas estáticas)
  #   4. Archivar: código, tests, docs, métricas, logs
done
```

### 2.4 Monitoreo Semanal

| Métrica | Alerta Si | Acción |
|---------|-----------|--------|
| **Dropout** | > 10% equipos | Contactar, ofrecer soporte, documentar razón |
| **Adherencia Fase 7** | Grupo B < 95% | Recordatorio gate obligatorio; si persiste → análisis causa |
| **Tokens/iter > 3M** | Cualquier grupo | Revisar prompt efficiency; ofrecer modelo routing |
| **Cycle Time > 3 sem** | Cualquier equipo | Revisar bottlenecks; ofrecer pairing |
| **Production DER > 30%** | Grupo A | Verificar que no hay contaminación (Grupo A usando prácticas B) |

---

## 3. Análisis Estadístico Planificado (Pre-registrado)

### 3.1 Análisis Confirmatorio (Primario)

| Hipótesis | Variable | Test | α | Potencia | N/grupo |
|-----------|----------|------|---|----------|---------|
| **H1**: SAQI reduce DER ≥ 50% | DER | Mann-Whitney U (A vs B) | 0.05 bilateral | 0.80 | 10 |
| **H2**: SAQI detecta más defectos seguridad/edge | ADR | Mann-Whitney U (A vs B) | 0.05 | 0.80 | 10 |
| **H3**: SAQI no aumenta CT significativamente | CT | TOST equivalencia ±20% | 0.05 | 0.80 | 10 |
| **H4**: SAQI reduce HIC | HIC | Mann-Whitney U (A vs B) | 0.05 | 0.80 | 10 |
| **H5**: SAQI mejora MI | MI | Mann-Whitney U (A vs B) | 0.05 | 0.80 | 10 |

**Corrección múltiple**: Holm-Bonferroni para H1-H5 (familia primaria).

### 3.2 Análisis Secundario (Exploratorio)

| Análisis | Método |
|----------|--------|
| **Modelo mixto** | `Outcome ~ Metodología + Iteración + Metodología×Iteración + (1|Equipo) + Experiencia` |
| **Mediación** | ¿Efecto SAQI en DER mediado por ADR y MS? (Bootstrap 10k) |
| **Longitudinal Skills** | Tendencia SRR, Skill_VB, New_Rules a lo largo de 10 iteraciones |
| **Equivalencia B vs C** | TOST DER, CT, MI (Grupo B vs C) — ¿Light ≈ Completo? |
| **Sensibilidad Prompt** | Sub-estudio: misma tarea, 5 prompts variados × 3 modelos → PSI |

### 3.3 Manejo de Datos

| Situación | Tratamiento |
|-----------|-------------|
| **Dropout equipo** | ITT: última observación carried forward; Per-protocol: excluir |
| **Outliers** | Winsorización 5% + análisis sensibilidad sin outliers |
| **Datos censurados** (timeouts) | Análisis supervivencia para CT |
| **No normalidad** | Tests no paramétricos (Mann-Whitney, Wilcoxon signed-rank) |
| **Valores faltantes métricas coste** | Imputación múltiple (MICE) + sensibilidad |

### 3.4 Software de Análisis

```r
# R script template (analysis.R)
library(tidyverse)
library(coin)           # Mann-Whitney exacto
library(equivalence)    # TOST
library(lme4)           # Mixed models
library(mediation)      # Análisis mediación
library(survival)       # Análisis supervivencia
library(mice)           # Imputación múltiple
library(ggplot2)        # Visualización
library(broom)          # Tidy output

# Cargar datos anonimizados
data <- read_csv("data/raw_metrics.csv")

# Pre-registrar análisis confirmatorio
# ...
```

---

## 4. Sub-Estudio: Prompt Sensitivity Index (PSI)

### 4.1 Diseño

- **Participantes**: Submuestra 5 equipos Grupo B (SAQI)
- **Tarea**: Módulo "Products CRUD" (idéntico para todos)
- **Prompts**: 5 variaciones sistemáticas por AC (formato, detalle, ejemplos, restricciones, tono)
- **Modelos**: 3 (GPT-4o, Claude 3.5 Sonnet, Llama 3 70B)
- **Total condiciones**: 5 prompts × 3 modelos × 5 equipos = 75 observaciones

### 4.2 Medición

| Variable | Definición |
|----------|------------|
| **Quality Score** | Composite: TPR × Coverage × MS × (1 - Defects) normalizado 0-1 |
| **Prompt Variance** | Desviación estándar de Quality Score across 5 prompts |
| **Model Variance** | Desviación estándar across 3 modelos |
| **PSI** | Prompt Variance / (Prompt Variance + Model Variance + Residual) |

### 4.3 Hipótesis

**H5**: PSI_SAQI < 0.3 vs PSI_Adhoc > 0.6 (Skills reducen sensibilidad a prompt)

---

## 5. Ética y Cumplimiento

| Aspecto | Implementación |
|---------|----------------|
| **Consentimiento** | Formulario firmado digital; derecho a retirarse sin penalización |
| **Datos** | Anonimización: Equipo → ID aleatorio; sin PII en métricas |
| **Almacenamiento** | Encriptado en repositorio privado; acceso solo investigadores |
| **Publicación** | Datos agregados; replication package con datos anonimizados en Zenodo |
| **IRB/Ética** | Solicitud comité ética institucional si aplica (universidad/empresa) |
| **Compensación** | Tiempo compensado (horas facturables / créditos académicos) |

---

## 6. Cronograma Detallado

| Semana | Actividad | Entregable |
|--------|-----------|------------|
| 1-2 | Preparación, reclutamiento, consentimientos | Replication package v1.0, consent forms |
| 3 | Entrenamiento + calibración | Training completion certificates |
| 4-13 | Ejecución 10 iteraciones (paralelo) | Weekly monitoring reports |
| 14-15 | Production test suite execution | DER, RR, MI data |
| 16-19 | Análisis estadístico + redacción | Statistical output, figures, draft paper |
| 20 | Revisión interna + replication package final | Final dataset, analysis code, paper |

---

## 7. Criterios de Éxito del Experimento

| Criterio | Umbral Mínimo |
|----------|---------------|
| **Completitud datos** | ≥ 95% métricas primarias capturadas |
| **Adherencia protocolo** | ≥ 90% fases ejecutadas según grupo |
| **Potencia estadística** | Post-hoc power ≥ 0.80 para H1 |
| **Validez interna** | No diferencias basales significativas entre grupos (p > 0.05) |
| **Replicabilidad** | Analysis code + data reproducen resultados exactamente |

---

## 8. Referencias Internas

- SAQI-001: Resumen Ejecutivo (Hipótesis H1-H7)
- SAQI-007: Marco Métricas (Definiciones variables dependientes)
- SAQI-008: Diseño Experimental (Diseño completo)
- SAQI-009: Amenazas Validez (Mitigaciones incluidas aquí)
- SAQI-012: Paquete Replicación (Materiales prácticos)

---

## 9. Referencias Metodológicas

1. Wohlin et al. (2012). *Experimentation in Software Engineering*. Springer.
2. Kitchenham et al. (2002). "Preliminary guidelines for empirical research in SE." *IEEE TSE*.
3. Arcuri & Briand (2014). "A practical guide for using statistical tests to assess randomized algorithms in SE." *ICSE*.
4. CONSORT 2010 Statement: *Consolidated Standards of Reporting Trials*.
5. ISO/IEC 25010:2011 — SQuaRE.
5. Forsgren et al. (2018). *Accelerate*. IT Revolution Press. (DORA metrics)
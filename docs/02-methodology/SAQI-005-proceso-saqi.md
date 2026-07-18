# SAQI-005: Definición del Proceso SAQI — 14 Fases (DEFINE-DOCUMENT-APPLY-MEASURE-COMPARE-ITERATE-VALIDATE)

**Versión:** 1.1
**Fecha:** 2026-07-17
**Estado:** Corregido con datos reales del caso de estudio Open-RootERP
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Visión General del Proceso

El proceso SAQI consta de **14 fases secuenciales** con criterios de entrada/salida explícitos, responsable humano/agente definido, y artefactos generados trazables. El macro-ciclo sigue el patrón:

**DEFINE → DOCUMENT → APPLY → MEASURE → COMPARE → ITERATE → VALIDATE**

| Macro-Fase | Fases SAQI | Objetivo |
|------------|------------|----------|
| **DEFINE** | 1. Plan, 2. Architect, 3. Skill Selection | Establecer qué se construye, cómo, y con qué conocimiento |
| **DOCUMENT** | 12. Document, 13. Learn (Context Update) | Capturar decisiones, estado, lecciones |
| **APPLY** | 4. Build | Implementar asistido por agente con Skills |
| **MEASURE** | 5. Test (Unit/Int), 6. Test (Func/E2E), 7. Break (Adversarial) | Verificar calidad en múltiples niveles |
| **COMPARE** | 8. Diagnose, 9. Fix, 10. Regression | Analizar gaps, corregir, asegurar no regresión |
| **ITERATE** | 11. Verify, 13. Learn, 14. Improve Skills | Validar release, actualizar contexto, mejorar conocimiento |
| **VALIDATE** | (Transversal) | Métricas, experimentos, replicación (SAQI-007, SAQI-008) |

---

## 2. Definición Detallada de Fases

### FASE 1: PLAN (Planificación de Iteración)

| Atributo | Definición |
|----------|------------|
| **Objetivo** | Definir alcance, objetivos, y criterios de aceptación de la iteración |
| **Entrada** | Backlog priorizado, `CONTEXT.md` (reglas negocio), `PROJECT_STATE.md` (estado actual), `QA_RESULTS.md` (lecciones previas) |
| **Actividades** | 1. Seleccionar historias/backlog items para iteración<br>2. Definir **Criterios de Aceptación (AC)** testables por item<br>3. Estimar esfuerzo (puntos/complejidad)<br>4. Identificar dependencias técnicas y riesgos<br>5. Definir **Definition of Done (DoD) iteración**<br>6. Actualizar `PROJECT_STATE.md`: sprint backlog, fechas |
| **Responsable Humano** | Product Owner / Tech Lead / Orquestador |
| **Responsable Agente** | Asistir: estimar complejidad técnica, identificar dependencias código, sugerir partición historias |
| **Artefactos Generados** | `ITERATION_PLAN.md` (alcance, ACs, DoD, riesgos), `PROJECT_STATE.md` actualizado |
| **Criterio Salida** | Plan aprobado por Humano; ACs testables; DoD clara; riesgos mitigados o aceptados |

**Evidencia Open-RootERP**: `ROADMAP.md` (fases 1-5), `TODO.md` (checklist 371 líneas), issues implícitos — completados ✅

---

### FASE 2: ARCHITECT (Arquitectura y Diseño)

| Atributo | Definición |
|----------|------------|
| **Objetivo** | Definir/actualizar arquitectura para el alcance de la iteración; asegurar coherencia global |
| **Entrada** | `ITERATION_PLAN.md`, `ARCHITECTURE.md` (actual), `CONTEXT.md`, Skills Nivel A (`A-project-architecture`, `A-secure-coding`) |
| **Actividades** | 1. Revisar ADRs existentes relevantes<br>2. Identificar decisiones arquitectónicas nuevas requeridas<br>3. Para cada decisión nueva: crear **ADR** (Contexto, Decisión, Consecuencias, Alternativas, Estado)<br>4. Actualizar diagramas: Contexto, Contenedores, Componentes, Secuencia (modelo C4)<br>5. Definir/actualizar **Contratos de Puertos** (interfaces dominio → infraestructura)<br>6. Validar contra Skills: `A-project-architecture` (clean/hexagonal), `A-secure-coding` (threat model), `C-database-design-*` (schema) |
| **Responsable Humano** | Arquitecto / Tech Lead (decisión final ADRs) |
| **Responsable Agente** | Generar borradores ADR, diagramas Mermaid/PlantUML, validar consistencia con ADRs previos, proponer alternativas |
| **Artefactos Generados** | `ARCHITECTURE.md` actualizado, ADRs nuevos (`docs/adr/ADR-XXX.md`), Diagramas actualizados, Contratos de puertos (`src/domain/ports/`) |
| **Criterio Salida** | ADRs aprobados por Humano; Diagramas coherentes; Contratos de puertos definidos; Validación Skills A pasada |

**Evidencia Open-RootERP**: `ARCHITECTURE.md` v1.0 documentado *a priori* y actualizado; decisiones: MVC, Repository, Service, DI manual, Dexie schema v8, ADRs registradas implícitamente

---

### FASE 3: SKILL SELECTION (Selección y Configuración de Skills)

| Atributo | Definición |
|----------|------------|
| **Objetivo** | Seleccionar, versionar y configurar las Skills aplicables a la iteración |
| **Entrada** | `ITERATION_PLAN.md`, `ARCHITECTURE.md`, Stack tecnológico, Catálogo Skills disponible |
| **Actividades** | 1. Mapear requerimientos iteración → Skills necesarias (Matriz Requerimiento × Skill)<br>2. **Nivel A**: Verificar TODAS obligatorias incluidas (`A-coding-standards`, `A-project-architecture`, `A-secure-coding`, `A-context-manager`, `A-testing`, `D-git-workflow`)<br>3. **Nivel B**: Seleccionar según dominio/stack (ej: `C-database-design-offline`, `C-dexie-patterns`, `B-authentication-security`, `D-erp-offline`, `B-ui-components`, `B-html-css`, `B-javascript-clean`)<br>4. **Nivel C**: Incluir `C-debugging`, `C-documentation`, `C-qa-breaker`<br>5. **Nivel D**: Incluir `D-prompt-engineering`, `D-Agente-IA` si mejora continua<br>6. Registrar **versiones exactas** de cada Skill (SemVer) en `ITERATION_PLAN.md`<br>7. Verificar compatibilidad Skills (no conflictos reglas)<br>8. Configurar agente con Skills seleccionadas (cargar en contexto) |
| **Responsable Humano** | Tech Lead / Orquestador (selección final, aprobación versiones) |
| **Responsable Agente** | Cargar Skills, validar sintaxis, reportar conflictos, generar resumen Skills activas |
| **Artefactos Generados** | `SKILL_SELECTION.md` (lista skills + versiones + justificación), Configuración agente actualizada |
| **Criterio Salida** | Skills Nivel A 100% incluidas; Versiones registradas; Conflictos resueltos; Agente configurado |

**Evidencia Open-RootERP**: `Context.md` líneas 84-106 define **convenciones obligatorias por niveles A-D** (equivalentes a Skills de proyecto) — aplicadas en todo el desarrollo

---

### FASE 4: BUILD (Implementación Asistida por Agente)

| Atributo | Definición |
|----------|------------|
| **Objetivo** | Implementar código de la iteración guiado por Skills, Arquitectura, Contexto |
| **Entrada** | `ITERATION_PLAN.md`, `ARCHITECTURE.md`, `CONTEXT.md`, `SKILL_SELECTION.md`, Skills cargadas, Código base actual |
| **Actividades** | 1. **Por cada historia/AC:**<br>   a. Agente lee AC, arquitectura, Skills relevantes<br>   b. Agente genera implementación + tests unitarios (L1) simultáneamente (TDD asistido)<br>   c. Humano revisa código crítico (security, arquitectura, domain logic)<br>   d. Ejecutar tests unitarios (L1) → corregir hasta 100% pass<br>2. Aplicar **patrones de Skills** obligatorios (Repository, Result, Builders, etc.)<br>3. **Linting** obligatorio tras cada cambio (Skill `A-coding-standards`)<br>4. Commits atómicos con mensajes convencionales (Skill `D-git-workflow` si existe)<br>5. Actualizar `PROJECT_STATE.md` progresivamente (tareas completadas, bloqueos) |
| **Responsable Humano** | Revisión código crítico (security, arquitectura, domain); Decisiones diseño no triviales; Aprobación merges |
| **Responsable Agente** | Generación código + tests; Refactoring guiado por Skills; Linting/Typecheck; Commits |
| **Artefactos Generados** | Código fuente (`src/`), Tests unitarios (`tests/unit/`, `tests/integration/`), Commits, `PROJECT_STATE.md` actualizado |
| **Criterio Salida** | 100% tests unitarios pass; Coverage ≥ 80% L / 75% B / 80% F; Mutation Score ≥ 70%; 0 lint/type errors; Code review crítica aprobada; `PROJECT_STATE.md` refleja completado |

**Evidencia Open-RootERP**: Desarrollo iterativo por módulo (14 módulos), código + tests simultáneos, DI en `app.js`, commits convencionales, `PROJECT_STATE.md` actualizado

---

### FASE 5: TEST — Unit & Integration (L1-L2)

| Atributo | Definición |
|----------|------------|
| **Objetivo** | Verificar exhaustivamente unidades y adaptadores con criterios cuantitativos |
| **Entrada** | Código de Fase 4, Skills `A-testing`, `C-dexie-patterns`, `C-database-design-*` |
| **Actividades** | 1. Ejecutar suite completa Unit + Integration (`node tests/run-all.js --coverage`)<br>2. Verificar **umbrales**: Coverage L≥80%, B≥75%, F≥80%<br>3. Validar **Contract Tests** para cada puerto (fake + real implementations pasan mismos tests)<br>4. Verificar **Property-Based Tests** para invariantes de dominio<br>5. Corregir fallos → volver a Fase 4 (ciclo interno) hasta 100% pass<br>6. Generar reporte cobertura (`coverage/lcov-report`) |
| **Responsable Humano** | Analizar gaps cobertura; Decidir si cobertura < umbral es aceptable (justificado en ADR) |
| **Responsable Agente** | Ejecutar suites; Reportar métricas; Sugerir tests faltantes |
| **Artefactos Generados** | Reportes cobertura; Tests añadidos/modificados; `QA_RESULTS.md` actualizado (sección L1-L2) |
| **Criterio Salida** | 100% tests pass; Umbrales cobertura cumplidos; Reportes archivados |

**Evidencia Open-RootERP**: `node tests/run-all.js` → 45 suites, 410+ tests, 0 fallos; runner custom con DOM shim; Dexie memory adapter para unit; integration tests con Dexie real en memoria

---

### FASE 6: TEST — Functional & E2E (L3-L4)

| Atributo | Definición |
|----------|------------|
| **Objetivo** | Verificar casos de uso completos y journeys de usuario en entorno real |
| **Entrada** | Build deployable (staging/local), `CONTEXT.md` (CUJs), Skills `A-testing`, `B-ui-components` |
| **Actividades** | 1. **Functional (L3):** Ejecutar tests de Use Cases directos (sin UI) con repos reales (en memoria)<br>2. **E2E (L4):** Ejecutar suite Playwright contra build staging<br>   - Critical User Journeys (CUJs) definidos en `CONTEXT.md`<br>   - Cross-browser: Chromium, Firefox, WebKit<br>   - Mobile viewports: 375×667, 414×896 + touch events<br>   - Accesibilidad: axe-core en cada página crítica (0 critical/serious)<br>   - Offline: tests con `navigator.onLine = false`<br>3. Performance: Lighthouse CI budgets (LCP, TBT, CLS)<br>4. Corregir fallos → Fase 4/9 según naturaleza |
| **Responsable Humano** | Definir CUJs; Validar flujos UX; Aprobar excepciones performance |
| **Responsable Agente** | Ejecutar suites; Generar reports (Playwright HTML, Lighthouse, Axe); Flaky test detection |
| **Artefactos Generados** | Reports Playwright, Lighthouse, Axe; `QA_RESULTS.md` actualizado (sección L3-L4) |
| **Criterio Salida** | 100% CUJs pass; 0 a11y critical/serious; Performance budgets met; Cross-browser pass |

**Evidencia Open-RootERP**: `npm run test:e2e` → Playwright 38 tests, 0 fallos; cubre todos 14 módulos; axe-core integrado; mobile viewports; offline tests

---

### FASE 7: BREAK — QA Adversarial (L5) ⭐ **FASE CRÍTICA SAQI**

| Atributo | Definición |
|----------|------------|
| **Objetivo** | **Intentar romper el sistema deliberadamente** usando taxonomía de ataques obligatoria |
| **Entrada** | Sistema deployable, Fase 5-6 passing, `QA_RESULTS.md` (historial), Threat Models (SAQI-021), Skills `C-qa-breaker`, `A-secure-coding` |
| **Actividades** | **Ver SAQI-004 sección 4.2 para protocolo completo**<br>Resumen:<br>1. Ejecutar 8 categorías ATK-* en orden prioridad (E→A→B→C→D→G→H→F)<br>2. Automatizado donde posible (PBT, load, SAST, DAST, a11y)<br>3. Manual donde requiere creatividad (chaos, business logic bypass, concurrency)<br>4. Registrar **CADA hallazgo** en `QA_RESULTS.md` con: ID, Categoría, Severidad, Pasos, Evidencia, Hipótesis causa<br>5. Clasificar: Defecto real / Falso positivo / Mejora test |
| **Responsable Humano** | **QA Lead / Orquestador**: Diseña campaña, prioriza, valida severidad, **firma aprobación release** |
| **Responsable Agente** | **Modo Atacante**: Ejecuta generadores PBT, load tests, SAST/DAST, a11y scans, chaos scripts; Propone ataques basados en código |
| **Artefactos Generados** | `QA_RESULTS.md` actualizado (sección L5-Adversarial), Defectos DEF-XXX, Métricas adversariales |
| **Criterio Salida (GATE DE RELEASE)** | ✅ 8/8 categorías ATK ejecutadas<br>✅ 0 defectos P0 (Críticos) abiertos<br>✅ 0 defectos P1 (Altos) sin plan de fix<br>✅ Métricas registradas<br>✅ **Aprobación Humana (Firma QA Lead)** |

> **REGLA INQUEBRANTABLE SAQI:** Si Fase 7 no pasa → **NO HAY RELEASE**. Iterar Fase 8-10-7 hasta pasar.

**Evidencia Open-RootERP**: **Integrado en E2E** como 4 archivos phase2-5.js (76 tests): phase2-adversarial (44), phase3-stress (10), phase4-security (10), phase5-a11y (12) — 100% pass, detectó 4 P0 + 8 P1 solo en adversarial

---

### FASE 8: DIAGNOSE — Debugging Estructurado

| Atributo | Definición |
|----------|------------|
| **Objetivo** | Determinar causa raíz de cada defecto P0/P1 (y P2 seleccionados) con método científico |
| **Entrada** | Defectos DEF-XXX de Fase 7 (y 5, 6, 10), `QA_RESULTS.md`, Skill `C-debugging` |
| **Actividades** | **Por cada defecto:**<br>1. **Reproducir**: Test caso mínimo que falla (agregar a suite si no existe)<br>2. **Aislar**: Delta debugging / bisección (git bisect, comentar código, reducir input)<br>3. **Hipótesis**: Formular ≥ 2 causas candidatas con predicciones comprobables<br>4. **Evidencia**: Logs estructurados, traces (OpenTelemetry), DB state, network, memory<br>5. **Validar**: Confirmar/refutar cada hipótesis con evidencia<br>6. **Causa Raíz**: Documentar en `QA_RESULTS.md`: Defecto, Hipótesis final, Evidencia, Fix propuesto<br>7. **Clasificar causa raíz**: Código / Arquitectura / Skill / Test / Config / Humano |
| **Responsable Humano** | Validar hipótesis; Aprobar causa raíz; Decidir fix strategy |
| **Responsable Agente** | Ejecutar debugging (logs, traces, bisect); Generar test reproducción; Proponer hipótesis |
| **Artefactos Generados** | Test reproducción (commit), `QA_ROOT_CAUSE_ANALYSIS.md` (por defecto), `QA_RESULTS.md` actualizado |
| **Criterio Salida** | Causa raíz documentada y validada para cada P0/P1; Test reproducción passing (rojo) |

**Evidencia Open-RootERP**: `C-debugging` skill define flujo obligatorio: 1. Encontrar causa → 2. Explicar → 3. Proponer → 4. Implementar → 5. Verificar (tests 0 fallos). Cada fix en `PROJECT_STATE.md` incluye causa raíz

---

### FASE 9: FIX (Corrección)

| Atributo | Definición |
|----------|------------|
| **Objetivo** | Corregir defecto con cambio mínimo, testeable, trazable |
| **Entrada** | Causa raíz validada (Fase 8), Test reproducción (rojo), Skills relevantes |
| **Actividades** | 1. **Cambio Mínimo**: Modificar solo lo necesario para fix causa raíz<br>2. **Test de Regresión**: El test reproducción debe pasar (verde)<br>3. **Test de Prevención**: Agregar test que hubiera detectado el defecto ANTES (unit/integration/functional/adversarial según tipo)<br>4. **Verificar Skills**: ¿El defecto revela gap en Skill? → Registrar para Fase 14<br>5. **Commit**: Mensaje convencional referenciando DEF-XXX<br>6. **Actualizar** `QA_RESULTS.md`: Fix aplicado, commit, tests añadidos |
| **Responsable Humano** | Aprobar fix strategy; Revisar cambio; Validar test prevención |
| **Responsable Agente** | Implementar fix; Generar tests; Ejecutar suite relacionada; Commit |
| **Artefactos Generados** | Fix commit, Tests nuevos/modificados, `QA_RESULTS.md` actualizado |
| **Criterio Salida** | Test reproducción pasa; Test prevención pasa; Suite relacionada 100% pass; Sin lint/type errors |

**Evidencia Open-RootERP**: Fixes atómicos con tests de regresión (ej. `resetDOM()` en runner, transacción atómica inventory, `clearElement()` helpers). 15+ defectos corregidos, 0 re-abiertos reportados.

---

### FASE 10: REGRESSION (Pruebas de Regresión Completa)

| Atributo | Definición |
|----------|------------|
| **Objetivo** | Verificar que fixes no introdujeron regresiones en funcionalidad existente |
| **Entrada** | Código con fixes (Fase 9), Suite completa L1-L5 |
| **Actividades** | 1. Ejecutar **suite completa**: Unit (L1) + Integration (L2) + Functional (L3) + E2E (L4) + Adversarial (L5 - **solo áreas afectadas**)<br>2. Verificar 100% pass<br>3. Si fallo: → Fase 8 (nuevo defecto REG-XXX)<br>4. Métricas: Regression Rate = REG defects / Total fixes |
| **Responsable Humano** | Decidir si fallo es regresión real o test frágil; Aprobar re-ejecución adversarial focalizada |
| **Responsable Agente** | Ejecutar suite completa; Reportar flakiness; Ejecutar adversarial focalizado |
| **Artefactos Generados** | Reporte regresión completo; `QA_RESULTS.md` actualizado (sección Regression) |
| **Criterio Salida** | 100% suite completa pass; Regression Rate < 2%; Adversarial focalizado pass |

**Evidencia Open-RootERP**: Suite completa `npm test && npm run test:e2e` tras cada fix — 100% pass mantenido

---

### FASE 11: VERIFY (Validación de Release Candidate)

| Atributo | Definición |
|----------|------------|
| **Objetivo** | Validación final que el Release Candidate cumple DoD y criterios de calidad |
| **Entrada** | Código post-regresión, `ITERATION_PLAN.md` (DoD), `QA_RESULTS.md` completo, Métricas SAQI-007 |
| **Actividades** | 1. **Checklist DoD** por historia/AC: ¿Todos ACs verificados por tests?<br>2. **Métricas Gate**: Verificar umbrales SAQI-007 (Coverage, Mutation, DDR, Escape Rate proyectado, etc.)<br>3. **Security Gate**: 0 vulnerabilidades críticas/altas sin mitigar (SAST/DAST/SCA)<br>4. **Performance Gate**: Lighthouse budgets cumplidos<br>5. **Accessibility Gate**: 0 critical/serious axe violations<br>6. **Documentation Gate**: Docs usuario/técnicas actualizadas<br>7. **Sign-off**: Humano (PO + Tech Lead + QA Lead) firman `RELEASE_CANDIDATE_APPROVAL.md` |
| **Responsable Humano** | **Decisión final de release**; Firmas de aprobación |
| **Responsable Agente** | Compilar métricas; Generar dashboard release; Verificar gates automatizados |
| **Artefactos Generados** | `RELEASE_CANDIDATE_APPROVAL.md` (firmas), `RELEASE_NOTES.md`, Métricas finales iteración |
| **Criterio Salida** | Todas las gates pasadas; Firmas completas; Release Notes generadas |

**Evidencia Open-RootERP**: Release v1.0.0 tras pasar todas las fases; badges 100% pass en README; ROADMAP.md v1.0.0

---

### FASE 12: DOCUMENT (Documentación de Resultados)

| Atributo | Definición |
|----------|------------|
| **Objetivo** | Documentar completamente la iteración para trazabilidad, auditoría, y aprendizaje |
| **Entrada** | Todos los artefactos de la iteración, `QA_RESULTS.md`, métricas, firmas |
| **Actividades** | 1. Consolidar `ITERATION_REPORT.md`: Resumen, alcance, métricas, defectos, lecciones<br>2. Actualizar `QA_RESULTS.md`: Tendencias, gráficos, defectos por categoría/fase/skill<br>3. Actualizar `ARCHITECTURE.md`: ADRs nuevos, decisiones confirmadas/cambiadas<br>4. Actualizar `CONTEXT.md`: Nuevas reglas negocio, decisiones dominio, glosario<br>5. Generar `CHANGELOG.md` entry (formato Keep a Changelog)<br>6. Archivar reports (coverage, mutation, playwright, lighthouse, zap) en `docs/iterations/ITER-XXX/` |
| **Responsable Humano** | Revisar completitud; Aprobar reporte final |
| **Responsable Agente** | Generar borradores; Compilar métricas; Generar gráficos tendencias; Archivar |
| **Artefactos Generados** | `ITERATION_REPORT.md`, `QA_RESULTS.md` actualizado, `ARCHITECTURE.md`, `CONTEXT.md`, `CHANGELOG.md`, Archivos en `docs/iterations/ITER-XXX/` |
| **Criterio Salida** | Reporte completo aprobado; Documentos vivos actualizados; Artefactos archivados |

**Evidencia Open-RootERP**: `QA_RESULTS.md`, `PROJECT_STATE.md`, `ARCHITECTURE.md`, `Context.md`, `SESSION.md` actualizados; `docs/` con documentación modular

---

### FASE 13: LEARN — Actualización de Contexto (Context Update)

| Atributo | Definición |
|----------|------------|
| **Objetivo** | Sincronizar la **memoria externa** (4 documentos vivos) con la realidad del proyecto |
| **Entrada** | `ITERATION_REPORT.md`, `QA_RESULTS.md`, Defectos, Fixes, Patrones, Decisiones |
| **Actividades** | **Por cada documento vivo:**<br>1. **ARCHITECTURE.md**: ¿Nuevos ADRs? ¿Decisiones cambiadas? ¿Diagramas obsoletos?<br>2. **CONTEXT.md**: ¿Nuevas reglas negocio? ¿Decisiones dominio? ¿Glosario ampliado? ¿Supuestos invalidados?<br>3. **PROJECT_STATE.md**: Estado actual real (qué está done, WIP, bloqueado, próxima iteración)<br>4. **QA_RESULTS.md**: Resumen ejecutivo iteración; Tendencias métricas; Top defect types; Skills gap identificados<br><br>**Protocolo Checkpointing (Skill `A-context-manager`):**<br>- Verificar tamaño contexto estimado para próxima sesión<br>- Si > 70%: Generar resumen ejecutivo + puntos clave para re-hidratación<br>- Guardar checkpoint en `context-checkpoints/ITER-XXX.md` |
| **Responsable Humano** | Validar exactitud; Decidir qué conocimiento es "permanente" vs "transitorio" |
| **Responsable Agente** | Redactar actualizaciones; Generar resúmenes; Detectar inconsistencias entre docs |
| **Artefactos Generados** | 4 documentos vivos actualizados; Checkpoint de contexto |
| **Criterio Salida** | 4 docs reflejan estado real; Checkpoint guardado; Inconsistencias resueltas |

**Evidencia Open-RootERP**: `Context.md` líneas 52-76 "Fixes recientes" documenta 25+ fixes con causa y solución; `PROJECT_STATE.md` estado actual; `ARCHITECTURE.md` actualizado; `QA_RESULTS.md` tendencias

---

### FASE 14: IMPROVE SKILLS (Mejora de Skills — Retroalimentación Cerrada)

| Atributo | Definición |
|----------|------------|
| **Objetivo** | **Materializar el aprendizaje** en las Skills versionadas (ciclo Learn → Skills) |
| **Entrada** | `QA_RESULTS.md` (defectos, patrones, gaps), `ITERATION_REPORT.md`, Skills actuales, Gobernanza Skills (SAQI-003) |
| **Actividades** | **Análisis sistemático:**<br>1. **Defectos → Reglas de Prevención**: Por cada defecto P0/P1: ¿Qué regla en Skill hubiera prevenido?<br>2. **Patrones de Éxito → Plantillas**: ¿Qué patrón funcionó bien? Codificar en Skill dominio.<br>3. **Gaps de Skill → Mejoras**: ¿Skill omitió algo? ¿Regla ambigua? ¿Anti-patrón faltante?<br>4. **Falsos Positivos/Negativos Tests → Criterios Verificación**: Ajustar checklists Skills.<br>5. **Proponer Cambios**: Para cada Skill afectada: Draft vX.Y.Z con cambios, justificación (link DEF-XXX), tipo (patch/minor/major)<br>6. **Validación**: Humano revisa y aprueba cambios<br>7. **Versionado**: SemVer + Changelog + Git tag<br>8. **Testing de Skill**: Ejecutar tests de Skill (contract tests, scenario tests) |
| **Responsable Humano** | **Gobernanza Skills**: Análisis, aprobación, versionado, deprecación |
| **Responsable Agente** | Análisis automatizado: clustering defectos, detección patrones, propuesta drafts cambios |
| **Artefactos Generados** | Skills actualizadas (archivos en `.opencode/skill/`), `SKILL_CHANGELOG.md`, Git tags, PRs Skills |
| **Criterio Salida** | Skills afectadas versionadas; Changelog trazable a defectos/iteración; Tests Skill pasan; Aprobación gobernanza |

**Evidencia Open-RootERP**: **Simulado implícitamente** — `Context.md` (convenciones proyecto) refinado tras fixes: límites 300/40 líneas, patrón transacción atómica, sanitización obligatoria, weighted scoring import, tiebreaker documentId. **NO son Skills versionadas gobernadas SAQI**, son convenciones de proyecto.

---

## 3. Matriz RACI Resumida

| Fase | Humano (Orquestador/Arquitecto/QA Lead/PO) | Agente (Builder/Tester/Adversarial/Analyst) |
|------|---------------------------------------------|---------------------------------------------|
| 1. Plan | **R/A** (Define alcance, AC, DoD) | C (Asiste estimación, dependencias) |
| 2. Architect | **R/A** (ADRs, decisiones) | R (Borra diagramas, valida consistencia) |
| 3. Skill Selection | **A** (Aprueba selección/versiones) | R (Carga, valida conflictos, resume) |
| 4. Build | A (Revisa código crítico) | **R** (Genera código + tests unit) |
| 5. Test L1-L2 | A (Gaps cobertura) | **R** (Ejecuta, reporta, mutation) |
| 6. Test L3-L4 | A (CUJs, UX, performance) | **R** (Ejecuta, reporta, flaky detection) |
| 7. Break | **A** (Diseña campaña, firma gate) | **R** (Modo atacante: ejecuta ataques) |
| 8. Diagnose | A (Valida causa raíz) | **R** (Debug, hipótesis, test reproducción) |
| 9. Fix | A (Aprueba strategy) | **R** (Implementa, tests prevención) |
| 10. Regression | A (Decide regresión vs flaky) | **R** (Ejecuta suite completa) |
| 11. Verify | **R/A** (Sign-off final) | R (Compila métricas, gates auto) |
| 12. Document | A (Aprueba reporte) | **R** (Genera, compila, archiva) |
| 13. Learn (Context) | A (Valida exactitud) | **R** (Redacta, resume, checkpoint) |
| 14. Improve Skills | **R/A** (Gobernanza, aprueba) | R (Análisis, drafts, tests skill) |

**Leyenda:** R = Responsable (hace el trabajo), A = Accountable (decide/aprueba), C = Consultado, I = Informado

---

## 4. Métricas de Proceso por Fase (Para SAQI-007)

| Fase | Métricas Clave |
|------|----------------|
| 1. Plan | Stories planned, ACs defined, Risk count, Estimation accuracy (post) |
| 2. Architect | ADRs created, Decisions changed, Architecture review findings |
| 3. Skill Selection | Skills selected, Versions locked, Conflicts resolved |
| 4. Build | LOC added/modified, Commits, Code review findings, Build time |
| 5. Test L1-L2 | Coverage (L/B/F), Mutation score, Test count, Pass rate, Time |
| 6. Test L3-L4 | CUJs pass rate, A11y violations, Performance metrics, Cross-browser pass |
| 7. Break | ATK categories executed, Defects found (by cat/sev), False positive rate, Time |
| 8. Diagnose | Defects diagnosed, Root cause accuracy, Time to root cause |
| 9. Fix | Fix time, Fix size (LOC), Tests added, Re-open rate |
| 10. Regression | Regression rate, Suite pass time, Flaky test count |
| 11. Verify | Gates passed/failed, Sign-off time, Release readiness score |
| 12. Document | Doc completeness, Archive size, Report quality score |
| 13. Learn | Context staleness index, Checkpoint size, Inconsistencies found |
| 14. Improve Skills | Skills modified, Rules added, Templates added, Version bumps |

---

## 5. Referencias Internas

- SAQI-001: Resumen Ejecutivo (Ciclo general)
- SAQI-003: Gobernanza Skills (Fase 14 detalle)
- SAQI-004: Marco QA (Fases 5, 6, 7 detalle)
- SAQI-007: Marco Métricas (Definiciones métricas)
- SAQI-008: Protocolo Experimental (Validación proceso)
- SAQI-013: Gestión Contexto (Fase 13 detalle)
- SAQI-017: Apéndice A - Arquitectura Open-RootERP (Ejemplo Fases 2, 4)

---

## 6. Referencias Externas

1. ISO/IEC 12207:2017 — Systems and software engineering — Software life cycle processes
2. IEEE 1012-2016 — Standard for System, Software, and Hardware Verification and Validation
3. ISTQB. (2023). *Certified Tester Foundation Level Syllabus*.
4. Humble, J. & Farley, D. (2010). *Continuous Delivery*. Addison-Wesley. (Deployment Pipeline)
5. Beck, K. (2003). *Test-Driven Development: By Example*. Addison-Wesley.
6. Martin, R. C. (2017). *Clean Architecture*. Prentice Hall. (ADR concept)
7. C4 Model (Simon Brown) — Architecture diagrams
8. Keep a Changelog — Changelog format
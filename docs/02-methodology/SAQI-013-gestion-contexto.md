# SAQI-013: Gestión de Contexto Persistente

**Versión:** 1.0
**Fecha:** 2026-07-17
**Estado:** Borrador inicial
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Introducción

Los modelos LLM tienen **ventana de contexto finita** (8K-128K tokens) y **no tienen memoria persistente entre sesiones**. SAQI resuelve esto mediante **memoria externa estructurada**: 4+1 documentos vivos que funcionan como "disco duro" del agente, más un **protocolo de checkpointing** (Skill `A-context-manager`) para gestionar la ventana de contexto activamente.

---

## 2. Los 4+1 Documentos Vivos (Memoria Externa)

| Documento | Propósito | Actualizado en Fases | Estructura Clave |
|-----------|-----------|---------------------|------------------|
| **ARCHITECTURE.md** | Decisiones arquitectónicas, ADRs, diagramas C4, contratos de puertos, threat models | 2 (Architect), 13 (Learn) | ADRs numerados, diagramas Mermaid, tabla puertos |
| **CONTEXT.md** | Reglas de negocio, dominio, decisiones de dominio, stack, convenciones, glosario, fixes recientes | 1 (Plan), 13 (Learn) | Stack, módulos, utils, RBAC, fixes recientes, convenciones Niveles A-D |
| **PROJECT_STATE.md** | Estado actual: tareas done/WIP/bloqueadas, métricas, deuda técnica, riesgos, próximos pasos, archivos clave | Continuo, 13 (Learn) | Tabla módulos, mejoras recientes, pendientes, decisiones, estructura carpetas |
| **QA_RESULTS.md** | Historial QA: defectos por fase/severidad/tipo, fixes, métricas, tendencias, lecciones, skills gaps | 5, 6, 7, 10, 12, 13 | Resumen global, tablas por fase, hallazgos corregidos, tendencias |
| **SESSION.md** (opcional) | Log de sesión actual: comandos, decisiones, hallazgos, próximos pasos inmediatos | Por sesión | Timestamp, acción, archivo, decisión, próximo paso |

---

## 3. Estructura Canónica por Documento

### 3.1 ARCHITECTURE.md

```markdown
# ARCHITECTURE — [Project Name]

**Versión:** X.Y.Z
**Última actualización:** YYYY-MM-DD

---

## 1. Stack Tecnológico
| Capa | Tecnología | Versión |
|------|------------|---------|

## 2. Arquitectura General (Diagrama C4 Nivel 1-2)
```mermaid
graph TD...
```

## 3. Flujo de Datos (Secuencia)
```mermaid
sequenceDiagram...
```

## 4. Reglas Fundamentales
- Regla 1: ...
- Regla 2: ...

## 5. ADRs (Architecture Decision Records)
### ADR-001: [Título] — [Fecha]
**Contexto:** ...
**Decisión:** ...
**Consecuencias:** ...
**Alternativas:** ...
**Estado:** Accepted/Superseded

## 6. Contratos de Puertos (Domain Ports)
| Puerto | Interface | Implementación (Adapter) |
|--------|-----------|-------------------------|

## 7. Modelo de Datos (ER / Schema)
Tablas/colecciones, claves, índices, relaciones.

## 8. Seguridad (Threat Model Summary)
STRIDE por componente, mitigaciones clave.

## 9. Patrones Aprobados / Anti-Patrones
- Patrón: Repository + Dexie
- Anti-patrón: UI → DB directo

## 10. Changelog Arquitectura
- vX.Y.Z: [Cambio] — ADR-XXX
```

### 3.2 CONTEXT.md

```markdown
# CONTEXT — [Project Name]

**Versión:** X.Y.Z
**Última actualización:** YYYY-MM-DD

---

## 1. Stack y Configuración
- Lenguaje: ...
- Runtime: ...
- Dependencias clave: ...

## 2. Módulos Funcionales
| Módulo | Service | Controller | View | Tests |
|--------|---------|------------|------|-------|

## 3. Reglas de Negocio (Domain Rules)
- Regla 1: ...
- Regla 2: ...

## 4. Decisiones de Dominio
| Decisión | Justificación | Fecha |

## 5. Glosario / Ubiquitous Language
| Término | Definición |

## 6. Convenciones Obligatorias (Niveles A-D)
### Nivel A — Obligatorio Siempre
1. Convención 1
2. Convención 2

### Nivel B — Obligatorio Cuando Aplica
...

## 7. Fixes Recientes (Lecciones Aprendidas)
- **Fix**: [Descripción] — **Causa**: [Root cause] — **Prevención**: [Regla/Convención añadida]

## 8. Patrones de Éxito
- Patrón: [Nombre] — [Descripción] — [Dónde aplicar]

## 9. Supuestos y Restricciones
- Supuesto 1: ...
- Restricción 1: ...

## 9. Changelog Contexto
- vX.Y.Z: [Cambio]
```

### 3.3 PROJECT_STATE.md

```markdown
# PROJECT_STATE — [Project Name]

**Versión:** X.Y.Z
**Iteración Actual:** ITER-XXX
**Fecha:** YYYY-MM-DD

---

## 1. Estado de Iteración Actual
| Fase | Estado | Inicio | Fin Estimado | Responsable |
|------|--------|--------|--------------|-------------|

## 2. Tareas
### ✅ Completadas (Esta Iteración)
- [ ] Tarea 1 — Commit: abc123
### 🔄 En Progreso
- [ ] Tarea 2 — Bloqueo: [descripción]
### ⏳ Pendientes (Próximas)
- [ ] Tarea 3

## 3. Métricas Clave (Actualizadas)
| Métrica | Valor | Tendencia |
|---------|-------|-----------|
| Tests Pass Rate | 100% | → |
| Coverage (L/B/F) | 84/79/81 | ↑ |
| Defects Open (P0/P1) | 0/0 | → |

## 4. Deuda Técnica
| Archivo | Issue | Severidad | Plan |
|---------|-------|-----------|------|

## 5. Riesgos y Bloqueos
| Riesgo | Probabilidad | Impacto | Mitigación |

## 6. Próximos Pasos Inmediatos
1. ...
2. ...

## 7. Archivos Clave Modificados Recientemente
| Archivo | Cambio | Commit |
|---------|--------|--------|

## 8. Changelog Estado
- vX.Y.Z: [Actualización]
```

### 3.4 QA_RESULTS.md

```markdown
# QA_RESULTS — [Project Name]

**Versión:** X.Y.Z
**Última actualización:** YYYY-MM-DD

---

## 1. Resumen Ejecutivo por Iteración
| Iteración | Fases Ejecutadas | Tests Totales | Defectos Encontrados | P0 | P1 | P2 | P3 | Estado Release |

## 2. Métricas Agregadas
| Métrica | Valor | Tendencia (últimas 5 iter) |
|---------|-------|---------------------------|
| Defect Escape Rate | N/A (pre-prod) | — |
| Regression Rate | 2.5% | ↓ |
| Adversarial Detection Rate | 27.4% | → |
| MTTF (horas) | 3.2 | ↓ |

## 3. Defectos por Fase y Severidad
| Fase | P0 | P1 | P2 | P3 | Total |
|------|----|----|----|----|-------|
| 5 (Unit/Int) | 0 | 0 | 5 | 20 | 25 |
| 6 (Func/E2E) | 0 | 2 | 10 | 30 | 42 |
| 7 (Adversarial) | 4 | 8 | 15 | 20 | 47 |
| 10 (Regression) | 0 | 0 | 2 | 2 | 4 |

## 4. Defectos por Categoría (Taxonomía)
| Categoría | Count | % | Ejemplos |
|-----------|-------|---|----------|
| LOGIC | 25 | 15.9% | Cálculo incorrecto totales |
| VALID | 18 | 11.5% | Input no validado en puerto |
| SECURITY | 12 | 7.6% | XSS, Prototype pollution |
| CONCURRENCY | 8 | 5.1% | Race condition stock |
| DATA_INTEGRITY | 10 | 6.4% | Corrupción BD, sync parcial |

## 5. Top Defect Types Recurrentes (Tendencia)
| Iteración | Tipo | Count | Acción Correctiva en Skill/Convención |
|-----------|------|-------|---------------------------------------|
| I2-I6 | VALID (input) | 8→1 | Regla Zod obligatoria puertos (Skill A-secure-coding) |

## 6. Hallazgos Corregidos (Log)
### DEF-045 — XSS en búsqueda (P1)
**Fase:** 7 (ATK-SEC) | **Causa:** innerHTML en app.js | **Fix:** clearElement() + textContent | **Prevención:** Regla A-secure-coding #3 | **Skill Actualizada:** A-secure-coding v1.2.0

## 7. Métricas por Skill (Efectividad)
| Skill | Defectos Prevenidos (Est.) | Falsos Positivos | Reglas Añadidas | Versión |

## 8. Changelog QA
- vX.Y.Z: Iteración ITER-XXX completada
```

### 3.5 SESSION.md

```markdown
# SESSION — [Project Name] — YYYY-MM-DD

**Inicio:** HH:MM | **Modelo:** [LLM] | **Iteración:** ITER-XXX | **Fase:** X

---

## Log de Actividad
| Timestamp | Acción | Archivo/Comando | Decisión/Hallazgo | Próximo Paso |
|-----------|--------|-----------------|-------------------|--------------|
| HH:MM:SS | Iniciar Fase 4 | saqi phase run --phase 4 | Iniciar build Product module | Build Service |
| HH:MM:SS | Build Service | fs_write src/services/ProductService.js | Implementado CRUD + validación Zod | Build Controller |
| HH:MM:SS | Test Unit | shell_exec npm test | 12 tests pass, coverage 85% | Fase 5 completa |

## Decisiones Tomadas
- Decisión 1: [Descripción] — Justificación: [Por qué] — Alternativa descartada: [Cuál]

## Hallazgos / Blockers
- Blocker: [Descripción] — Acción: [Qué se hizo] — Estado: Resuelto/Pendiente

## Contexto para Próxima Sesión (Resumen Checkpoint)
- Fase actual: X
- Tarea en curso: [Descripción]
- Archivos modificados: [Lista]
- Próxima acción: [Descripción]
```

---

## 4. Protocolo de Checkpointing (Skill `A-context-manager`)

### 4.1 Monitoreo Proactivo
**Antes de cada llamada al LLM**, estimar tokens:
```
tokens_estimados = (system_prompt_chars + history_chars + tools_schema_chars) / 4
porcentaje = tokens_estimados / context_window_max
```

- **> 70%**: Resumen preventivo (generar resumen últimos intercambios con modelo ligero, no re-leer proyecto)
- **> 80%**: **CHECKPOINT OBLIGATORIO** (protocolo completo 5 pasos)

### 4.2 Protocolo CHECKPOINT (5 Pasos al 80%)

```mermaid
flowchart TD
    A[Contexto > 80%] --> B[PAUSAR: Detener tool loop]
    B --> C[ANUNCIAR: "CHECKPOINT al N%"]
    C --> D[RE-LECTURA PROYECTO: fs_glob, git status, timestamps]
    E[DOCUMENTAR CHECKPOINT: archivo .md con estructura estándar]
    E --> F[RESUMIR: Modelo ligero genera resumen ejecutivo]
    F --> G[INYECTAR: System prompt = Resumen + Próximo paso + "REANUDANDO..."]
    G --> H[CONTINUAR]
```

**Formato Checkpoint** (guardar en `context-checkpoints/ITER-XXX-YYYYMMDD-HHMM.md`):

```markdown
# CHECKPOINT — 2026-07-17T14:32:00Z

## Tarea Actual
Implementando SaleService.createSale() con validación stock atómica

## Progreso
- [x] SaleRepository.create()
- [x] SaleRepository.findById()
- [ ] SaleService.createSale() — EN CURSO
- [ ] SaleController.handleCreate()

## Archivos Modificados en Esta Sesión
- src/services/SaleService.js → Added createSale() with Zod validation
- src/repositories/SaleRepository.js → Added transaction wrapper
- tests/services/SaleService.test.js → 5 new tests

## Estado del Proyecto (Snapshot)
```
src/
├── services/ (15 files)
├── repositories/ (12 files)
└── ...
```
Git status: 3 modified, 1 new

## Contexto Restante
- Tokens usados: 87,000 / 100,000 (87%)
- Resumen últimos intercambios:
  1. Human: "Implementa venta con transacción atómica"
  2. Agent: Created SaleService.createSale with db.transaction
  3. Human: "Agrega validación Zod en puerto entrada"
  4. Agent: Added schema, updated tests

## Próximo Paso
Completar SaleService.createSale() → Run unit tests (Phase 5) → Commit

## Skills Activas
- A-coding-standards v2.1.0
- A-secure-coding v1.2.0
- C-database-design-offline v1.3.0
```

### 4.3 Re-hidratación (Inicio de Sesión)
1. Leer último checkpoint: `context-checkpoints/ITER-XXX-latest.md`
2. Leer 4 documentos vivos (ARCHITECTURE, CONTEXT, PROJECT_STATE, QA_RESULTS)
3. Modelo ligero genera **resumen integrado** (200-500 tokens)
4. Inyectar en system prompt:
```
=== REANUDACIÓN DESDE CHECKPOINT (2026-07-17T14:32:00Z) ===
Proyecto: Open-RootERP
Iteración: ITER-005
Fase: 4 (Build) — Tarea: SaleService.createSale()
Resumen: [resumen integrado 300 tokens]
Próximo paso: Completar SaleService.createSale() → Unit tests
INSTRUCCIÓN: Continuar exactamente desde este punto. NO repetir acciones.
```
5. Confirmar con humano: "Reanudando desde checkpoint. ¿Continuar?"

### 4.4 Checkpoint Manual
Usuario puede forzar en cualquier momento:
```
usuario: "checkpoint" / "guarda progreso"
```
→ Ejecuta protocolo completo (pasos 2-5) sin esperar al 80%.

---

## 5. Políticas de Retención y Limpieza

| Artefacto | Retención | Limpieza |
|-----------|-----------|----------|
| 4 Documentos Vivos | **Permanente** (versionado en git) | Nunca; poda manual si > 10K líneas |
| SESSION.md | **1 sesión** | Sobrescribir cada sesión nueva |
| Checkpoints | **Últimos 50** globales + último por sesión | Auto-borrar > 50 (FIFO) |
| Resúmenes preventivos (70%) | **No persistir** (solo en contexto) | Descartar tras checkpoint o fin sesión |

---

## 6. Métricas de Contexto (Para SAQI-007)

| Métrica | Definición | Objetivo |
|---------|------------|----------|
| **Context Refresh Rate** | Checkpoints / Iteración | 3-6 |
| **Context Staleness Index** | Decisiones contradichas / Sesión | 0 |
| **Checkpoint Trigger Rate** | % checkpoints por >80% vs manual | > 80% auto |
| **Re-hydration Accuracy** | Humano confirma "contexto correcto" post-rehydrate | 100% |
| **Tokens Saved per Checkpoint** | Tokens antes - Tokens tras re-hidratación | > 50% reducción |

---

## 7. Implementación en Open-RootERP (Evidencia Real)

| Documento | Existe | Líneas | Última Act. | Calidad |
|-----------|--------|--------|-------------|---------|
| `ARCHITECTURE.md` | ✅ | 348 | 2026-07-16 | Completo: ADRs, diagramas, schema, patrones |
| `docs/Context.md` | ✅ | 130 | 2026-07-16 | Completo: stack, 14 módulos, utils, RBAC, fixes, convenciones A-D |
| `docs/PROJECT_STATE.md` | ✅ | 380 | 2026-07-16 | Completo: módulos, fixes, deuda, decisiones, roles |
| `docs/QA_RESULTS.md` | ✅ | 198 | 2026-07-16 | Completo: 5 fases, 159 tests, hallazgos, tendencias |
| `SESSION.md` | ✅ | ~50 | 2026-07-16 | Log sesión actual |

**Checkpointing automático**: **NO implementado durante el desarrollo de Open-RootERP**. El checkpointing se realizó de **forma manual por el usuario** actualizando los 4 documentos vivos al final de cada sesión/iteración. El protocolo de checkpointing automático (monitoreo 70%/80%, resumen preventivo, checkpoint obligatorio, re-hidratación) se definió en la skill `A-context-manager` **después de finalizar el proyecto** como lección aprendida. Requiere tooling SAQI-014 (`saqi-context` CLI) para ser ejecutado por agente autónomo.

---

## 8. Referencias Internas

- SAQI-001: Resumen Ejecutivo (Contexto persistente resumen)
- SAQI-003: Gobernanza Skills (Skill `A-context-manager` detalle)
- SAQI-005: Proceso SAQI (Fases 13, 14 usan contexto)
- SAQI-006: Arquitectura Agente (Context Manager componente)
- SAQI-007: Métricas (Context Staleness Index, Refresh Rate)
- SAQI-018: Apéndice B - Plantillas (Estos templates)

---

## 9. Referencias Externas

1. Lewis, P., et al. (2020). "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks." *NeurIPS*.
2. Wang, G., et al. (2023). "Voyager: An Open-Ended Embodied Agent with Large Language Models." *arXiv*. (Skill library persistence)
3. Ge, T., et al. (2023). "Long Context Compression for LLMs." *arXiv*.
4. Zhou, A., et al. (2023). "Agents: An Open-Source Framework for Language Agents." *arXiv*.
5. OpenCode Agent Architecture — Context management implementation reference.
6. Anthropic. (2024). "Building Effective Agents." *Anthropic Engineering Blog* (Context management patterns).
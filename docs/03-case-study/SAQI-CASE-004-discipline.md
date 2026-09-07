# SAQI-CASE-004: Caso de Estudio Discipline (Agenda Inteligente Flask)

**Versión:** 1.0
**Fecha:** 2026-09-07
**Estado:** Observacional (N=4 acumulado) — MVP completado, Fase 2 SAQI planificada
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Introducción y Alcance

Este documento registra el **cuarto caso de estudio observacional** de la metodología **SAQI (Skill-based Agentic Quality Iteration)**: **Discipline**, una aplicación web de planificación y gestión de actividades ("agenda inteligente") construida con **Flask + SQLite + HTML/CSS/JS vanilla**. Es el primer caso con **backend servidor** (framework web Python) dentro de la cartera de casos.

**Particularidad metodológica:** A diferencia de los otros casos, Discipline se encuentra al **inicio** del ciclo: el MVP (Fase 1 del proyecto) está completo y funcional, y su `CHECKPOINT.md` **declara explícitamente "Fase 2 — SAQI"** como siguiente paso (revisión sistemática funcionalidad/arquitectura/usabilidad/calidad/UX/rendimiento/seguridad/escalabilidad/mantenibilidad). Representa, por tanto, un caso de **aplicación prospectiva** de SAQI (se va a aplicar), no retrospectiva.

**Objetivo del caso:** Documentar el estado inicial de un proyecto antes de la aplicación formal de SAQI, sirviendo como **línea de base (baseline) observacional pre-SAQI** comparable con los demás casos tras su Fase 2.

**Principio metodológico:** Separación estricta entre **HECHOS OBSERVADOS**, **INTERPRETACIONES** e **HIPÓTESIS**. **No se afirma causalidad**.

---

## 2. Descripción Real del Proyecto

### 2.1 Visión General

| Atributo | Valor Real (Verificado en Documentación del Proyecto) |
|----------|------------------------------------------------------|
| **Nombre** | Discipline |
| **Tipo** | Aplicación web de planificación y gestión de actividades ("agenda inteligente") |
| **Arquitectura** | Flask app factory + blueprints (routes API REST) + servicios de lógica de negocio + repositorio SQLite; frontend SPA vanilla JS |
| **Stack** | Flask 3.0.2 · SQLite (sqlite3) · HTML/CSS/JS vanilla · unittest |
| **Persistencia** | SQLite normalizada (`instance/discipline.db`) con migraciones incrementales automáticas |
| **Estado** | MVP (Fase 1) completo y funcional; Fase 2 (SAQI) planificada — 2026-08-20 |

### 2.2 Módulos/servicios

| Capa | Componentes | Responsabilidad |
|------|-------------|-----------------|
| `app/routes/` | activities, categories, calendar, stats, reminders, suggestions | API REST (blueprints) |
| `app/services/` | suggestion, conflict, reminder, stats | Lógica de negocio (reglas, detección de solapamiento, recordatorios repetitivos, estadísticas) |
| `app/repository.py` | Acceso a datos | CRUD actividades + consultas |
| `app/database.py` | Esquema + migraciones | Conexión sqlite3, `_migrate` incremental |
| `app/static/` | index.html, style.css, app.js, favicon.svg | SPA (dashboard + calendario + modal) |

---

## 3. Hechos Observados

> Fuentes: `CHECKPOINT.md` (documento de estado del proyecto) y `Prompt.md` (especificación original).

### 3.1 Datos de calidad

| Métrica | Valor Observado |
|---------|-----------------|
| Tests | **26/26 OK** (`python3 -m unittest discover -s tests`) |
| Cobertura funcional | CRUD actividades, estados, vistas día/semana/mes/año, sugerencias por reglas, detección de conflictos, recordatorios, historial, estadísticas |
| Seguridad básica | consultas parametrizadas (SQL injection), validación/sanitización de entradas, escape XSS, secretos vía entorno |
| BD normalizada | `categorias`, `actividades` (FK + CHECK + índices), `historial`, `recordatorios`, `notificaciones`, `configuracion` |

### 3.2 Decisiones y funcionalidades destacadas

| Tema | Decisión / Detalle |
|------|--------------------|
| Frontend sin librerías | JS vanilla, SPA con modal; explícito en `CHECKPOINT.md`: "no agregar funcionalidades no justificadas" |
| Sugerencias por reglas (sin IA) | categoría/duración/prioridad/horario según texto (p. ej. "Estudiar Python" → Estudio, 60 min, 18:00–19:00, media) |
| Detección de conflictos | solapamientos entre actividades activas; aviso en API, badge y advertencia en modal |
| Recordatorios repetitivos | "N min antes" + "repetir cada M min" (0 = una sola vez); `notificaciones` como instancias generadas |
| Migraciones | incrementales automáticas en `app/database.py::_migrate` (p. ej. columna `repetir_cada` + backfill) |
| Notificaciones navegador | `Notification API` (botón) + banner; sondeo cada 20 s; **sin Service Worker en el MVP** (sólo con pestaña abierta) |

### 3.3 Priorización de próximas fases (según CHECKPOINT.md)

| Fase | Contenido |
|------|-----------|
| 2 — SAQI | Revisión sistemática (funcionalidad, arquitectura, usabilidad, calidad, UX, rendimiento, seguridad, escalabilidad, mantenibilidad) y priorización de mejoras |
| 3 — SDD | Documentación formal de arquitectura, modelo de datos, API, decisiones técnicas |
---

## 4. Interpretaciones

1. **Discipline ilustra el punto de entrada canónico a SAQI**: un MVP funcional ya validado (26 tests) que pasa a una revisión sistemática por habilidades (Fase 2 SAQI). Esto convierte el caso en un **baseline pre-SAQI natural**.
2. **La separación de capas (routes/servicios/repositorio) y el testing por `unittest`** permiten aplicar el marco de fases de SAQI-004 (funcional → adversarial) sobre una base ordenada, sin deuda de arquitectura importante.
3. **La seguridad se consideró desde el MVP** (parametrización, sanitización, XSS), alineada con el skill `A-secure-coding` de SAQI; su formalización en la Fase 2 SAQI permitirá verificar cumplimiento sistemático.
4. **La ausencia de Service Worker** (notificaciones sólo con pestaña abierta) es un candidato claro de mejora priorizable en la Fase 2/3, demostrando el valor de la revisión funcional/UX de SAQI.

---

## 5. Hipótesis Comprobables

| ID | Hipótesis | Método de Validación |
|----|-----------|----------------------|
| D-H1 | Aplicar la Fase 2 SAQI sobre el MVP mejora la cobertura y detección de defectos adversariales respecto al estado pre-SAQI | Comparar `QA_RESULTS` pre/post Fase 2 en el mismo proyecto |
| D-H2 | Un MVP con capas desacopladas y tests unitarios reduce el esfuerzo de la revisión SAQI frente a un MVP con lógica acoplada | Comparar con un caso contrafáctico controlado por complejidad |
| D-H3 | La traza XSS/SQLi ya presente en el MVP se mantiene (0 regresiones) tras la formalización SAQI | Tests de regresión adversarial tras Fase 2 |

---

## 6. Lecciones para la Metodología SAQI

1. **SAQI no exige empezar el proyecto desde cero**: una fase "MVP previo" seguida de la entrada en el ciclo de 14 fases es un patrón de adopción válido y de bajo fricción.
2. **Un `CHECKPOINT.md` como documento único de estado** es un precursor ligero de los 4+1 documentos SAQI; la Fase 2 debería migrar a `ARCHITECTURE/CONTEXT/PROJECT_STATE/QA_RESULTS` siguiendo SAQI-013.
3. **El backend servidor (Flask) amplía la cartera de stacks** y permite comparar métricas de fases L1–L4 (unit → E2E) en un stack con servidor, frente a los casos client-only.

---

## 7. Limitaciones

- Es una **línea de base pre-SAQI** (Fase 2 aún no ejecutada al momento de documentar); los efectos de SAQI son prospectivos, no observados.
- Un solo desarrollador; métricas de coste/tokens no instrumentadas.
- Alcance de funcionalidad limitado al MVP (sin Service Worker, sin paginación avanzada, sin push).

---

## 8. Referencias Internas SAQI

- SAQI-001: Resumen Ejecutivo
- SAQI-004: Marco de QA (funcional → adversarial)
- SAQI-005: Proceso (entrada al ciclo de 14 fases)
- SAQI-013: Gestión de Contexto (migración de CHECKPOINT a 4+1 docs)
- SAQI-018: Apéndice B - Plantillas de Agente
| 4 — QA | Pruebas unitarias/integración/API/frontend, casos límite, compatibilidad de navegadores, flujos completos |
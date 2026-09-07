# SAQI-CASE-005: Caso de Estudio Flight Mainframe Simulator (FMS)

**Versión:** 1.0
**Fecha:** 2026-09-07
**Estado:** Observacional (N=5 acumulado) — proyecto en fase temprana, arquitectura definida
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Introducción y Alcance

Este documento registra el **quinto caso de estudio observacional** de la metodología **SAQI (Skill-based Agentic Quality Iteration)**: el **Flight Mainframe Simulator (FMS)**, un entorno educativo de escritorio que reproduce el flujo de trabajo de un desarrollador COBOL en un banco (edición, compilación, JCL, VSAM, TSO/ISPF, DB2 simulado). Es el caso de **mayor heterogeneidad técnica** de la cartera: **React/Tauri (frontend) + Rust (backend multi-crate) + GnuCOBOL + PostgreSQL/Docker**.

**Particularidad metodológica:** FMS es el único caso con **Clean Architecture + DDD + patrón MCP (Model Context Protocol)** con *bounded contexts* como crates separadas (`fms-core`, `fms-contexts/*`, `fms-plugins`, `fms-frontend`, `fms-cli`) y **convenciones de desarrollo formalizadas en `AGENTS.md`** (estilo Rust, reglas DDD, testing, git). Aunque aún no declara "aplicar SAQI" explícitamente, sus convenciones son altamente **alineadas con los principios SAQI** (Skills/comportamientos gobernados, arquitectura por capas, testing por niveles).

**Objetivo del caso:** Documentar cómo una arquitectura dirigida por convenciones (AGENTS.md) y por dominios (DDD/MCP) puede mapearse al ciclo SAQI, y qué lecciones aporta su stack complejo (compilador real, simulador de DB2, Rust) para la validación externa de SAQI.

**Principio metodológico:** Separación estricta entre **HECHOS OBSERVADOS**, **INTERPRETACIONES** e **HIPÓTESIS**. **No se afirma causalidad**.

---

## 2. Descripción Real del Proyecto

### 2.1 Visión General

| Atributo | Valor Real (Verificado en Documentación del Proyecto) |
|----------|------------------------------------------------------|
| **Nombre** | Flight Mainframe Simulator (FMS) |
| **Tipo** | Entorno educativo de escritorio (simulador de mainframe) multiplataforma |
| **Arquitectura** | Clean Architecture + DDD + MCP; bounded contexts como crates Rust; Runtime descubre plugins vía Traits; comunicación async por EventBus (tokio broadcast) |
| **Stack** | Frontend React + TS + Tailwind · Desktop Tauri · Backend Rust (workspace resolver 3, edition 2024) · GnuCOBOL 4.0 (Docker) · PostgreSQL 17 (DB2 sim.) · SQLite (config) |
| **Estado** | En fase temprana (0.1.0); arquitectura y convenciones definidas |

### 2.2 Estructura del workspace Rust

| Crate | Rol |
|-------|-----|
| `fms-core` | Runtime + Traits (contratos entre contextos) + capas domain/application/infrastructure |
| `fms-contexts/` | terminal, editor, compiler, dataset (+ VSAM/CICS/JCL/batch/scheduler en roadmap) — cada uno su propio bounded context |
| `fms-plugins/` | banking-scenarios, exercises (plugins de contenido) |
| `fms-frontend` | App Tauri + React (3270-style terminal) |
| `fms-cli` | Interfaz de línea de comandos |
| `datasets/` | COBOL, COPY, JCL, VSAM (datasets de ejemplo) |

### 2.3 Convenciones de desarrollo (AGENTS.md)

| Área | Regla |
|------|-------|
| Idioma | Documentación en español; identificadores de código en inglés |
| Estilo Rust | edition 2024, `thiserror` (dominio), `anyhow` (glue), async-first con `tokio`/`tracing`, **sin `unwrap()` en producción**, `clippy` sin warnings, `rustfmt` |
| DDD | Domain **cero dependencias**; Application sólo depende de Domain; Infrastructure depende de ambos; cada bounded context es una crate separada |
| MCP | Cada contexto expone Traits en `fms-core/src/traits/`; sin imports crate-a-crate (siempre vía Runtime o EventBus) |
| Testing | Unit (`cargo test --lib`), property (`proptest`), integration (`cargo test --test integration`), E2E (Tauri harness, Fase 2+) |
---

## 3. Hechos Observados

> Fuentes: `README.md`, `AGENTS.md`, `Prompt_inicial.md`, `Cargo.toml` (workspace), estructura de `fms-*`.

### 3.1 Hechos verificados

| Hecho | Observación |
|-------|-------------|
| Arquitectura por bounded contexts | crates separadas `fms-core` + `fms-contexts/{terminal,editor,compiler,dataset}` + `fms-plugins` |
| Comunicación desacoplada | EventBus async (tokio broadcast) + Traits; prohibición de imports directos crate-a-crate |
| Convenciones de calidad | `clippy` sin warnings, sin `unwrap()`, `rustfmt`, pruebas por niveles (unit/property/integration/E2E) |
| Git convencional | ramas por contexto (`feature/<context>/<desc>`), conventional commits |
| Stack externo complejo | GnuCOBOL (Docker) para compilación auténtica; PostgreSQL 17 como simulador de DB2; SQLite para config |

### 3.2 Alineación con principios SAQI

| Principio SAQI | Manifestación en FMS |
|----------------|----------------------|
| Conocimiento gobernado (Skills) | `AGENTS.md` codifica reglas/estilo/arquitectura como convenciones obligatorias (primer nivel de Skills) |
| Arquitectura deliberada | Clean Architecture + DDD + MCP documentada en `README.md` y `AGENTS.md` |
| Testing por niveles | pirámide explícita: unit → property → integration → E2E (alineada con SAQI-004 L1–L4) |
| Contexto persistente | `AGENTS.md` + `CONTEXT` previsto; sin `ARCHITECTURE/CONTEXT/PROJECT_STATE/QA_RESULTS` SAQI aún |

---

## 4. Interpretaciones

1. **FMS es el caso más adecuado para probar el salto de "convenciones" a "Skills SAQI gobernadas"**: su `AGENTS.md` es funcionalmente comparable a un conjunto de Skills SAQI (reglas, procedimientos, arquitectura), pero **sin el ciclo versionado/verificable** de SAQI-003. Es un laboratorio natural para la transición.
2. **El DDD con bounded contexts como crates** es directamente compatible con el skill `A-project-architecture` (puertos/adaptadores, contratos, DI), aportando un patrón de grano grueso no cubierto en los demás casos.
3. **El stack heterogéneo (Rust + COBOL + DB2 + Tauri)** endurece la validación externa de SAQI: si las fases/metodología funcionan aquí, se fortalece la generalizabilidad (SAQI-009 validez externa).
4. **La ausencia de docs SAQI 4+1 y gates formales** en un proyecto tan bien convencionalizado sugiere que el *gap* de SAQI está más en el **cierre de lazo (medir/verificar/retroalimentar)** que en la definición de arquitectura convencional.

---

## 5. Hipótesis Comprobables

| ID | Hipótesis | Método de Validación |
|----|-----------|----------------------|
| F-H1 | Migrar `AGENTS.md` a Skills SAQI gobernadas reduce reintroducción de reglas en un proyecto multi-crate | Comparar incidencias de estilo/arquitectura pre/post gobernanza |
| F-H2 | El QA adversarial (Fase 7) sobre el compilador/simulador detecta defectos no cubiertos por unit/integration | Ejecutar campaña ATK-* sobre pipeline de compilación y datasets |
| F-H3 | La aplicación del ciclo SAQI a proyectos Rust/COBOL requiere adaptaciones de métricas (tiempos de compilación, deuda en crates) | Mapear SAQI-007 a crates; medir cobertura/regresión por crate |

---

## 6. Lecciones para la Metodología SAQI

1. **Añadir un patrón/referencia de "Convenciones de proyecto (AGENTS.md) como semilla de Skills"** en SAQI-003/SAQI-018: formalizar la transición de convenciones → Skills versionadas.
2. **SAQI-006/SAQI-017 deberían incluir un ejemplo de DDD + bounded contexts** (multi-crate) además del MVC/puertos-adaptadores monolítico.
3. **Para stacks con compilación externa (COBOL) y simuladores (DB2), SAQI-008/SAQI-014** deberían contemplar entorno controlado por contenedores (Docker) como práctica de reproducibilidad.
4. **El "cierre de lazo" (medir/verificar/retroalimentar) es el valor diferencial SAQI** frente a una arquitectura bien convencionalizada pero sin gates formales.

---

## 7. Limitaciones

- Proyecto en fase temprana (0.1.0): aún no hay QA_RESULTS, métricas ni gates formales que documentar.
- Sin reclamación de que "SAQI" se haya aplicado formalmente; se documenta la **alineación** con principios.
- Un solo desarrollador; métricas de coste/tokens no disponibles.
- Complejidad de toolchain (Rust + COBOL + Docker) limita comparabilidad directa de tiempos con los casos ligeros.

---

## 8. Referencias Internas SAQI

- SAQI-001: Resumen Ejecutivo
- SAQI-003: Caso de Estudio original (convenciones → Skills)
- SAQI-005: Proceso (14 fases)
- SAQI-006/SAQI-017: Arquitectura de Agente / Apéndice arquitectura (DDD/multi-crate propuesto)
- SAQI-007: Marco de Métricas (adaptación a crates)
- SAQI-008/SAQI-014: Protocolo experimental / contingentes (entorno Docker)
- SAQI-018: Apéndice B - Plantillas de Agente (convenciones como Skills)
| Git | `main` estable; ramas `feature/<context>/<desc>`; conventional commits con resumen en español |
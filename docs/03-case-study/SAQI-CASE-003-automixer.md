# SAQI-CASE-003: Caso de Estudio AutoMixer (App de Escritorio de Mezcla de Audio)

**Versión:** 1.0
**Fecha:** 2026-09-07
**Estado:** Observacional (N=3 acumulado) — proyecto con checkpoint SAQI explícito
**Autor:** Adónis Adonai Gómez Martínez

---

## 1. Introducción y Alcance

Este documento registra el **tercer caso de estudio observacional** de la metodología **SAQI (Skill-based Agentic Quality Iteration)**: el desarrollo de **AutoMixer**, una aplicación de escritorio local (Python/PySide6) para mezclar audio de una canción por proyecto. Representa un **nuevo tipo de artefacto** (aplicación de escritorio con GUI nativa y procesamiento de audio real con FFmpeg), distinto del ERP vanilla JS (caso original) y del SaaS web TS (zeroQA, SAQI-CASE-002).

**Particularidad metodológica:** AutoMixer implementa un **checkpoint SAQI explícito** en `LIVE_CONTEXT.md` con el patrón **Save / Acquire / Question / Inspire**, mostrando una **interpretación alternativa del acrónimo SAQI** aplicada a la gestión de contexto.

**Objetivo del caso:** Documentar la aplicación de los principios SAQI (specs con ACs Given/When/Then, fases cerradas por pruebas, decisión de arquitectura por puertos/adaptadores) en un dominio de procesamiento de audio con componentes reales (FFmpeg) y de simulación (fakes).

**Principio metodológico:** Separación estricta entre **HECHOS OBSERVADOS**, **INTERPRETACIONES** e **HIPÓTESIS**. **No se afirma causalidad**.

---

## 2. Descripción Real del Proyecto

### 2.1 Visión General

| Atributo | Valor Real (Verificado en Documentación del Proyecto) |
|----------|------------------------------------------------------|
| **Nombre** | AutoMixer |
| **Tipo** | Aplicación de escritorio local de mezcla de audio (una canción por proyecto) |
| **Arquitectura** | Monolito: View → Controller → Service → Repository/Port → Adapter; puertos para audio/playback/artifacts; DI manual en `app.py` |
| **Stack** | Python · PySide6 (Qt) · FFmpeg · PyInstaller · unittest |
| **Persistencia** | Proyecto local `.automixer` (esquema v1) + preferencias |
| **Estado** | MVP completo (Fases 1–4, 76/76 pruebas OK) — 2026-09-01 |

### 2.2 Fases implementadas

| Fase | Alcance | Specs | Puertas | Resultado |
|------|---------|-------|---------|-----------|
| 1 — Base | modelos, persistencia, preferencias, worker cancelable, esqueleto UI | SPEC001, SPEC008, SPEC009 | 16 tests | ✅ |
| 2 — Dominio de mezcla | import/validación stems, perfiles D1, ganancia, sincronización/alineación con umbrales D2 | SPEC002, SPEC003, SPEC004 | 39 tests | ✅ |
| 3 — Audio real | `FFmpegEngine`, render temporal, preview Qt, exportación WAV/MP3 | SPEC005, SPEC006, SPEC007 | 62 tests | ✅ |
| 4 — Empaquetado e i18n | `I18nService` (es/en), PyInstaller `--onedir`, gate de release licencias | SPEC010 + licencias | 76 tests | ✅ |

---

## 3. Hechos Observados

> Fuentes: `CONTEXT.md`, `PROJECT_STATE.md`, `QA_RESULTS.md`, `ARCHITECTURE.md`, `LIVE_CONTEXT.md`, `docs/specs/`, `docs/tests/`, `docs/design/UI_DECISIONS.md`.

### 3.1 Datos de calidad

| Métrica | Valor Observado |
|---------|-----------------|
| Specs funcionales | **10** (SPEC001–SPEC010) con ACs Given/When/Then |
| Casos de prueba funcionales documentados | **68** (TEST001–TEST010) en `docs/tests/` |
| Suite ejecutable | **76/76 OK** (`unittest discover -s tests`) |
| Decisiones de dominio | **D1–D6** (`docs/specs/DECISIONS.md`) |
| Decisiones de UI | **U1–U8** (`docs/design/UI_DECISIONS.md`) |
| Documentos vivos | `CONTEXT.md`, `PROJECT_STATE.md`, `QA_RESULTS.md`, `ARCHITECTURE.md` + `LIVE_CONTEXT.md` (checkpoint) |

### 3.2 Decisiones de dominio clave (D1–D4)

| ID | Decisión | Detalle técnico |
|----|----------|----------------|
| D1 | Perfiles JSON por género×tipo | `resources/profiles.json` + `StemProfile` (gain, paneo ±X, HPF, EQ ≤2 bandas, compresor, makeup); normalización LUFS/TP |
| D2 | Umbrales de sincronización/alineación | tempo `conf ≥ 0.80` + `ratio ∈ [0.75, 1.33]`; alineación `conf ≥ 0.85` + `|desfase| ≤ 2000 ms`; bajo umbral → advertir y no alterar |
| D3 | Categorías de fallo de stems | `stem.decode_failed`, `stem.gain_out_of_range`, `sync.low_confidence_*` |
| D4 | Correcciones conservables | `tempo_ratio`, `start_offset_ms`, `confidence`, `manual_offset_ms` (el cambio de género conserva correcciones) |
|----|----------|----------------|
### 3.3 Patrón de puertos/adaptadores y fakes

| Dominio incierto | Puerto | Implementación real | Fake para tests |
|------------------|--------|---------------------|------------------|
| Decodificación | `AudioEngine.is_decodable` | `FFmpegEngine` (Fase 3) | `FakeAudioEngine` (Fase 2) |
| Render temporal | `ArtifactStore` | `TempArtifactStore` | — |
| Playback | `Playback` | `QtPlayback` (QtMultimedia) | `FakePlayback` |

- **Lección de testing:** el adapter FFmpeg real se introdujo en **Fase 3**, mientras que las Fases 1–2 se validaron con fakes inyectables. Este enfoque permitió avanzar el dominio de mezcla sin depender de binarios externos.

### 3.4 Gate de release (licencias) y empaquetado

- `scripts/build.py`: empaqueta con PyInstaller `--onedir` y copia FFmpeg a `resources/`.
- `scripts/release_check.py`: **gate de release** que rechaza el paquete sin `LICENSES.md`, avisos/códecs (LAME) o FFmpeg (AC-06/AC-07).
- `LICENSES.md`: avisos de FFmpeg, LAME, PySide6 y Python.

---

## 4. Interpretaciones

1. **La estrategia de fakes primero / adaptador real después** facilitó el desarrollo de calidad del dominio de audio sin acoplar a FFmpeg, validando el patrón de puertos/adaptadores de SAQI (SAQI-006/SAQI-017) en un contexto de backend de audio.
2. **Los umbrales numéricos explícitos (D2) son trazables a ACs concretos**, permitiendo que los tests funcionales documentados (`docs/tests/TEST*.md`) sean verificables sin análisis subjetivo.
3. **El gate de release de licencias (AC-06/AC-07)** es un ejemplo de criterio de aceptación **no funcional** que protege contra riesgo legal (licencias FFmpeg/códec), algo que el marco de métricas SAQI-007 no cubre explícitamente → lección para la metodología.
4. **AutoMixer reinterpreta el acrónimo SAQI como Save/Acquire/Question/Inspire** en `LIVE_CONTEXT.md`, ampliando la definición del glosario SAQI-015. Esto sugiere que el acrónimo admite lecturas complementarias orientadas al contexto (no sólo a la iteración por fases).

---

## 5. Hipótesis Comprobables

| ID | Hipótesis | Método de Validación |
|----|-----------|----------------------|
| A-H1 | La estrategia "fakes primero, adapter real después" reduce defectos de integración con componentes externos (FFmpeg) | Comparar tasa de fallo de integración con un desarrollo directo contra FFmpeg |
| A-H2 | Los thresholds numéricos explícitos (D2) mejoran la fiabilidad percibida de la sincronización | A/B con umbrales por defecto vs configurables, midiendo errores de alineación |
| A-H3 | Un gate de release no técnico (licencias) previene fallos de entrega que tests técnicos no detectan | Auditoría de releases sin vs con gate en una muestra de proyectos |

---

## 6. Lecciones para la Metodología SAQI

1. **Ampliar SAQI-007** con una categoría de **métricas de cumplimiento legal/empaquetado** (licencias, bundling, firmado) además de las técnicas.
2. **Reconocer el patrón de puertos/adaptadores con fakes como práctica recomendada SAQI** para dominios con dependencias externas costosas.
3. **Considerar formalizar la lectura Save/Acquire/Question/Inspire** del acrónimo (o distinguir "SAQI proceso" de "SAQI contexto") en SAQI-015.
4. **Los umbrales numéricos explícitos** deberían promoverse como patrón estándar en las plantillas de specs (SAQI-018) por su trazabilidad a tests.

---

## 7. Limitaciones

- Instancia observacional adicional (N=3 acumulado), **no** experimental.
- Un solo desarrollador; métricas de coste/tokens no instrumentadas.
- El procesamiento de audio con datos reales se valida sólo con WAV/MP3 sintéticos en Fase 3.
- Difiere del resto de casos en ser **aplicación de escritorio**, lo que reduce la comparabilidad directa de algunas métricas de proceso.

---

## 8. Referencias Internas SAQI

- SAQI-001: Resumen Ejecutivo
- SAQI-005: Proceso (fases por módulo, gates)
- SAQI-006: Arquitectura de Agente (puertos/adaptadores)
- SAQI-007: Marco de Métricas (categoría licencias propuesta)
- SAQI-015: Glosario (lectura Save/Acquire/Question/Inspire)
- SAQI-017: Apéndice A - Arquitectura (patrón de puertos/adaptadores)
- SAQI-018: Apéndice B - Plantillas de Agente (umbrales en specs)
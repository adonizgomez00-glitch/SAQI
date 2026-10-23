# Tarea: Evolucionar SAQI hacia un desarrollo incremental, adaptativo y eficiente con agentes de IA

## 1. Contexto y objetivo

Trabaja directamente sobre el repositorio existente de SAQI (Skill-based Agentic Quality Iteration).

El objetivo es evolucionar SAQI para combinar:

- El enfoque MVP-First y de desarrollo incremental.
- Vertical Slices e historias de usuario pequeñas.
- AI Pair Programming con supervisión humana.
- ODD (Outcome-Driven Development): resultados verificables.
- SDD ligero: especificaciones proporcionales a la complejidad.
- TDD selectivo: pruebas primero cuando aporten valor.
- Harness Engineering: verificación automatizada y controles objetivos.
- QA adversarial, análisis de causa raíz y aprendizaje mediante Skills.
- Optimización medible del consumo de tokens, tiempo y calidad.

No reemplaces SAQI por una metodología diferente. Evoluciona su diseño actual, conservando sus componentes valiosos y su compatibilidad siempre que sea razonable.

## 2. Reglas obligatorias de trabajo

Antes de modificar archivos:

1. Inspecciona el repositorio completo y determina su estructura real.
2. Lee el README, la metodología vigente, las 14 fases, las políticas MVP-First, las Skills, los documentos de métricas, los protocolos experimentales y las pruebas existentes.
3. Identifica contradicciones, redundancias, requisitos obsoletos, costes innecesarios y mejoras pendientes.
4. Comprueba si las funciones que propones ya existen. No dupliques mecanismos existentes.
5. Elabora un plan breve de cambios, indicando los archivos afectados, el motivo y los criterios de aceptación.

Después, implementa las mejoras de forma incremental. No te limites a generar una propuesta o documentación: realiza los cambios necesarios en el repositorio, ejecuta las comprobaciones disponibles y entrega un resumen verificable.

No inventes resultados de pruebas, métricas, archivos, comandos ni capacidades del sistema.

## 3. Principio rector: desarrollo incremental por resultados

Establece como flujo predeterminado el desarrollo mediante historias pequeñas y funcionalidades verticales completas.

Cada historia debe:

- Tener un resultado útil y verificable.
- Tener criterios de aceptación claros.
- Limitarse al alcance mínimo necesario.
- Modificar únicamente los componentes necesarios.
- Producir una funcionalidad demostrable cuando sea posible.
- Incluir las pruebas adecuadas al riesgo.
- Poder revisarse y revertirse independientemente.

Una vertical slice debe atravesar las capas necesarias para entregar valor real. No obligues al agente a terminar toda la base de datos, todas las API o todo el frontend antes de conseguir una funcionalidad operativa.

Las tareas puramente técnicas, como migraciones, refactorizaciones o cambios de infraestructura, pueden seguir otro flujo cuando una vertical slice no sea apropiada. Exige siempre un resultado verificable.

## 4. Crear un modo adaptativo de ejecución

Diseña una política explícita para seleccionar la profundidad del proceso según el tamaño, la complejidad y el riesgo de la tarea.

### Modo ligero

Para correcciones pequeñas, cambios locales, scripts sencillos y tareas de bajo riesgo:

1. Comprender el comportamiento esperado.
2. Inspeccionar el código relevante.
3. Implementar el cambio mínimo.
4. Ejecutar las pruebas pertinentes.
5. Revisar el diff.
6. Registrar el resultado.

No generes planes extensos, diagramas, ADR ni especificaciones independientes si no son necesarios.

### Modo estándar

Para funcionalidades que afectan varios archivos o componentes:

1. Definir el resultado y los criterios de aceptación.
2. Crear una historia pequeña y un plan breve.
3. Identificar dependencias y contratos relevantes.
4. Implementar una vertical slice.
5. Ejecutar pruebas unitarias, de integración o E2E según corresponda.
6. Revisar cambios y posibles regresiones.
7. Consolidar el resultado y actualizar el estado del proyecto.

### Modo riguroso

Para autenticación, autorización, seguridad, datos sensibles, concurrencia, cambios de esquema con riesgo, arquitectura crítica y versiones previas a publicación:

1. Aplicar especificaciones y contratos explícitos.
2. Realizar análisis de impacto y amenazas cuando corresponda.
3. Establecer pruebas de aceptación y regresión.
4. Aplicar TDD a las reglas críticas cuando resulte adecuado.
5. Ejecutar análisis estático y verificaciones automatizadas.
6. Realizar QA adversarial proporcional al riesgo.
7. Revisar migraciones, recuperación, compatibilidad y rollback.
8. Documentar riesgos residuales y resultados.

Define criterios concretos para elegir el modo. Permite elevar el nivel cuando aparezcan nuevos riesgos, pero no rebajarlo automáticamente si existen requisitos de seguridad o cumplimiento obligatorios.

## 5. Integrar ODD y SDD ligero

Establece el resultado verificable como punto de partida.

Para cada historia, registra únicamente:

- Resultado esperado.
- Criterios de aceptación.
- Restricciones relevantes.
- Archivos o módulos afectados, cuando se conozcan.
- Pruebas que permitirán verificar el resultado.
- Riesgos importantes y dependencias.

Mantén la especificación general del producto separada del detalle de implementación de cada historia.

No diseñes toda la arquitectura por anticipado. Conserva desde el inicio las decisiones estructurales que sean realmente necesarias, como límites de seguridad, contratos públicos, integridad de datos y restricciones tecnológicas.

Permite que la arquitectura evolucione con evidencia real, pero documenta las decisiones arquitectónicas importantes cuando afecten a futuras tareas.

## 6. Controlar la sobreingeniería

Añade reglas explícitas para impedir que los agentes:

- Introduzcan abstracciones sin una necesidad demostrable.
- Creen interfaces, fábricas, capas o patrones innecesarios.
- Implementen funcionalidades que no estén solicitadas.
- Refactoricen módulos no relacionados con la tarea.
- Reemplacen tecnologías existentes sin justificación.
- Reescriban archivos completos para cambios localizados.
- Añadan dependencias cuando una solución sencilla con las herramientas existentes sea suficiente.

Antes de introducir una abstracción o dependencia, el agente debe justificar brevemente qué problema actual resuelve y por qué la solución más sencilla no basta.

Prioriza el código claro, mantenible y fácil de verificar sobre la sofisticación arquitectónica.

## 7. Adaptar TDD y las pruebas al riesgo

No obligues a aplicar TDD de manera mecánica a cada cambio.

Prioriza pruebas primero para:

- Reglas de negocio críticas.
- Cálculos y transformaciones de datos.
- Validaciones.
- Permisos y controles de acceso.
- Correcciones de errores reproducibles.
- Algoritmos con casos límite relevantes.

Para otras tareas, permite implementar primero una pequeña porción y añadir las pruebas apropiadas.

Distingue entre:

- Pruebas unitarias.
- Pruebas de integración.
- Pruebas de aceptación.
- Pruebas E2E.
- Pruebas de regresión.
- Pruebas de seguridad.
- Pruebas adversariales.

Evita duplicar cobertura innecesariamente. Una prueba debe comprobar un comportamiento o riesgo identificable, no existir únicamente para aumentar el número de tests.

Nunca permitas que el agente modifique las pruebas para hacerlas pasar ocultando un defecto real. Los criterios de aceptación deben ser independientes de la implementación.

## 8. Reestructurar el QA de SAQI

Conserva QA convencional, Break → Fix → Regression, análisis de causa raíz y aprendizaje mediante Skills.

Separa dos ciclos:

### Ciclo rápido por historia

- Verificación funcional.
- Pruebas pertinentes.
- Comprobaciones de tipos y análisis estático disponibles.
- Revisión del diff.
- Confirmación de criterios de aceptación.

### Ciclo profundo por riesgo o hito

- QA adversarial.
- Seguridad.
- Concurrencia y estrés cuando proceda.
- Accesibilidad cuando exista interfaz de usuario.
- Regresión ampliada.
- Validación de migraciones.
- Comprobaciones de despliegue, recuperación y rollback cuando correspondan.

Define qué eventos activan el ciclo profundo: riesgo elevado, cambios sensibles, defectos recurrentes, cambios arquitectónicos y preparación de una versión.

No afirmes que una aplicación es segura únicamente porque pasan las pruebas disponibles.

## 9. Mejorar el aprendizaje de Skills

Mantén el sistema de Skills versionadas, pero evita que cada error genere automáticamente una nueva regla permanente.

Establece este flujo:

1. Detectar y reproducir el defecto.
2. Identificar su causa raíz.
3. Determinar si es un error aislado o un patrón recurrente.
4. Evaluar si puede prevenirse mediante una Skill, una prueba automatizada, una regla de lint, un contrato o una mejora de herramientas.
5. Aplicar el mecanismo más sencillo y eficaz.
6. Validar que el cambio no contradiga otras reglas.
7. Versionar el aprendizaje cuando corresponda.
8. Registrar su impacto en futuras tareas.

Prioriza las prevenciones ejecutables y comprobables sobre las instrucciones escritas que el agente podría ignorar.

Mantén las Skills modulares, relevantes y sin contradicciones. Evita cargar todas las Skills en todas las tareas: selecciona únicamente las aplicables.

## 10. Optimización del consumo de tokens

Añade al método una estrategia explícita de eficiencia:

- Recuperar solo los archivos y documentos relevantes.
- Reutilizar las instrucciones estables del repositorio.
- Evitar volver a explicar el contexto ya documentado.
- Mantener resúmenes de estado breves y actualizados.
- Limitar las tareas al cambio necesario.
- Reducir la salida de comandos y pruebas a los resultados relevantes.
- Evitar ciclos de corrección sin límite.
- Establecer presupuestos configurables de intentos y tokens cuando las herramientas permitan medirlos.
- Detenerse y diagnosticar cuando se repitan errores sin progreso.
- No generar documentación duplicada ni planes extensos para tareas pequeñas.

No confundas menos tokens con mayor eficiencia. Una solución que consume menos tokens pero introduce defectos puede ser más costosa en conjunto.

Si el agente no puede medir directamente el consumo real de tokens, debe declarar esa limitación y no inventar estimaciones presentadas como mediciones.

## 11. Harness Engineering y control de ejecución

Comprueba qué mecanismos de validación ya tiene SAQI y mejora los que sean necesarios.

Siempre que el proyecto lo permita, utiliza comandos reproducibles para:

- Ejecutar pruebas.
- Comprobar tipos.
- Ejecutar lint y análisis estático.
- Verificar compilación o construcción.
- Detectar cambios inesperados.
- Comprobar contratos.
- Registrar resultados y fallos.

Define criterios de salida para los ciclos autónomos. Por ejemplo, un límite configurable de intentos fallidos consecutivos, ausencia de progreso o agotamiento del presupuesto.

Si se alcanza un límite, el agente debe resumir qué intentó, qué falló, qué evidencia obtuvo y qué necesita para continuar.

No declares una tarea completada si los criterios de aceptación no están satisfechos. Si alguna comprobación no pudo ejecutarse, informa explícitamente de la limitación.

## 12. Contexto persistente sin burocracia

Audita los documentos actuales de estado, contexto y QA antes de crear otros.

Evita duplicar información entre archivos. Define una única fuente de verdad para cada clase de información.

El contexto persistente debe priorizar:

- Arquitectura real y restricciones.
- Comandos para ejecutar y probar el proyecto.
- Decisiones importantes.
- Estado de las historias.
- Defectos conocidos.
- Riesgos y limitaciones pendientes.
- Resultados de validación relevantes.

Actualiza el estado al cerrar una historia o alcanzar un checkpoint significativo, no después de cada operación trivial.

No introduzcas documentación obligatoria si no facilita la ejecución, la recuperación del contexto o el mantenimiento.

## 13. Métricas y protocolo experimental

Audita las métricas actuales de SAQI y amplíalas únicamente si aportan información útil.

Como mínimo, define cómo registrar:

- Tokens consumidos por historia aceptada.
- Tiempo transcurrido hasta completar una historia.
- Número de ciclos de corrección.
- Defectos encontrados durante el desarrollo.
- Defectos detectados después de considerar terminada la historia.
- Regresiones.
- Tasa de cumplimiento de criterios de aceptación.
- Coste de las pruebas y de la validación profunda, cuando sea medible.

Distingue entre defectos encontrados antes de cerrar una historia y defectos escapados a una etapa posterior.

Propón un experimento comparativo entre:

A. Flujo incremental con AI Pair Programming, pruebas y commits frecuentes.

B. El mismo flujo incorporando los mecanismos relevantes de SAQI: Skills, contexto persistente, QA adaptativo y aprendizaje.

Utiliza tareas comparables, condiciones documentadas y varias repeticiones cuando sea viable. Mantén constantes, en lo posible, el modelo, el entorno y la complejidad de las tareas.

Registra resultados reales, incluidos los negativos. No declares que SAQI mejora la calidad o reduce tokens hasta disponer de evidencia suficiente.

## 14. Documentación y compatibilidad

Actualiza los documentos existentes en lugar de crear documentos paralelos innecesarios.

Revisa, según corresponda:

- README.
- Metodología y proceso de 14 fases.
- Política MVP-First.
- Skills.
- Documentación de QA.
- Métricas y protocolo experimental.
- Ejemplos de uso.
- Plantillas y criterios de aceptación.

Mantén trazabilidad entre la metodología, las instrucciones para agentes y las pruebas que demuestran su aplicación.

Conserva las capacidades útiles existentes y evita cambios incompatibles sin justificación. Si es necesario modificar las 14 fases, determina si conviene convertirlas en un catálogo de capacidades que se activan según el riesgo, en vez de eliminar controles importantes.

## 15. Criterios de aceptación finales

Considera la tarea terminada solo cuando hayas:

1. Auditado el diseño actual de SAQI.
2. Identificado y resuelto las principales fuentes de burocracia y sobreingeniería.
3. Incorporado un flujo incremental por historias y vertical slices.
4. Definido una política adaptativa de profundidad y QA.
5. Integrado ODD, SDD ligero y TDD selectivo sin duplicar procesos.
6. Conservado y mejorado el aprendizaje mediante Skills.
7. Añadido controles de eficiencia y límites de iteración.
8. Revisado la coherencia de la documentación.
9. Ejecutado las pruebas y validaciones disponibles.
10. Registrado los cambios realizados y las limitaciones pendientes.

## 16. Informe final obligatorio

Al finalizar, entrega un informe breve que incluya:

- Diagnóstico inicial.
- Principales problemas encontrados.
- Archivos modificados.
- Mejoras implementadas.
- Decisiones de diseño y justificación.
- Pruebas ejecutadas y resultados reales.
- Funcionalidades que quedaron pendientes.
- Riesgos o incompatibilidades.
- Cómo utilizar el nuevo flujo en una tarea pequeña, una mediana y una de alto riesgo.
- Cómo medir tokens, tiempo y calidad para comprobar si las mejoras funcionan.

Trabaja sobre el repositorio real, respeta las convenciones existentes y evita introducir complejidad innecesaria. Prioriza una implementación funcional, verificable, incremental y mantenible.

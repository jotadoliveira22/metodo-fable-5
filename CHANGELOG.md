# Changelog

Formato: [Keep a Changelog](https://keepachangelog.com/es/). Versionado: semver
(parche = ajuste de redacción/corrección, menor = regla o referencia nueva,
mayor = cambio de estructura o de procedimiento principal). La versión vive en tres
lugares que deben coincidir: frontmatter de `SKILL.md`, este archivo y `evals/evals.json`.

## [1.0.1] - 2026-07-11

Correcciones derivadas de la prueba con instancia aislada (evaluador independiente
sin contexto de creación) sobre simulaciones de debugging, arquitectura y
automatización PYME. Los tres casos aprobaron (EV-06, EV-02, EV-03); los defectos
corregidos son de coherencia interna y auditabilidad:

### Corregido

- Contradicción: la tabla de complejidad de `SKILL.md` no exigía revisión
  adversarial en tareas complejas, mientras la Etapa 8 y `quality-gates.md` sí.
  Ahora la tabla la incluye y remite a `quality-gates.md` como fuente completa.
- Duplicación: la lista de revisión adversarial existía dos veces con contenido
  distinto (6 vs. 8 preguntas). `quality-gates.md` §5 es ahora la fuente única.
- Regla no auditable: `decision-framework.md` §4 pedía "puntuar mentalmente" la
  matriz; ahora exige comunicar en la entrega las 3–4 filas que decidieron la elección.
- Ambigüedad: `error-recovery.md` §3 no aclaraba si el rollback en producción
  requiere confirmación; ahora se propone al usuario salvo autoridad delegada.

### Añadido

- Campo `version` en el frontmatter de `SKILL.md` como ubicación canónica, con
  regla de sincronización en el protocolo de aprendizaje (paso 7).
- Disparadores concretos en la `description` (bugs, deploys, MVP, CRM, webhooks,
  producción) para reducir el riesgo de sub-activación.
- `EV-13`: caso negativo que verifica que el Skill NO se aplica a preguntas triviales.

## [1.0.0] - 2026-07-11

### Añadido

- Versión inicial del Skill `metodo-fable-5`.
- `SKILL.md`: procedimiento principal de 10 etapas (comprensión, clasificación,
  definición de éxito, descomposición, selección de herramientas, ejecución
  incremental, verificación, revisión adversarial, recuperación ante errores,
  entrega), reglas de autonomía, formato de decisión visible, resumen de
  arquitectura y protocolo de aprendizaje continuo.
- `references/decision-framework.md`: árboles de clasificación y ambigüedad,
  descomposición, matriz arquitectónica, señales de sobrearquitectura.
- `references/quality-gates.md`: verificación mínima por nivel, catálogo de
  verificaciones, lista de puertas, revisión adversarial.
- `references/tool-selection.md`: tabla evidencia → herramienta, reglas
  transversales, anti-patrones.
- `references/error-recovery.md`: diagnóstico, debugging, fallos de despliegue,
  recuperación de automatizaciones, escalamiento.
- `references/software-projects.md`: reglas para código, secretos, dependencias,
  migraciones, solución temporal vs. definitiva.
- `references/automation-projects.md`: mapeo de procesos, idempotencia,
  reintentos, trazabilidad, costos, validación con casos reales, mantenimiento.
- `examples/`: tres casos completos (debugging en producción, arquitectura de
  MVP, automatización para PYME).
- `evals/evals.json`: 12 casos de evaluación con criterios de aprobación.

### Notas de creación

- Origen de la evidencia: comportamientos operativos observables del agente y
  buenas prácticas generales de ingeniería de software y agentes. Las reglas que
  son inferencias están marcadas como heurísticas operativas; ninguna afirma
  reproducir mecanismos internos del modelo.
- La carpeta `~/.claude/skills/metodo-fable-5/` no existía antes de esta versión;
  no hubo contenido previo que respaldar.
- Probado con una instancia aislada (subagente sin contexto de creación) sobre
  simulaciones de debugging, arquitectura y automatización PYME contra los
  criterios de `evals/evals.json`.

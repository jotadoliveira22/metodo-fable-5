# Changelog

Formato: [Keep a Changelog](https://keepachangelog.com/es/). Versionado: semver
(parche = ajuste de redacción/corrección, menor = regla o referencia nueva,
mayor = cambio de estructura o de procedimiento principal).

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

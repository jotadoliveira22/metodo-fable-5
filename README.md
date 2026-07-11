# metodo-fable-5

Skill personal de Claude Code: marco operativo para analizar, planificar, ejecutar,
verificar y entregar tareas complejas de software, automatización, arquitectura,
debugging, investigación técnica y consultoría.

No reproduce razonamiento interno de ningún modelo: sistematiza comportamientos
observables y buenas prácticas de agentes en un procedimiento explícito y auditable.
Las reglas que son inferencias están marcadas como heurísticas operativas.

## Instalación como Skill personal

```bash
git clone https://github.com/jotadoliveira22/metodo-fable-5 ~/.claude/skills/metodo-fable-5
```

O copia el contenido de este repositorio a `~/.claude/skills/metodo-fable-5/`.
Al ser un Skill personal, queda disponible en todos tus proyectos.

## Uso

- Invocación manual: `/metodo-fable-5`
- Activación automática: Claude lo aplica cuando la solicitud requiere varias etapas,
  decisiones técnicas, uso de herramientas, validación o recuperación ante errores.
  No se activa para preguntas triviales.

## Estructura

```
SKILL.md                                  Procedimiento principal (10 etapas)
references/decision-framework.md          Clasificación, descomposición, arquitectura, ambigüedad
references/quality-gates.md               Verificación y puertas de calidad
references/tool-selection.md              Qué herramienta usar según la evidencia necesaria
references/error-recovery.md              Debugging, fallos de despliegue, recuperación
references/software-projects.md           Reglas para código y repositorios
references/automation-projects.md         Reglas para automatizaciones e integraciones
examples/                                 El método aplicado de punta a punta (3 casos)
evals/evals.json                          13 casos de evaluación
CHANGELOG.md                              Historial de versiones
```

`SKILL.md` usa progressive disclosure: contiene el procedimiento principal y enlaza
las referencias, que solo se cargan cuando la tarea lo requiere.

## Actualización (aprendizaje continuo)

Di a Claude frases como "aprende de este proyecto", "incorpora esta corrección al método"
o "esto debe hacerse así en adelante". El Skill incluye un protocolo que:
clasifica el aprendizaje (local vs. general), actualiza el archivo correcto, añade o
ajusta una evaluación, incrementa la versión (semver) y lo registra en `CHANGELOG.md`.

Las preferencias específicas de UN proyecto van al `CLAUDE.md` de ese proyecto,
no a este Skill.

## Evaluación

`evals/evals.json` contiene 13 casos con solicitud, contexto, comportamiento esperado,
errores a evitar y criterios de aprobación. Para probar el Skill tras un cambio, pide a
una instancia de Claude sin contexto previo que resuelva los casos y puntúa con esos
criterios.

## Limitaciones

- El método no sustituye el juicio: clasificar mal la complejidad de una tarea degrada
  el resto del procedimiento.
- Las reglas de APIs, precios y límites deben verificarse siempre en documentación
  vigente; el Skill lo exige pero no puede garantizar que la fuente consultada sea correcta.
- Los ejemplos son ilustrativos, no plantillas: cada proyecto requiere adaptar el nivel
  de verificación a su riesgo real.

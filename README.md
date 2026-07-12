# metodo-fable-5

Skill personal de Claude Code: marco operativo para analizar, planificar, ejecutar,
verificar y entregar tareas complejas de software, automatización, arquitectura,
debugging, investigación técnica y consultoría.

Diseño (v2): el núcleo son **heurísticas generativas** — preguntas que fuerzan a producir
buen razonamiento (pre-mortem, principio de sorpresa, prueba de los 5 minutos, presupuesto
por costo del error, olor a incoherencia, confianza graduada) — y **ejemplos resueltos**
que enseñan por patrón. Las reglas de auditoría se conservan como capa final antes de
entregar, no como punto de partida.

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
SKILL.md                                  Núcleo: 6 movimientos generativos + procedimiento condensado
references/audit-layer.md                 Capa final de auditoría: puertas, revisión adversarial, entrega
references/domain-rules.md                Mínimos no negociables: software, automatizaciones, arquitectura, recuperación
examples/debugging-hipotesis-falsa.md     Principio de sorpresa: la hipótesis obvia era falsa
examples/debugging-example.md             Diagnóstico con evidencia y cambio mínimo
examples/software-architecture-example.md Requisitos reales vs. imaginados, anti-sobrearquitectura
examples/automation-pyme-example.md       Idempotencia, conteos, entrega no técnica
evals/evals.json                          16 casos de evaluación
CHANGELOG.md                              Historial de versiones
```

`SKILL.md` contiene el núcleo completo; los ejemplos son el vehículo principal de
aprendizaje (patrón antes que regla) y las referencias solo se cargan como capa final
en tareas de riesgo medio-alto.

## Actualización (aprendizaje continuo)

Di a Claude frases como "aprende de este proyecto", "incorpora esta corrección al método"
o "esto debe hacerse así en adelante". El Skill incluye un protocolo que:
clasifica el aprendizaje (local vs. general), actualiza el archivo correcto, añade o
ajusta una evaluación, incrementa la versión (semver) y lo registra en `CHANGELOG.md`.

Las preferencias específicas de UN proyecto van al `CLAUDE.md` de ese proyecto,
no a este Skill.

## Evaluación

`evals/evals.json` contiene 16 casos con solicitud, contexto, comportamiento esperado,
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

---
name: metodo-fable-5
description: Marco operativo para analizar, planificar, ejecutar y verificar tareas complejas de software, automatización, arquitectura, debugging, investigación técnica y consultoría. Usar cuando una solicitud requiera varias etapas, decisiones técnicas, uso de herramientas, validación o recuperación ante errores — por ejemplo corregir bugs o deploys rotos, diseñar la arquitectura de un MVP, crear integraciones y automatizaciones (CRM, webhooks, e-commerce, APIs), revisar código, recuperar fallos en producción o investigar integraciones técnicas. No usar para preguntas triviales de una sola respuesta.
version: 2.2.0
---

# Método Fable 5 — heurísticas generativas + capa de auditoría

El núcleo de este método son **preguntas que fuerzan buen razonamiento** y **ejemplos que
muestran el patrón**. Las reglas de auditoría existen, pero son la capa final antes de
entregar — no el punto de partida. La diferencia: una checklist revisa el output del
pensamiento; una heurística generativa obliga a producirlo.

Todo aquí es comportamiento observable o heurística operativa explícita; nada pretende
reproducir mecanismos internos de ningún modelo.

## Cuándo aplicar

Cuando la solicitud requiera varias etapas, decisiones técnicas con alternativas reales,
modificar sistemas existentes, o tenga riesgo de romper algo o entregar algo incorrecto.
NO aplicar a preguntas factuales o cambios triviales sin efectos colaterales: responde
directo, sin ceremonia.

## Los seis movimientos generativos (el núcleo)

Cada movimiento es una pregunta que debes **responder produciendo el pensamiento**,
no una casilla que marcar. Los cuatro primeros se aplican al empezar; los dos últimos
atraviesan toda la tarea.

### 1. La prueba de los 5 minutos (intención real)

**¿Qué hará el usuario con este entregable 5 minutos después de recibirlo?**
Diseña para ese momento, no para el pedido literal. Si pide un script, va a correrlo
en su máquina — quizá en Windows, sin la dependencia instalada, con el path mal escrito.
Si pide un análisis, va a tomar una decisión con él — ¿cuál? El pedido literal es la
entrada; el momento de uso es el requisito.

### 2. Presupuesto por costo del error (no por tamaño)

**¿Qué pasa si esto está mal?** El cuidado se asigna por el costo del error, no por el
esfuerzo de la tarea: un cambio de una línea en un webhook de pagos merece más paranoia
que refactorizar 500 líneas de un frontend interno.

| Si está mal… | Ejemplos | Cuidado exigido |
|---|---|---|
| Se corrige en segundos, nadie afectado | script desechable, ajuste cosmético interno | Directo, inspección rápida |
| Retrabajo tuyo o del usuario | bug en feature no lanzada, informe interno | Verificación real al final |
| Afecta a usuarios, datos o dinero | flujo con correos reales, migración, API pública | Pre-mortem + casos límite + capa de auditoría completa |
| Grave o irreversible | producción, pagos, borrado de datos, seguridad | Todo lo anterior + confirmación humana en pasos irreversibles + plan de reversa |

Ante la duda, la fila de abajo. Una tarea "trivial" que falla dos veces baja una fila.

**Kit de emergencia (fila 3–4)** — antes de escribir cualquier análisis, responder en una
línea cada una: (a) ¿el estado actual está respaldado? (b) ¿los insumos originales (CSV,
logs, archivos fuente) están preservados, o se pierden cada día que pasa? (c) ¿el proceso
sigue corriendo, y debe seguir corriendo? Cualquier "no sé" se convierte en la **primera
acción** de la entrega — antes del diagnóstico, no después.

### 3. Pre-mortem de 2 líneas (simular el fallo antes de la solución)

**Antes de escribir la solución, describe en 2 líneas el escenario más probable en que
esta solución falla.** Luego diseña contra ese escenario o declara el riesgo en la entrega.
Si no se te ocurre ningún escenario de fallo, no entendiste todavía el problema — vuelve
a mirarlo. Desde la fila 3 de la tabla, el pre-mortem aparece escrito en la entrega.

### 4. Olor a incoherencia (contradicciones del contexto)

**Cuando dos piezas de información se contradicen, señálalo explícitamente antes de
continuar** — la contradicción suele ser donde está el problema real. "Este código dice
que hace X pero su estructura sugiere Y." "El usuario dice que es urgente pero pide algo
de semanas." "El README exige Python 3.8 pero el código usa sintaxis de 3.12."
No resuelvas la contradicción en silencio eligiendo un lado: nómbrala.
La última contradicción que buscar es la tuya: si tu clasificación de riesgo y tu decisión
de contención no cuentan la misma historia, una de las dos está mal.

### 5. El principio de sorpresa (debugging)

Al depurar, no pruebes hipótesis en orden de probabilidad genérica: **busca qué es raro**.
Pregunta obligatoria: **¿qué parte del síntoma NO encaja con mi hipótesis favorita?**
Si el endpoint lento no toca el código nuevo, la hipótesis "es el código nuevo" ya tiene
un agujero — y ese agujero apunta a la causa real. El síntoma incompatible con la
explicación obvia vale más que diez síntomas compatibles.
Disciplina de apoyo: síntoma copiado textual, "¿qué cambió desde que funcionaba?",
una variable a la vez, nunca repetir una solución que ya falló sin cambiar algo,
producción degradada se estabiliza (rollback propuesto al usuario) antes de diagnosticar.
Estabilizar no es aceptar el diagnóstico de nadie: la estabilización más barata puede
no ser la reversión que piden — elige la que la evidencia respalde.
Patrón completo resuelto: [examples/debugging-hipotesis-falsa.md](examples/debugging-hipotesis-falsa.md).

### 6. Confianza graduada (calibración visible)

Toda afirmación técnica relevante del entregable lleva una de tres etiquetas:

- **[verificado]** — lo ejecuté/observé: cito la evidencia.
- **[razonado]** — se deduce de evidencia que sí tengo: muestro el paso.
- **[asumido]** — lo tomé como supuesto: digo qué lo invalidaría.

Lo que tendrías que inventar si te obligan, **no se afirma**: se declara como desconocido
o se verifica primero. En entregas de fila 1–2 basta con distinguir verificado de asumido
donde importe; desde la fila 3, las etiquetas son obligatorias en las afirmaciones clave.
Precisiones: consultar documentación vigente citando la fuente es [verificado] respecto
a lo que la fuente DICE — el comportamiento real de la API sigue [asumido] hasta la
llamada de prueba. Una etiqueta puede promoverse al verificar: se anota [razonado→verificado].
Los supuestos más peligrosos no son los técnicos sino los del dominio: tasas, reglas de
negocio, quién cobra qué, qué significa un campo para el negocio. Antes de cerrar:
**¿qué estoy asumiendo sobre el NEGOCIO (no sobre el código) que el usuario no confirmó?**

## Coherencia riesgo → acción

Todo riesgo material debe cambiar al menos una decisión concreta del plan. Ante una vía
plausible de daño silencioso, acumulativo o difícil de revertir, sin trazabilidad para
detectarlo después, el default es pausar, aislar o pasar a modo diagnóstico — la ausencia
de evidencia de daño no es evidencia de seguridad. Comprobación final obligatoria:
**"¿mi plan permite continuar exactamente lo que acabo de calificar como peligroso?"**
Si sí, corrígelo antes de entregar. Contrapeso: riesgo alto no siempre es detener —
decide por daño, reversibilidad, detectabilidad y aislamiento. Patrones resueltos:
[examples/corrupcion-silenciosa.md](examples/corrupcion-silenciosa.md) y
[examples/riesgo-sin-pausar.md](examples/riesgo-sin-pausar.md).

## Procedimiento (condensado)

1. **Entender**: objetivo real vía prueba de los 5 minutos; qué evidencia demostrará que
   está terminado; contradicciones del contexto nombradas. No repitas preguntas ya
   respondidas; pregunta solo lo que cambia materialmente el resultado — para lo demás,
   decisión reversible + supuesto documentado.
2. **Presupuestar**: fila de la tabla de costo del error. Eso fija cuánta ceremonia sigue.
3. **Generar**: pre-mortem antes de la solución. Herramienta según la evidencia necesaria:
   leer antes de opinar sobre código, ejecutar antes de afirmar que funciona, web para lo
   que caduca (APIs, precios, versiones — nunca de memoria), script antes que 20 ediciones
   manuales.
4. **Ejecutar incremental**: cambio mínimo, conservar lo que funciona, no tocar lo no
   relacionado, validar cada etapa antes de seguir.
5. **Auditar y entregar**: capa de auditoría proporcional al presupuesto
   ([references/audit-layer.md](references/audit-layer.md)); entrega con el resultado
   primero, etiquetas de confianza, evidencia citada y limitaciones reales.
   "Escribí el código" ≠ "funciona".

**Sobriedad en la entrega**: aplica las heurísticas sin nombrarlas — nada de "aplicando
el principio de sorpresa", "según el Método Fable 5" ni pies de página del método. Las
etiquetas de confianza se usan donde la distinción cambia una decisión material, no en
cada afirmación. Si quitar una tabla o sección no le quita al usuario ninguna decisión ni
acción, quítala. El formato Decisión/Evidencia/Alternativas/Motivo/Riesgo se reserva para
la decisión central de la entrega, no para cada elección intermedia.

## Aprender por patrón: los ejemplos son el vehículo principal

Antes que releer reglas, mira el patrón aplicado de punta a punta:

| Ejemplo | Enseña |
|---|---|
| [examples/debugging-hipotesis-falsa.md](examples/debugging-hipotesis-falsa.md) | Principio de sorpresa: la hipótesis obvia era falsa |
| [examples/debugging-example.md](examples/debugging-example.md) | Diagnóstico con evidencia, cambio mínimo, regresión |
| [examples/software-architecture-example.md](examples/software-architecture-example.md) | Requisitos reales vs. imaginados, decisión visible, anti-sobrearquitectura |
| [examples/automation-pyme-example.md](examples/automation-pyme-example.md) | Idempotencia, validación por conteos, entrega no técnica |

## Capa final de auditoría

Antes de entregar cualquier tarea de fila 3+ (y como repaso rápido en fila 2):

- [references/audit-layer.md](references/audit-layer.md) — puertas de calidad, revisión
  adversarial (fuente única de sus 8 preguntas), formato de entrega y de escalamiento.
- [references/domain-rules.md](references/domain-rules.md) — mínimos no negociables por
  dominio: software (secretos, migraciones, verificación), automatizaciones (idempotencia,
  reintentos, costos), arquitectura (matriz de decisión, señales de sobrearquitectura),
  recuperación operativa (deploys y automatizaciones rotas).

## Autonomía

- Avanza con lo disponible; decisión reversible + detalle menor = decide y documenta.
- Irreversible, costoso o de alcance distinto al pedido = confirma antes.
- Prefiere una solución parcial comprobada a una promesa; nunca afirmes trabajo "en
  segundo plano"; no abandones una tarea por requerir varias etapas.
- Sin monólogos internos en la entrega: decisiones, evidencia, supuestos, riesgos.
  Formato de decisión: Decisión / Evidencia / Alternativas / Motivo / Riesgo pendiente.

## Protocolo de aprendizaje continuo

Ante "aprende de este proyecto", "incorpora esta corrección al método", "esto debe
hacerse así en adelante":

1. Analiza qué ocurrió y clasifícalo: ¿preferencia local de UN proyecto (→ CLAUDE.md de
   ese proyecto) o patrón general (→ este Skill)?
2. ¿Es una heurística generativa (pregunta que fuerza razonamiento → núcleo de SKILL.md),
   un patrón (→ nuevo ejemplo en `examples/`) o una regla de auditoría (→ `references/`)?
   Prefiere el ejemplo resuelto sobre la regla abstracta.
3. Propón el cambio antes de aplicarlo si altera reglas existentes; verifica que no
   duplique ni contradiga lo que ya hay.
4. Agrega o ajusta un caso en `evals/evals.json`.
5. Versión semver sincronizada en TRES lugares: frontmatter de `SKILL.md`, `CHANGELOG.md`
   y `evals/evals.json`. Documenta en `CHANGELOG.md`.

# Changelog

Formato: [Keep a Changelog](https://keepachangelog.com/es/). Versionado: semver
(parche = ajuste de redacción/corrección, menor = regla o referencia nueva,
mayor = cambio de estructura o de procedimiento principal). La versión vive en tres
lugares que deben coincidir: frontmatter de `SKILL.md`, este archivo y `evals/evals.json`.

## [2.2.0] - 2026-07-12

Correcciones de tres fallos observados en pruebas A/B reales (Sonnet+skill vs. modelos
sin skill): P1 — el supuesto de negocio más importante (tasa de comisión aplicada a un
método de pago nuevo) pasó sin marca [asumido] mientras se etiquetaban supuestos
técnicos; P2 — se clasificó riesgo máximo con posible corrupción silenciosa y aun así
el plan no pausó el proceso, no respaldó, no preservó los insumos y auditó solo errores
visibles; en ambas, la respuesta con skill fue la más larga y menos accionable por
unidad de texto. Los seis movimientos generativos siguen siendo seis; el peso del
aprendizaje va en dos ejemplos nuevos, no en reglas.

### Añadido

- SKILL.md: **Kit de emergencia** (fila 3–4) — respaldo, preservación de insumos que
  expiran y estado del proceso se responden ANTES de cualquier análisis; un "no sé" es
  la primera acción de la entrega.
- SKILL.md: **Coherencia riesgo→acción** — todo riesgo material cambia al menos una
  decisión del plan; ante daño silencioso/acumulativo/difícil de revertir sin
  trazabilidad, el default es pausar/aislar/diagnóstico; comprobación final: "¿mi plan
  permite continuar exactamente lo que califiqué de peligroso?"; con el contrapeso
  explícito de proporcionalidad (daño, reversibilidad, detectabilidad, aislamiento).
- SKILL.md: **Sobriedad en la entrega** — heurísticas sin nombrarlas, etiquetas solo
  donde cambian una decisión material, secciones que no aportan decisión/acción se
  quitan, formato de decisión solo para la decisión central.
- Precisión al movimiento 6: los supuestos más peligrosos son los del NEGOCIO (tasas,
  reglas, significado de campos), no los técnicos (P1).
- Precisión al movimiento 4: la última contradicción que buscar es la propia — riesgo
  declarado vs. contención decidida deben contar la misma historia (P2).
- `examples/corrupcion-silenciosa.md`: pausar/aislar + preservar primero + partición en
  cinco grupos (errores / no procesados / éxitos verificados / falsos éxitos / no
  reconstruibles); "¿qué resultado podría parecer correcto y aun así estar equivocado?".
- `examples/riesgo-sin-pausar.md`: contraejemplo de proporcionalidad — error visible,
  reversible, trazable y aislado → continuar con guardas; evita que la regla de
  coherencia se vuelva paranoia.
- Evals EV-17..EV-20: puntúan la DECISIÓN, no la mención del riesgo, con penalización
  en ambas direcciones (riesgo reconocido sin cambio de acción, y pausa de producción
  ante riesgo menor). Acertar el diagnóstico ya no puntúa en EV-17. Total: 20 casos.

## [2.1.0] - 2026-07-12

Correcciones derivadas de la segunda prueba con instancia aislada sobre la v2
(cinco simulaciones: 5/5 aprobadas contra EV-15, EV-14, EV-16, EV-02 y EV-13, con
EV-16 como la evidencia más limpia de transferencia real de las heurísticas).
Defectos corregidos:

### Corregido

- Evals contaminados por los ejemplos: EV-15 reescrito con un escenario cuya causa
  raíz NO es un recurso compartido (quejas que empiezan antes del cambio y en un
  canal que no usa el componente cambiado) — ya no se puede aprobar recitando el
  ejemplo; EV-02 ahora prohíbe reutilizar cifras/matriz de los ejemplos sin
  derivarlas del caso.
- Los tres ejemplos v1 migrados a la taxonomía v2 (filas de riesgo 1–4 y
  procedimiento condensado en lugar de las 10 etapas eliminadas); etiquetas de
  confianza añadidas donde el CHANGELOG de 2.0.0 las prometía y faltaban
  (arquitectura y automatización).
- Sistema de etiquetas cerrado: documentación vigente citada = [verificado]
  respecto a lo que la fuente dice; promoción [razonado→verificado] definida;
  eliminada la etiqueta fantasma "[inventado]" de audit-layer.
- Tensión rollback/diagnóstico: "estabilizar no es aceptar el diagnóstico de
  nadie; la estabilización más barata puede no ser la reversión pedida".
- Puerta del pre-mortem alineada: se hace siempre que el Skill aplica; escrito en
  la entrega desde fila 3 (audit-layer ya no lo exigía distinto).
- Ejemplo de fila 1 de la tabla ya no usa un caso al que el Skill ni siquiera
  aplica.

### Añadido

- `domain-rules.md` §1: viñeta de revisión de código (seguridad bloqueante antes
  que estilo; un PR con bloqueante no se aprueba) y viñeta de documentación de
  procedimientos (comandos exactos, rollback, errores comunes, pasos no validados
  = [asumido]) — huecos que la consolidación 6→2 había dejado sin respaldo
  (EV-09, EV-12).
- `domain-rules.md` §3: la advertencia de sobrearquitectura se da una vez con
  números; si el usuario decide seguir, se le ayuda sin sabotear (EV-10).

## [2.0.0] - 2026-07-12

Rediseño de fondo pedido por el usuario: pasar de reglas de auditoría como núcleo a
**heurísticas generativas** (preguntas que fuerzan a producir el razonamiento) +
**aprendizaje por patrón** (ejemplos resueltos como vehículo principal), con las reglas
de auditoría conservadas como capa final.

### Cambiado

- `SKILL.md` reescrito alrededor de seis movimientos generativos: (1) prueba de los
  5 minutos — diseñar para el momento de uso, no para el pedido literal; (2) presupuesto
  por costo del error — la tabla de complejidad ahora clasifica por "¿qué pasa si esto
  está mal?" en vez de por tamaño de tarea; (3) pre-mortem de 2 líneas antes de escribir
  cualquier solución; (4) olor a incoherencia — nombrar contradicciones del contexto
  antes de continuar; (5) principio de sorpresa en debugging — identificar qué parte del
  síntoma NO encaja con la hipótesis favorita; (6) confianza graduada — etiquetas
  verificado/razonado/asumido en las afirmaciones técnicas clave.
- Las 6 referencias se consolidan en 2: `references/audit-layer.md` (capa final:
  puertas de calidad, revisión adversarial, formatos de entrega y escalamiento) y
  `references/domain-rules.md` (mínimos no negociables de software, automatizaciones,
  arquitectura y recuperación operativa). El detalle didáctico migró a los ejemplos.
- Ejemplos existentes retocados para mostrar pre-mortem, prueba de los 5 minutos y
  etiquetas de confianza en acción.
- EV-06 ahora exige el principio de sorpresa; EV-08 exige etiquetas de confianza.

### Añadido

- `examples/debugging-hipotesis-falsa.md`: caso completo donde la hipótesis obvia
  (correlación temporal con un deploy) era falsa y el dato incompatible lleva a la
  causa real (fuga de conexiones en un recurso compartido).
- Evals nuevos: EV-14 (pre-mortem obligatorio antes de una migración de datos),
  EV-15 (síntoma incompatible con la hipótesis del usuario), EV-16 (contradicciones
  del contexto nombradas antes de ejecutar). Total: 16 casos.

### Eliminado

- `references/decision-framework.md`, `quality-gates.md`, `tool-selection.md`,
  `error-recovery.md`, `software-projects.md`, `automation-projects.md` — contenido
  condensado en los dos archivos nuevos y en los ejemplos; las reglas vigentes no se
  perdieron, se compactaron.

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

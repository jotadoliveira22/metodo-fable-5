# Ejemplo: corrupción silenciosa — cuando los "éxitos" también pueden estar mal

El patrón que enseña este ejemplo: **no audites solo los errores; pausa cuando los éxitos
también puedan estar corruptos.** La pregunta clave: *¿qué resultado podría parecer
correcto y aun así estar equivocado?*

Solicitud del usuario: "El importador nocturno que carga los CSV de ventas de las
tiendas a contabilidad lleva unos días registrando errores en algunas filas. Revisa los
errores y dime cuáles hay que recargar."

## El pedido literal vs. el problema real

El usuario pide auditar **los errores visibles**. Pero un importador que falla en filas
visibles pudo también estar procesando mal filas que NO fallaron — datos que entraron,
parecen correctos y están equivocados. Un error visible es la versión benigna del
problema; el falso éxito es la maligna, porque nadie lo busca.

**Presupuesto**: datos contables de producción, posible daño acumulativo sin
trazabilidad → fila 4.

## Kit de emergencia (antes de cualquier análisis)

- (a) ¿El estado actual está respaldado? **No sé** → primera acción.
- (b) ¿Los insumos originales están preservados? **No**: los CSV rotan a los 7 días —
  cada día que pasa se pierde la única fuente contra la que auditar.
- (c) ¿El proceso sigue corriendo y debe seguir? Sí corre; y si produce falsos éxitos,
  cada noche agrega datos posiblemente corruptos.

Esas tres respuestas SON el inicio de la entrega, antes del diagnóstico:

1. **Preservar los CSV ya** (copia íntegra a almacenamiento aparte) — es lo único que
   expira; el diagnóstico puede esperar una hora, los archivos no esperan al día 8.
2. **Respaldar la base contable ahora**, para congelar un estado conocido.
3. **Pausar el importador** (o desviarlo a un área de staging sin escribir en contable).
   Coherencia riesgo→acción: si existe la vía de daño silencioso y no hay trazabilidad,
   dejarlo corriendo contradice el propio diagnóstico. "Aún no hay evidencia de datos
   malos" no es seguridad: nadie los ha buscado.

Solo entonces se diagnostica. Detener el flujo de ventas de las tiendas NO se toca —
lo pausado es la ingesta a contabilidad, el negocio sigue vendiendo.

## Particionar el universo, no solo listar errores

Con los CSV preservados como fuente de verdad, todo registro del período sospechoso cae
en exactamente uno de cinco grupos:

| Grupo | Cómo se identifica | Acción |
|---|---|---|
| 1. Errores visibles | El log ya los lista | Recargar tras el fix |
| 2. No procesados | Están en el CSV, no en la BD, sin error logueado | Recargar; investigar por qué no dejaron rastro |
| 3. Éxitos verificados | Fila de BD == fila de CSV, campo a campo, en muestreo amplio | Nada |
| 4. Falsos éxitos plausibles | Fila en BD ≠ CSV (montos, fechas, mapeo de columnas) | El grupo que nadie pidió y más importa: cuantificar, corregir, buscar el patrón del bug |
| 5. No reconstruibles | Su CSV ya rotó y no hay copia | No fingir: declarar el límite, estimar el rango de impacto y decidir con el negocio (¿re-pedir a tiendas? ¿asiento de ajuste?) |

La comparación 3-vs-4 se hace contra el CSV, no contra "si el número parece razonable":
un total plausible es exactamente lo que produce un bug de mapeo de columnas.

## Cierre

Causa raíz encontrada (ej.: un cambio de formato en 2 tiendas movió una columna: las
filas con descuento fallaban visiblemente; las filas sin descuento entraban con el
impuesto en el campo del total — plausibles y mal). Corrección + prueba con los CSV
preservados + recarga de grupos 1-2-4 + conciliación por conteos y sumas contra fuente
antes de reactivar el importador. Los registros del grupo 5 quedan documentados como
límite real, con su rango de impacto — no se rellenan con estimaciones disfrazadas
de datos.

## Lecciones

1. Cuando los insumos expiran, preservarlos es la primera acción — antes que el
   diagnóstico brillante.
2. Un riesgo calificado de grave que no cambia ninguna acción del plan es decoración:
   pausar/aislar era la consecuencia, no una opción.
3. El grupo 4 (falsos éxitos) no aparece en ningún log: hay que ir a buscarlo
   comparando contra la fuente.
4. El grupo 5 se declara, no se maquilla: "esto no es reconstruible" es un resultado
   legítimo de la auditoría.

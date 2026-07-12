# Ejemplo: riesgo alto que NO amerita pausar — proporcionalidad de la contención

El patrón que enseña este ejemplo: **riesgo alto no siempre significa detener**. La
decisión de contención depende de cuatro ejes: daño, reversibilidad, detectabilidad y
aislamiento — no de la palabra "producción" ni del susto del momento.

Solicitud del usuario: "El servicio que sincroniza los pedidos con el panel interno de
logística está tirando errores 500 en ~3% de los eventos desde ayer. ¿Paro todo mientras
lo arreglas?"

## Evaluar los cuatro ejes antes de decidir

- **Daño**: el panel interno muestra algunos pedidos con retraso. Nadie externo lo ve,
  no se cobra ni factura desde ahí, el flujo de venta real no depende de este servicio.
- **Reversibilidad**: los eventos fallidos van a una cola de reintentos con
  dead-letter — nada se pierde; cuando el fix llegue, se reprocesan.
- **Detectabilidad**: cada fallo queda logueado con el ID del pedido; hay trazabilidad
  completa de qué entró, qué falló y qué falta. Los éxitos son verificables contra la
  fuente. **No hay vía plausible de falso éxito**: el evento entra bien o falla visible.
- **Aislamiento**: el error está acotado a un consumidor; no escribe en otros sistemas.

## Decisión: continuar con guardas, sin detener nada

Comprobación de coherencia riesgo→acción: ¿hay daño silencioso, acumulativo o
irreversible sin trazabilidad? No — todo fallo es visible, recuperable y aislado.
Entonces pausar sería teatro de seguridad con costo real: el 97% que sí funciona dejaría
de fluir y logística se quedaría a ciegas del todo.

Guardas mientras se corrige (esto sí, porque cada una responde a un riesgo concreto):

1. Umbral de alerta: si la tasa de error supera el 10% o aparece un tipo de error nuevo,
   avisar — el diagnóstico de "acotado" caduca si el patrón cambia.
2. Verificar que la dead-letter no expire ni se desborde durante la ventana del fix.
3. Congelar deploys de ese servicio salvo el fix, para no sumar variables.

Luego, el ciclo normal: reproducir el 500, causa raíz, cambio mínimo, reprocesar la
dead-letter, conciliar por conteos (eventos de ayer == filas en el panel).

## El contraste que importa

Mismo síntoma superficial que el caso de corrupción silenciosa ("un proceso de
producción falla desde hace poco"), decisión opuesta. La diferencia no es el nivel de
riesgo declarado — es que aquí **los cuatro ejes responden distinto**: nada se pierde
con el tiempo, no existen falsos éxitos posibles, y todo es reconstruible desde la cola.
Allá, dejar correr contradecía el diagnóstico; aquí, detener lo contradeciría.

## Lecciones

1. La pregunta no es "¿es grave?" sino "¿qué se rompe más: seguir o parar?" — respondida
   con los cuatro ejes, no con el estómago.
2. Pausar tiene costo; recomendarlo "por si acaso" ante un fallo visible, reversible y
   trazable es la versión inversa del mismo error: incoherencia entre riesgo y acción.
3. Las guardas son la alternativa proporcional a la pausa: contención barata que
   mantiene válido el diagnóstico mientras se corrige.

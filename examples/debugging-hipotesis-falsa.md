# Ejemplo: debugging donde la hipótesis obvia era falsa

El patrón que enseña este ejemplo: **la parte del síntoma que NO encaja con la explicación
obvia vale más que todas las partes que sí encajan.** (Principio de sorpresa, movimiento 5.)

Solicitud del usuario: "Desde que desplegamos el buscador nuevo el viernes, el checkout
se cae con timeouts. Revierte el buscador."

## La hipótesis obvia (y por qué era tentadora)

Correlación temporal perfecta: deploy del buscador el viernes → timeouts desde el viernes.
Hipótesis favorita de todos: "el código nuevo del buscador es lento y tumba el checkout".
El usuario ya viene con la solución: revertir.

**Presupuesto**: checkout de producción caído = fila 4 (grave). Y el pedido literal
("revierte") es una acción sobre producción → se propone, no se ejecuta a ciegas.

## El movimiento que cambia todo: ¿qué NO encaja?

Antes de aceptar la hipótesis, buscar la pieza del síntoma incompatible con ella:

- Evidencia 1 [verificado]: los timeouts son en `/checkout`, pero el checkout **no llama
  al código del buscador** — grep de imports y trazas: cero rutas compartidas de código.
- Evidencia 2 [verificado]: los logs muestran timeouts también a las 3 a.m., con tráfico
  casi nulo y nadie usando el buscador.

Si la causa fuera "el código del buscador es lento", ninguna de esas dos cosas debería
pasar. **La hipótesis obvia tiene dos agujeros → se descarta antes de gastar en ella.**
(Un agente sin este movimiento habría revertido, visto que los timeouts seguían —quizá
con menos frecuencia—, y entrado en una espiral de parches.)

## La pregunta correcta

Lo raro: dos rutas de código que no se tocan fallan juntas. ¿Qué **comparten** que no sea
código? Candidatos: la base de datos, el pool de conexiones, la memoria/CPU del host, la red.

- Evidencia 3 [verificado]: métricas del pool de conexiones a la BD — desde el viernes el
  pool está saturado (100% en uso, cola de espera creciente en los picos y también en los
  cronjobs nocturnos… a las 3 a.m.).
- Evidencia 4 [verificado]: el código nuevo del buscador abre una conexión por request
  y **no la devuelve al pool** en el caso de búsqueda sin resultados (early return antes
  del release). Fuga lenta: el pool se agota, y la víctima es cualquier endpoint que
  necesite una conexión — el checkout el primero, por ser el más usado.

Causa raíz [razonado→verificado]: fuga de conexiones en el buscador, no lentitud. El
síntoma aparecía en checkout porque el recurso compartido (pool) es global. Se confirma
reproduciendo en staging: 50 búsquedas sin resultados → pool agotado → timeout en checkout.

## Resolución

- Estabilización inmediata (propuesta al usuario, aprobada): reiniciar el servicio libera
  el pool (minutos de alivio) mientras sale el fix — mejor que revertir todo el buscador,
  y ahora sabemos POR QUÉ.
- Cambio mínimo: mover el release de la conexión a un `finally`. Una línea movida.
- Verificación: reproducción de staging ya no agota el pool [verificado]; métrica del pool
  plana tras 1 hora en producción [verificado]; suite completa en verde [verificado].
- Regresión: test que hace N búsquedas sin resultados y asserta que el pool vuelve a su
  tamaño [verificado].
- Prevención [asumido — pendiente de aprobar]: alerta cuando el pool supere el 80%
  sostenido; habría convertido 4 días de misterio en 10 minutos de diagnóstico.

## Lo que este ejemplo enseña

1. La correlación temporal genera la hipótesis, no la confirma.
2. Busca activamente el dato que tu hipótesis favorita NO puede explicar; si existe,
   la hipótesis muere ahí, por barata que fuera de "arreglar".
3. Cuando dos cosas que no comparten código fallan juntas, la causa está en lo que
   comparten fuera del código (pool, BD, host, red, límites).
4. El pedido literal ("revierte") no era la solución; el objetivo real (checkout estable)
   se logró con una línea. Pero la reversión se trató con respeto: se explicó por qué no,
   con evidencia, y la estabilización se propuso antes de tocar producción.

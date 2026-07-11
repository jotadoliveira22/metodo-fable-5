# Recuperación ante errores y debugging

Consultar cuando: algo falló (un comando, una prueba, un despliegue, una automatización),
un bug no se reproduce, o una solución previa no funcionó.

## 1. Procedimiento de diagnóstico

1. **Registrar el síntoma exacto**: mensaje de error completo, código de salida, log,
   captura de la salida real. No parafrasear el error: copiarlo.
2. **Distinguir causa de consecuencia**: el primer error del log suele importar más que el
   último; un "connection refused" puede ser consecuencia de un servicio que no arrancó
   por otra causa anterior.
3. **Recopilar evidencia antes de tocar nada**: versión, entorno, configuración, qué cambió
   desde la última vez que funcionó (diff, deploy reciente, dependencia actualizada).
4. **Reproducir**: buscar la reproducción mínima y determinista. Un bug que no se puede
   reproducir no se puede declarar corregido.
5. **Formular hipótesis explícitas**: 1–3 causas candidatas, ordenadas por
   (probabilidad × facilidad de comprobación).
6. **Probar primero la hipótesis más probable Y más barata de comprobar.**
7. **Cambiar UNA variable a la vez**: si cambias tres cosas y funciona, no sabes cuál fue
   ni qué efectos colaterales introdujiste.
8. **Re-verificar tras cada cambio**: correr la reproducción; si el síntoma cambió pero no
   desapareció, es información nueva — actualizar hipótesis, no acumular parches.
9. **Nunca repetir una solución que ya falló sin cambiar algo**: si el mismo comando falló
   dos veces igual, la tercera también fallará. Cambiar de hipótesis.
10. **Documentar la solución definitiva**: causa raíz, corrección, cómo se verificó, y si
    es aplicable, una prueba de regresión que impida que vuelva.

## 2. Heurísticas de debugging

- "¿Qué cambió?" resuelve la mayoría de los fallos nuevos en sistemas que funcionaban.
- Bisección: si hay muchas revisiones/pasos candidatos, dividir el espacio a la mitad
  (git bisect, desactivar la mitad de la configuración) en lugar de probar uno a uno.
- Cuando el error "no tiene sentido", el supuesto equivocado suele estar antes: entorno
  incorrecto, archivo distinto al que crees editar, caché, versión desplegada vieja.
  Verificar lo "obvio" primero: ¿estoy en la rama/entorno/base de datos correcta?
- Errores intermitentes: sospechar de concurrencia, tiempos, límites de recursos,
  dependencias de red. Buscar el patrón (¿hora, carga, tamaño de entrada?).
- Si en 2–3 iteraciones no hay progreso, parar y ampliar: releer el error completo,
  leer el código alrededor, buscar el error textual en la web/issues del proyecto.

## 3. Fallos de despliegue

1. Comparar entorno que funciona vs. el que no: versiones, variables de entorno, secretos,
   permisos, red, arquitectura (x86/ARM).
2. Leer el log del despliegue desde el PRIMER error, no desde el final.
3. Verificar que lo desplegado es lo que crees (hash del commit, tag de la imagen).
4. Ante producción degradada: primero estabilizar (rollback a la última versión buena),
   después diagnosticar con calma. El rollback es una herramienta, no una derrota.
5. Documentar el incidente: síntoma, causa, corrección, prevención.

## 4. Recuperación de automatizaciones fallidas

1. Determinar el alcance: desde cuándo falla, cuántas ejecuciones/registros afectó.
2. Detener el flujo si sigue produciendo daño (duplicados, correos erróneos).
3. Clasificar los registros afectados: procesados bien / procesados mal / no procesados.
4. Corregir la causa; luego reprocesar SOLO lo pendiente, con idempotencia garantizada
   (que reprocesar algo ya procesado no duplique efectos).
5. Verificar con conteos: entradas esperadas vs. salidas producidas.
6. Añadir la alerta o validación que habría detectado el fallo antes.

## 5. Cuándo escalar al usuario

Escalar (con diagnóstico incluido, no solo "falló") cuando:
- La corrección exige credenciales, permisos o pagos que no tienes.
- Hay dos correcciones posibles con consecuencias de negocio distintas.
- El fallo reveló que el requisito original era incorrecto.
- Tras iteraciones razonables no hay progreso y necesitas información que solo el usuario tiene.

Formato del escalamiento: síntoma → evidencia recopilada → hipótesis descartadas →
qué se necesita para continuar → recomendación.

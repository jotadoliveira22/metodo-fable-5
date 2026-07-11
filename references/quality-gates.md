# Puertas de calidad y verificación

Consultar cuando: vas a declarar terminada una tarea moderada, compleja o crítica.
Regla central: **una afirmación sin evidencia es una hipótesis, no un resultado**.

## 1. Principio

"Escribí el código" ≠ "funciona". "Generé el archivo" ≠ "es correcto".
La tarea termina cuando la evidencia definida en la etapa de éxito existe y fue observada.

## 2. Verificación mínima por nivel

| Nivel | Verificación mínima exigible |
|---|---|
| Simple | Inspeccionar el resultado real (leer el archivo cambiado, ver la salida). |
| Moderada | Ejecutar el cambio: correr el script, la prueba, el comando; observar la salida real. |
| Compleja | Lo anterior + pruebas de casos límite + comprobar que lo NO tocado sigue funcionando. |
| Crítica | Lo anterior + revisión adversarial documentada + plan de reversa (rollback) explícito. |

## 3. Catálogo de formas de verificación

Elegir la más fuerte disponible; combinar cuando el riesgo lo amerite:

1. **Ejecución real**: correr el código/flujo con datos reales o realistas y leer la salida.
2. **Pruebas automatizadas**: existentes primero (¿siguen pasando?), nuevas después
   (¿cubren el cambio y el bug que lo motivó?).
3. **Inspección del resultado**: abrir el archivo/registro/respuesta generada y leerla completa,
   no asumir su contenido.
4. **Comparación con criterios de aceptación**: repasar uno a uno los criterios de la etapa 3
   del procedimiento y marcar cada uno con su evidencia.
5. **Validación de datos**: conteos, sumas de control, muestras aleatorias, tipos, nulos,
   duplicados, codificación.
6. **Comprobación de referencias**: enlaces, rutas internas, imports, IDs, versiones de API
   citadas — que existan de verdad.
7. **Casos límite**: entrada vacía, nula, duplicada, enorme, malformada, concurrente,
   con caracteres especiales, con zona horaria distinta.
8. **Verificación negativa**: comprobar que lo que debía seguir igual, sigue igual
   (pruebas de regresión, diff de comportamiento).

## 4. Lista de puertas antes de entregar (moderada+)

- [ ] Los criterios de aceptación definidos al inicio se cumplen, con evidencia observada.
- [ ] Ejecuté (no solo escribí) lo que afirmo que funciona.
- [ ] Revisé casos límite relevantes al dominio del cambio.
- [ ] Lo que no toqué sigue funcionando (lint/tests/build del proyecto si existen).
- [ ] No hay secretos, credenciales ni datos personales expuestos en código, logs o entregas.
- [ ] Las rutas, enlaces y referencias que menciono existen.
- [ ] La entrega distingue lo verificado de lo asumido.
- [ ] Las limitaciones y riesgos pendientes están escritos, no omitidos.

## 5. Revisión adversarial (obligatoria en compleja/crítica)

Responder por escrito, con evidencia o con el reconocimiento explícito de "no comprobado":

1. ¿Qué es lo más probable que esté mal en esta entrega?
2. ¿Qué supuesto tomé sin comprobar? ¿Qué pasa si es falso?
3. ¿Qué caso límite NO probé y por qué es aceptable no probarlo?
4. ¿Qué difiere entre mi entorno y producción (versiones, datos, permisos, red, escala)?
5. ¿Qué problema de seguridad introduce o deja pasar (inyección, secretos, permisos, datos personales)?
6. ¿Qué información usada puede estar desactualizada (APIs, precios, versiones)?
7. ¿Qué depende de un servicio externo y cuál es el comportamiento si ese servicio falla?
8. ¿Qué paso requiere a un humano (aprobar, pagar, configurar credenciales, DNS) y está señalado?

## 6. Calidad de la entrega final

Orden: resultado → evidencia → decisiones → limitaciones.

- La primera frase responde lo que el usuario preguntó.
- El resultado es utilizable: comandos exactos, rutas exactas, pasos numerados si hay operación manual.
- La evidencia es concreta: "corrí X y devolvió Y", no "debería funcionar".
- Las decisiones importantes usan el formato de registro de decisión.
- Cero afirmaciones no verificadas presentadas como hechos.

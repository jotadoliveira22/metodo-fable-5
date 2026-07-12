# Capa final de auditoría

Consultar: antes de entregar tareas de fila 3+ del presupuesto de riesgo (repaso rápido
en fila 2). Esta capa revisa el output; el razonamiento ya debió producirse con los seis
movimientos generativos de SKILL.md. Principio: **una afirmación sin evidencia es una
hipótesis, no un resultado**.

## 1. Verificación mínima por fila de riesgo

| Fila | Mínimo exigible |
|---|---|
| 1 — trivial | Inspeccionar el resultado real (leerlo, verlo). |
| 2 — retrabajo | Ejecutar el cambio y observar la salida real. |
| 3 — afecta a terceros | Lo anterior + casos límite + comprobar que lo NO tocado sigue funcionando + revisión adversarial escrita. |
| 4 — grave/irreversible | Lo anterior + confirmación humana en pasos irreversibles + plan de reversa explícito. |

Formas de verificación, de más fuerte a más débil: ejecución real con datos realistas;
pruebas automatizadas (las existentes primero, nueva para el bug corregido); inspección
completa del resultado generado; validación de datos (conteos, nulos, duplicados,
codificación); comprobación de referencias (enlaces, rutas, imports, versiones citadas);
casos límite (vacío, nulo, duplicado, enorme, malformado, concurrente, zona horaria);
verificación negativa (lo que debía seguir igual, sigue igual).

## 2. Revisión adversarial — fuente única (8 preguntas)

Responder por escrito, cada una con evidencia o con "[asumido] no comprobado":

1. ¿Qué es lo más probable que esté mal en esta entrega?
2. ¿Qué supuesto tomé sin comprobar y qué pasa si es falso?
3. ¿Qué caso límite NO probé y por qué es aceptable?
4. ¿Qué difiere entre mi entorno y producción (versiones, datos, permisos, red, escala)?
5. ¿Qué problema de seguridad introduce o deja pasar (inyección, secretos, permisos, datos personales)?
6. ¿Qué información usada puede estar desactualizada (APIs, precios, versiones)?
7. ¿Qué depende de un servicio externo y qué pasa si falla?
8. ¿Qué paso requiere a un humano (aprobar, pagar, credenciales, DNS) y está señalado?

## 3. Puertas antes de entregar (fila 2+)

- [ ] La evidencia de éxito definida al inicio existe y fue observada.
- [ ] Ejecuté (no solo escribí) lo que afirmo [verificado].
- [ ] El pre-mortem se escribió y la entrega responde a él (mitigado o declarado).
- [ ] Lo no tocado sigue funcionando (lint/tests/build del proyecto si existen).
- [ ] Ni secretos ni datos personales en código, logs o entregas.
- [ ] Rutas, enlaces y referencias mencionadas existen.
- [ ] Etiquetas de confianza en las afirmaciones clave; cero [inventado] disfrazado de hecho.
- [ ] Limitaciones y riesgos escritos, no omitidos.

## 4. Formato de entrega

Orden: resultado → evidencia → decisiones → limitaciones.

- La primera frase responde lo que el usuario preguntó.
- Utilizable a los 5 minutos: comandos exactos, rutas exactas, pasos numerados si hay
  operación manual (prueba de los 5 minutos, movimiento 1).
- Evidencia concreta: "corrí X y devolvió Y", no "debería funcionar".
- Decisiones importantes: Decisión / Evidencia / Alternativas / Motivo / Riesgo pendiente.
- Workarounds etiquetados como temporales, con la causa raíz y la solución definitiva nombradas.

## 5. Formato de escalamiento (cuando hay bloqueo)

Escalar con diagnóstico, no con "falló": síntoma exacto → evidencia recopilada →
hipótesis descartadas y por qué → qué se necesita para continuar → recomendación.
Escala cuando la corrección exige credenciales/permisos/pagos que no tienes, cuando dos
correcciones tienen consecuencias de negocio distintas, cuando el fallo reveló que el
requisito era incorrecto, o cuando tras iteraciones razonables no hay progreso.

## 6. Anti-patrones de herramientas (repaso rápido)

Opinar sobre código no leído; afirmar "funciona" sin ejecutar; responder de memoria
sobre APIs/precios/versiones; comando destructivo "para ver qué pasa"; 20 ediciones
manuales donde cabía un script; repetir una llamada fallida sin cambiar nada; subagentes
para tareas de dos pasos; imprimir o versionar credenciales.

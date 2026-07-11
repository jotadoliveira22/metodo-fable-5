# Selección de herramientas

Consultar cuando: dudes qué herramienta usar, o notes que estás usando una por costumbre.
Principio: **la herramienta se elige por la evidencia que necesitas, no por hábito**.
La pregunta previa siempre es: "¿qué necesito saber u obtener, y cuál es la forma más
barata y confiable de conseguirlo?".

## 1. Tabla evidencia → herramienta

| Necesito… | Herramienta | Regla |
|---|---|---|
| Saber qué hace un código | Lectura de archivos | Leer el archivo real completo o la sección relevante. Nunca opinar sobre código no leído. |
| Encontrar dónde está algo en un repo | Búsqueda (grep/glob) | Buscar por símbolo, no adivinar rutas. Varias búsquedas paralelas si son independientes. |
| Saber si algo funciona | Terminal / ejecución | Ejecutarlo. "Compila" o "los tipos pasan" no equivale a "funciona". |
| Información que cambia con el tiempo (APIs, precios, versiones, docs de terceros) | Búsqueda web / fetch | No responder de memoria; la memoria tiene fecha de corte. Citar la fuente consultada. |
| Comportamiento de una API externa | Llamada real (curl/script) con credenciales de prueba | La documentación puede estar desactualizada; la llamada real es la evidencia. |
| Estado de datos | Consulta a la base de datos / lectura del archivo de datos | Consultas de solo lectura primero; escrituras solo con respaldo o confirmación. |
| Verificar una UI o flujo web | Navegación/automatización de navegador | Solo cuando la inspección de código/respuestas HTTP no baste. |
| Transformación repetitiva o cálculo | Script desechable | Escribir un script antes que hacer 20 ediciones manuales propensas a error. |
| Confirmar que un cambio no rompe nada | Pruebas automatizadas del proyecto | Ejecutar las existentes antes y después; añadir una para el bug corregido. |
| Búsqueda amplia en territorio desconocido | Subagente de exploración | Solo si el barrido es grande y solo se necesita la conclusión, no los detalles. |
| Entender un problema conceptual | Análisis directo | No toda tarea necesita herramientas; no ejecutar por ejecutar. |
| Documento/informe entregable | Generación de archivos | Escribir el archivo real y verificar que se lee correctamente. |

## 2. Reglas transversales

1. **Inspección antes que suposición**: si el dato existe en un archivo, log o sistema
   accesible, obtenerlo de ahí en lugar de preguntarlo o asumirlo.
2. **Paralelizar lo independiente**: lecturas y búsquedas sin dependencias entre sí se
   lanzan juntas.
3. **Lo barato primero**: leer un archivo antes que clonar otro repo; un grep antes que un
   subagente; una llamada de prueba antes que instalar un SDK completo.
4. **Efectos colaterales bajo control**: comandos que mutan estado (instalar, borrar,
   migrar, desplegar) se ejecutan solo cuando la evidencia respalda esa acción específica,
   y en tareas críticas, con confirmación o plan de reversa.
5. **Credenciales**: nunca imprimirlas, loguearlas ni escribirlas en archivos versionados.
   Usar variables de entorno o gestores de secretos.
6. **Subagentes**: útiles para (a) exploración amplia de solo lectura, (b) probar un
   entregable "en frío" sin el contexto de su creación. Costosos y sin tu contexto:
   no delegarles lo que puedes hacer directo en menos pasos.
7. **Registrar lo relevante**: cuando una herramienta produce la evidencia clave
   (una salida, un error, un número), citarla en la entrega.

## 3. Anti-patrones

- Responder de memoria sobre versiones, precios o límites de una API en lugar de verificar.
- Editar un archivo sin haberlo leído.
- Correr un comando destructivo para "ver qué pasa".
- Usar el navegador para algo que un curl resuelve.
- Lanzar subagentes para tareas de dos pasos.
- Repetir la misma llamada fallida sin cambiar nada (ver error-recovery.md).

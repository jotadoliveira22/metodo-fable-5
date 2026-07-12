# Ejemplo: debugging de un fallo en producción

Solicitud del usuario: "La API devuelve 500 en `/orders` desde ayer. Arréglalo."

## Aplicación del método

**1. Comprensión.** Objetivo real: que `/orders` vuelva a funcionar y no reincida.
Entregable: corrección desplegable + causa raíz + prueba de regresión.
Dato clave dado: "desde ayer" → algo cambió ayer.

**2. Clasificación.** Producción degradada → **crítica**. Verificación completa y
revisión adversarial obligatorias; cambios en producción con plan de reversa.

**3. Éxito.** `GET /orders` devuelve 200 con datos correctos en los casos que hoy fallan;
una prueba cubre el caso; se explica la causa raíz.

**4–5. Evidencia antes de tocar nada.**
- Leer logs desde el PRIMER error: `TypeError: Cannot read property 'total' of null`
  en `orders/serializer.js:42`.
- ¿Qué cambió ayer? `git log --since=yesterday` → deploy que añadió "pedidos borrador".
- Leer `serializer.js:42`: asume que `order.invoice` siempre existe.
- Consulta de solo lectura: `SELECT count(*) FROM orders WHERE invoice_id IS NULL` → 17 filas,
  todas creadas después del deploy.

**Hipótesis** (probabilidad × costo de comprobar): los pedidos borrador no tienen factura
y el serializador no lo contempla. Comprobación barata: reproducir en local con un pedido
sin factura → 500 reproducido. Hipótesis confirmada.

**Pre-mortem (2 líneas) antes de escribir el fix**: "esta solución falla si hay OTROS
puntos del código que asumen `invoice` no nulo — arreglar solo el serializador movería el
500 a otro endpoint." Ese pre-mortem es el que motiva el grep de la revisión adversarial.

**6. Cambio mínimo.** Manejar `invoice == null` en el serializador (devolver `total: null`
o el subtotal según regla de negocio). NO se refactoriza el serializador entero.
Ambigüedad material (¿qué debe mostrar un borrador como total?) → si hay regla en el código
o especificación, seguirla; si no, decisión reversible (mostrar `null`) documentada como supuesto.

**7. Verificación.**
- Prueba nueva: serializar pedido sin factura → 200. Ejecutada: pasa.
- Suite existente completa: pasa (verificación negativa).
- Reproducción original en local: ya no falla.

**8. Revisión adversarial.** ¿Otros endpoints asumen `invoice` no nulo? → grep de
`\.invoice\.` en el repo → dos usos más; uno vulnerable al mismo fallo → se corrige y
se reporta. ¿Los 17 registros necesitan reparación? → no, son datos válidos (borradores).

**10. Entrega (con confianza graduada).**
- **Qué pasó** [verificado]: el deploy de ayer introdujo pedidos sin factura; el
  serializador asumía factura siempre presente — reproducido en local.
- **Qué se hizo** [verificado]: manejo de nulo en `serializer.js:42` y en
  `reports/summary.js:18` (mismo patrón); prueba de regresión añadida.
- **Evidencia** [verificado]: suite completa en verde; reproducción local corregida.
- **Supuesto** [asumido]: borradores muestran `total: null`; lo invalida cualquier regla
  de negocio que exija subtotal — confirmar con negocio.
- **Riesgo pendiente** [razonado]: consumidores del API que asuman `total` numérico
  podrían romperse; se deduce del cambio de contrato, no se verificó contra clientes reales.

## Errores que este ejemplo evita

- Parchear el síntoma (try/catch global) sin encontrar la causa.
- Cambiar varias cosas a la vez "por si acaso".
- Declarar corregido sin reproducir primero el fallo.
- Ignorar los otros dos puntos con el mismo patrón.

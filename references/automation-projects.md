# Reglas para automatizaciones e integraciones (PYMES incluidas)

Consultar cuando: la tarea es automatizar un proceso, integrar plataformas (CRM, correo,
hojas de cálculo, e-commerce, facturación, mensajería), o diseñar flujos con herramientas
tipo n8n/Make/Zapier o scripts propios.

## 1. Mapear antes de automatizar

1. **Proceso actual**: quién hace qué, con qué herramienta, cuántas veces, qué tarda,
   dónde se equivoca. Automatizar un proceso mal definido automatiza el error.
2. **Disparador (trigger)**: ¿qué inicia el flujo? (evento, webhook, horario, acción humana).
   ¿Con qué frecuencia real dispara? ¿Puede disparar dos veces por el mismo hecho?
3. **Entradas y salidas**: formato, campos obligatorios, volumen diario/mensual, casos raros
   reales (acentos, campos vacíos, adjuntos enormes).
4. **Sistema fuente y sistema destino**: cuál es la fuente de verdad de cada dato.
   Si dos sistemas pueden editar el mismo dato, definir quién gana.

## 2. Diseño robusto (mínimos no negociables)

- **Idempotencia**: ejecutar el flujo dos veces con la misma entrada NO debe duplicar
  efectos (dos facturas, dos correos). Usar claves únicas/IDs externos y comprobar
  existencia antes de crear.
- **Detección de duplicados**: definir la clave de deduplicación (email, ID de pedido,
  hash del contenido) antes de construir.
- **Reintentos**: para fallos transitorios de red/API, reintentos con espera creciente
  (backoff) y un máximo; distinguir errores reintentable (500, timeout) de no
  reintentables (400, validación).
- **Registro de errores**: cada ejecución fallida debe dejar rastro consultable — qué
  entrada, qué paso, qué error. Los fallos silenciosos son el peor resultado posible.
- **Trazabilidad**: poder responder "¿qué pasó con el pedido 123?" siguiendo un ID de
  extremo a extremo.
- **Cola de errores / recuperación manual**: los registros que fallan tras los reintentos
  van a un lugar visible (hoja, etiqueta, canal) con un procedimiento humano documentado
  para reprocesarlos.

## 3. APIs y límites

- Verificar límites de tasa (rate limits), cuotas y paginación de cada API ANTES de
  diseñar el flujo; consultarlos en la documentación vigente, no de memoria.
- Autenticación: preferir OAuth/API keys con alcance mínimo; renovar tokens automáticamente
  cuando aplique.
- Costos: estimar el costo mensual real (llamadas × precio, ejecuciones de la plataforma,
  almacenamiento) y decirlo al usuario ANTES de construir. Para PYMES el costo recurrente
  suele ser un criterio de diseño de primer orden.

## 4. Credenciales

- Nunca incrustar credenciales en el flujo, código o documentación.
- Usar el gestor de credenciales de la plataforma o variables de entorno.
- Alcance mínimo y una credencial por integración (para poder revocar sin romper todo).

## 5. Validación con casos reales

Antes de dar por terminada una automatización:

1. Ejecutarla con 3–5 casos reales (o copias realistas), incluyendo al menos un caso raro.
2. Verificar por conteo: N entradas → N salidas esperadas, 0 duplicados.
3. Probar el camino de error: forzar un fallo (credencial mala, campo vacío) y comprobar
   que se registra y no corrompe datos.
4. Probar la idempotencia: re-ejecutar el mismo caso y comprobar que no duplica.
5. Dejar el flujo apagado o en modo prueba hasta que el usuario apruebe encenderlo
   sobre datos reales.

## 6. Entrega y mantenimiento

Documentar SIEMPRE:

- Diagrama o descripción paso a paso del flujo (disparador → pasos → salidas).
- Sistemas conectados y qué credencial usa cada uno (nombre, no valor).
- Qué hacer cuando falla: dónde mirar los errores, cómo reprocesar manualmente.
- Límites conocidos: volumen máximo probado, supuestos sobre los datos.
- Costo estimado mensual y qué lo haría crecer.
- Responsable de mantenimiento y qué revisar periódicamente (tokens que caducan,
  cambios de API anunciados).

Para PYMES, la prueba final: **una persona no técnica del negocio debe poder saber si el
flujo funcionó hoy y a quién llamar si no.**

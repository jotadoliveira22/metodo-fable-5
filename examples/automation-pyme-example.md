# Ejemplo: automatización para una PYME

Solicitud del usuario: "Cada vez que alguien llena el formulario de mi web, quiero que
se cree el contacto en mi CRM y se le envíe un correo de bienvenida. Hoy lo hago a mano."

## Aplicación del método

**Entender.** Objetivo real: cero trabajo manual y cero leads perdidos.
Preguntas que SÍ valen una consulta (cambian el diseño): ¿qué formulario y qué CRM exactos?
¿volumen mensual aproximado? Todo lo demás se decide con supuestos documentados.

**Presupuesto por costo del error.** Fila 3 (afecta a terceros: correos a personas
reales). Encender el flujo sobre datos reales sube a fila 4 → aprobación del dueño
antes de ese paso.

**Evidencia de éxito.** Un envío del formulario crea exactamente UN contacto y UN correo; los fallos
quedan registrados y son reprocesables; el flujo queda documentado; costo mensual conocido.

**Mapa del proceso** (antes de construir):
- Disparador: webhook del formulario (mejor que sondeo por horario: inmediato y más barato).
- Fuente de verdad: el CRM para datos de contacto.
- Caso raro real: la misma persona llena el formulario dos veces.

**Verificación de APIs** (documentación vigente, no memoria): límites de tasa del CRM,
campos obligatorios del contacto, si el CRM soporta upsert por email, límites del proveedor
de correo. Costo estimado del flujo al volumen declarado → se informa ANTES de construir.

**Pre-mortem (2 líneas)**: "esto falla enviando el correo de bienvenida DOS veces a la
misma persona — el webhook del formulario puede re-entregar, y un correo duplicado a un
cliente real no se puede deshacer." Todo el diseño de idempotencia sale de ese pre-mortem.

**Diseño robusto (mínimos de domain-rules.md §2):**
- **Deduplicación/idempotencia**: clave = email normalizado (minúsculas, sin espacios).
  Buscar contacto antes de crear; si existe, actualizar y NO reenviar el correo de bienvenida.
- **Reintentos**: 3 con backoff para errores 5xx/timeout; los 4xx no se reintentan.
- **Cola de errores**: los envíos que fallan tras reintentos van a una hoja de cálculo
  visible con fecha, datos y error, y el dueño recibe un aviso.
- **Trazabilidad**: cada ejecución registra el email y el resultado de cada paso.
- **Credenciales**: en el gestor de la plataforma, alcance mínimo, una por sistema.

**Validación con casos reales** (flujo en modo prueba):
- 5 envíos de prueba, incluidos: email con mayúsculas, campo teléfono vacío, envío duplicado.
- Conteo: 5 entradas → 4 contactos (1 duplicado detectado) → 4 correos [verificado].
- Camino de error forzado (credencial inválida): el fallo queda en la cola de errores [verificado].
- Re-ejecución del mismo webhook: no duplica contacto ni correo [verificado].

**Auditar: revisión adversarial.** ¿Qué pasa si el CRM está caído 1 hora? → los reintentos cubren
cortes breves; cortes largos caen a la cola de errores reprocesable — se documenta el
procedimiento manual. ¿Consentimiento/datos personales? → el correo de bienvenida requiere
que el formulario informe del uso de datos; se señala al usuario como pendiente legal [asumido: que el aviso legal del formulario
existe — lo invalida no tenerlo, y eso debe resolverlo el dueño].

**Entrega (con confianza graduada).**
- Flujo activado en modo prueba, a la espera de aprobación del usuario para producción.
- Documento de operación: diagrama del flujo, dónde ver errores, cómo reprocesar,
  costo mensual estimado, qué revisar si cambia el CRM.
- Prueba final PYME: el dueño puede saber si el flujo funcionó hoy mirando una hoja,
  sin conocimientos técnicos.

## Errores que este ejemplo evita

- Construir sin conocer volumen ni costo mensual.
- Correos duplicados a clientes reales (idempotencia ausente).
- Fallos silenciosos que pierden leads sin que nadie lo note.
- Encender el flujo sobre datos reales sin aprobación del dueño.

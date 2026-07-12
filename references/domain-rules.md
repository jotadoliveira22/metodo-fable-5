# Reglas de dominio — mínimos no negociables

Consultar la sección que aplique; no cargar entero para tareas de fila 1–2.
El patrón completo de cada dominio está en `examples/` — el ejemplo enseña, esta página
solo fija los mínimos que no se negocian.

## 1. Software

- **Antes de editar**: identificar stack, comandos de dev/test/lint/build, y leer el
  código a modificar Y sus usos (quién lo llama). Respetar convenciones y librerías ya
  elegidas.
- **Al editar**: cambio mínimo; diff pequeño; una intención por commit; nada de
  reescrituras sin justificación aceptada; comentarios solo para restricciones que el
  código no puede expresar.
- **Verificación**, en orden según exista: lint → types → build → suite completa (no solo
  la prueba nueva) → **ejecutar el flujo real afectado**. Sin red de seguridad en el
  proyecto: verificar manualmente y declararlo.
- **Secretos**: nunca en código, logs, commits ni salidas; `.env.example` documenta
  nombres, jamás valores; secreto ya expuesto = avisar y rotar (limpiar el historial no
  basta). Tokens con alcance mínimo.
- **Dependencias**: ¿ya hay algo en el proyecto que lo hace? ¿mantenida, licencia, peso?
  Tras cambiarlas: reinstalar y correr pruebas.
- **Cambios incompatibles**: señalar SIEMPRE roturas de API/esquema/formato/env vars;
  migraciones con respaldo o reversa + orden de despliegue explícito; pasos operativos
  del usuario numerados en la entrega.
- **Revisión de código**: seguridad antes que estilo. Inyección, secretos o datos
  personales en logs, permisos excesivos = bloqueante, con ubicación exacta y corrección
  concreta; un PR con un bloqueante abierto no se aprueba, por limpio que sea lo demás.
- **Documentar un procedimiento**: extraerlo de la evidencia real (scripts, CI,
  historial), no de suposiciones; requisitos previos, pasos con comandos exactos,
  verificación de éxito, rollback y errores comunes. Los pasos no validados se marcan
  [asumido] — un procedimiento no probado es una hipótesis.

## 2. Automatizaciones e integraciones

- **Mapear antes de construir**: proceso actual, disparador (¿puede disparar dos veces
  por el mismo hecho?), entradas/salidas con volumen real, fuente de verdad por dato.
- **Idempotencia y deduplicación**: clave única definida ANTES de construir; ejecutar dos
  veces la misma entrada no duplica efectos (ni facturas ni correos). Comprobar existencia
  antes de crear.
- **Errores**: reintentos con backoff solo para 5xx/timeout (los 4xx no se reintentan);
  todo fallo deja rastro consultable; cola de recuperación manual visible con
  procedimiento humano documentado. El fallo silencioso es el peor resultado posible.
- **APIs y costos**: límites de tasa, cuotas y paginación verificados en documentación
  vigente (nunca de memoria); costo mensual estimado e informado ANTES de construir.
- **Credenciales**: gestor de la plataforma, alcance mínimo, una por integración.
- **Validación**: 3–5 casos reales incluyendo un raro + un duplicado + un fallo forzado;
  conciliar por conteos (N entradas → N salidas, 0 duplicados); flujo apagado o en modo
  prueba hasta aprobación del dueño.
- **Entrega PYME**: una persona no técnica debe poder saber si el flujo funcionó hoy y a
  quién llamar si no. Documentar: flujo, credenciales (nombres), qué hacer cuando falla,
  límites probados, costo, mantenimiento.

## 3. Decisiones de arquitectura

- Separar requisitos **reales** (en la solicitud o derivados de datos: usuarios, volumen,
  normativa) de **imaginados** ("algún día podríamos…"). Los imaginados no justifican
  complejidad; solo justifican no cerrar puertas gratis.
- Evaluar opciones serias contra: simplicidad, costo (hoy y a 10x), escalabilidad real,
  mantenibilidad, seguridad, lock-in, UX, velocidad, observabilidad, reversibilidad,
  conocimientos del equipo, operación. **Comunicar en la entrega las 3–4 filas que
  decidieron** — la matriz debe ser auditable, no privada.
- Desempates: gana la más simple que cumple los requisitos reales; entre equivalentes,
  la que el equipo sabe operar; las decisiones difíciles de revertir (BD, cloud, modelo
  de datos) merecen más análisis que las reversibles (una librería).
- Señales de sobrearquitectura: microservicios para equipos de 1–3 sin problema de escala
  demostrado; colas/K8s "por si acaso"; abstracciones para un solo caso; más tiempo en
  infraestructura que en el problema. Respuesta: versión simple + señal explícita
  (métrica/volumen/incidente) que justificaría evolucionar.
- La advertencia de desproporción se da UNA vez, con números y alternativa concreta.
  Si el usuario decide seguir con su elección, se le ayuda a hacerla lo mejor posible,
  sin sabotear ni volver a sermonear.
- Entregar siempre UNA recomendación con formato de decisión, no un catálogo neutro.

## 4. Recuperación operativa

**Deploy roto**: log desde el PRIMER error, no el último; diff contra el último estado
bueno (commits, deps, config); ¿lo desplegado es lo que crees? (hash/tag); comparar
entornos (versiones, env vars, secretos, permisos, arquitectura); producción degradada →
estabilizar primero (rollback PROPUESTO al usuario con plan de reversa; sin preguntar
solo si delegó esa autoridad); documentar síntoma/causa/corrección/prevención.

**Automatización rota**: 1) contener — ¿sigue produciendo daño? detener si sí;
2) alcance con datos — desde cuándo, cuántos registros; 3) clasificar — procesados bien /
mal / no procesados; 4) corregir la causa ANTES de reprocesar; 5) reprocesar SOLO lo
pendiente con idempotencia garantizada; 6) conciliar por conteos; 7) confirmar con el
usuario antes de efectos irreversibles hacia terceros (facturas, correos);
8) añadir la alerta que habría detectado el fallo en horas, no en días.

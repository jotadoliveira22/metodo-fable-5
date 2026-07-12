# Ejemplo: arquitectura para un MVP

Solicitud del usuario: "Quiero un MVP de una app para que los clientes de mi gimnasio
reserven clases. ¿Qué arquitectura uso? Tengo poco presupuesto y soy el único desarrollador."

## Aplicación del método

**Entender.** Objetivo real: validar el negocio rápido y barato, no construir una
plataforma. Restricciones dadas: presupuesto bajo, equipo de 1. Entregable: recomendación
de arquitectura accionable, no un catálogo de opciones.

**Presupuesto por costo del error.** Fila 2 (si está mal, cuesta retrabajo: aún no hay
producción ni datos). La revisión adversarial no es obligatoria en fila 2, pero una
decisión de arquitectura es cara de revertir — se hace igual.

**Evidencia de éxito.** Una recomendación concreta con stack, costo estimado, justificación contra
la matriz, y señales explícitas de cuándo evolucionar.

**Requisitos reales vs. imaginados.**
- Reales: reservas con cupo, vista de horario, aviso al cliente, ~200 socios, un gimnasio.
- Imaginados (no justifican complejidad hoy): "miles de gimnasios", "app nativa en las
  dos tiendas", "IA de recomendaciones".

**Matriz aplicada** (opciones serias: A. monolito web + BD gestionada;
B. microservicios en Kubernetes; C. no-code tipo Airtable/Softr).

| Criterio | A. Monolito | B. Microservicios | C. No-code |
|---|---|---|---|
| Simplicidad | Alta | Baja | Alta |
| Costo mensual | ~5–20 USD | >100 USD + tiempo | 20–60 USD |
| Velocidad a producción | Semanas | Meses | Días |
| Equipo de 1 lo opera | Sí | No realista | Sí |
| Reversibilidad | Media | Baja | Media (export de datos) |
| Personalización futura | Alta | Alta | Baja |

**Decisión (formato visible):**
- **Decisión**: monolito web (un framework full-stack con SSR), PostgreSQL gestionado,
  despliegue en PaaS, notificaciones por WhatsApp/email vía API de terceros. PWA en lugar
  de app nativa.
- **Evidencia**: restricciones de presupuesto y equipo de 1 [verificado: están en la
  solicitud]; decenas de reservas/día [razonado: se deduce de ~200 socios — lo invalidaría
  un volumen real mucho mayor].
- **Alternativas**: microservicios (descartado: complejidad operativa sin problema de
  escala demostrado); no-code (segundo mejor: más rápido, pero limita la lógica de cupos
  y penaliza la evolución — se ofrece como opción si el usuario prioriza días sobre semanas).
- **Motivo**: la más simple que cumple los requisitos reales y no cierra puertas.
- **Riesgo pendiente**: la elección de PaaS concreta depende de la región y del stack que
  el usuario ya conozca — se pregunta solo eso, no todo.

**Señales para evolucionar** (se entregan junto con la decisión): >2.000 socios o multi-sede
→ separar la BD y añadir cache; necesidad real de notificaciones push nativas → evaluar app.

**Pre-mortem de la recomendación**: "esto falla si el único desarrollador no conoce el
stack elegido y quema el presupuesto aprendiendo" — por eso el criterio Equipo decide el
empate entre monolito y BaaS, y por eso la única pregunta al usuario es sobre su stack.

**Prueba de los 5 minutos**: 5 minutos después de leer esto, el usuario va a elegir y
ponerse a construir — necesita UNA recomendación con primer paso concreto, no un catálogo
para seguir investigando.

**Auditar: revisión adversarial.** ¿Qué falla primero? Los avisos dependen de una API externa
(WhatsApp) → el MVP debe funcionar aunque el aviso falle (la reserva se guarda igual y
hay reintento). ¿Datos personales? Sí → HTTPS, contraseñas hasheadas, borrado a petición.

## Errores que este ejemplo evita

- Recomendar la arquitectura "impresionante" en lugar de la adecuada.
- Entregar un catálogo neutro de opciones sin recomendación.
- Diseñar para requisitos imaginados.
- Ignorar quién opera el sistema y cuánto cuesta al mes.

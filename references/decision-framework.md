# Marco de decisión: clasificación, descomposición y arquitectura

Consultar cuando: la tarea es compleja o crítica, hay decisiones de diseño con alternativas
reales, los requisitos son ambiguos, o hay que decidir entre solución simple y elaborada.

## 1. Árbol de clasificación de la tarea

```
¿La acción es irreversible, toca producción, dinero, datos de clientes o seguridad?
├─ SÍ → CRÍTICA: plan explícito + confirmación del usuario en pasos irreversibles
│        + verificación completa + revisión adversarial obligatoria.
└─ NO → ¿Requiere decisiones de diseño o toca varios sistemas/archivos?
    ├─ SÍ → COMPLEJA: plan con fases, entregables parciales, pruebas + casos límite.
    └─ NO → ¿Más de un paso o algún efecto colateral posible?
        ├─ SÍ → MODERADA: lista breve de pasos, verificar el cambio ejecutándolo.
        └─ NO → SIMPLE: resolver directamente, inspección del resultado.
```

Heurísticas operativas:
- Ante la duda entre dos niveles, elegir el superior.
- Una tarea "simple" que falla dos veces se reclasifica como moderada (algo no se entendió).
- "Crítica" no depende del tamaño del cambio: borrar una línea puede ser crítico.

## 2. Árbol de manejo de ambigüedad

```
¿La ambigüedad cambia materialmente el resultado o el costo?
├─ NO → Tomar la opción más razonable y REVERSIBLE. Documentar el supuesto en la entrega.
└─ SÍ → ¿La respuesta está en el contexto, los archivos o la documentación disponible?
    ├─ SÍ → Buscarla ahí. No preguntar lo que se puede verificar.
    └─ NO → ¿Puedo entregar variantes o una parte útil sin resolver la ambigüedad?
        ├─ SÍ → Entregar lo verificable + pregunta concreta con opciones.
        └─ NO → Preguntar al usuario UNA vez, con opciones cerradas y una recomendación.
```

Errores a evitar: preguntar en cascada (una pregunta por mensaje), preguntar lo ya respondido,
bloquear todo el trabajo por una ambigüedad que solo afecta a una parte.

## 3. Descomposición de problemas grandes

Procedimiento:

1. Enunciar el objetivo final en una frase verificable.
2. Identificar los entregables parciales (cada uno debe poder mostrarse/probarse solo).
3. Ordenarlos por dependencias: qué bloquea a qué. Hacer primero lo que reduce más incertidumbre
   (heurística: valida temprano la parte que no sabes si es posible).
4. Marcar los puntos de decisión (dónde hay alternativas) y los puntos de validación
   (dónde se comprueba antes de seguir).
5. Estimar qué fases puede necesitar intervención del usuario y avisarlo desde el principio.

Anti-patrones:
- Plan de 20 pasos para una tarea de 3 (el plan no es el entregable).
- Fases que terminan en "avanzar en X" en lugar de algo comprobable.
- Descomponer por tecnología ("hacer el backend", "hacer el frontend") en lugar de por
  valor verificable ("el usuario puede registrarse de punta a punta").

## 4. Matriz de decisión arquitectónica

Para cada opción seria, puntuar (alto/medio/bajo) contra los criterios siguientes y
comunicar en la entrega las 3–4 filas que decidieron la elección — la matriz debe ser
auditable en la respuesta, no un ejercicio privado:

| Criterio | Pregunta clave |
|---|---|
| Simplicidad | ¿Cuántas piezas móviles añade? |
| Costo | ¿Cuánto cuesta al mes y al crecer 10x? |
| Escalabilidad | ¿Aguanta la carga real esperada (no la fantaseada)? |
| Mantenibilidad | ¿Quién lo mantiene y con qué conocimientos? |
| Seguridad | ¿Amplía la superficie de ataque? ¿Maneja secretos/datos personales? |
| Vendor lock-in | ¿Qué cuesta salir del proveedor? |
| UX | ¿Mejora o degrada la experiencia real del usuario? |
| Velocidad | ¿Cuánto tarda en estar en producción? |
| Observabilidad | ¿Se puede saber qué pasa cuando falla? |
| Reversibilidad | ¿Se puede deshacer la decisión en semanas o queda cementada? |
| Equipo | ¿El equipo ya conoce esta tecnología? |
| Operación | ¿Quién lo opera a las 3 a.m.? |

Reglas de desempate:
1. Gana la opción más simple que cumple los requisitos REALES de hoy y no bloquea los de mañana.
2. Requisito real = está en la solicitud o se deriva de datos concretos (usuarios, volumen,
   normativa). Requisito imaginado = "algún día podríamos necesitar...". Los imaginados no
   justifican complejidad; solo justifican no cerrar puertas gratis.
3. Entre dos opciones equivalentes, gana la que el equipo ya sabe operar.
4. Las decisiones difíciles de revertir (base de datos, proveedor cloud, modelo de datos)
   merecen más análisis; las reversibles (una librería, un formato interno) se deciden rápido.

## 5. Sobrearquitectura: señales de alarma

- Microservicios para un equipo de 1–3 personas sin problema de escala demostrado.
- Colas, caches o Kubernetes "por si acaso", sin métrica que lo justifique.
- Abstracciones para un solo caso de uso ("por si añadimos otro proveedor").
- Más tiempo configurando infraestructura que resolviendo el problema del usuario.

Respuesta correcta: proponer la versión simple, y listar explícitamente qué señal futura
(métrica, volumen, incidente) justificaría evolucionar a la versión compleja.

## 6. Formato de registro de decisión (para entregas)

- **Decisión**: …
- **Evidencia**: …
- **Alternativas consideradas**: …
- **Motivo**: …
- **Riesgo o limitación pendiente**: …

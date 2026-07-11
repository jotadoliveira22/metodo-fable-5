# Reglas para proyectos de software

Consultar cuando: la tarea toca código, repositorios, builds, dependencias o despliegues.

## 1. Antes de editar

1. **Inspeccionar el repositorio**: estructura, README, archivos de configuración
   (package.json, pyproject.toml, go.mod, docker-compose, CI). Identificar stack,
   versión del lenguaje, gestor de paquetes y arquitectura general.
2. **Identificar cómo se ejecuta y se prueba**: comandos de dev, test, lint, build.
   Si existen, son la vara de medir de cualquier cambio.
3. **Leer el código que se va a modificar y sus usos**: quién llama a la función,
   qué depende del módulo, qué pruebas lo cubren.
4. **Respetar convenciones existentes**: estilo, nombres, estructura de carpetas,
   librerías ya elegidas. No introducir una librería nueva si el proyecto ya resuelve
   eso con otra.

## 2. Al editar

- **Cambio mínimo necesario**: resolver el problema sin refactorizar de paso lo que
  no se pidió. Un diff pequeño es más fácil de revisar, revertir y confiar.
- **No romper lo que funciona**: si un cambio obliga a tocar código no relacionado,
  detenerse y reconsiderar el enfoque.
- **Nada de reescrituras completas sin justificación explícita** aceptada por el usuario.
- **Comentarios**: solo para restricciones que el código no puede expresar; seguir la
  densidad de comentarios del proyecto.
- **Trazabilidad**: commits pequeños y descriptivos; una intención por commit.

## 3. Secretos y configuración

- Nunca escribir credenciales, tokens o claves en código, logs, commits o salidas.
- Variables de entorno: documentarlas (nombre, propósito, ejemplo ficticio) en
  `.env.example` o README; nunca versionar `.env` reales.
- Si se detecta un secreto ya expuesto en el repo: avisar al usuario de inmediato,
  recomendarle rotarlo (quitarlo del historial no basta: ya estuvo público).
- Validar permisos mínimos: tokens con el menor alcance que funcione.

## 4. Dependencias

- Antes de añadir una: ¿ya hay algo en el proyecto que lo hace? ¿está mantenida?
  ¿su licencia es compatible? ¿cuánto pesa lo que aporta?
- Fijar versiones según la convención del proyecto (lockfile).
- Tras cambiar dependencias: reinstalar y correr las pruebas, no asumir compatibilidad.

## 5. Verificación de cambios de código

En orden, según exista en el proyecto:

1. Lint y formato (`lint`, `fmt`).
2. Type checking si el lenguaje lo tiene.
3. Build.
4. Pruebas: las existentes deben seguir pasando; el bug corregido merece una prueba nueva.
5. **Ejecutar la aplicación real** cuando sea posible y comprobar el flujo afectado
   de punta a punta — pasar los tipos no equivale a funcionar.

Si el proyecto no tiene pruebas ni lint: verificar ejecutando manualmente el flujo
afectado y decir explícitamente en la entrega que el proyecto carece de red de seguridad.

## 6. Migraciones y cambios incompatibles

- Señalar SIEMPRE cuando un cambio rompe compatibilidad: API pública, esquema de datos,
  formato de archivo, variables de entorno nuevas u obligatorias.
- Para migraciones de datos: script reversible o respaldo previo + orden de despliegue
  explícito (¿primero el código o primero la migración?).
- Documentar en la entrega los pasos operativos exactos que el usuario debe ejecutar.

## 7. Solución temporal vs. definitiva

- Etiquetar explícitamente los workarounds como temporales, con su deuda asociada
  ("esto desbloquea, pero la causa raíz es X y conviene arreglarla haciendo Y").
- No presentar un parche como solución definitiva si la causa raíz sigue viva.

## 8. Entrega en proyectos de software

Incluir: qué archivos cambiaron y por qué, comandos para ejecutar/probar, evidencia de
verificación (salida de tests/build), pasos operativos pendientes (migraciones, variables
de entorno, despliegue), y riesgos conocidos.

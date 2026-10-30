# Investigación: Notificaciones de deducciones

Fecha: 2026-10-30

## Decisión: Disparar flujos desde los eventos guardados de Dataverse

**Razonamiento**: Usar el desencadenador de Dataverse “Cuando se agrega,
modifica o elimina una fila”. La creación usa el cambio `Added`. La captura
SAP usa `Modified` con la columna escalar del número SAP en **Select columns**.
Dataverse ejecuta los desencadenadores de forma asíncrona después de guardar,
por lo que los fallos de correo no revierten la fila.

**Alternativas consideradas**: Llamar un flujo sincrónicamente desde el
formulario (acoplaría el guardado al servicio de correo); disparar con cualquier
actualización de la fila (generaría ejecuciones irrelevantes). Se descartan
para conservar el registro y limitar cada evento.

**Límites**: Select columns dispara si la columna forma parte de la solicitud
de actualización, incluso si se vuelve a escribir el mismo valor. No conserva
por sí solo el valor previo para distinguir una transición vacío-a-valor. El
flujo de SAP requiere tanto filtro de columnas como un indicador de
notificación procesada y condición sobre el valor actual.

**Fuente**:
[Desencadenador de Dataverse para agregar, modificar o eliminar filas](https://learn.microsoft.com/power-automate/dataverse/create-update-delete-trigger#filter-columns)

## Decisión: Separar los flujos por tipo de evento

**Razonamiento**: Mantener un flujo de creación y otro de modificación SAP,
ambos en la solución `AIMA`. Aíslan las condiciones, los destinatarios y el
contenido de cada correo. El flujo de modificación escucha solamente la
columna SAP y no modifica esa columna.

**Alternativas consideradas**: Un solo flujo con ramas condicionales. Se
descarta porque mezcla dos destinatarios/eventos y dificulta comprobar que un
cambio general no cause el correo de SAP.

**Anti-bucle**: El flujo de actualización no escribe en la columna SAP. Si
actualiza el indicador técnico en la misma fila, el filtro de columnas limitado
a SAP evita que ese cambio dispare otra ejecución. La guía oficial advierte
contra los flujos que se disparan a sí mismos y recomienda condiciones y
terminación explícitas.

**Fuentes**:
[Desencadenador de Dataverse](https://learn.microsoft.com/power-automate/dataverse/create-update-delete-trigger)
[Evitar patrones de bucle infinito en flujos](https://learn.microsoft.com/power-automate/guidance/coding-guidelines/avoid-anti-patterns#avoid-infinite-loops)

## Decisión: Idempotencia de la captura inicial de SAP

**Razonamiento**: Agregar a la tabla Deducción un indicador técnico de aviso
enviado, con prefijo del publisher `AIMA`. Inicializarlo durante la puesta en
marcha a enviado para filas históricas que ya contienen SAP y pendiente para
filas sin SAP. El flujo requiere SAP no vacío e indicador pendiente; marca el
indicador enviado después del correo. La concurrencia del flujo de SAP debe
limitarse para reducir carreras de dos actualizaciones cercanas.

**Alternativas consideradas**: Confiar solo en Select columns o en el valor
posterior a la actualización. Se descartan porque no distinguen cambios de
valor existente ni repeticiones del mismo valor.

**Límite**: El envío de correo y la actualización del indicador son dos
operaciones, no una transacción atómica. Un envío exitoso seguido de error al
marcar el indicador podría causar un correo repetido al reintentar. El historial
de ejecuciones deberá guiar cualquier reintento manual. No se promete entrega
exactamente una vez.

**Fuente**:
[Desencadenador de Dataverse: columnas seleccionadas y actualizaciones](https://learn.microsoft.com/power-automate/dataverse/create-update-delete-trigger#filter-columns)

## Decisión: Resolver destinatarios desde los roles de Dataverse

**Razonamiento**: Consultar los usuarios activos que pertenecen al rol
destinatario y usar su correo interno corporativo. La entidad Role documenta
una relación N:N de roles a usuarios; `systemuser.isdisabled` distingue cuentas
deshabilitadas. Antes de implementar debe verificarse si el ambiente asigna
roles a usuarios directamente, por equipos, o por ambos mecanismos, y resolver
la pertenencia efectiva en consecuencia.

**Alternativas consideradas**: Direcciones codificadas en el flujo o listas
manuales fuera de Dataverse. Se descartan porque no se mantienen
automáticamente alineadas con los roles y usuarios activos.

**Fuentes**:
[Entidad Role de Dataverse](https://learn.microsoft.com/power-apps/developer/data-platform/reference/entities/role)
[Entidad SystemUser de Dataverse](https://learn.microsoft.com/power-apps/developer/data-platform/reference/entities/systemuser)

## Decisión: Construir enlaces model-driven seguros

**Razonamiento**: Construir el enlace de registro con la URL del entorno,
`etn` (nombre lógico de la tabla), `pagetype=entityrecord` y el GUID codificado.
Agregar `appid` solo si se confirma la aplicación model-driven destino. La
seguridad del registro continúa en Dataverse; un enlace no evita las
autorizaciones por rol.

**Alternativas consideradas**: Enlazar solamente a la lista de deducciones o
usar un código de tipo de tabla variable. Se descartan porque el requisito pide
abrir el registro específico y la documentación recomienda `etn` en enlaces
construidos.

**Fuente**:
[Abrir formularios, vistas, cuadros de diálogo e informes con una URL](https://learn.microsoft.com/power-apps/developer/model-driven-apps/open-forms-views-dialogs-reports-url#url-addressable-forms-and-views)

## Decisión: Usar conectores corporativos compatibles con DLP y revisar fallos

**Razonamiento**: El administrador del ambiente debe confirmar que Dataverse y
el conector de correo aprobado están en grupos compatibles de la política DLP y
proveer una conexión corporativa administrada. No se debe elegir una identidad
de remitente por suposición. Los fallos se consultan en el historial de
ejecuciones por los propietarios/co-propietarios autorizados; los reintentos
de errores transitorios se configuran en la acción de correo. Las alertas
automáticas de error no cubren todos los fallos, así que no sustituyen el
historial.

**Alternativas consideradas**: Conector personal sin verificar DLP o una tabla
de errores nueva. Se descartan porque la constitución exige conectores aprobados
y no autoriza una entidad de negocio adicional para el registro de fallos.

**Fuentes**:
[Políticas de prevención de pérdida de datos](https://learn.microsoft.com/power-platform/admin/prevent-data-loss)
[Notificaciones de error de flujos](https://learn.microsoft.com/power-automate/understand-flow-failure-notifications)
[Manejo de errores y reintentos en flujos](https://learn.microsoft.com/power-automate/guidance/coding-guidelines/error-handling)

## Decisiones de ambiente pendientes

- Conector de correo corporativo aprobado y clasificación DLP efectiva.
- Identidad del remitente/conexión compartida y propietarios autorizados de
  ejecución.
- Asignación directa de roles a usuarios frente a asignación por equipos.
- URL del ambiente, nombre lógico de la tabla y aplicación `appid`.
- Tamaño de los grupos de destinatarios, para validar límites aplicables del
  correo corporativo.

Estas verificaciones no cambian la spec ni se resuelven sin acceso y aprobación
del ambiente destino.

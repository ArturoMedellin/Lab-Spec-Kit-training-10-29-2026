# Modelo de datos: Notificaciones de deducciones

## Tabla existente: Deducción

La función reutiliza la tabla Deducción definida en
[001-registro-deducciones/data-model.md](../001-registro-deducciones/data-model.md).
No agrega una entidad de negocio nueva.

### Atributos usados por notificaciones

| Campo conceptual | Origen | Uso en notificación |
|---|---|---|
| Identificador de deducción | Identificador Dataverse | Construir enlace directo al registro. |
| Cliente | Deducción | Mostrar el cliente. |
| Origen | Deducción | Mostrar Pago, Ajuste AR o Netting. |
| Referencia | Deducción | Mostrar cheque, ajuste o transacción Netting. |
| Monto | Deducción | Mostrar el monto en USD. |
| Centro de costo UD | Deducción | Mostrar el valor o “Sin asignar”. |
| Tipo de transacción | Deducción | Mostrar F-30 o F-28. |
| Tipo de documento | Deducción | Mostrar CP. |
| Estado | Deducción | Mostrar el estado disponible. |
| Fecha de registro | Deducción | Mostrar cuándo se creó la deducción. |
| Registrado por | Deducción | Mostrar la identidad que creó el registro. |
| Número de documento SAP | Deducción | Mostrar “Pendiente” en la creación si está vacío; en su alta, identificar el nuevo valor. |
| Modificado por / fecha de modificación | Auditoría estándar Dataverse | Mostrar quién agregó el número SAP y cuándo, sujeto a verificación en el ambiente. |

### Atributo técnico nuevo propuesto

| Campo conceptual | Requerido | Regla |
|---|---:|---|
| Notificación SAP enviada | Sí | Columna booleana técnica en Deducción, con nombre de esquema generado bajo el prefijo `AIMA`. Predeterminado falso para registros nuevos sin SAP. Solo la automatización administra el campo. Se vuelve verdadero después de enviar la notificación de alta inicial de SAP. Para filas existentes, inicializar verdadero si SAP ya tiene valor y falso si está vacío. |

Este indicador pertenece a la entidad principal, no representa una entidad de
negocio independiente y no debe exponerse como campo editable en los
formularios de usuario.

## Eventos y relaciones

- Al crear una fila Deducción por Aprobador UA, el evento genera una
  notificación al rol Revisor UD.
- Cuando el campo SAP pasa inicialmente de vacío a un valor, el evento genera
  una notificación al rol Asociado Cash App y actualiza el indicador.
- Los avisos no modifican permisos, estado ni el contenido de negocio de la
  deducción, excepto el indicador técnico de procesamiento.

## Validaciones e invariantes

1. El aviso de creación solo puede ocurrir después de que la fila exista.
2. El aviso SAP requiere un número SAP no vacío y el indicador en pendiente.
3. Cambios a otros campos no activan el flujo SAP.
4. Filas anteriores que ya tienen número SAP deben marcarse como ya notificadas
   antes de habilitar el flujo, para no generar avisos históricos.
5. Los destinatarios se filtran a usuarios activos con dirección de correo
   corporativa y pertenencia efectiva al rol destinatario.
6. Los datos del correo no alteran las reglas de acceso Dataverse del registro.

## Historial de notificaciones

No se crea una tabla de notificación ni de auditoría para esta función. El
historial de ejecuciones de Power Automate es el mecanismo operativo para
consultar entregas fallidas y orientar los reintentos por los responsables
autorizados del flujo. La aprobación del plan debe confirmar que la retención
del historial cumple la necesidad operativa; no se especificó un período de
retención.

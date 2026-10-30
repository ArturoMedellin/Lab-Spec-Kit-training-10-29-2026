# Contrato de notificaciones por correo

Este documento define los eventos y el contenido observable de los correos.
Los nombres lógicos de tabla/columnas, la URL del ambiente y la app concreta
deben confirmarse en la solución destino.

## Evento A: nueva deducción

**Condición**: La fila Deducción se creó correctamente por Aprobador UA.

**Destinatarios**: Usuarios activos del rol Revisor UD con dirección de correo
corporativa. Debe resolverse la pertenencia efectiva, incluyendo la modalidad
de asignación de roles utilizada en el ambiente.

**Asunto**: `Nueva deducción registrada: {identificador o cliente}`

**Contenido mínimo**:

- Evento: nueva deducción registrada.
- Enlace directo a la fila Deducción correspondiente.
- Cliente, origen, referencia correspondiente, monto en USD, centro de costo
  UD, tipo de transacción, tipo de documento, estado y fecha de registro.
- Identidad de quien registró la deducción.
- Número de documento SAP, o el texto `Pendiente` cuando esté vacío.

## Evento B: número SAP agregado

**Condición**: Un Revisor UD guardó por primera vez un valor no vacío en el
campo SAP de una deducción y la notificación está pendiente.

**Destinatarios**: Usuarios activos del rol Asociado Cash App con dirección de
correo corporativa. Debe resolverse la pertenencia efectiva, incluyendo la
modalidad de asignación de roles utilizada en el ambiente.

**Asunto**: `Número SAP agregado a deducción: {identificador o cliente}`

**Contenido mínimo**:

- Evento: número de documento SAP agregado.
- Enlace directo a la fila Deducción correspondiente.
- Número SAP nuevo, identidad del Revisor UD que lo agregó y fecha/hora del
  cambio.
- Cliente, origen, referencia correspondiente, monto en USD, centro de costo
  UD, tipo de transacción, tipo de documento, estado y fecha de registro.
- Identidad de quien registró originalmente la deducción.

## Reglas comunes

- Los correos y asuntos estarán en español.
- Los valores opcionales vacíos se presentan como `Sin asignar` o `Pendiente`;
  no se omite silenciosamente un campo que la spec exige mostrar.
- El enlace abre la fila dentro de la aplicación model-driven autorizada. El
  acceso sigue sujeto a las credenciales y permisos Dataverse del destinatario.
- Los correos solo se envían tras guardar el evento correspondiente.
- Un fallo de correo no revierte la fila; el responsable autorizado consulta
  la ejecución fallida en el historial del flujo.
- Los dos eventos usarán conectores corporativos aprobados por DLP. No se
  enviará información a servicios o destinatarios externos no autorizados.

## Semántica de repetición

- Un cambio de campos distinto del número SAP no genera el Evento B.
- Tras el primer envío exitoso del Evento B, el indicador técnico impide un
  segundo aviso.
- Si se entrega el correo pero falla la actualización del indicador, puede
  ocurrir un duplicado al reintentar manualmente; se debe revisar el historial
  antes del reenvío. La plataforma no ofrece atomicidad entre ambas acciones
  dentro de esta especificación.

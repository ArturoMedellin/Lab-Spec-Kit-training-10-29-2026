# Guía de preparación y validación: Notificaciones de deducciones

## Prerrequisitos

1. Ambiente Power Platform no productivo autorizado para la prueba y sujeto a
   las políticas DLP vigentes.
2. Solución única `AIMA` y aplicación model-driven con la tabla Deducción.
3. Flujos instalados en la solución: uno para nueva fila y uno para captura
   inicial de SAP.
4. Confirmación del conector de correo y la conexión/identidad de remitente
   corporativa aprobados por DLP; los conectores Dataverse y correo deben poder
   coexistir en la política.
5. Cuentas de prueba con los roles Aprobador UA, Revisor UD y Asociado Cash App,
   con buzones controlados y acceso Dataverse esperado.
6. Destinatarios de prueba identificados a partir de la asignación efectiva de
   roles del ambiente. No enviar correos de prueba a direcciones reales sin
   autorización.
7. Confirmar URL del entorno, nombre lógico de la tabla y `appid` de la app
   destino.
8. Inicializar el indicador técnico de SAP: filas históricas con SAP como
   notificado; filas sin SAP como pendiente.

No se requieren credenciales de SAP/TNET/TradePro, conexión a esos sistemas ni
secretos en este repositorio.

## Validación de aceptación manual

| Caso | Acción | Resultado esperado |
|---|---|---|
| Alta y aviso | Aprobador UA crea una deducción válida con todos los campos aplicables. | La fila se guarda y, tras el evento Added, el Revisor UD de prueba recibe un correo en español con el resumen y el enlace directo. |
| Destinatarios del rol | Repetir con miembros directos y, si el ambiente los usa, miembros de equipos con el rol. | Reciben solo los destinatarios activos del rol esperado; los usuarios deshabilitados o sin correo se excluyen. |
| Fila no guardada | Provocar una validación fallida antes del guardado. | No se crea una fila ni se envía el correo de alta. |
| Enlace y seguridad | Abrir el enlace desde una cuenta con permiso y desde una cuenta sin permiso. | La cuenta autorizada ve el registro; la no autorizada no obtiene acceso por el enlace. |
| SAP agregado por primera vez | Revisor UD agrega SAP a una fila cuyo campo está vacío. | El Asociado Cash App recibe un correo con el nuevo SAP, autor y fecha del cambio; el indicador queda como enviado. |
| Otros campos modificados | Cambiar un campo distinto de SAP tras la captura. | El flujo de SAP no se dispara por la actualización que no incluye la columna SAP. |
| SAP ya notificado | Cambiar/regrabar el campo SAP o hacer una actualización posterior. | El indicador impide un segundo aviso; registrar si se requiere tratamiento especial al corregir un SAP ya enviado. |
| Registro histórico | Intentar actualizar un registro que ya tenía SAP antes de activar los flujos. | El indicador precargado evita una notificación histórica no solicitada. |
| Entrega fallida | En el ambiente de prueba, hacer que falle la acción de correo con una conexión de prueba no válida. | El guardado original permanece; la ejecución queda visible para los dueños/co-dueños autorizados y el fallo no se pierde. |
| DLP y solución | Revisar los componentes del flujo y las conexiones después de importarlos. | Ambos flujos están en la solución `AIMA`, usan solo conectores permitidos por la política DLP y no contienen credenciales en archivos del repositorio. |
| Sin integración externa | Revisar conexiones, acciones y resultados de ejecución. | Ningún flujo invoca SAP, TNET ni TradePro ni realiza contabilizaciones. |

## Aprobación

Registrar resultado, ambiente, cuentas/roles probados, evidencias de las
ejecuciones y cualquier fallo. La guía no sustituye la aprobación humana del
diseño y del ensayo antes de continuar a implementación o liberación.

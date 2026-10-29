# Guía de preparación y validación

## Preparación

1. Confirmar que el entorno Power Platform de destino está aprobado y cumple las políticas DLP vigentes.
2. Confirmar la solución Dataverse con publisher prefix `AIMA` y la aplicación model-driven correspondiente.
3. Asignar cuentas Gruma de prueba a los roles Asociado Cash App, Aprobador UA y Revisor UD.
4. Preparar clientes de prueba: DOT Foods, Safeway, Gordon Food Service, un cliente distinto y variantes de mayúsculas/espacios al inicio o final.
5. Preparar referencias de prueba para Pago, Ajuste AR y Netting.

No se requieren credenciales de SAP/TNET/TradePro, flujos, conexiones externas ni datos secretos.

## Validación manual de aceptación

| Caso | Acción | Resultado esperado |
|---|---|---|
| Registro mínimo | Aprobador UA registra una deducción con cliente, origen, referencia, monto y centro de costo cuando aplique. | Se guarda en menos de un minuto, con estado Nueva, fecha de registro y tipo CP. |
| Cliente F-30 | Registrar con DOT Foods, Safeway o Gordon Food Service. | Se indica F-30. |
| Otro cliente | Registrar con un cliente distinto a los tres anteriores. | Se indica F-28 y se requiere centro de costo UD. |
| Normalización de cliente | Probar los tres clientes especiales con diferencias de mayúsculas o espacios al inicio/final. | Se clasifican como F-30; otros nombres se clasifican como F-28. |
| Campos obligatorios | Intentar guardar sin cliente, origen o monto. | Se impide guardar y se identifica el dato faltante. |
| Referencia por origen | Probar Pago, Ajuste AR y Netting con su referencia correspondiente. | Se exige número de cheque, número de ajuste o número de transacción Netting, respectivamente. |
| Posible duplicado | Repetir cliente, origen y referencia de un registro existente. | Se muestra advertencia y el usuario aún puede guardar. |
| Consulta | Iniciar sesión con cada uno de los tres roles. | Cada rol puede consultar todos los registros. |
| Deducciones abiertas | Consultar registros abiertos, incluyendo datos de prueba con un registro cerrado. | Los abiertos se muestran y el cerrado no se presenta como abierto; el cambio de estado/cierre no se realiza en esta feature. |
| Creación restringida | Intentar crear como Asociado Cash App o Revisor UD. | No pueden crear; Aprobador UA sí puede. |
| Modificación restringida | Intentar modificar como Asociado Cash App, Aprobador UA y Revisor UD. | Solo Revisor UD modifica los datos autorizados; no se implementan cambio de estado ni cierre. |
| Documento SAP | Revisor UD captura el número después de contabilizar fuera del sistema. | El dato queda guardado manualmente; no se produce conexión ni contabilización externa. |

## Aprobación

Una persona responsable debe revisar los resultados, confirmar los permisos y aprobar el diseño antes de continuar a la descomposición de tareas o implementación. Esta guía no sustituye la aprobación de la solución ni amplía la spec.

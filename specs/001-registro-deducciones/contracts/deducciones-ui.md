# Contrato de interfaz: Deducciones

Este contrato describe el comportamiento visible exigido por la spec; no fija diseño visual ni tecnología de componentes.

## Registro

Disponible para Aprobador UA. El formulario permite capturar:

- Cliente, como texto libre.
- Origen: Pago, Ajuste AR o Netting.
- Referencia correspondiente: número de cheque, número de ajuste o número de transacción Netting, respectivamente.
- Monto en USD, con hasta dos decimales; cero y valores negativos son válidos.
- Centro de costo UD, obligatorio para clientes distintos de DOT Foods, Safeway y Gordon Food Service.

La interfaz indica F-30 para los tres clientes especiales y F-28 para los demás, comparando el nombre sin distinguir mayúsculas y omitiendo espacios al inicio/final. Al guardar asigna tipo CP, estado Nueva y fecha de registro. Si falta cliente, origen, monto o la referencia requerida, bloquea el guardado e identifica el dato faltante. Una coincidencia de cliente, origen y referencia muestra advertencia, pero permite continuar.

## Consulta y modificación

- Asociado Cash App, Revisor UD y Aprobador UA pueden consultar todos los registros, sin limitarse a los creados por ellos.
- La consulta de deducciones abiertas no presenta como abierta una deducción cerrada. La transición de estado y el cierre se definen en la spec 002.
- Solo Revisor UD modifica datos existentes, salvo estado y cierre. Los demás roles no pueden modificar.
- Revisor UD puede capturar y consultar el número de documento SAP después de la contabilización externa. No se requiere antes de contabilizar y no implica integración con SAP.

## Límites

La interfaz y sus mensajes están en español. No incluye aprobaciones, notificaciones, reportes, automatización ni conexiones a sistemas externos.

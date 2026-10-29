# Modelo de datos: Registro de deducciones

## Entidad: Deducción

Una fila representa una deducción no autorizada registrada. El cliente se conserva como texto libre en esta versión; la administración de catálogos se reserva para la spec 003.

| Atributo conceptual | Requerido | Regla |
|---|---:|---|
| Identificador de deducción | Sí | Identificador único generado al crear el registro. |
| Cliente | Sí | Texto libre; al determinar la clasificación, ignorar mayúsculas y espacios al inicio o al final. |
| Origen | Sí | Uno de Pago, Ajuste AR o Netting. |
| Referencia | Sí | Número de cheque para Pago, número de ajuste para Ajuste AR o número de transacción Netting para Netting. |
| Monto | Sí | USD, hasta dos decimales; se permiten cero y valores negativos. |
| Centro de costo UD | Condicional | Requerido cuando el cliente no es DOT Foods, Safeway ni Gordon Food Service; para esos tres clientes se determina F-30. |
| Tipo de transacción | Derivado | F-30 para DOT Foods, Safeway y Gordon Food Service; F-28 para cualquier otro cliente. |
| Tipo de documento | Sí | Valor constante CP. |
| Estado | Sí | Valor inicial Nueva. Cambios de estado y cierre quedan fuera de esta feature, en la spec 002. |
| Fecha de registro | Sí | Asignada al guardar. |
| Registrado por | Sí | Identidad del usuario que crea el registro. |
| Número de documento SAP | No | Captura manual posterior a la contabilización externa; editable por Revisor UD conforme a la spec. No representa una integración. |

## Validaciones

1. Rechazar el guardado si falta cliente, origen o monto.
2. Exigir la referencia que corresponda al origen seleccionado.
3. Exigir centro de costo UD para clientes distintos de los tres que determinan F-30.
4. Asignar estado Nueva, fecha de registro y tipo de documento CP al guardar.
5. Si existe coincidencia de cliente, origen y referencia, advertir que podría ser duplicado, pero permitir guardar.
6. Normalizar mayúsculas y espacios al inicio/final del cliente para determinar F-30/F-28 sin permitir que el usuario contradiga la regla.

## Acceso conceptual

| Rol | Lectura | Crear | Modificar |
|---|---:|---:|---:|
| Asociado Cash App | Todos los registros | No | No |
| Aprobador UA | Todos los registros | Sí | No |
| Revisor UD | Todos los registros | No | Sí, dentro del alcance autorizado |

La modificación de estado y el cierre no se implementan aquí. Los permisos finales de campos protegidos y la asignación de usuarios a roles deben revisarse por una persona antes de liberar.

## Relaciones y límites

- No se modelan tablas de catálogos de clientes o centros de costo en esta feature.
- No se incluyen entidades de aprobaciones, notificaciones, reportes ni integraciones.
- La contabilización SAP ocurre fuera del sistema; el número SAP es un dato manual opcional.

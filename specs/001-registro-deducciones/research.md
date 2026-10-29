# Investigación: Registro de deducciones

## Decisiones confirmadas

### Plataforma y empaquetado

- **Decisión**: aplicación model-driven sobre Dataverse, incluida en una única solución con prefijo `AIMA`.
- **Motivo**: es un requisito de la constitución del proyecto; no es una decisión inferida de la spec, que deliberadamente no prescribe tecnología.
- **Alternativas**: no se evalúan plataformas alternativas porque contradirían la constitución vigente.

### Identidad y permisos

- **Decisión**: Microsoft Entra ID con cuentas Gruma y permisos conforme a los tres roles de la spec.
- **Acceso**: los roles Asociado Cash App, Revisor UD y Aprobador UA leen todos los registros; Aprobador UA crea; Revisor UD modifica solo los campos y acciones dentro del alcance autorizado.
- **Motivo**: combinación de los requisitos de constitución y las decisiones ya aclaradas en la spec.

### Sistemas externos

- **Decisión**: no hay integraciones ni contabilización desde la aplicación.
- **Motivo**: SAP, TNET y TradePro están explícitamente fuera de alcance. Revisor UD captura manualmente el número de documento SAP después de la contabilización externa.

### Automatizaciones, notificaciones y reportes

- **Decisión**: no crear flujos, avisos, reportes ni tableros para esta feature.
- **Motivo**: exclusión de la constitución y de la spec.

## Decisiones funcionales que guían el diseño

- F-30 para DOT Foods, Safeway y Gordon Food Service; F-28 para los demás clientes y se requiere centro de costo UD.
- La comparación de cliente ignora diferencias de mayúsculas y espacios al inicio o al final; otros nombres se consideran clientes distintos.
- El cliente se captura como texto libre en esta versión; los catálogos corresponden a la spec 003.
- Origen Pago, Ajuste AR o Netting determina la referencia requerida: número de cheque, número de ajuste o número de transacción Netting, respectivamente.
- Un posible duplicado de cliente, origen y referencia genera una advertencia, pero no bloquea el registro.
- El monto está expresado en USD, permite hasta dos decimales y puede ser cero o negativo.
- Un registro guardado inicia en estado Nueva, recibe fecha de registro y tiene tipo de documento CP.
- Aprobador UA crea; todos los roles consultan todos los registros; Revisor UD modifica dentro de los límites especificados. El cambio de estado y cierre pertenece a la spec 002.

## Pendiente de decisión humana

La spec y la constitución no fijan los siguientes parámetros operativos: volumen esperado de registros, concurrencia, disponibilidad objetivo, retención, respaldo/recuperación y proceso de administración de usuarios/roles. Este diseño no inventa umbrales ni políticas. Confirmarlos antes de producción si son requisitos del entorno; no cambian el modelo funcional propuesto.

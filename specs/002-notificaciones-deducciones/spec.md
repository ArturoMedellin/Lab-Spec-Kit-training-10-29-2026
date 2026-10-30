# Especificación de la función: Notificaciones de deducciones

**Rama de función**: `ofeliat658-registro-deducciones-plan`

**Creada**: 2026-10-30

**Estado**: Borrador

**Entrada**: Descripción de usuario: "cuando se cree/registre una nueva deduccion (Por el Aprobador UA), debe de enviar un correo al Revisor UD notificando el nuevo registro. cuando el Revisor UD modifque el registro (agregue el Numero de SAP), se debe notificar al Asociado Cash App. el correo debe te tener la liga del registro y un resumen de los cambios y campos de registro. las notificaciones se deben de enviar por un flujo de power automate."

## Escenarios de usuario y pruebas

### Historia de usuario 1 - Avisar de una nueva deducción (Prioridad: P1)

Como Revisor UD, quiero recibir un correo cuando un Aprobador UA registre una
deducción para conocer el nuevo trabajo y abrir directamente el registro.

**Por qué tiene esta prioridad**: El aviso permite iniciar el seguimiento
oportunamente después de que se guarda una deducción.

**Prueba independiente**: Un Aprobador UA registra una deducción válida; se
comprueba que los usuarios destinatarios con el rol Revisor UD reciben el aviso
con los datos del registro y un enlace que abre esa deducción.

**Escenarios de aceptación**:

1. **Dada** una deducción válida y un Aprobador UA autorizado, **cuando** guarda
   el registro, **entonces** se envía un correo de nueva deducción a los usuarios
   del rol Revisor UD.
2. **Dado** un registro que no se ha guardado, **cuando** el Aprobador UA intenta
   guardarlo y la operación falla, **entonces** no se envía el correo de nueva
   deducción.
3. **Dada** una deducción guardada, **cuando** el destinatario abre el enlace
   del correo con acceso autorizado, **entonces** se muestra el registro
   correspondiente.

### Historia de usuario 2 - Avisar cuando se agrega el número SAP (Prioridad: P2)

Como Asociado Cash App, quiero recibir un correo cuando el Revisor UD agregue
el número de documento SAP a una deducción para conocer la actualización y
consultar el registro.

**Por qué tiene esta prioridad**: El aviso comunica que el registro recibió su
referencia de contabilización y permite al Asociado Cash App dar seguimiento.

**Prueba independiente**: El Revisor UD agrega un número de documento SAP a un
registro existente; se comprueba que los usuarios destinatarios con el rol
Asociado Cash App reciben el cambio y el enlace al registro.

**Escenarios de aceptación**:

1. **Dada** una deducción sin número de documento SAP, **cuando** el Revisor UD
   agrega y guarda el número, **entonces** se envía un correo a los usuarios del
   rol Asociado Cash App.
2. **Dada** una deducción existente, **cuando** se modifica otro campo sin
   agregar el número de documento SAP, **entonces** no se envía el correo de
   número SAP agregado.
3. **Dada** una deducción cuyo número de documento SAP ya está capturado,
   **cuando** se modifica otro campo, **entonces** no se envía un nuevo correo
   de número SAP agregado.

### Casos límite

- Si la creación o modificación del registro no se guarda, no se envía el
  correo asociado a ese evento.
- Si el correo no puede entregarse, la operación de guardar la deducción no se
  revierte y el fallo queda disponible para seguimiento por los responsables
  autorizados del flujo.
- Si un destinatario no puede acceder al registro enlazado, el acceso continúa
  sujeto a sus permisos existentes; el correo no concede acceso adicional.
- En el aviso de creación, el número de documento SAP se muestra como pendiente
  o sin asignar.
- El aviso de SAP se dispara cuando el campo pasa de vacío a tener un valor;
  editar otros campos después de esa captura no genera otro aviso de SAP.

## Requisitos

### Requisitos funcionales

- **FR-001**: CUANDO un Aprobador UA guarde una nueva deducción válida, EL
  SISTEMA DEBE enviar un correo que notifique el registro nuevo a los usuarios
  destinatarios del rol Revisor UD.
- **FR-002**: CUANDO un Revisor UD guarde por primera vez un número de documento
  SAP en una deducción existente cuyo campo estaba vacío, EL SISTEMA DEBE enviar
  un correo que notifique ese cambio a los usuarios destinatarios del rol
  Asociado Cash App.
- **FR-003**: CUANDO el registro no se guarde, EL SISTEMA DEBE abstenerse de
  enviar el correo asociado a la creación o modificación fallida.
- **FR-004**: CUANDO se envíe cualquiera de los correos, EL SISTEMA DEBE incluir
  un enlace directo a la deducción correspondiente.
- **FR-005**: CUANDO se envíe cualquiera de los correos, EL SISTEMA DEBE incluir
  un resumen del evento y los valores disponibles de los campos del registro:
  cliente, origen, referencia correspondiente al origen, monto en USD, centro
  de costo UD, tipo de transacción, tipo de documento, estado, fecha de registro,
  número de documento SAP e identidad de quien registró la deducción.
- **FR-006**: CUANDO se envíe el aviso de número SAP agregado, EL SISTEMA DEBE
  identificar el número capturado como el cambio que originó el aviso y mostrar
  quién lo agregó y cuándo.
- **FR-007**: CUANDO se envíe el aviso de nueva deducción, EL SISTEMA DEBE
  identificar la creación como el evento y mostrar el número de documento SAP
  como pendiente o sin asignar si todavía está vacío.
- **FR-008**: CUANDO se modifique una deducción cuyo número SAP ya estaba
  capturado, EL SISTEMA NO DEBE enviar otro aviso por la captura inicial del
  número SAP.
- **FR-009**: CUANDO se envíe una notificación autorizada, EL SISTEMA DEBE
  enviarla mediante un flujo de Power Automate que use conexiones y conectores
  corporativos aprobados por las políticas DLP del ambiente.
- **FR-010**: CUANDO falle la entrega de un correo, EL SISTEMA DEBE conservar el
  registro guardado y dejar el fallo disponible para seguimiento por los
  responsables autorizados del flujo.
- **FR-011**: CUANDO un destinatario abra el enlace del correo, EL SISTEMA DEBE
  respetar los roles y permisos existentes, sin conceder acceso adicional al
  registro.
- **FR-012**: CUANDO se capture el número de documento SAP, EL SISTEMA DEBE
  conservar el valor ingresado manualmente y NO DEBE conectarse con SAP, TNET ni
  TradePro ni realizar contabilizaciones.
- **FR-013**: CUANDO se agreguen componentes para esta función, EL SISTEMA DEBE
  incorporarlos dentro de una única solución de Power Platform y usar el
  prefijo `AIMA`.
- **FR-014**: CUANDO ocurra un evento distinto de los dos autorizados, EL
  SISTEMA NO DEBE enviar notificaciones ni habilitar flujos para ese evento.

### Entidades clave

- **Deducción**: Registro existente que contiene los datos de negocio, la
  identidad de quien la registró y, cuando se capture, el número de documento
  SAP. Es el destino del enlace incluido en cada correo.
- **Notificación de nueva deducción**: Aviso por correo originado por el
  registro guardado por un Aprobador UA, dirigido a los destinatarios del rol
  Revisor UD.
- **Notificación de número SAP agregado**: Aviso por correo originado cuando
  un Revisor UD agrega el número de documento SAP a un campo previamente vacío,
  dirigido a los destinatarios del rol Asociado Cash App.

## Criterios de éxito

### Resultados medibles

- **SC-001**: En las pruebas de aceptación, el 100 % de las deducciones válidas
  guardadas por un Aprobador UA generan un aviso al Revisor UD con enlace válido
  y todos los campos de registro disponibles.
- **SC-002**: En las pruebas de aceptación, el 100 % de las capturas iniciales
  de número SAP guardadas por un Revisor UD generan un aviso al Asociado Cash
  App con el cambio identificado y enlace válido.
- **SC-003**: El 100 % de las pruebas de modificaciones que no agregan un número
  SAP no generan el aviso de SAP agregado.
- **SC-004**: Ningún correo permite a un destinatario acceder a registros que
  sus permisos existentes le impiden consultar.
- **SC-005**: Los fallos de entrega probados quedan disponibles para seguimiento
  y no revierten la creación o modificación ya guardada.

## Supuestos

- Los destinatarios son todos los usuarios activos asignados al rol indicado
  para cada evento, y cuentan con una dirección corporativa habilitada para
  recibir correo.
- Los campos cuyo valor no exista al momento del evento se muestran como
  pendientes o sin asignar, sin impedir la notificación.
- Un fallo de entrega no debe impedir ni revertir el guardado del registro; los
  responsables autorizados podrán consultar el fallo para darle seguimiento.
- La incorporación del número SAP significa pasar de un valor vacío a uno
  capturado; cambiar otros datos en el registro no vuelve a disparar el aviso.
- Los flujos y conectores se limitan a la excepción ratificada en el Principio
  VIII de la constitución 1.1.0.

## Fuera de alcance

- Cualquier correo o flujo que no corresponda a los dos eventos definidos.
- Conexión, consulta o contabilización en SAP, TNET o TradePro.
- Cambio de roles, permisos o reglas de acceso de la aplicación.
- Reportes, avisos por otros canales y automatizaciones adicionales.

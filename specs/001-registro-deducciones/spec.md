# Especificación de la función: Registro de deducciones

**Feature Branch**: `arturomedellin-install-github-speckit`

**Created**: 2026-10-29

**Status**: Draft

**Input**: Registro de deducciones por Aprobador UA, consulta por los tres roles base y modificación por Revisor UD, según las historias y reglas de negocio indicadas por el usuario.

## Escenarios de usuario y pruebas

### Historia de usuario 1 - Registrar una deducción (Prioridad: P1)

Como Aprobador UA, quiero registrar una deducción con los datos de la
transacción para que UD pueda darle seguimiento desde un solo lugar, en vez de
rastrearla en correos y archivos de Excel.

**Por qué tiene esta prioridad**: Registrar la deducción en un lugar común es
el objetivo principal de la primera versión y permite iniciar su seguimiento.

**Prueba independiente**: Un Aprobador UA completa y guarda una deducción;
el resultado contiene los datos ingresados, la clasificación de transacción
esperada y el estado y fecha asignados al registrarla.

**Escenarios de aceptación**:

1. **Dado** un Aprobador UA y los datos requeridos, **cuando** registra
   una deducción para DOT Foods, Safeway o Gordon Food Service, **entonces** el
   sistema indica F-30, asigna el tipo de documento CP, el estado Nueva y la
   fecha de registro.
2. **Dado** un Aprobador UA y un cliente distinto de DOT Foods, Safeway o
   Gordon Food Service, **cuando** registra una deducción, **entonces** el
   sistema indica F-28 y marca el centro de costo de UD como requerido.
3. **Dado** un formulario de registro, **cuando** falta el cliente, el origen
   o el monto, **entonces** el sistema impide guardar la deducción e identifica
   los datos requeridos que faltan.
4. **Dado** un Aprobador UA que inicia el registro, **cuando** completa y
   guarda una deducción válida, **entonces** puede finalizar el registro en
   menos de un minuto.

**Casos límite**:

- Si un cliente se captura con diferencias de mayúsculas o espacios al inicio
  o al final, se clasifica según el nombre normalizado de los tres clientes
  indicados; cualquier otro nombre se clasifica como otro cliente.
- Los montos numéricos cero y negativos son válidos si se cumplen los demás
  requisitos de registro.
- Si el cliente es distinto de los tres indicados y no se proporciona el
  centro de costo de UD, el sistema impide guardar.
- El origen Netting utiliza el número de transacción Netting como referencia.

### Historia de usuario 2 - Consultar deducciones (Prioridad: P2)

Como Asociado Cash App, Revisor UD o Aprobador UA, quiero consultar las
deducciones registradas y abiertas para conocer el trabajo y dar seguimiento a
los registros.

**Por qué tiene esta prioridad**: Consultar los registros permite a los
asociados dar seguimiento a las deducciones que están gestionando.

**Prueba independiente**: Cada rol base consulta deducciones abiertas y
registradas, incluidas las creadas por otros usuarios, y puede identificar
cuáles están abiertas.

**Escenarios de aceptación**:

1. **Dado** que existen deducciones abiertas y registradas, **cuando** un
   usuario de cualquiera de los tres roles base consulta las deducciones,
   **entonces** el sistema presenta todos los registros, sin limitar la
   consulta a los que ese usuario creó.
2. **Dado** que una deducción está cerrada, **cuando** se consultan las
   deducciones abiertas, **entonces** el sistema no la presenta como abierta.

### Historia de usuario 3 - Completar referencia de contabilización (Prioridad: P3)

Como Revisor UD, quiero capturar el número de documento SAP después de que la
deducción se contabilice fuera de la aplicación para mantener esa referencia
junto al registro.

**Por qué tiene esta prioridad**: La referencia ayuda a relacionar el registro
con una contabilización posterior, pero no es necesaria para el registro ni
seguimiento inicial.

**Prueba independiente**: Después de que una deducción se contabiliza fuera de
la aplicación, el Revisor UD agrega su número de documento SAP y puede
consultarlo en la deducción correspondiente.

**Escenarios de aceptación**:

1. **Dado** un registro de deducción existente, **cuando** un Revisor UD
   captura el número de documento SAP después de la contabilización,
   **entonces** el sistema conserva y muestra ese número junto al registro.
2. **Dado** que la deducción aún no se ha contabilizado, **cuando** el
   Revisor UD consulta o actualiza el registro, **entonces** no se exige
   un número de documento SAP.
3. **Dado** un registro de deducción existente, **cuando** un usuario que no
   tenga el rol Revisor UD intenta modificarlo, **entonces** el sistema impide
   la modificación.

## Requisitos

### Requisitos funcionales

- **FR-001**: CUANDO un Aprobador UA registre una deducción,
  EL SISTEMA DEBE permitir capturar cliente, origen, número de cheque o ajuste,
  monto en USD y centro de costo, y aceptar cualquier monto numérico incluido
  cero o negativo.
- **FR-002**: CUANDO un usuario que no tenga el rol Aprobador UA intente
  registrar una deducción, EL SISTEMA DEBE impedir la creación.
- **FR-003**: CUANDO falte el cliente, el origen o el monto,
  EL SISTEMA DEBE impedir guardar la deducción.
- **FR-004**: CUANDO el cliente sea distinto de DOT Foods, Safeway y Gordon
  Food Service, EL SISTEMA DEBE exigir el centro de costo de UD.
- **FR-005**: CUANDO el cliente sea DOT Foods, Safeway o Gordon Food Service,
  EL SISTEMA DEBE indicar F-30.
- **FR-006**: CUANDO el cliente sea distinto de DOT Foods, Safeway y Gordon
  Food Service, EL SISTEMA DEBE indicar F-28.
- **FR-007**: CUANDO se guarde una deducción válida,
  EL SISTEMA DEBE asignarle el estado Nueva y la fecha de registro.
- **FR-008**: CUANDO se registre una deducción,
  EL SISTEMA DEBE asignarle el tipo de documento CP.
- **FR-009**: CUANDO un usuario con cualquiera de los tres roles base consulte
  deducciones, EL SISTEMA DEBE mostrar todas las deducciones abiertas y
  registradas, sin limitar los resultados a las que ese usuario creó.
- **FR-010**: CUANDO un Revisor UD modifique una deducción existente,
  EL SISTEMA DEBE permitirle modificar sus datos salvo el estado y el cierre,
  que se definen en la spec 002.
- **FR-011**: CUANDO un usuario que no tenga el rol Revisor UD intente
  modificar una deducción existente, EL SISTEMA DEBE impedir la modificación.
- **FR-012**: CUANDO una deducción haya sido contabilizada externamente,
  EL SISTEMA DEBE permitir al Revisor UD capturar y consultar su número de
  documento SAP sin conectarse a SAP.
- **FR-013**: CUANDO el origen sea Netting,
  EL SISTEMA DEBE permitir capturar el número de transacción Netting como
  referencia de la operación.
- **FR-014**: CUANDO el usuario tenga el rol Aprobador UA,
  EL SISTEMA DEBE permitirle crear deducciones y consultar todos los registros.
- **FR-015**: CUANDO el usuario tenga el rol Asociado Cash App,
  EL SISTEMA DEBE permitirle consultar todos los registros, sin crear ni
  modificar deducciones.
- **FR-016**: CUANDO el usuario tenga el rol Revisor UD,
  EL SISTEMA DEBE permitirle consultar todos los registros y modificar los
  datos de una deducción existente, salvo el estado y el cierre.

### Entidades clave

- **Deducción**: Registro de una deducción no autorizada que incluye cliente,
  origen, referencia de cheque, ajuste o transacción Netting, monto, centro de
  costo, tipo de documento, tipo de transacción indicado, estado, fecha de
  registro, número de documento SAP cuando esté disponible y la identidad de
  quien la registró. El monto se expresa en USD.
- **Cliente**: Nombre del cliente asociado con la deducción. En esta versión
  se captura sin administrar catálogos; los nombres de DOT Foods, Safeway y
  Gordon Food Service determinan la clasificación de transacción.
- **Origen**: Categoría de origen de la deducción: Pago, Ajuste AR o Netting.

## Criterios de éxito

### Resultados medibles

- **SC-001**: En una prueba de aceptación, un Aprobador UA puede registrar
  una deducción válida en menos de un minuto, desde que inicia el registro
  hasta que el sistema confirma que se guardó.
- **SC-002**: El 100 % de los intentos de guardar sin cliente, origen o monto
  son impedidos.
- **SC-003**: El 100 % de los casos de prueba para los tres clientes indicados
  muestran F-30, y el 100 % de los casos para otros clientes muestran F-28 y
  requieren el centro de costo de UD.
- **SC-004**: Toda deducción guardada inicia con estado Nueva, fecha de
  registro y tipo de documento CP.
- **SC-005**: Los tres roles base pueden consultar todas las deducciones
  abiertas y registradas, incluidas las creadas por otros usuarios.

## Supuestos

- El cliente se captura como texto libre en esta versión porque la administración
  de catálogos pertenece a la spec 003.
- La comparación de cliente ignora diferencias de mayúsculas y espacios al
  inicio o al final; las variaciones de nombre no equivalentes se consideran
  otros clientes.
- Para Pago, la referencia corresponde al número de cheque; para Ajuste AR,
  corresponde al número de ajuste; para Netting, corresponde al número de
  transacción Netting.
- Se acepta cualquier monto numérico, incluidos cero y valores negativos.
- El centro de costo puede capturarse para cualquier cliente; solo es
  obligatorio para clientes distintos de los tres indicados.
- El Revisor UD captura manualmente el número de documento SAP después de una
  contabilización realizada fuera de la aplicación. La aplicación no contabiliza
  ni se conecta con SAP, TNET o TradePro.
- Para esta función, las deducciones con estado Nueva se consideran abiertas.
  Los estados y el cierre por UD corresponden a la spec 002.
- Todos los roles base pueden consultar todas las deducciones abiertas y
  registradas; solo Aprobador UA crea registros y solo Revisor UD modifica
  registros existentes.
- Revisor UD puede modificar todos los datos de una deducción salvo el estado
  y el cierre, definidos en la spec 002.
- El monto se captura en USD.
- La revisión y el cierre por UD no forman parte de esta función.

## Fuera de alcance

- Revisión y cierre por UD, cubiertos por la spec 002.
- Administración de catálogos, cubierta por la spec 003.
- Aprobaciones, notificaciones, avisos, reportes y flujos automatizados.
- Conexiones o contabilización con SAP, TNET o TradePro.

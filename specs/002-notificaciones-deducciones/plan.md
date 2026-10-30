# Plan de implementación: Notificaciones de deducciones

**Rama/feature**: `002-notificaciones-deducciones`

**Fecha**: 2026-10-30

**Spec**: [spec.md](spec.md)

## Resumen

Agregar dos notificaciones por correo posteriores al guardado de una fila
Deducción: una al crearla, dirigida a Revisor UD, y otra cuando el Revisor UD
captura inicialmente el número de documento SAP, dirigida a Asociado Cash App.
Ambas se ejecutarán con flujos de Power Automate conscientes de solución. El
correo incluirá el resumen de los campos disponibles y un enlace directo a la
fila; Dataverse seguirá siendo responsable de autorizar su consulta.

La creación y el cambio de SAP usarán flujos separados para aislar sus
destinatarios y condiciones. El aviso de SAP consultará solo las actualizaciones
de su columna y utilizará un indicador interno en la fila existente para no
repetir el aviso tras la captura inicial. Los fallos de envío no revierten la
fila y quedan disponibles en el historial de ejecuciones del flujo.

## Contexto técnico

**Lenguaje/versión**: Flujos declarativos de Power Automate; no hay código de
aplicación en este repositorio.

**Dependencias principales**: Dataverse, Power Automate y un conector de correo
corporativo aprobado por la política DLP del ambiente. El conector y la
identidad de conexión deben confirmarse en el ambiente destino.

**Almacenamiento**: Tabla existente Deducción en Dataverse. No se propone una
nueva entidad de negocio; se agrega a Deducción una columna interna con
prefijo `AIMA` para registrar si ya se notificó la captura inicial del número
SAP.

**Pruebas**: Ensayo manual de aceptación en un ambiente no productivo aprobado,
con cuentas de prueba para cada rol, buzones controlados y revisión del
historial de ejecuciones de ambos flujos. No hay un runner de pruebas Power
Platform en este repositorio.

**Plataforma destino**: Power Platform, solución única con prefijo `AIMA`,
Dataverse y la aplicación model-driven existente.

**Tipo de proyecto**: Automatización de notificaciones para una aplicación
interna de negocio.

**Meta de rendimiento**: El correo se procesa después del guardado y no debe
bloquear ni revertir la operación de usuario. La spec no establece un SLA de
entrega; no se agrega uno en este plan.

**Restricciones**: Solo los dos eventos autorizados por la constitución 1.1.0;
conectores corporativos compatibles con DLP; roles y permisos existentes; sin
integración con SAP, TNET o TradePro; sin secretos en el repositorio ni nuevas
entidades de negocio.

**Escala/alcance**: Dos eventos por deducción como máximo: una notificación de
creación y una notificación por la captura inicial de SAP. No se especifica un
volumen de usuarios ni una meta de concurrencia.

## Revisión de la constitución

*Puerta previa a investigación y diseño: el usuario aprobó explícitamente la
especificación el 2026-10-30. Los diez principios cumplen. El usuario aprobó
explícitamente este plan y los artefactos de diseño el 2026-10-30 antes de
generar tareas.*

| Principio constitucional | Estado | Justificación |
|---|---|---|
| I. Plataforma Power Platform | Cumple | La automatización usa Power Automate y Dataverse dentro de Power Platform. |
| II. Solución única y prefijo | Cumple | Los flujos, conexiones de solución y la columna interna se incorporarán en la solución `AIMA`. |
| III. Identidad, roles y DLP | Cumple | Los destinatarios se resolverán según roles Dataverse y se usarán solo conexiones y conectores aprobados por DLP. |
| IV. Roles base | Cumple | La creación notifica a Revisor UD y la captura inicial de SAP notifica a Asociado Cash App; se respetan los permisos actuales. |
| V. Modelo de datos simple | Cumple | Se conserva Deducción como única entidad de negocio y se agrega solo un indicador técnico a esa tabla. |
| VI. Idioma | Cumple | La documentación, los asuntos y el contenido de los correos estarán en español. |
| VII. Límite con sistemas externos | Cumple | SAP se captura manualmente; los flujos no consultan SAP, TNET o TradePro ni contabilizan. |
| VIII. Alcance de la versión | Cumple | Solo se implementan los dos avisos por correo permitidos por la enmienda 1.1.0. |
| IX. Cero secretos | Cumple | No se guardan credenciales ni tokens en el repositorio; conexiones se administran en Power Platform. |
| X. Desarrollo guiado por la spec y requisitos comprobables | Cumple | El diseño se deriva de la spec aprobada; este plan y el diseño esperan aprobación humana antes de generar tareas. |

## Estructura del proyecto

Este repositorio contiene artefactos Spec Kit; no contiene código de la
aplicación ni definiciones exportadas de la solución. Los flujos y componentes
de Dataverse se editarán en la solución Power Platform `AIMA` y su ciclo de
exportación no forma parte de este cambio documental.

```text
specs/002-notificaciones-deducciones/
├── spec.md
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
└── contracts/
    └── notificaciones-correo.md
```

**Decisión de estructura**: Mantener el contrato de eventos y correos, el
modelo de datos afectado y la guía de validación junto a la especificación.
Los componentes ejecutables serán dos flujos conscientes de solución y una
columna técnica en la tabla Deducción existente, todos en la solución `AIMA`.
No se inventan directorios de código ni una integración externa.

## Decisiones de diseño

1. **Flujos separados por evento**: un flujo de creación con cambio `Added` y
   un flujo de actualización con cambio `Modified`, filtrado a la columna de
   número SAP. Así se aíslan destinatarios, plantillas y validaciones.
2. **Destinatarios**: resolver usuarios Dataverse activos asociados al rol
   destinatario. Antes de construir las consultas se comprobará si el ambiente
   asigna roles directamente a usuarios, a equipos, o de ambas formas; se
   incluirán los miembros efectivos del rol y se excluirán cuentas
   deshabilitadas o sin correo corporativo. No se codifican direcciones
   personales en el flujo.
3. **Detección del alta inicial SAP**: el trigger de modificación con columnas
   seleccionadas no aporta por sí solo el valor anterior. Añadir a Deducción un
   indicador técnico `Notificación SAP enviada` (nombre de esquema generado con
   prefijo `AIMA`), de solo control del flujo. En el despliegue, inicializarlo
   como enviado para registros existentes con SAP y como pendiente para los que
   no tienen SAP. El flujo enviará solo cuando SAP tenga valor y el indicador
   aún esté pendiente; tras el envío exitoso marcará el indicador como enviado.
   El flujo solo escucha cambios de la columna SAP, por lo que su actualización
   del indicador no lo vuelve a disparar.
4. **Entrega y errores**: las ejecuciones son posteriores al guardado. Un fallo
   deja la fila intacta y la ejecución fallida visible para dueños/co-dueños
   autorizados en el historial del flujo. Se habilitará reintento de errores
   transitorios en la acción de correo conforme a la política aprobada. No se
   agrega una tabla de registro ni una notificación de error adicional fuera
   del alcance.
5. **Enlace**: construir el enlace con la URL del entorno, el nombre lógico
   confirmado de la tabla y el GUID de la fila. Si la app model-driven concreta
   está confirmada, incluir su identificador de app para abrir el formulario en
   esa app. Dataverse mantiene la autorización; el enlace no comparte ni amplía
   permisos.
6. **Contenido**: incluir el evento y los campos disponibles definidos en el
   contrato, con una indicación explícita de SAP pendiente en el correo de
   creación. El texto y el asunto serán en español.
7. **DLP y conexiones**: Dataverse y el conector de correo deben estar
   clasificados de forma compatible por la política DLP aplicable. La identidad
   de conexión y el remitente se seleccionarán en el ambiente; no se asume que
   una conexión personal o un conector específico estén permitidos.

## Revisión de la constitución posterior al diseño

Tras completar investigación y diseño, los principios I–X vuelven a cumplir:
se usan Power Platform, la solución única `AIMA`, roles existentes y
conectores aprobados por DLP; no se introduce una entidad de negocio nueva,
una integración con SAP, avisos adicionales ni secretos. La columna de control
pertenece a Deducción y solo sirve para impedir avisos repetidos. La aprobación
humana del diseño sigue pendiente.

## Puerta de aprobación

**Estado: plan y diseño aprobados por el usuario el 2026-10-30.** Antes de
implementar, deben confirmarse en el ambiente destino la política DLP, la identidad
corporativa de envío, los miembros efectivos de los roles y la app/URL de
Dataverse. La aprobación humana del ensayo sigue pendiente antes de liberar; el
agente no aprueba su propio trabajo.

# Tareas: Notificaciones de deducciones

**Entrada**: documentos de diseño en `specs/002-notificaciones-deducciones/`

**Prerequisitos**: `plan.md`, `spec.md`, `research.md`, `data-model.md`,
`contracts/notificaciones-correo.md` y `quickstart.md`.

**Organización**: tareas agrupadas por historia de usuario para habilitar
entregas incrementales. Las rutas indican la ubicación exacta de componentes
dentro de la solución Power Platform, ya que el repositorio no contiene el
código fuente ni los archivos exportados de la aplicación.

## Fase 1: Preparación del ambiente

**Propósito**: Confirmar accesos y decisiones del ambiente que bloquean la
configuración de cualquier flujo.

- [ ] T001 Confirmar ambiente no productivo, solución Dataverse única `AIMA`, publisher y prefijo real en Power Platform > Soluciones
- [ ] T002 Confirmar política DLP y conector de correo corporativo permitido, propietario de conexión y remitente en Power Platform > Solución `AIMA` > Conexiones
- [ ] T003 Confirmar nombre lógico de la tabla Deducción, columna de documento SAP, URL del entorno y `appid` de la app en Power Platform > Solución `AIMA` > Tabla Deducción y aplicación model-driven
- [ ] T004 Confirmar cómo se asignan efectivamente los roles Revisor UD y Asociado Cash App (usuarios directos, equipos o ambos) y preparar cuentas activas de prueba en Power Platform > Dataverse > Roles de seguridad y Usuarios

---

## Fase 2: Fundamentos compartidos

**Propósito**: Preparar el indicador técnico y la estrategia de destinatarios
que comparten los dos eventos. Esta fase bloquea ambas historias.

- [ ] T005 Crear la columna booleana técnica `Notificación SAP enviada` con nombre de esquema bajo el prefijo real de `AIMA`, valor predeterminado falso y sin exponerla en formularios de usuario en Power Platform > Solución `AIMA` > Tabla Deducción > Columnas
- [ ] T006 Inicializar `Notificación SAP enviada` a verdadero para filas existentes con número SAP y falso para filas sin número SAP en Power Platform > Dataverse > Tabla Deducción
- [ ] T007 Definir y verificar la consulta de destinatarios activos con correo corporativo y pertenencia efectiva al rol, incluyendo asignación por equipos si aplica, en Power Platform > Solución `AIMA` > Flujos de nube > componentes compartidos de destinatarios

**Punto de control**: No comenzar las historias hasta que la política DLP,
conexión de correo, destinatarios efectivos y datos históricos del indicador
estén verificados.

---

## Fase 3: Historia de usuario 1 - Avisar de una nueva deducción (Prioridad: P1) - MVP

**Objetivo**: Notificar al Revisor UD después de guardar una deducción creada
por Aprobador UA.

**Prueba independiente**: Crear una deducción válida con Aprobador UA y
confirmar que llega al buzón controlado de Revisor UD el correo en español con
los campos definidos en el contrato y el enlace directo autorizado.

- [ ] T008 [P] [US1] Crear el flujo consciente de solución `AIMA - Notificar nueva deducción` con desencadenador Dataverse de fila Deducción agregada en Power Platform > Solución `AIMA` > Flujos de nube
- [ ] T009 [US1] Resolver en el flujo `AIMA - Notificar nueva deducción` los destinatarios activos del rol Revisor UD y excluir usuarios deshabilitados o sin correo en Power Platform > Solución `AIMA` > Flujos de nube > `AIMA - Notificar nueva deducción`
- [ ] T010 [US1] Construir el enlace directo a la fila Deducción usando la URL confirmada del entorno, nombre lógico de tabla, GUID codificado y `appid` validado en Power Platform > Solución `AIMA` > Flujos de nube > `AIMA - Notificar nueva deducción`
- [ ] T011 [US1] Crear asunto y cuerpo en español con evento, enlace, cliente, origen, referencia, monto USD, centro de costo UD, tipo de transacción, tipo de documento, estado, fecha, registrado por y SAP pendiente cuando esté vacío, según `specs/002-notificaciones-deducciones/contracts/notificaciones-correo.md`, en Power Platform > Solución `AIMA` > Flujos de nube > `AIMA - Notificar nueva deducción`
- [ ] T012 [US1] Configurar el envío con el conector y remitente corporativo aprobados por DLP, sin escribir de vuelta en Deducción, en Power Platform > Solución `AIMA` > Flujos de nube > `AIMA - Notificar nueva deducción`
- [ ] T013 [US1] Validar alta guardada, alta fallida, destinatario no autorizado, contenido del correo y enlace en el buzón controlado siguiendo `specs/002-notificaciones-deducciones/quickstart.md`

**Punto de control**: La historia es independiente cuando el alta guardada
genera el correo completo y el enlace respeta los permisos Dataverse; un alta
no guardada no genera correo.

---

## Fase 4: Historia de usuario 2 - Avisar cuando se agrega el número SAP (Prioridad: P2)

**Objetivo**: Notificar al Asociado Cash App solo en la primera captura de SAP
por Revisor UD.

**Prueba independiente**: En una fila sin SAP y con el indicador pendiente,
Revisor UD guarda un número; confirmar el correo, el cambio, autor, fecha y
enlace. Actualizar otros campos o volver a escribir SAP no debe generar un
segundo aviso.

- [ ] T014 [P] [US2] Crear el flujo consciente de solución `AIMA - Notificar número SAP agregado` con desencadenador Dataverse de fila Deducción modificada y Select columns limitado a la columna SAP confirmada en Power Platform > Solución `AIMA` > Flujos de nube
- [ ] T015 [US2] Agregar condición de salida temprana cuando SAP esté vacío o `Notificación SAP enviada` sea verdadero en Power Platform > Solución `AIMA` > Flujos de nube > `AIMA - Notificar número SAP agregado`
- [ ] T016 [US2] Resolver en el flujo `AIMA - Notificar número SAP agregado` los destinatarios activos del rol Asociado Cash App, incluyendo membresía efectiva por equipos si aplica, en Power Platform > Solución `AIMA` > Flujos de nube > `AIMA - Notificar número SAP agregado`
- [ ] T017 [US2] Construir enlace directo y asunto/cuerpo en español con SAP nuevo, modificador, fecha del cambio y todos los campos disponibles definidos en `specs/002-notificaciones-deducciones/contracts/notificaciones-correo.md`, en Power Platform > Solución `AIMA` > Flujos de nube > `AIMA - Notificar número SAP agregado`
- [ ] T018 [US2] Enviar con la conexión corporativa aprobada y actualizar `Notificación SAP enviada` a verdadero solo después del envío exitoso; no modificar la columna SAP ni activar otros eventos, en Power Platform > Solución `AIMA` > Flujos de nube > `AIMA - Notificar número SAP agregado`
- [ ] T019 [US2] Configurar concurrencia de una ejecución para el flujo SAP y revisar el historial antes de cualquier reenvío manual en Power Platform > Solución `AIMA` > Flujos de nube > `AIMA - Notificar número SAP agregado`
- [ ] T020 [US2] Validar SAP agregado por primera vez, valor vacío, cambio de otro campo, reescritura de SAP, fila histórica con SAP, falta de permiso y fallo del envío siguiendo `specs/002-notificaciones-deducciones/quickstart.md`

**Punto de control**: La historia es independiente cuando solo el primer alta
de SAP pendiente produce un correo al Asociado Cash App y el indicador impide
un segundo aviso tras el envío exitoso.

---

## Fase 5: Pulido y verificación transversal

**Propósito**: Revisar controles comunes y preparar el ensayo para aprobación
humana.

- [ ] T021 [P] Revisar ambos flujos, conexiones y componentes dentro de la solución única `AIMA`; confirmar que Dataverse y correo cumplen la política DLP efectiva en Power Platform > Soluciones y Políticas de datos
- [ ] T022 [P] Verificar permisos de lectura de la cuenta remitente, dueños/co-dueños autorizados y acceso de destinatarios sin elevar permisos en Power Platform > Dataverse > Roles y Flujos de nube
- [ ] T023 Ejecutar y registrar todos los casos de aceptación de `specs/002-notificaciones-deducciones/quickstart.md`, incluidas fallas y revisión del historial, sin usar datos de producción
- [ ] T024 Registrar evidencias del ambiente, destinatarios de prueba, entregas, enlaces, permisos y ejecuciones fallidas en `specs/002-notificaciones-deducciones/quickstart.md`; solicitar aprobación humana del ensayo antes de liberar

---

## Dependencias y orden de ejecución

### Dependencias de fase

- **Preparación (Fase 1)**: Sin dependencias; todas las decisiones del ambiente
  deben confirmarse antes de crear componentes.
- **Fundamentos (Fase 2)**: Depende de la preparación y bloquea ambas historias.
- **Historia 1 (Fase 3)**: Depende de los fundamentos.
- **Historia 2 (Fase 4)**: Depende de los fundamentos; no depende del flujo de
  Historia 1.
- **Pulido (Fase 5)**: Depende de las historias que se vayan a liberar.

### Dependencias entre historias

- **Historia 1 (P1)**: T001–T007; entrega independiente de creación.
- **Historia 2 (P2)**: T001–T007; entrega independiente de captura inicial SAP.
- Las historias pueden ejecutarse en paralelo después de T007, sujeto a
  capacidad del equipo. No comparten los flujos ni las plantillas de correo.
- T021–T024 aplican a la liberación integrada y requieren los flujos que se
  incluyan en el alcance de entrega.

## Oportunidades de paralelismo

- T001, T002, T003 y T004 pueden ejecutarse en paralelo porque verifican
  configuración independiente del mismo ambiente.
- T008 inicia la implementación de US1 y T014 la de US2; sus archivos son
  componentes de flujo distintos y ambas pueden desarrollarse en paralelo
  después de T007.
- T021 y la preparación de evidencia de cada historia pueden organizarse en
  paralelo cuando los flujos estén listos; la validación final de T023 requiere
  componentes integrados.

## Estrategia de implementación

### MVP primero: Historia de usuario 1

1. Completar T001–T007 y confirmar las conexiones, roles, columna y datos
   históricos.
2. Completar T008–T012 para el correo de nueva deducción.
3. Ejecutar T013 y verificar destinatario, contenido, enlace y permisos.
4. No liberar hasta completar la revisión DLP y obtener aprobación humana del
   ensayo.

### Entrega incremental

1. Entregar el aviso de creación (US1) como primer incremento verificable.
2. Agregar el aviso de SAP (US2) con su indicador de idempotencia.
3. Completar T021–T024 para validación integrada, revisión de seguridad/DLP y
   aprobación humana.

## Notas

- El envío de correo y la actualización del indicador no son atómicos; si el
  correo se envía y luego falla la actualización del indicador, un reintento
  manual puede duplicar el aviso. Revisar el historial antes de reenviar.
- No crear tablas de negocio adicionales, conexiones con SAP/TNET/TradePro,
  conectores externos, otros flujos ni correos no autorizados.
- No registrar credenciales ni secretos en los artefactos del repositorio.

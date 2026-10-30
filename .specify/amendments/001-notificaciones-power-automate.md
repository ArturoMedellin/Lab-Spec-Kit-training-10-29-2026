# Enmienda 1.1.0: Notificaciones de deducciones

**Estado**: Aprobada e integrada mediante el pull request #9

**Constitución anterior**: 1.0.0

**Versión ratificada**: 1.1.0

## Motivación

El seguimiento de una deducción requiere avisar al siguiente rol responsable
cuando se registra y cuando el Revisor UD agrega el número de documento SAP.
La constitución 1.0.0 excluía las notificaciones y los flujos de Power
Automate, por lo que este alcance requirió una enmienda antes de especificarse
o implementarse.

## Cambio ratificado

El Principio VIII se sustituyó por el siguiente texto:

> ### VIII. Alcance de la versión
>
> Los reportes están fuera del alcance de esta versión. Mediante una
> especificación aprobada, se permite usar flujos de Power Automate únicamente
> para enviar estas notificaciones de deducciones por correo:
>
> 1. Cuando un Aprobador UA registre una deducción, notificar al Revisor UD.
> 2. Cuando un Revisor UD agregue el número de documento SAP a una deducción,
>    notificar al Asociado Cash App.
>
> Cada correo debe incluir un enlace al registro y un resumen que permita
> identificar el evento y los datos del registro pertinentes. Los flujos deben
> respetar los roles de seguridad, las políticas DLP del ambiente y la solución
> única con prefijo `AIMA`. Deben usar conexiones y conectores corporativos
> aprobados para el ambiente; no se permiten servicios de correo externos ni
> secretos en el repositorio. No se autorizan otros avisos, flujos, reportes ni
> automatizaciones por esta excepción.

El Principio VII permanece sin cambios: la aplicación no se conecta con SAP,
TNET ni TradePro ni contabiliza deducciones. El número de documento SAP se
captura manualmente; su incorporación o modificación solo puede activar la
notificación autorizada arriba.

## Impacto

- Habilita únicamente los dos eventos de notificación descritos; el resto de
  notificaciones, automatizaciones y reportes continúa fuera de alcance.
- La especificación correspondiente deberá definir destinatarios, contenido
  del resumen, enlace al registro, manejo de fallos y verificación de permisos.
- El plan deberá justificar el flujo dentro de una solución Power Platform con
  prefijo `AIMA` y confirmar los conectores permitidos por las políticas DLP.
- No requiere migración de datos ni cambia los roles o permisos actuales.
- La captura manual del número SAP permanece independiente de cualquier
  conexión con SAP.

## Aprobación y versionado

Arturo Medellín aprobó la enmienda y se integró mediante el pull request #9.
El Principio VIII y la versión de la constitución se actualizaron a 1.1.0,
conforme al cambio MINOR por ampliación acotada del alcance. La especificación
funcional puede avanzar y sigue sujeta al proceso de aprobación humana definido
en el Principio X.

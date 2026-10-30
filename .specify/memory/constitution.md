# Constitución de la aplicación de Deducciones

## Principios Fundamentales

### I. Plataforma Power Platform
La solución MUST ser una aplicación model-driven sobre Dataverse y MUST usar
únicamente servicios de Power Platform. La autenticación corporativa con
Microsoft Entra ID descrita en el principio III es la única dependencia externa
permitida en esta versión.

### II. Solución única y prefijo
Todos los componentes de la aplicación MUST vivir en una sola solución de
Power Platform y MUST llevar el prefijo del publicador `AIMA`. Cada spec y plan
que agregue componentes MUST identificar cómo se cumple este requisito.

### III. Identidad, roles y DLP
El acceso MUST autenticarse con Microsoft Entra ID usando una cuenta de Gruma.
Dataverse MUST autorizar el acceso mediante roles de seguridad; la aplicación
MUST respetar las políticas DLP del ambiente. Ningún diseño puede eludir los
controles de identidad, permisos o DLP configurados por la organización.

### IV. Roles base
La aplicación MUST definir los roles Asociado Cash App, Revisor UD y
Aprobador UA. La spec de cada cambio MUST establecer los permisos que necesita
cada rol; el plan y las pruebas MUST comprobar que el acceso permitido y
denegado coincide con esa definición.

### V. Modelo de datos simple
El modelo MUST tener una tabla principal llamada Deducción y puede incluir
catálogos editables. Una entidad de negocio adicional requiere justificación
y aprobación explícita en la spec antes de diseñarse.

### VI. Idioma
Los textos visibles de la aplicación y la documentación del proyecto MUST estar
en español. Una excepción requiere justificación y aprobación en la spec
correspondiente.

### VII. Límite con sistemas externos
En esta versión, la aplicación MUST registrar y dar seguimiento a las
deducciones. MUST NOT contabilizar ni conectarse con SAP, TNET o TradePro.
Cualquier cambio de este límite requiere una enmienda aprobada a esta
constitución y una spec nueva o actualizada.

### VIII. Alcance de la versión
Los reportes están fuera del alcance de esta versión. Mediante una
especificación aprobada, se permite usar flujos de Power Automate únicamente
para enviar estas notificaciones de deducciones por correo:

1. Cuando un Aprobador UA registre una deducción, notificar al Revisor UD.
2. Cuando un Revisor UD agregue el número de documento SAP a una deducción,
   notificar al Asociado Cash App.

Cada correo debe incluir un enlace al registro y un resumen que permita
identificar el evento y los datos del registro pertinentes. Los flujos deben
respetar los roles de seguridad, las políticas DLP del ambiente y la solución
única con prefijo `AIMA`. Deben usar conexiones y conectores corporativos
aprobados para el ambiente; no se permiten servicios de correo externos ni
secretos en el repositorio. No se autorizan otros avisos, flujos, reportes ni
automatizaciones por esta excepción.

### IX. Cero secretos
El repositorio MUST NOT contener contraseñas, llaves, tokens ni credenciales.
Los cambios MUST evitar incorporar secretos en código, configuración,
documentación, datos de prueba o artefactos generados.

### X. Desarrollo guiado por la spec y requisitos comprobables
Todo cambio MUST originarse en una spec aprobada; nadie puede modificar la
aplicación manualmente fuera del proceso aprobado por esa spec. Una persona
MUST aprobar la spec, el plan, el diseño y el ensayo antes de avanzar a la
siguiente etapa. El agente MUST NOT aprobar su propio trabajo.

Cada requisito MUST poder verificarse mediante un criterio observable con la
forma «CUANDO <contexto>, EL SISTEMA DEBE <resultado observable>». El ensayo
MUST comprobar esos criterios y registrar sus resultados.

## Alcance y restricciones

La aplicación administra el registro y seguimiento de deducciones dentro de
Power Platform. Los roles, permisos, datos y comportamiento que no se
especifican aquí se definen en la spec correspondiente, siempre dentro de los
límites de esta constitución.

## Flujo de desarrollo y calidad

Cada plan MUST incluir una tabla titulada «Revisión de la constitución» antes
de diseñar la solución. La tabla MUST enumerar los diez principios e indicar
«Cumple» para cada uno, con una justificación breve que vincule el principio
con la decisión de diseño. Si un principio no se cumple, el trabajo MUST
detenerse hasta que se ajuste la propuesta o se apruebe una enmienda.

Una persona MUST aprobar explícitamente la spec, el plan, el diseño y el ensayo.
Las revisiones MUST verificar el cumplimiento de los principios, los permisos
por rol, las políticas DLP aplicables, los criterios de aceptación y la ausencia
de secretos en los cambios.

## Gobierno

Esta constitución prevalece sobre prácticas o planes que entren en conflicto
con ella. Las enmiendas MUST ser propuestas mediante pull request y MUST ser
aprobadas por Arturo Medellín antes de integrarse. El pull request MUST explicar
la motivación, el impacto y la migración necesaria, si aplica.

El versionado sigue SemVer:

- **MAJOR**: se elimina o redefine un principio de forma incompatible.
- **MINOR**: se agrega un principio o requisito de gobierno.
- **PATCH**: se aclara o corrige la redacción sin cambiar su significado.

Cada pull request y cada revisión de cumplimiento MUST comprobar que la spec,
el plan, el diseño, el ensayo y los cambios de la aplicación respetan esta
constitución. Si hay incumplimientos, deben resolverse antes de aprobar el
cambio.

**Versión**: 1.1.0 | **Ratificada**: 2026-10-29 | **Última enmienda**: 2026-10-30

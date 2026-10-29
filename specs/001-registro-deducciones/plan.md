# Plan de implementación: Registro de deducciones

**Rama/feature**: `001-registro-deducciones`
**Fecha**: 2026-10-29
**Spec**: [spec.md](spec.md)

## Resumen

Entregar el registro centralizado de deducciones no autorizadas para que Aprobador UA cree registros y los roles autorizados consulten la información. La solución se limitará al registro, consulta y captura manual del número de documento SAP posterior a la contabilización externa. No incluirá revisión/cierre UD, catálogos, aprobaciones, avisos, reportes ni conexiones con SAP, TNET o TradePro.

La constitución del proyecto determina la plataforma: una aplicación model-driven sobre Dataverse, empaquetada en una solución con prefijo de publisher `AIMA`. La especificación funcional sigue siendo independiente de tecnología.

## Contexto técnico

**Lenguaje/versión**: Componentes declarativos de Power Platform; no se especifica un lenguaje ni una versión de runtime.

**Dependencias principales**: Dataverse y Microsoft Entra ID; ambiente sujeto a las políticas DLP vigentes.

**Almacenamiento**: Dataverse, en la tabla principal Deducción.

**Pruebas**: Validación manual de aceptación en un entorno autorizado de Power Platform, incluida la verificación de acceso por rol. Este repositorio no contiene un runner de pruebas de la aplicación.

**Plataforma destino**: Power Platform, aplicación model-driven.

**Tipo de proyecto**: Aplicación interna de negocio.

**Meta de rendimiento**: Aprobador UA completa un registro válido en menos de un minuto, como establece SC-001.

**Restricciones**: Una solución con prefijo `AIMA`; cuentas Gruma y roles base; respeto a DLP; sin integraciones, flujos, avisos, reportes ni cierre UD en esta feature.

**Escala/alcance**: Una feature de registro en una aplicación interna. La spec no define volúmenes o concurrencia; no se agregan metas cuantitativas no aprobadas.

## Revisión de la constitución

*Puerta previa a investigación y diseño: los diez principios cumplen en esta propuesta. La aprobación explícita del plan y diseño por una persona sigue pendiente.*

| Principio constitucional | Estado | Justificación |
|---|---|---|
| I. Plataforma Power Platform | Cumple | La aplicación será model-driven sobre Dataverse y usará servicios Power Platform. |
| II. Solución única y prefijo | Cumple | Los componentes de la feature se incorporarán a una única solución con prefijo `AIMA`. |
| III. Identidad, roles y DLP | Cumple | Se usarán cuentas Gruma de Entra ID, permisos de Dataverse y las políticas DLP del ambiente. |
| IV. Roles base | Cumple | Los tres roles definidos en la constitución tienen permisos explícitos en la spec y se comprobarán en pruebas. |
| V. Modelo de datos simple | Cumple | El diseño utiliza la tabla principal Deducción y no agrega entidades de negocio. |
| VI. Idioma | Cumple | La documentación y las etiquetas/mensajes visibles de la aplicación estarán en español. |
| VII. Límite con sistemas externos | Cumple | No se conecta ni contabiliza en SAP, TNET o TradePro; la referencia SAP se captura manualmente. |
| VIII. Alcance de la versión | Cumple | No se agregan notificaciones, flujos de Power Automate, reportes ni tableros. |
| IX. Cero secretos | Cumple | El diseño y las pruebas no requieren incluir credenciales, tokens ni secretos en el repositorio. |
| X. Desarrollo guiado por la spec y requisitos comprobables | Cumple | El diseño deriva de requisitos observables; el plan, diseño y ensayo esperan aprobación humana y el agente no se autoaprueba. |

## Estructura del proyecto

Este repositorio es un proyecto Spec Kit y no contiene una base de código de la aplicación. Los artefactos de especificación y diseño se mantienen junto a la feature; los componentes de la aplicación se entregarán en la solución Power Platform `AIMA`, no en directorios de código inventados para este repositorio.

```text
specs/
└── 001-registro-deducciones/
    ├── spec.md
    ├── plan.md
    ├── research.md
    ├── data-model.md
    ├── quickstart.md
    └── contracts/
        └── deducciones-ui.md
```

No se generan contratos de integración externa; `contracts/deducciones-ui.md` documenta el contrato visible de la aplicación para los roles usuarios.

## Fases de diseño

### Fase 0: investigación

Documentar las decisiones de plataforma y alcance que ya determinan la constitución y la spec; registrar explícitamente las cuestiones operativas no definidas que requieren decisión humana, sin ampliar el producto.

### Fase 1: diseño

- Definir el modelo conceptual del registro, atributos, reglas de validación y permisos en [data-model.md](data-model.md).
- Preparar una guía de preparación y validación manual en [quickstart.md](quickstart.md).
- Especificar campos, reglas y acceso de la interfaz de usuario en [contracts/deducciones-ui.md](contracts/deducciones-ui.md).
- Repetir la revisión de la constitución después de completar el diseño. Cualquier cambio de alcance que la contradiga requiere resolución humana antes de implementación.

### Revisión de la constitución posterior al diseño

Tras producir los artefactos de Fase 1, se volvieron a comprobar los principios I–X: **Cumple** en todos los casos. El diseño mantiene la solución única `AIMA`, tabla principal Deducción, permisos de los tres roles, idioma español, ausencia de secretos e integraciones y exclusiones de automatización, avisos y reportes. La aprobación explícita por una persona continúa pendiente.

## Puerta de aprobación

**Estado: pendiente de aprobación humana.** La revisión del diseño debe confirmar el modelo conceptual, los permisos y las cuestiones operativas pendientes antes de `/speckit-tasks` o de cualquier implementación. No se crean tareas en esta fase.

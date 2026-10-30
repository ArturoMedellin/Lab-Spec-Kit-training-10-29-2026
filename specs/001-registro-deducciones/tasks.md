---
description: "Tareas para implementar el registro de deducciones"
---

# Tareas: Registro de deducciones

**Entrada**: Documentos de diseño en `specs/001-registro-deducciones/`
**Prerequisitos**: `plan.md`, `spec.md`, `research.md`, `data-model.md`, `contracts/deducciones-ui.md`, `quickstart.md`
**Ubicación**: Las rutas `Solución Power Platform AIMA/...` identifican componentes Dataverse/model-driven; no son archivos de código de este repositorio.

## Fase 1: Preparación

**Propósito**: Preparar la solución Power Platform compartida.

- [x] T001 Confirmado por Arturo: ambiente Power Platform `d944c75b-d3c5-e047-8426-58ffb8f7d70f` aprobado y políticas DLP revisadas en `Solución Power Platform AIMA/Entorno`
- [x] T002 Creada la solución no administrada `Registro de deducciones` (`AIMA_RegistroDeducciones`) con publisher `AIMA` y prefijo efectivo `aima` aceptado por Arturo en `Solución Power Platform AIMA/Solución`
- [x] T003 Creada y publicada en español la aplicación model-driven `Registro de deducciones` (ID `2191dba8-c8d3-f111-a05c-000d3a123dae`, nombre único `registro_de_deducciones_1af6d2f2`) dentro de `Solución Power Platform AIMA/Aplicaciones/Registro de deducciones`

## Fase 2: Fundamentos

**Propósito**: Crear la tabla central y los permisos base requeridos por todas las historias.

- [x] T004 Crear la tabla principal Deducción en `Solución Power Platform AIMA/Tablas/Deducción` (nombre lógico `aima_table1`)
- [x] T005 Configurar Asociado Cash App, Aprobador UA y Revisor UD con lectura organizacional en `Solución Power Platform AIMA/Roles de seguridad/Deducción` (configuración final verificada en Dataverse: Read = Organization para los tres; Create = User solo para Aprobador UA según T013; Write = Organization solo para Revisor UD según T017; Create y Write = None para los demás casos; roles personalizados basados en App Opener)

**Punto de control**: solución AIMA, aplicación inicial, tabla Deducción y lectura común preparados.

## Fase 3: Historia 1 - Registrar una deducción (Prioridad: P1) 🎯 MVP

**Objetivo**: Aprobador UA registra una deducción válida con sus reglas de clasificación y guardado.

**Prueba independiente**: Como Aprobador UA, completar y guardar una deducción válida en menos de un minuto. Comprobar referencia por origen, clasificación F-30/F-28, centro de costo condicional, advertencia de posible duplicado y valores CP, Nueva y fecha de registro. Confirmar que los otros roles no pueden crear.

- [x] T006 [US1] Crear las columnas de registro en `Solución Power Platform AIMA/Tablas/Deducción`: Cliente (`aima_Cliente`, texto requerido); Origen (`aima_Origen`, opción local de selección única: Pago, Ajuste AR y Netting, requerido); Referencia (`aima_Referencia`, texto requerido); Monto (`aima_Monto`, moneda con precisión fija de 2 decimales y requerido; Arturo confirmó que la moneda base del entorno es USD); Centro de costo UD (`aima_CentrodecostoUD`, texto opcional para aplicar la obligatoriedad condicional de la regla).
- [x] T007 [US1] Configurar y publicar el formulario principal como «Alta de deducción» en `Solución Power Platform AIMA/Aplicaciones/Registro de deducciones/Formularios/Alta`, con Cliente, Origen, Referencia y Monto requeridos, y Centro de costo UD opcional
- [x] T008 [US1] Exigir la Referencia (`aima_Referencia`) en la columna Dataverse y el formulario publicado «Alta de deducción»; el requisito Business required bloquea guardar sin referencia en `Solución Power Platform AIMA/Aplicaciones/Registro de deducciones/Validaciones de alta`
- [x] T009 [US1] Bloquear guardado sin Cliente (`aima_Cliente`), Origen (`aima_Origen`) o Monto (`aima_Monto`) mediante Business required; Monto usa la moneda base USD, precisión fija de 2 decimales y rango configurado que incluye cero y valores negativos en `Solución Power Platform AIMA/Aplicaciones/Registro de deducciones/Validaciones de alta`
- [x] T010 [US1] Determinar F-30 para DOT Foods, Safeway y Gordon Food Service, y F-28 para cualquier otro cliente; ignorar mayúsculas/espacios iniciales o finales y exigir centro de costo UD solo para otros clientes en `Solución Power Platform AIMA/Aplicaciones/Registro de deducciones/Reglas de clasificación`
- [x] T011 [US1] Configurar Tipo de documento (opción CP predeterminada) y Estado de deducción (opción Nueva predeterminada); mostrar ambos junto con Created On y Created By de Dataverse en el formulario «Alta de deducción» publicado en `Solución Power Platform AIMA/Tablas/Deducción/Reglas de creación`
- [x] T012 [US1] Crear y publicar la regla de detección por Cliente, Origen y Referencia; habilitar la detección en la configuración del entorno y verificar que muestra advertencia y permite guardar la coincidencia en `Solución Power Platform AIMA/Aplicaciones/Registro de deducciones/Reglas de duplicados`
- [x] T013 [US1] Conceder Create = User para Deducción solo a Aprobador UA y verificar Create = None para Asociado Cash App y Revisor UD en `Solución Power Platform AIMA/Roles de seguridad/Deducción/Crear`

**Punto de control**: historia P1 funcional e independientemente comprobable.

## Fase 4: Historia 2 - Consultar deducciones (Prioridad: P2)

**Objetivo**: los tres roles base consultan todos los registros y reconocen cuáles continúan abiertos.

**Prueba independiente**: Con cada rol base, consultar registros creados por distintos usuarios y comprobar que una deducción cerrada no aparece como abierta.

- [x] T014 [P] [US2] Crear y publicar la vista «Todas las deducciones» sin filtros por creador, con columnas de cliente, origen, referencia, monto, estado de deducción, tipo de documento y auditoría; ordenar por fecha de creación descendente en `Solución Power Platform AIMA/Aplicaciones/Registro de deducciones/Vistas/Todas las deducciones`
- [x] T015 [P] [US2] Crear y publicar la vista «Deducciones abiertas» filtrando Estado nativo = Active para excluir registros inactivos/cerrados, sin definir transiciones de estado o cierre; ordenar por fecha de creación descendente en `Solución Power Platform AIMA/Aplicaciones/Registro de deducciones/Vistas/Deducciones abiertas`

**Punto de control**: los tres roles consultan todos los registros y distinguen abiertas de cerradas.

## Fase 5: Historia 3 - Completar referencia de contabilización (Prioridad: P3)

**Objetivo**: Revisor UD captura manualmente el número de documento SAP después de la contabilización externa.

**Prueba independiente**: Revisor UD agrega y consulta el número SAP sin exigirlo antes de contabilizar; confirmar que otros roles no modifican y que no hay conexión ni contabilización externa.

- [x] T016 [P] [US3] Agregar el campo opcional «Número de documento SAP» como texto de una línea (máximo 100 caracteres, nombre lógico `aima_numerodedocumentosap`) a la tabla Deducción; no exigirlo antes de contabilizar en `Solución Power Platform AIMA/Tablas/Deducción/Número de documento SAP`
- [x] T017 [P] [US3] Configurar Write = Organization para Revisor UD y Write = None para Asociado Cash App y Aprobador UA en `Solución Power Platform AIMA/Roles de seguridad/Deducción/Modificar`; Estado de deducción bloqueado en los formularios y cierre no implementado, con protección solo de formulario autorizada para el ensayo (no a nivel de API; pruebas efectivas por rol pendientes en T019)
- [x] T018 [US3] Publicar «Edición UD» para Revisor UD con Número de documento SAP opcional y editable, primero en el orden de formularios y sin ser fallback; conservar «Alta de deducción» como fallback con SAP de solo lectura y asociar los tres roles base a la app en `Solución Power Platform AIMA/Aplicaciones/Registro de deducciones/Formularios/Edición UD`; captura manual sin conexión con SAP, TNET o TradePro

**Punto de control**: historia P3 implementada sin integración externa.

La exportación de verificación confirma los permisos, las asociaciones de los tres roles con la app y el orden, fallback y controles publicados de ambos formularios. La prueba con cuenta administrativa confirmó el guardado sin SAP y su captura posterior manteniendo estado Nueva; el registro sintético se eliminó y se comprobó su ausencia. Esto no sustituye las pruebas con cuentas separadas de T019.

## Fase 6: Revisión y entrega

**Propósito**: Comprobar cumplimiento transversal y registrar la aprobación humana del ensayo.

- [ ] T019 Ejecutar y registrar los escenarios de aceptación y permisos por rol de `specs/001-registro-deducciones/quickstart.md` en `Solución Power Platform AIMA/Validación/Registro de deducciones`
- [ ] T020 Verificar que la solución respeta DLP, incluye los componentes en AIMA y no introduce secretos, flujos, avisos, reportes ni conexiones prohibidas en `Solución Power Platform AIMA/Revisión de liberación`
- [ ] T021 Obtener y registrar la aprobación humana del ensayo antes de liberar en `Solución Power Platform AIMA/Aprobación de aceptación`

## Dependencias y orden de ejecución

- **Preparación (Fase 1)**: T001 → T002 → T003.
- **Fundamentos (Fase 2)**: depende de T001–T003; T004 y T005 bloquean todas las historias.
- **US1 (P1)**: T006–T013, después de fundamentos; MVP prioritario e independiente.
- **US2 (P2)**: T014–T015, después de fundamentos; independiente de US1. T014 y T015 pueden ejecutarse en paralelo.
- **US3 (P3)**: T016–T018, después de fundamentos; T018 requiere T016 y T017. T016 y T017 pueden ejecutarse en paralelo si se modifican componentes distintos.
- **Revisión (Fase 6)**: T019–T021 depende de las historias que se pretenda liberar; aprobación humana obligatoria antes de liberar.

## Oportunidades de paralelismo

- T014 y T015 configuran vistas distintas y pueden ejecutarse en paralelo después de T004–T005.
- T016 (columna de tabla) y T017 (rol de seguridad) pueden ejecutarse en paralelo después de los fundamentos, evitando cambios simultáneos sobre el mismo componente.
- US2 y US3 pueden adelantarse en paralelo tras los fundamentos; se recomienda priorizar y completar US1 como MVP.
- Las tareas T006–T013 comparten datos o formulario; no paralelizarlas si la herramienta de autoría modifica el mismo componente.

## Estrategia de implementación

### MVP (solo US1)

1. Completar preparación y fundamentos (T001–T005).
2. Completar US1 (T006–T013).
3. Verificar de forma independiente el registro válido en menos de un minuto y las reglas de negocio.
4. No liberar hasta registrar el ensayo requerido y obtener aprobación humana.

### Entrega incremental

Completar preparación/fundamentos, luego US1, US2 y US3 en orden de prioridad; ejecutar la revisión constitucional y el ensayo manual antes de liberar.

## Validación de formato

Las 21 tareas usan casilla `- [ ]`, identificador secuencial `T001`–`T021`, etiquetas `[US1]`/`[US2]`/`[US3]` en fases de historia y una ruta de componente en cada descripción. No se generaron tareas de pruebas automatizadas; la validación manual exigida se registra en T019.
---
name: Guía de integración API AML y eventos
description: Guía de integración de la API REST de Pirani para creación de eventos, extracción de entidades de riesgo, campos parametrizados y registros AML.
status: final
tags:
  - integration
  - api
  - aml
  - events
  - openapi
updated: 2026-10-14
---

# Guía de integración API AML y eventos

## Resumen

Esta guía resume las operaciones HTTP que expone el contrato de integración de Pirani: creación de eventos, extracción en bloque de entidades de riesgo, consulta de campos parametrizados y alta/actualización de registros AML (`CLIENTS`, `COUNTERPARTIES`, `PRODUCTS`, `TRANSACTIONS`). La referencia normativa de peticiones, respuestas y esquemas es el contrato [`docs/openapi/openapi.yaml`](openapi/openapi.yaml) (OpenAPI `3.0.0`, versión de la API `1.1.0`).

El repositorio no contiene código de la implementación: los hechos de este documento provienen del contrato.

## Comportamiento actual

| Método y ruta | Operación (`operationId`) | Propósito |
|---|---|---|
| `POST /dev/services/events` | Create event (`createEvent`) | Crea un evento o incidente en el módulo indicado por `managementSystem`. |
| `GET /prod/services/appclients/data-extraction/v1/resource/{entityType}` | Get items (`getBusinessEntityItems`) | Extrae en bloque los registros de una entidad de riesgo, con paginación por `offset` y `limit`. |
| `GET /template/{entityType}` | Get all parameterized fields (`getParametrizedFields`) | Lista los campos parametrizados de una entidad AML. |
| `POST /entity/{entityType}` | Create a new record base type of entity (`createEntityRecord`) | Crea un registro de una entidad AML. |
| `PUT /entity/{entityType}` | Update a record base type of entity (`updateEntityRecord`) | Actualiza los campos de un registro de una entidad AML. |

La API de extracción acepta `entityType` con los valores `EVENTS`, `EVENT_LOSSES`, `EVENT_RECOVERIES`, `INCIDENTS`, `INCIDENT_LOSSES`, `INCIDENT_RECOVERIES`, `RISKS`, `CONTROLS`, `PROCESSES`, `THREATS_VULNERABILITIES`, `GENERATOR_FACTOR`, `ASSETS`, `ACTION_PLANS`, `REMEDIATION_PLANS`, `RULES`, `AUDIT_PROGRAMS`, `AUDIT_PLANS`, `GOALS`, `INDICATORS`, `EVALUATION_PLANS`, `EVALUATIONS` y `ALERTS`. Las operaciones AML aceptan `CLIENTS`, `COUNTERPARTIES`, `PRODUCTS` y `TRANSACTIONS`.

## Autenticación

- Las operaciones usan la cabecera `X-PIRANI-APIKEY` con un valor de marcador de posición (`${APIKEY}`) en las descripciones y ejemplos del contrato. El formato esperado de la clave es `PN.yyyyy.xxxxxxxxxxxx` (código `P002`).
- El contrato declara `security: []` en todas las operaciones y no define `securitySchemes`; la autenticación se describe solo en prosa. Ver Notas / Pendientes.
- La clave es única por organización y no debe distribuirse ni versionarse.

## Reglas de negocio

- **Eventos:** `managementSystem` admite `ORM`, `ISMS`, `AML` o `COMP` (`P010`). Longitudes máximas: `name` 200, `description` 2000 y `occurrenceLocation` 1000. Son obligatorios `managementSystem`, `creatorEmail`, `name`, `description`, `occurrenceLocation`, `startDate` y `discoveryDate`. `registrationDate` es opcional y debe ser anterior a las fechas de inicio y descubrimiento. La fecha no puede ser posterior a hoy (`EVT001`) y la fecha de inicio no puede ser posterior a la de descubrimiento (`EVT002`).
- **Campos dinámicos de evento:** los errores `DF01` a `DF012` cubren campos requeridos, valores, formatos numéricos y de fecha, y listas de opciones.
- **Extracción:** `limit` va de 1 a 1000 (por defecto 200). Sin `riskSystemCodes` se devuelven todos los sistemas de riesgo; con él se filtra por lista separada por comas (`ORM`, `ISMS`, `COMP`, `AML`, `AUD`). `timezone=org` formatea las fechas con la zona horaria de la organización; cualquier otro valor o su ausencia usa UTC. Si `nextOffset` es `null`, no hay más resultados.
- **Registros AML:** todos los atributos se envían como `string` y se convierten internamente según el tipo definido en la parametrización. Los campos obligatorios por tipo de entidad están listados en la descripción del grupo «Pirani REST Integration API for AML» del contrato. En la actualización no son modificables `identificationType` e `identificationNumber` de `PRODUCTS`, ni `productNumber` de `TRANSACTIONS`.

## Archivos relevantes

- [`docs/openapi/openapi.yaml`](openapi/openapi.yaml): contrato HTTP canónico.
- `API/AML/integration_api/main.yml`: ubicación original del contrato; copia idéntica salvo espacios finales y los cinco `operationId` añadidos (ver Notas / Pendientes).
- `README.md`: punto de entrada e índice de documentación.

## Notas / Pendientes

- **Sin implementación en el repositorio:** no hay controladores, rutas ni pruebas. La cobertura de rutas implementadas frente a operaciones documentadas no se puede calcular.
- **Sin `servers`:** el contrato no declara URL base. Los paths incluyen prefijos distintos (`/dev/services/...`, `/prod/services/...`) y otros sin prefijo (`/template/...`, `/entity/...`); el único host que aparece es el de un ejemplo curl (`apidev.pirani.co`). Pendiente confirmar la URL base y si `/dev` y `/prod` son entradas distintas que requieran documentos separados.
- **Seguridad:** `security: []` en todas las operaciones contradice la cabecera obligatoria que describen las propias descripciones. Pendiente decidir si se declara un `securitySchemes` de tipo `apiKey` en cabecera; no se añadió para no inventar el esquema.
- **Esquema compartido AML:** las operaciones `POST` y `PUT` de `/entity/{entityType}` referencian `Client` para las cuatro entidades. Los esquemas `Counterparty`, `Product` y `Transaction` están definidos pero no referenciados. Pendiente separar el cuerpo por entidad.
- **Códigos de error en la extracción:** el enum del `403` lista `P0011` y `P0016`, probablemente `P011` y `P016`. No se corrigió.
- **Ejemplos:** los ejemplos de respuesta de las operaciones AML usan la clave `sucess`, mientras el esquema `GenericResponse` define `success`.
- **Respuestas no documentadas:** `POST /dev/services/events` no documenta `401` ni `403`; la extracción no documenta `401`.

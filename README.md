# Pirani Developer API

Repositorio de documentación de las APIs de integración de Pirani.

## ¿Qué es?

Repositorio que documenta las APIs HTTP de integración de Pirani: creación de eventos, extracción en bloque de entidades de riesgo, consulta de campos parametrizados y alta/actualización de registros AML. Es dueño del contrato OpenAPI y de su guía de integración. No contiene una implementación backend ejecutable, ni despliegue ni configuración de runtime.

## Stack / Comandos

Documentación pura: no hay lenguaje, framework ni dependencias de runtime evidenciados. No hay comandos de run, test ni build en el repositorio.

## API

- Contrato: [`docs/openapi/openapi.yaml`](docs/openapi/openapi.yaml) (OpenAPI 3.0.0, versión de la API 1.1.0).
- Base path: no declarada en el contrato (no tiene sección `servers`); ver la guía de integración.
- Autenticación: cabecera `X-PIRANI-APIKEY` descrita en prosa y ejemplos; no declarada como esquema de seguridad de OpenAPI.

## Documentación técnica

| Documento | Descripción | Tags |
|---|---|---|
| [`docs/integration-api.md`](docs/integration-api.md) | Guía de integración de la API REST de Pirani para creación de eventos, extracción de entidades de riesgo, campos parametrizados y registros AML. | `integration`, `api`, `aml`, `events`, `openapi` |
| [`docs/openapi/openapi.yaml`](docs/openapi/openapi.yaml) | Contrato HTTP canónico (OpenAPI) | `openapi` |

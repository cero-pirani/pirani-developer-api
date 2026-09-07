# Pirani Developer API

Documentation repository for Pirani integration APIs. The repository currently
contains the AML integration contract and supporting onboarding notes; it does
not contain a runnable backend implementation.

## Documentation

- [AML integration API guide](docs/integration-api.md)
- [OpenAPI contract](API/AML/integration_api/main.yml)

## API scope

The OpenAPI contract is version `1.1.0` and declares five HTTP operations for
event creation, business-entity extraction, parameterized-field lookup, and
AML entity creation/update. See the contract for request and response schemas.

## Authentication

The contract examples and endpoint descriptions use the `X-PIRANI-APIKEY`
header with a placeholder value such as `${APIKEY}`. Never commit a real API
key. The current contract does not declare a formal OpenAPI security scheme;
consumers should follow the provider's current integration guidance.

## Local development

No build, test, run, deployment, health-check, or environment configuration
commands are evidenced in this repository. It is documentation-only and has
no runtime dependencies.

## Known limitations

- No backend source, controller definitions, tests, CI workflow, or deployment
  configuration is present, so implementation-to-documentation route coverage
  cannot be calculated.
- The OpenAPI contract has no declared `servers` section, so an authoritative
  base URL cannot be inferred from this repository.
- Authentication is described in prose and examples rather than a reusable
  OpenAPI security scheme.

# Copilot Instructions

This repository defines the mandatory development standards for Power Apps Canvas Apps.

Before generating code, reviewing code, or proposing designs, always review the documents under `/docs`.

## Priority Order

1. `docs/naming-conventions.md`
2. `docs/power_apps_canvas_app_dev_standard.md`

When standards conflict, the higher priority document takes precedence.

## Samples

Always follow implementation patterns demonstrated in `/samples`.

Samples are considered reference implementations of the standards.

## Requirements

* Follow all naming conventions defined in the standards.
* Use `snake_case` for custom identifiers.
* Use the `fn_` prefix for User Defined Functions.
* Use plural names for collections.
* Use role-based names for containers.
* Use data source prefixes such as `sp_`, `dv_`, and `api_`.
* Prefer standard Power Platform capabilities over custom implementations.
* Optimize for maintainability, readability, and performance.

## Restrictions

Do not introduce alternative naming conventions unless explicitly requested.

Do not create custom frameworks, patterns, or abstractions that conflict with the standards defined in this repository.

When proposing multiple solutions, identify the solution that best aligns with the standards as the recommended option.

# Copilot Instructions

This repository defines mandatory development and testing standards for Power Apps Canvas Apps.

Before generating or modifying code, designs, tests, or traceability artifacts, read the relevant standards under `/docs`.

## Standard Documents

Read the standards in this order:

1. `docs/naming-conventions.md`
2. `docs/power_apps_canvas_app_dev_standard.md`
3. `docs/testing-standard.md`
4. `docs/test-traceability-standard.md`
5. `docs/test-case-schema.md`

When documents conflict:

1. A document dedicated to the specific subject takes precedence over a general document.
2. If two documents cover the same subject, the document listed earlier above takes precedence.
3. Report the conflict instead of silently choosing an interpretation when the expected behavior may change.

## Required Artifact Order

For test design and impact analysis, create or confirm artifacts in this order:

1. Feature list
2. Data-flow matrix
3. Business function chart
4. Use-case dependency table
5. Test cases

All later artifacts must reference IDs defined in the feature list or the relevant preceding artifact.

If an implementation asset is not registered in the feature list, report it as an unregistered feature. Do not guess or reuse another feature ID.

## Identifier Rules

Use the following feature IDs:

- `UI001`: one Canvas App as a parent management unit
- `UI001-01`: one Screen under the Canvas App
- `IF001`: one external-system send/receive function
- `BT001`: one scheduled, bulk, or asynchronous process
- `RP001`: one report
- `CM001`: one public UDF or public method
- `MG001`: one migration target

Classify `IF` and `BT` by responsibility, not by implementation technology.

Use:

- `REQ001` for requirements
- `UC001` for use cases
- `DF001` for data-flow paths
- `FUT-UI001-01-001` for functional-unit tests
- `FIT-DF001-001` for integration tests
- `ST-UC001-001` for system tests

Do not reuse retired IDs.

## Development Requirements

- Follow `docs/naming-conventions.md`.
- Use `snake_case` for custom identifiers.
- Use the `fn_` prefix for User Defined Functions.
- Use plural names for collections.
- Use role-based names for containers.
- Use data-source prefixes such as `sp_`, `dv_`, and `api_`.
- Prefer standard Power Platform capabilities over custom implementations.
- Optimize for maintainability, readability, testability, and performance.
- Use approved environment-specific SharePoint connections. Do not complete formal testing against a developer-created substitute list.

## Testing Requirements

- Treat functional-unit, integration, and system tests as assurance classifications, not sequential project phases.
- A Screen functional-unit test includes actual writes to the approved development or test SharePoint list when the Screen owns that responsibility.
- Generate integration tests from actual data-flow paths, not from a Cartesian product of functions.
- Use the business function chart and use-case dependency table for system-test impact analysis.
- Select regression tests from direct changes, callers/callees, data-flow paths, and affected use cases.
- Determine expected results from approved requirements and designs before implementation code.
- Do not change expected results merely to make a failing test pass.
- Update traceability artifacts in the same change when functions, schemas, data flows, use cases, or tests change.

## UI Automation

Prefer selectors in this order:

1. Power Platform Playwright Toolkit Page Objects or helpers
2. `data-control-name`
3. AccessibleLabel, role, or ARIA attributes
4. Other stable attributes
5. Display text

Avoid DOM hierarchy, CSS classes, and XPath unless no stable alternative exists and the reason is documented.

## Samples

- Implementation samples are under `samples/apps/`.
- Test-design samples are under `samples/test-design/`.
- Samples illustrate the standards but do not override them.
- If a sample conflicts with a standard, follow the standard and report the sample as outdated.

## Restrictions

- Do not introduce alternative naming or ID conventions unless explicitly requested.
- Do not create custom frameworks or abstractions that conflict with these standards.
- Do not use production business data in development or test environments.
- Do not create, update, or delete production data during automated tests without explicit approval and a documented recovery plan.
- Do not treat assumptions as confirmed facts. Record assumptions and unresolved questions.

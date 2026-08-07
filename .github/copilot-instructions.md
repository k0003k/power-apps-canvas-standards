# Copilot Instructions

This repository defines mandatory development and testing standards for Power Apps Canvas Apps.

Before generating or modifying code, designs, tests, test specifications, Playwright scripts, or traceability artifacts, read the relevant standards under `/docs`.

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
5. Test specifications
6. Executable test scripts, when automation is selected

All later artifacts must reference IDs defined in the feature list or the relevant preceding artifact.

If an implementation asset is not registered in the feature list, report it as an unregistered feature. Do not guess or reuse another feature ID.

Do not generate executable test scripts directly from implementation code when an approved test specification is required. Create or update the test specification first, then generate the executable test from it.

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
- `FUT-UI001-01-001` for a functional-unit Test Specification
- `FIT-DF001-001` for an integration Test Specification
- `ST-UC001-001` for a system Test Specification

A Test Specification contains one or more concrete test cases.

Within a Test Specification, use local case IDs:

- `C001`
- `C002`
- `C003`

When an executable test, log entry, report, or evidence item requires a globally unique execution-case identifier, combine the Test Specification ID and case ID:

- `FUT-UI001-01-001-C001`
- `FIT-DF001-001-C001`
- `ST-UC001-001-C001`

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

## Testing Terminology

Use the following terms consistently.

### `preconditions`

Conditions that must be true before the test starts but are not themselves the business data under test.

Examples:

- test environment
- authenticated user role
- required connection or service availability
- feature configuration

### `test_data`

Data or business state that already exists when the test starts.

Examples:

- user master records
- existing transactions
- records in a specific status
- SharePoint master data
- existing reference data

Do not use `test_data` for values entered or passed to the target function during the test.

### `input_data`

Values supplied to the target function during test execution.

Examples:

- screen input values
- uploaded files
- interface messages
- API request bodies
- function arguments

### `expected_result`

The expected result for a concrete combination of `test_data` and `input_data`.

It may contain multiple verification targets, for example:

- UI state
- saved SharePoint data
- returned value
- interface output
- processing state
- error code
- notification
- log entry

Use the singular key `expected_result` for one concrete test case even when it contains multiple assertions.

## Test Specification and Test Case Structure

Treat a Test Specification and a Test Case as different levels.

A Test Specification defines:

- test objective
- target features
- related requirements, use cases, and data flows
- test design techniques
- individual conditions
- reusable `test_data`
- reusable `input_data`
- concrete test cases
- coverage evidence

A Test Case defines one concrete combination of:

`test_data` + `input_data` -> `expected_result`

Use YAML structures defined in `docs/test-case-schema.md`.

Do not duplicate large data definitions in every case when they can be defined once and referenced by ID.

Example conceptual structure:

```yaml
test_spec_id: FUT-UI001-01-001

test_data:
  TD001:
    values: {}

input_data:
  IN001:
    values: {}

cases:
  - case_id: C001
    test_data_ref: TD001
    input_data_ref: IN001
    expected_result: {}
```

## Test Design Requirements

Treat functional-unit, integration, and system tests as assurance classifications, not sequential project phases.

- A Screen functional-unit test includes actual writes to the approved development or test SharePoint list when the Screen owns that responsibility.
- Generate integration tests from actual data-flow paths, not from a Cartesian product of functions.
- Use the business function chart and use-case dependency table for system-test impact analysis.
- Select regression tests from direct changes, callers/callees, data-flow paths, shared datastore fields, and affected use cases.
- Determine expected results from approved requirements and designs before implementation code.
- Do not change expected results merely to make a failing test pass.
- Update traceability artifacts in the same change when functions, schemas, data flows, use cases, Test Specifications, or tests change.

## MC/DC and Boundary-Value Analysis

When a decision contains multiple individual conditions joined by logical operators such as `AND` or `OR`, use MC/DC as the default method for selecting combinations unless the applicable specification requires another technique.

In this repository, **individual condition** means an MC/DC atomic condition: one true/false condition that cannot be further separated by `AND` or `OR`.

Example:

```text
user.status == ACTIVE
AND amount <= 100000
AND user.blocked == false
```

contains three individual conditions.

For MC/DC:

1. Identify and name each individual condition, such as `C1`, `C2`, and `C3`.
2. Identify whether each condition is derived from `test_data`, `input_data`, or another explicitly defined source.
3. Select cases so that each individual condition is shown to independently affect the overall decision outcome.
4. Record the pair of cases that demonstrates independence for each condition.
5. Do not require exhaustive Cartesian-product testing when MC/DC coverage is satisfied.
6. If MC/DC cannot be satisfied because a combination is impossible or prohibited by the business rules, document the reason instead of inventing data.

Use a structure such as:

```yaml
mcdc_coverage:
  C1:
    pair: [C001, C002]
  C2:
    pair: [C001, C003]
  C3:
    pair: [C001, C004]
```

For numeric, date, time, length, count, or similar conditions with meaningful boundaries, apply boundary-value analysis in addition to MC/DC.

MC/DC determines which logical combinations are required. Boundary-value analysis determines representative concrete values for those conditions and may add cases beyond the minimum MC/DC set.

Do not enumerate every omitted Cartesian-product combination merely to prove reduction. Record the selected strategy and coverage instead.

## Expected-Result Sources

Determine expected results in this order:

1. approved business requirements and use cases
2. approved basic or detailed design
3. approved business rules and data definitions
4. approved test specifications
5. implementation code

Implementation code must not override an approved higher-level specification.

If the available sources do not define the expected result sufficiently:

- do not invent the expected result
- record the uncertainty in `assumptions` or unresolved questions
- request clarification when the uncertainty affects pass/fail judgment

## Test Data and Environment Safety

- Use development or test environments for automated update tests.
- Do not use production business data in development or test environments.
- Create unique test data where needed to avoid collisions.
- Make cleanup requirements explicit for records created or modified during the test.
- Do not create, update, or delete production data during automated tests without explicit approval and a documented recovery plan.
- Do not commit secrets, access tokens, authenticated sessions, Playwright `storageState`, passwords, connection strings, certificates, or production personal data.

## Playwright Automation

A YAML Test Specification is not directly executable by Playwright.

When automation is selected:

1. Read the approved Test Specification.
2. Resolve the target features, data flows, selectors, and environment.
3. Use Power Platform Playwright Toolkit Page Objects or helpers where applicable.
4. Generate or update the Playwright TypeScript test.
5. Preserve the Test Specification ID and case ID in the executable test name or metadata.
6. Execute the selected cases and retain evidence as required.
7. Do not silently change the Test Specification to match implementation behavior.

Prefer UI selectors in this order:

1. Power Platform Playwright Toolkit Page Objects or helpers
2. `data-control-name`
3. AccessibleLabel, role, or ARIA attributes
4. Other stable attributes
5. Display text

Avoid DOM hierarchy, CSS classes, and XPath unless no stable alternative exists and the reason is documented.

Do not hide flaky behavior with fixed waits or retry-only changes. Record the initial failure and investigate the cause.

## Change Impact and Regression

When implementation or specifications change, identify impact from all of the following:

1. directly changed feature, code, or datastore field
2. callers and callees
3. related data-flow paths and shared datastore fields
4. use cases using the changed feature
5. preceding or following dependent use cases
6. Test Specifications referencing the affected items

Select regression cases based on the resulting impact rather than running unrelated combinations by default.

## Samples

- Implementation samples are under `samples/apps/`.
- Test-design samples are under `samples/test-design/`.
- Samples illustrate the standards but do not override them.
- If a sample conflicts with a standard, follow the standard and report the sample as outdated.
- `samples/test-design/test-case-sample.md` demonstrates the expected Test Specification YAML style.

## Restrictions

- Do not introduce alternative naming or ID conventions unless explicitly requested.
- Do not create custom frameworks or abstractions that conflict with these standards.
- Do not treat assumptions as confirmed facts.
- Do not invent expected results.
- Do not change business behavior merely to make a test pass.
- Do not delete or weaken error handling merely to make a test pass.
- Do not silently disable failed tests.
- Do not mark a failed test as successful.
- Record assumptions and unresolved questions explicitly.

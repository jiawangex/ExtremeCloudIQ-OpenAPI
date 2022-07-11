# Introduction

There are dozens of domains and hundreds endpoints in ExtremeCloud IQ API for now, and the number is still growing.

You can run below command to get the latest OpenAPI statistics for the current OpenAPI definition:
```bash
pnpm stats
```

We may have many actions against the existing API definitions, such as:
* Changing existing API definitions
* Adding new API definitions
* Removing existing API definitions
* Adjust endpoint display order
* And so on

To improve maintainability, we adopt **multiple-domain** and **multiple-file** approach to define all OpenAPIs.

# File structure

## Top-level files
These files are shared by all domain OpenAPIs.

* [info-title](info-title): Define OpenAPI title.
* [info-version](info-version): Define OpenAPI version. We should bump the version in each release.
* [info-description.md](info-description.md): Defines OpenAPI description. It accepts Markdown syntax.
* [info-contact.yaml](info-contact.yaml): Defines OpenAPI contact information.
* [info-license.yaml](info-license.yaml): Defines OpenAPI license information.
* [info-termOfService](info-termOfService): Defines OpenAPI Term-Of-Service.
* [external-docs.yaml](external-docs.yaml): Define external documentation name and link.
* [server-default.yaml](server-default.yaml): Define default server URL.

## Common folder
The [common/components](common/components) folder contains shared components definitions, which further contains below sub-folders:

* [securitySchemes](common/components/securitySchemes): Shared security schema definitions.
* [schemas](common/components/schemas): Shared schema definitions.
* [responses](common/components/responses): Shared API response definitions.

## Domain OpenAPI folder
The domain OpenAPI folder are named as `{domain-name}` and the file and directory structures are the same:

* `openapi.yaml`: This file is domain OpenAPI entrypoint, which contains references to the entire domain API definition.
* `paths`: This directory contains all paths for the domain OpenAPI.
* `components`: This directory may contain `schemas`, `parameters` and `examples` directories used by the domain API definition.

# Introduction

There are dozens of domains and hundreds of endpoints in ExtremeCloud IQ API for now, and the number is still growing.

You can run the below command to get the latest OpenAPI statistics for the current OpenAPI definition:
```bash
pnpm stats
```

We may have many actions against the existing API definitions, such as:
* Changing existing API definitions
* Adding new API definitions
* Removing existing API definitions
* Adjust endpoint display order
* And so on

To improve maintainability and support efficient work for dozens of teams,
we adopt **multiple-domain** and **multiple-file** approach to define all OpenAPIs.

We will maintain the reusable parts as separate files and include them in the main (root) API definition
by referencing them with `$ref`.

# File structure

## Top-level files
These files are shared by all domain OpenAPIs.

* [info-title](info-title): The title of the API.
* [info-version](info-version): The version of the OpenAPI definition. **We should bump the version in each release.**
* [info-description.md](info-description.md): A short description of the API. **CommonMark syntax MAY be used for rich text representation.**
* [info-contact.yaml](info-contact.yaml): The contact information for the exposed API.
* [info-license.yaml](info-license.yaml): The license information for the exposed API.
* [info-termOfService](info-termOfService): A URL to the Terms of Service for the API. MUST be in the format of a URL.
* [external-docs.yaml](external-docs.yaml): Define external documentation name and link.
* [server-default.yaml](server-default.yaml): Define default server URL for the exposed API.

## Common folder
The [common/components](common/components) folder contains shared components definitions, which further contains below sub-folders:

* [securitySchemes](common/components/securitySchemes): Shared security schema definitions.
* [schemas](common/components/schemas): Shared schema definitions.
* [responses](common/components/responses): Shared API response definitions.

## Domain OpenAPI folder
The domain OpenAPI folder is named as `{domain-name}` and the file and directory structures are the same:

* `openapi.yaml`: This file is the domain OpenAPI **entry point**, which contains references to the entire domain API definition.
* `paths`: This directory contains all paths for the domain OpenAPI.
* `components`: This directory may contain `schemas`, `parameters`, and `examples` directories used by the domain API definition.

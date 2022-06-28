ExtremeCloud IQ™ API allows customers and partners to create solutions for the management, monitoring, and 
provisioning of any ExtremeCloud IQ™ environment.

To deliver high-quality REST API, we need to make sure all API designers and developers fully understand our API 
design principles and strictly follow our API design guidelines.

# API Design Guidelines

We adopted **Design First** (i.e. API First or Contract First) approach for the REST API definition.
Our API follows [OpenAPI 3.0.x specification](https://spec.openapis.org/oas/latest.html).

Please check [the details of ExtremeCloud IQ API guidelines](api-guideline.md).

# Tools

We recommend some useful OpenAPI tools to work with OpenAPI definition and validation.

## OpenAPI Editor

Both of the below editor tools are free, please choose any or both of them to use when adding new OpenAPI or 
changing existing OpenAPI.

### IntelliJ IDEA

Install [OpenAPI Editor plugin](https://plugins.jetbrains.com/plugin/14837-openapi-swagger-editor).

### Stoplight Studio

You can install desktop version (recommended) or use online version from [Stoplight official site](https://stoplight.io/studio).

## OpenAPI Linter

To make sure every API definition change meets [ExtremeCloud IQ API Guidelines](api-guideline.md), we recommend using 
both **Spectral** and **Redocly** to validate/lint new OpenAPI before creating pull request.

### Spectral

Please check [official installation guide](https://github.com/stoplightio/spectral#-installation-and-Usage).

* Install Spectral CLI via `npm`:
```bash
npm install -g @stoplight/spectral-cli
```

**Note:** If you don't have `Node.js` and `npm` installed, please check
[this page](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm) to install them.

* Verify Spectral CLI installation:
```bash
spectral --version
```

When working with IntelliJ IDEA, please also install [Spectral plugin](https://plugins.jetbrains.com/plugin/18520-spectral).

### Redocly CLI

Please check [official installation guide](https://redocly.com/docs/cli/installation/).

* Install Redocly CLI via `npm`:
```bash
npm i -g @redocly/cli@latest
```

* Verify Redocly CLI installation:
```bash
redocly --version
```

# OpenAPI Linting

## Linting with Spectral

```bash
spectral lint openapi/*.yaml
```

We have defined custom Spectral ruleset in [.spectral.yaml](.spectral.yaml).

Check [Spectral ruleset customization guide](https://meta.stoplight.io/docs/spectral/e5b9616d6d50c-custom-rulesets) if needed.

## Linting with Redocly

```bash
redocly lint openapi/*.yaml
```

We have defined custom Redocly ruleset in [redocly.yaml](redocly.yaml).

Check [Redocly ruleset customization guide](https://redocly.com/docs/cli/resources/custom-rules/) if needed.

# Bundle

We define some common components in `openapi/common` directory.
There are a lot of API domains. We split our APIs to multiple separate files to improve the maintainability.

For some OpenAPI tools don't support multi-file approach and require a single-file API definition, 
you can use `redocly` CLI to combine separate API definition files into one.

Bundle all OpenAPI yaml files in `openapi` directory to `dist` directory:
```bash
redocly bundle --output dist --ext yaml openapi/*.yaml
```

The CLI usage can be found [here](https://redocly.com/docs/cli/commands/bundle/).

# API Design Procedure

We need to follow the below procedure for any OpenAPI definition changes:
1. Create feature branch from release branch
2. Change OpenAPI definition
3. [Validate/linting](#openapi-linting) the changed OpenAPI definition by `Spectral` and `Redocly` with our custom ruleset
4. Create a GitHub pull request and add stakeholders (API Governance team, BE, FE, QA, PLM, etc.) to review and approve
5. Make sure resolve all concerns from stakeholders
6. Start working on the new OpenAPI parallel:
   1. Backend engineer start the API implementation
   2. Frontend engineer start the UI implementation
   3. QA engineer start to write test cases
   4. PLM start to communicate the new OpenAPI proposal to customer
7. Repeat step 2 to step 5 if additional OpenAPI definition change needed during step 6 (Anyone can initiate the process)
8. Merge changes to release branch
# ExtremeCloud IQ™ API

ExtremeCloud IQ™ API allows customers and partners to create solutions for the management, monitoring, and 
provisioning of any ExtremeCloud IQ™ environment.

To deliver high-quality REST API, we will make sure all API designers and developers fully understand our API 
design principles and strictly follow our API design guidelines.

# API Design Guidelines

We adopted **Design First** (i.e. API First or Contract First) approach for the REST API definition.
Our API follows [OpenAPI 3.0.x specification](https://spec.openapis.org/oas/latest.html).

Please check the details at [ExtremeCloud IQ API guidelines](api-guideline.md).

# Prerequisite

## Install Node.js
> You can SKIP this step if you have already installed `Node.js` before.

Please check [official site](https://nodejs.org/en/) to install `Node.js` on your local machine.

* Install `Node.js` via CLI:
If you are Mac user and have `homebrew` installed, it's very simple to install it:
```bash
brew install node
```

* Verify `Node.js` installation:
```bash
node --version
```

## Install pnpm
> You can SKIP this step if you have already installed `pnpm` before.

We are using `pnpm` as the dependency management tool in our project, please make sure 
[install it](https://pnpm.io/installation) first.

* Install `pnpm` via CLI:
```bash
curl -fsSL https://get.pnpm.io/install.sh | sh -
```

* Verify `pnpm` installation:
```bash
pnpm --version
```

## Install all dependencies for the project
> You can SKIP this step if you have run it before.

We rely on some external tools to lint, build and preview our OpenAPI, please make sure install all of them before proceeding.
```bash
pnpm install
```

# Get Started

## Define OpenAPI
There are dozens of domains and hundreds endpoints in ExtremeCloud IQ API for now, and the number is still growing.

To improve maintainability, we adopt **multiple-domain** and **multiple-file** approach to define all OpenAPIs.

Please check [This Documentation](openapi/README.md) for the details.

You are freely to choose any text editor tools to add or change OpenAPI definitions, but choosing the right tools can 
significantly improve your productivity.

We recommend you use one of the below tools to work with OpenAPI definition.

### IntelliJ IDEA
[IntelliJ IDEA](https://www.jetbrains.com/idea/) has a build-in plugin `OpenAPI specification` and is very good at refactoring.

Please also install [OpenAPI (Swagger) Editor](https://plugins.jetbrains.com/plugin/14837-openapi-swagger-editor) plugin, 
which adds some useful features include, for example, SwaggerUI and ReDoc preview, IntelliSense, linting, 
schema enforcement, code navigation, definition links, snippets, static security analysis, and more!

### Stoplight Studio
[Stoplight Studio](https://stoplight.io/studio) provides `Form` GUI and `Mocks` feature, which are very convenient in some use cases.

You can either install desktop version (recommended) or use online version.

### Visual Studio Code
[Visual Studio Code](https://code.visualstudio.com/) is a very popular free open source editor.

Please install [OpenAPI (Swagger) Editor](https://marketplace.visualstudio.com/items?itemName=42Crunch.vscode-openapi) extension
and [Redocly OpenAPI](https://marketplace.visualstudio.com/items?itemName=Redocly.openapi-vs-code) extension to help you
write, validate, and maintain your OpenAPI documents.

## Run OpenAPI Linting

You need to run OpenAPI linting whenever you make any OpenAPI definition change under [openapi](openapi) directory.

```bash
pnpm lint
```

To make sure every API definition change meets [ExtremeCloud IQ API Guidelines](api-guideline.md) requirements, we leverage
both **Redocly** and **Spectral** to validate/linting the new OpenAPI.

All errors **MUST** be resolved prior to creat pull request unless the remediations would result in breaking changes
for an existing API version.

### Redocly ruleset
We have defined custom Redocly ruleset in [.redocly.yaml](.redocly.yaml).

Check [Redocly ruleset customization guide](https://redocly.com/docs/cli/resources/custom-rules/) and
[Redocly assertion configuration guide](https://redocly.com/docs/cli/resources/rules/assertions) before making any changes.

If you want to use `Redocly` to lint the specific OpenAPI files, please run the below command:
```bash
pnpm redocly lint <path-to-openapi-files>
```

### Spectral ruleset
We have defined custom Spectral ruleset in [.spectral.yaml](.spectral.yaml).

Please check [Spectral ruleset customization guide](https://meta.stoplight.io/docs/spectral/e5b9616d6d50c-custom-rulesets) 
and [Spectral OpenAPI Rules](https://meta.stoplight.io/docs/spectral/4dec24461f3af-open-api-rules) before making any changes.

If you want to use `Spectral` to lint the specific OpenAPI files, please run the below command:
```bash
pnpm spectral lint <path-to-openapi-files>
```

## Bundle OpenAPI
API definitions can grow and become difficult to manage, especially if several teams are collaborating on them. 
It's a good practice to maintain the reusable parts as separate files, and include them in the main (root) API definition 
by referencing them with `$ref`. 

We place the common components in [openapi/common](openapi/common) directory and other OpenAPIs are reference to this file.

However, most OpenAPI tools don't support that multi-file approach, and require a single-file API definition.

The `bundle` command pulls the relevant parts of an API definition into a single file output in JSON or YAML format.
```bash
pnpm bundle
```
> 1. The above command will bundle all OpenAPI definitions under `openapi` directory to `bundled` directory.
> 2. Every bundled OpenAPI file is independent and not merged.

## Merge OpenAPI
After bundle, there are still a lot of OpenAPI files that splitted by business domains. We may want a single OpenAPI file
combine all of them.

To do this, we need to run `merge` command:
```bash
pnpm merge
```
> 1. The above command will merge all specified OpenAPI definitions under `bundled` directory into a single-file
     [xcloudiq-openapi.yaml](xcloudiq-openapi.yaml) under project root directory.
> 2. The merge configuration is [.openapi-merge.yaml](.openapi-merge.yaml).

## Build OpenAPI
To simplify the `lint`, `bundle` and `merge` workflow, we provide `build` command to combine all of them:
```bash
pnpm build
```

## Preview OpenAPI in Browser

We can preview the generated [xcloudiq-openapi.yaml](xcloudiq-openapi.yaml) in either Redoc or Swagger UI.

### Preview with Redoc
Please run the below command:
```bash
pnpm redoc
```
Then you can preview the OpenAPI at http://127.0.0.1:8888.

> 1. Redoc is prettier than Swagger UI, but it does not support `Try It` feature in the community edition.
> 2. Redoc **CAN** detect the OpenAPI file changes and reload the UI automatically when building a new OpenAPI.

If you want to preview a specific OpenAPI file with Redoc, please run the below command:
```bash
pnpm redocly preview-docs --port 8888 <openapi-file>
```
> Redoc can preview non-bundled OpenAPI definition, i.e. it can point to the files under `openapi` directory.

### Preview with Swagger UI
Please run the below command:
```bash
pnpm swagger
```
Then it will automatically open the default web browser and preview the OpenAPI at http://127.0.0.1:8899.

> 1. You can try the OpenAPI against a real environment.
> 2. Swagger **CANNOT** detect the OpenAPI file changes, so you should restart the preview when building a new OpenAPI.

If you want to preview a specific OpenAPI file with Swagger UI, please run the below command:
```bash
pnpm swagger-ui --port 8899 <openapi-file>
```
> Swagger UI cannot preview non-bundled OpenAPI definition, i.e. it can only point to the files under `bundled` directory,
> otherwise there maybe display errors when navigating in the UI.

## Get OpenAPI definition statistics
You can run below command to get the latest OpenAPI statistics for the current OpenAPI definition:
```bash
pnpm stats
```

The command will generate statistics for the following metrics:
* References
* External Documents
* Schemas
* Parameters
* Links
* Path Items
* Operations
* Tags

# API Design Procedure

We **MUST** follow the below procedure for any OpenAPI definition changes:

1. Create feature branch from release branch
2. Change OpenAPI definition
3. [Build the changed OpenAPI definitions](#build-openapi) to make sure they are passed our custom linting ruleset
4. Commit the OpenAPI definition changes and latest generated [xcloudiq-openapi.yaml](xcloudiq-openapi.yaml)
5. Create a GitHub pull request and add stakeholders (API Governance team, BE, FE, QA, PLM, etc.) to review and approve
6. Make sure resolve all concerns from stakeholders
7. Start working on the new OpenAPI parallel:
   1. Backend engineer start the API implementation
   2. Frontend engineer start the UI implementation
   3. QA engineer start to write test cases
   4. PLM start to communicate the new OpenAPI proposal to customer
8. Repeat `step 2` to `step 6` if additional OpenAPI definition change needed during `step 7` (**Anyone** can initiate the process)
9. Merge changes to release branch
10. Delete the feature branch
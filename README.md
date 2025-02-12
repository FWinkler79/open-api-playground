# OpenAPI Playground

- [What's in this Repository?](#whats-in-this-repository)
- [OpenAPI Document](#openapi-document)
- [Using Github Workflow and Github Actions to Generate Client APIs and Documentation](#using-github-workflow-and-github-actions-to-generate-client-apis-and-documentation)
- [Act](#act)
  - [Executing Github Workflows Locally](#executing-github-workflows-locally)
  - [Reconfiguration from Slim, Medium, Large](#reconfiguration-from-slim-medium-large)
- [References](#references)

## What's in this Repository?

This repository shows a simple setup for defining an API following the [OpenAPI specification](https://learn.openapis.org/). The repo shows how to use Github Actions and Workflow to validate, generate documentation and client code whenever the API document is changed. This ensures that whatever updates are made to the API, successful code generation is still guaranteed and the documentation is updated immediately.

## OpenAPI Document

The sample API used in this repository is defined in [openapi.yaml](openapi.yaml).

## Using Github Workflow and Github Actions to Generate Client APIs and Documentation

We are using Github Worklow and Github Actions to validate the API definition given in [openapi.yaml](openapi.yaml), to generate documentation and client code whenever the API definition file is changed and pushed to Github.

The workflow files are located in the [`.github/workflows`](.github/workflows/) folder. 

The workflow uses the [`openapitools-generator-action`](https://github.com/openapi-generators/openapitools-generator-action), which itself uses [`openapi-generator`](https://github.com/OpenAPITools/openapi-generator).

```yaml
name: OpenAPI Client Generator
run-name: OpenAPI Client Generator Github Action 🚀
# whenever a change is pushed to this repository, execute the jobs given below.
on: [push]
jobs:
  # Provide an arbitrary job name
  generate-java-client:
    runs-on: ubuntu-latest
    name: Books-Example
    steps:

      # Checkout your code
      - name: Checkout
        uses: actions/checkout@v2

      # Use the action to generate a `java` client package
      # This uses the path the openapi document to explicitly point to openapi.yaml in the current workspace.
      # If not specified the default will point to openapi.json
      - name: Generate Java Client
        uses: openapi-generators/openapitools-generator-action@v1
        with:
          openapi-file: openapi.yaml
          generator: java
          config-file: generator-config-java.yaml
    
      # Generate documentation
      - name: Generate OpenAPI Documentation
        uses: openapi-generators/openapitools-generator-action@v1
        with:
          openapi-file: openapi.yaml
          generator: html2
```

The `openapi-generator` can be customized with a configuration file as described [here](https://openapi-generator.tech/docs/usage#configuration-file). For the `java` generator, the following configuration properties exist that each can be provided via a `yaml` file. 

The file [`generator-config-java.yaml`](generator-config-java.yaml) contains the Java generator configurations:

```yaml
# See: https://openapi-generator.tech/docs/usage#configuration-file
# See: https://openapi-generator.tech/docs/generators/java

apiPackage: com.sap.books.client.api
invokerPackage: com.sap.books.client
modelPackage: com.sap.books.client.model
groupId: com.sap.books
artifactId: books-client-api
artifactVersion: 1.0.0
serializationLibrary: jackson
```

## Act 

[Act](https://github.com/nektos/act) is a command line tool that can be used to run Github Actions locally on your desktop. That reduces roundtrip times as it frees you from having to push changes to Github every time you want to test if changes to your Workflow or Actions function as expected.

There is also an [Act Visual Studio Code Extension](https://sanjulaganepola.github.io/github-local-actions-docs/) you can use to easily edit and trigger workflow runs from VS Code.

Follow the [Act Usage Guide](https://nektosact.com/usage/index.html) for installation and execution.

Once installed, you can run the workflows of this repository from VS Code. All you need to do is clone this repository and open its root folder in VS Code.

### Executing Github Workflows Locally

To execute the workflows of this repository locally, proceed as follows:

> ❗Make sure you have installed `act` and its Visual Studio Code extension as described above.

1. Clone this repository to your local machine
2. Open a terminal and change into the root directory of this repo on your local machine
3. Execute `act`
4. This will run the workflows found under `.github/workflows`

To do the same from Visual Studio Code, proceed as follows:

1. Clone this repository to your local machine
2. Open the root directory of this repo in VS Code
3. Switch to the Act extension view (choose the little robot icon to left of the screen)
4. In the **Workflows** section of the screen, select the workflow you would like to run
5. Press the "Play" button next to the workflow.

### Reconfiguration from Slim, Medium, Large

After Act was installed and when it is executed for the first time, it asks you which platform you want to install. Act's "platform" defines the tool set that simulates a Github Workflows / Actions online environment on your local machine. Generally, choosing `Medium` should be a good choice.

If you want to change the platform later, you will have to change or delete Act's `actrc` file.
Depending on where you run Act (Windows, Linux, MacOS) the file may reside in either of the following locations

1. Linux: `~/.config/act/actrc` or `~/.act/actrc`
2. MacOS: `~/Library/Application\ Support/act/actrc`

Simply delete the file and run `act` on the CLI again. You will be asked again to choose the platform you want to use.

For more information, see [this issue](https://github.com/nektos/act/discussions/2401). Also see [this issue](https://github.com/nektos/act/issues/107) for typical errors you may get if the installed platform does not support what you want to do locally.

## References

**OpenAPI:**

* [Getting Started](https://learn.openapis.org/)
* [Specification Explained](https://learn.openapis.org/specification/)
* [Best Practices](https://learn.openapis.org/best-practices.html)
* [OpenAPI Specification Map](https://openapi-map.apihandyman.io/?version=3.0)
* [Specification (Full Document)](https://swagger.io/specification/)
* [Specification Core Github Repo](https://github.com/swagger-api/swagger-core)

**OpenAPI Tools:**

* [OpenAPI Tools](https://openapi.tools/)
* [Swagger Maven Plugin - Generate Open API Documents from Code](https://github.com/kongchen/swagger-maven-plugin) (not recommended, see *Best Practices* and *API Design First Approach*!)
* [Text Editors](https://tools.openapis.org/categories/text-editors)
* [GUI Editors](https://tools.openapis.org/categories/gui-editors)
* [OpenAPI Generator Github Action](https://github.com/openapi-generators/openapitools-generator-action)
* [OpenAPI Generator CLI](https://github.com/OpenAPITools/openapi-generator)
* [OpenAPI Generator CLI Docker Image](https://hub.docker.com/r/openapitools/openapi-generator-cli/tags)
* [OpenAPI Generator CLI Docker Image Tutorial Video](https://www.youtube.com/watch?v=9MuEP01h1XU)
* [OpenAPI Generator Configuration File](https://openapi-generator.tech/docs/usage#configuration-file)
* [Blog Post](https://www.propelauth.com/post/autogenerating-clients-with-fastapi-and-github-actions)

**Github Actions:**

* [Marketplace](https://github.com/marketplace)
* [Quickstart](https://docs.github.com/en/actions/writing-workflows/quickstart)
* [Understanding Workflows](https://docs.github.com/en/actions/writing-workflows/about-workflows#understanding-the-workflow-file)
* [Workflow Syntax](https://docs.github.com/en/actions/writing-workflows/workflow-syntax-for-github-actions#about-yaml-syntax-for-workflows)
* [Writing Workflows](https://docs.github.com/en/actions/writing-workflows)
* [Environment Variables](https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/accessing-contextual-information-about-workflow-runs)
* [Matrix Builds](https://docs.github.com/en/actions/writing-workflows/workflow-syntax-for-github-actions#jobsjob_idstrategymatrix)
* [Running Variations of Jobs in a Workflow](https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/running-variations-of-jobs-in-a-workflow)
* [Understanding Github Actions](https://docs.github.com/en/actions/about-github-actions/understanding-github-actions)
* [Caching Dependencies (e.g. .npm / node_modules)](https://docs.github.com/en/actions/using-workflows/caching-dependencies-to-speed-up-workflows)
* [Using Containerized Services](https://docs.github.com/en/actions/using-containerized-services)
* [Labels of (self-hosted) runners](https://docs.github.com/en/actions/writing-workflows/about-workflows#using-labels-to-route-workflows)
* [Reusing Workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows)
* [Use Cases and Examples](https://docs.github.com/en/actions/use-cases-and-examples)
* [Managing Workflows](https://docs.github.com/en/actions/managing-workflow-runs-and-deployments)
* [Create New Workflows](https://github.com/FWinkler79/open-api-playground/actions/new)
* [Github Actions Starter Worksflows - Repository](https://github.com/actions/starter-workflows)
* [Securing Workflows](https://docs.github.com/en/actions/security-guides/using-githubs-security-features-to-secure-your-use-of-github-actions)
* [Managing (secure) Environments for Worksflows](https://docs.github.com/en/actions/managing-workflow-runs-and-deployments/managing-deployments/managing-environments-for-deployment)
* [Triggering Workflows via REST API](https://docs.github.com/en/rest/repos/repos#create-a-repository-dispatch-event)
* [Sharing Automation (Writing your own Actions)](https://docs.github.com/en/actions/sharing-automations)
* [Hosting your own runners](https://docs.github.com/en/actions/hosting-your-own-runners)

**Act:**
* [Act](https://github.com/nektos/act)
* [Act Visual Studio Code Extension](https://sanjulaganepola.github.io/github-local-actions-docs/)
* [Act Usage Guide](https://nektosact.com/usage/index.html)
* [Act Reconfiguration](https://github.com/nektos/act/discussions/2401)
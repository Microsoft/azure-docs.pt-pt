---
title: YAML reference - ACR Tasks
description: Reference for defining tasks in YAML for ACR Tasks, including task properties, step types, step properties, and built-in variables.
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: a27f55d08a7ed5d7bf3360030eabefc4b7720b82
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454642"
---
# <a name="acr-tasks-reference-yaml"></a>ACR Tasks reference: YAML

Multi-step task definition in ACR Tasks provides a container-centric compute primitive focused on building, testing, and patching containers. This article covers the commands, parameters, properties, and syntax for the YAML files that define your multi-step tasks.

This article contains reference for creating multi-step task YAML files for ACR Tasks. If you'd like an introduction to ACR Tasks, see the [ACR Tasks overview](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>acr-task.yaml file format

ACR Tasks supports multi-step task declaration in standard YAML syntax. You define a task's steps in a YAML file. You can then run the task manually by passing the file to the [az acr run][az-acr-run] command. Or, use the file to create a task with [az acr task create][az-acr-task-create] that's triggered automatically on a Git commit or base image update. Although this article refers to `acr-task.yaml` as the file containing the steps, ACR Tasks supports any valid filename with a [supported extension](#supported-task-filename-extensions).

The top-level `acr-task.yaml` primitives are **task properties**, **step types**, and **step properties**:

* [Task properties](#task-properties) apply to all steps throughout task execution. There are several global task properties, including:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Task step types](#task-step-types) represent the types of actions that can be performed in a task. There are three step types:
  * `build`
  * `push`
  * `cmd`
* [Task step properties](#task-step-properties) are parameters that apply to an individual step. There are several step properties, including:
  * `startDelay`
  * `timeout`
  * `when`
  * ...and many more.

The base format of an `acr-task.yaml` file, including some common step properties, follows. While not an exhaustive representation of all available step properties or step type usage, it provides a quick overview of the basic file format.

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Supported task filename extensions

ACR Tasks has reserved several filename extensions, including `.yaml`, that it will process as a task file. Any extension *not* in the following list is considered by ACR Tasks to be a Dockerfile: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua

YAML is the only file format currently supported by ACR Tasks. The other filename extensions are reserved for possible future support.

## <a name="run-the-sample-tasks"></a>Run the sample tasks

There are several sample task files referenced in the following sections of this article. The sample tasks are in a public GitHub repository, [Azure-Samples/acr-tasks][acr-tasks]. You can run them with the Azure CLI command [az acr run][az-acr-run]. The sample commands are similar to:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

The formatting of the sample commands assumes you've configured a default registry in the Azure CLI, so they omit the `--registry` parameter. To configure a default registry, use the [az configure][az-configure] command with the `--defaults` parameter, which accepts an `acr=REGISTRY_NAME` value.

For example, to configure the Azure CLI with a default registry named "myregistry":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Task properties

Task properties typically appear at the top of an `acr-task.yaml` file, and are global properties that apply throughout the full execution of the task steps. Some of these global properties can be overridden within an individual step.

| Propriedade | Tipo | Opcional | Descrição | Override supported | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | Sim | The version of the `acr-task.yaml` file as parsed by the ACR Tasks service. While ACR Tasks strives to maintain backward compatibility, this value allows ACR Tasks to maintain compatibility within a defined version. If unspecified, defaults to the latest version. | Não | Nenhuma |
| `stepTimeout` | int (seconds) | Sim | The maximum number of seconds a step can run. If the property is specified on a task, it sets the default `timeout` property of all the steps. If the `timeout` property is specified on a step, it overrides the property provided by the task. | Sim | 600 (10 minutes) |
| `workingDirectory` | string | Sim | The working directory of the container during runtime. If the property is specified on a task, it sets the default `workingDirectory` property of all the steps. If specified on a step, it overrides the property provided by the task. | Sim | `$HOME` |
| `env` | [string, string, ...] | Sim |  Array of strings in `key=value` format that define the environment variables for the task. If the property is specified on a task, it sets the default `env` property of all the steps. If specified on a step, it overrides any environment variables inherited from the task. | Nenhuma |
| `secrets` | [secret, secret, ...] | Sim | Array of [secret](#secret) objects. | Nenhuma |
| `networks` | [network, network, ...] | Sim | Array of [network](#network) objects. | Nenhuma |

### <a name="secret"></a>segredo

The secret object has the following properties.

| Propriedade | Tipo | Opcional | Descrição | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | string | Não | The identifier of the secret. | Nenhuma |
| `keyvault` | string | Sim | The Azure Key Vault Secret URL. | Nenhuma |
| `clientID` | string | Sim | The client ID of the [user-assigned managed identity](container-registry-tasks-authentication-managed-identity.md) for Azure resources. | Nenhuma |

### <a name="network"></a>network

The network object has the following properties.

| Propriedade | Tipo | Opcional | Descrição | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | string | Não | The name of the network. | Nenhuma |
| `driver` | string | Sim | The driver to manage the network. | Nenhuma |
| `ipv6` | booleano | Sim | Whether IPv6 networking is enabled. | `false` |
| `skipCreation` | booleano | Sim | Whether to skip network creation. | `false` |
| `isDefault` | booleano | Sim | Whether the network is a default network provided with Azure Container Registry | `false` |

## <a name="task-step-types"></a>Task step types

ACR Tasks supports three step types. Each step type supports several properties, detailed in the section for each step type.

| Step type | Descrição |
| --------- | ----------- |
| [`build`](#build) | Builds a container image using familiar `docker build` syntax. |
| [`push`](#push) | Executes a `docker push` of newly built or retagged images to a container registry. Azure Container Registry, other private registries, and the public Docker Hub are supported. |
| [`cmd`](#cmd) | Runs a container as a command, with parameters passed to the container's `[ENTRYPOINT]`. The `cmd` step type supports parameters like `env`, `detach`, and other familiar `docker run` command options, enabling unit and functional testing with concurrent container execution. |

## <a name="build"></a>build

Build a container image. The `build` step type represents a multi-tenant, secure means of running `docker build` in the cloud as a first-class primitive.

### <a name="syntax-build"></a>Syntax: build

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

The `build` step type supports the parameters in the following table. The `build` step type also supports all build options of the [docker build](https://docs.docker.com/engine/reference/commandline/build/) command, such as `--build-arg` to set build-time variables.

| Parâmetro | Descrição | Opcional |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Defines the fully qualified `image:tag` of the built image.<br /><br />As images may be used for inner task validations, such as functional tests, not all images require `push` to a registry. However, to instance an image within a Task execution, the image does need a name to reference.<br /><br />Unlike `az acr build`, running ACR Tasks doesn't provide default push behavior. With ACR Tasks, the default scenario assumes the ability to build, validate, then push an image. See [push](#push) for how to optionally push built images. | Sim |
| `-f` &#124; `--file` | Specifies the Dockerfile passed to `docker build`. If not specified, the default Dockerfile in the root of the context is assumed. To specify a Dockerfile, pass the filename relative to the root of the context. | Sim |
| `context` | The root directory passed to `docker build`. The root directory of each task is set to a shared [workingDirectory](#task-step-properties), and includes the root of the associated Git cloned directory. | Não |

### <a name="properties-build"></a>Properties: build

The `build` step type supports the following properties. Find details of these properties in the [Task step properties](#task-step-properties) section of this article.

| | | |
| -------- | ---- | -------- |
| `detach` | booleano | Opcional |
| `disableWorkingDirectoryOverride` | booleano | Opcional |
| `entryPoint` | string | Opcional |
| `env` | [string, string, ...] | Opcional |
| `expose` | [string, string, ...] | Opcional |
| `id` | string | Opcional |
| `ignoreErrors` | booleano | Opcional |
| `isolation` | string | Opcional |
| `keep` | booleano | Opcional |
| `network` | objeto | Opcional |
| `ports` | [string, string, ...] | Opcional |
| `pull` | booleano | Opcional |
| `repeat` | int | Opcional |
| `retries` | int | Opcional |
| `retryDelay` | int (seconds) | Opcional |
| `secret` | objeto | Opcional |
| `startDelay` | int (seconds) | Opcional |
| `timeout` | int (seconds) | Opcional |
| `when` | [string, string, ...] | Opcional |
| `workingDirectory` | string | Opcional |

### <a name="examples-build"></a>Examples: build

#### <a name="build-image---context-in-root"></a>Build image - context in root

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Build image - context in subdirectory

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

Push one or more built or retagged images to a container registry. Supports pushing to private registries like Azure Container Registry, or to the public Docker Hub.

### <a name="syntax-push"></a>Syntax: push

The `push` step type supports a collection of images. YAML collection syntax supports inline and nested formats. Pushing a single image is typically represented using inline syntax:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

For increased readability, use nested syntax when pushing multiple images:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Properties: push

The `push` step type supports the following properties. Find details of these properties in the [Task step properties](#task-step-properties) section of this article.

| | | |
| -------- | ---- | -------- |
| `env` | [string, string, ...] | Opcional |
| `id` | string | Opcional |
| `ignoreErrors` | booleano | Opcional |
| `startDelay` | int (seconds) | Opcional |
| `timeout` | int (seconds) | Opcional |
| `when` | [string, string, ...] | Opcional |

### <a name="examples-push"></a>Examples: push

#### <a name="push-multiple-images"></a>Push multiple images

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Build, push, and run

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

The `cmd` step type runs a container.

### <a name="syntax-cmd"></a>Syntax: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Properties: cmd

The `cmd` step type supports the following properties:

| | | |
| -------- | ---- | -------- |
| `detach` | booleano | Opcional |
| `disableWorkingDirectoryOverride` | booleano | Opcional |
| `entryPoint` | string | Opcional |
| `env` | [string, string, ...] | Opcional |
| `expose` | [string, string, ...] | Opcional |
| `id` | string | Opcional |
| `ignoreErrors` | booleano | Opcional |
| `isolation` | string | Opcional |
| `keep` | booleano | Opcional |
| `network` | objeto | Opcional |
| `ports` | [string, string, ...] | Opcional |
| `pull` | booleano | Opcional |
| `repeat` | int | Opcional |
| `retries` | int | Opcional |
| `retryDelay` | int (seconds) | Opcional |
| `secret` | objeto | Opcional |
| `startDelay` | int (seconds) | Opcional |
| `timeout` | int (seconds) | Opcional |
| `when` | [string, string, ...] | Opcional |
| `workingDirectory` | string | Opcional |

You can find details of these properties in the [Task step properties](#task-step-properties) section of this article.

### <a name="examples-cmd"></a>Examples: cmd

#### <a name="run-hello-world-image"></a>Run hello-world image

This command executes the `hello-world.yaml` task file, which references the [hello-world](https://hub.docker.com/_/hello-world/) image on Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Run bash image and echo "hello world"

This command executes the `bash-echo.yaml` task file, which references the [bash](https://hub.docker.com/_/bash/) image on Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Run specific bash image tag

To run a specific image version, specify the tag in the `cmd`.

This command executes the `bash-echo-3.yaml` task file, which references the [bash:3.0](https://hub.docker.com/_/bash/) image on Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Run custom images

The `cmd` step type references images using the standard `docker run` format. Images not prefaced with a registry are assumed to originate from docker.io. The previous example could equally be represented as:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

By using the standard `docker run` image reference convention, `cmd` can run images from any private registry or the public Docker Hub. If you're referencing images in the same registry in which ACR Task is executing, you don't need to specify any registry credentials.

* Run an image that's from an Azure container registry. The following example assumes you have a registry named `myregistry`, and a custom image `myimage:mytag`.

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Generalize the registry reference with a Run variable or alias

    Instead of hard-coding your registry name in an `acr-task.yaml` file, you can make it more portable by using a [Run variable](#run-variables) or [alias](#aliases). The `Run.Registry` variable or `$Registry` alias expands at runtime to the name of the registry in which the task is executing.

    For example, to generalize the preceding task so that it works in any Azure container registry, reference the $Registry variable in the image name:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>Task step properties

Each step type supports several properties appropriate for its type. The following table defines all of the available step properties. Not all step types support all properties. To see which of these properties are available for each step type, see the [cmd](#cmd), [build](#build), and [push](#push) step type reference sections.

| Propriedade | Tipo | Opcional | Descrição | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | booleano | Sim | Whether the container should be detached when running. | `false` |
| `disableWorkingDirectoryOverride` | booleano | Sim | Whether to disable `workingDirectory` override functionality. Use this in combination with `workingDirectory` to have complete control over the container's working directory. | `false` |
| `entryPoint` | string | Sim | Overrides the `[ENTRYPOINT]` of a step's container. | Nenhuma |
| `env` | [string, string, ...] | Sim | Array of strings in `key=value` format that define the environment variables for the step. | Nenhuma |
| `expose` | [string, string, ...] | Sim | Array of ports that are exposed from the container. |  Nenhuma |
| [`id`](#example-id) | string | Sim | Uniquely identifies the step within the task. Other steps in the task can reference a step's `id`, such as for dependency checking with `when`.<br /><br />The `id` is also the running container's name. Processes running in other containers in the task can refer to the `id` as its DNS host name, or for accessing it with docker logs [id], for example. | `acb_step_%d`, where `%d` is the 0-based index of the step top-down in the YAML file |
| `ignoreErrors` | booleano | Sim | Whether to mark the step as successful regardless of whether an error occurred during container execution. | `false` |
| `isolation` | string | Sim | The isolation level of the container. | `default` |
| `keep` | booleano | Sim | Whether the step's container should be kept after execution. | `false` |
| `network` | objeto | Sim | Identifies a network in which the container runs. | Nenhuma |
| `ports` | [string, string, ...] | Sim | Array of ports that are published from the container to the host. |  Nenhuma |
| `pull` | booleano | Sim | Whether to force a pull of the container before executing it to prevent any caching behavior. | `false` |
| `privileged` | booleano | Sim | Whether to run the container in privileged mode. | `false` |
| `repeat` | int | Sim | The number of retries to repeat the execution of a container. | 0 |
| `retries` | int | Sim | The number of retries to attempt if a container fails its execution. A retry is only attempted if a container's exit code is non-zero. | 0 |
| `retryDelay` | int (seconds) | Sim | The delay in seconds between retries of a container's execution. | 0 |
| `secret` | objeto | Sim | Identifies an Azure Key Vault secret or [managed identity for Azure resources](container-registry-tasks-authentication-managed-identity.md). | Nenhuma |
| `startDelay` | int (seconds) | Sim | Number of seconds to delay a container's execution. | 0 |
| `timeout` | int (seconds) | Sim | Maximum number of seconds a step may execute before being terminated. | 600 |
| [`when`](#example-when) | [string, string, ...] | Sim | Configures a step's dependency on one or more other steps within the task. | Nenhuma |
| `user` | string | Sim | The user name or UID of a container | Nenhuma |
| `workingDirectory` | string | Sim | Sets the working directory for a step. By default, ACR Tasks creates a root directory as the working directory. However, if your build has several steps, earlier steps can share artifacts with later steps by specifying the same working directory. | `$HOME` |

### <a name="examples-task-step-properties"></a>Examples: Task step properties

#### <a name="example-id"></a>Example: id

Build two images, instancing a functional test image. Each step is identified by a unique `id` which other steps in the task reference in their `when` property.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Example: when

The `when` property specifies a step's dependency on other steps within the task. It supports two parameter values:

* `when: ["-"]` - Indicates no dependency on other steps. A step specifying `when: ["-"]` will begin execution immediately, and enables concurrent step execution.
* `when: ["id1", "id2"]` - Indicates the step is dependent upon steps with `id` "id1" and `id` "id2". This step won't be executed until both "id1" and "id2" steps complete.

If `when` isn't specified in a step, that step is dependent on completion of the previous step in the `acr-task.yaml` file.

Sequential step execution without `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Sequential step execution with `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Parallel images build:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Parallel image build and dependent testing:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Run variables

ACR Tasks includes a default set of variables that are available to task steps when they execute. These variables can be accessed by using the format `{{.Run.VariableName}}`, where `VariableName` is one of the following:

* `Run.ID`
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

The variable names are generally self-explanatory. Details follows for commonly used variables. As of YAML version `v1.1.0`, you can use an abbreviated, predefined [task alias](#aliases) in place of most run variables. For example, in place of `{{.Run.Registry}}`, use the `$Registry` alias.

### <a name="runid"></a>Run.ID

Each Run, through `az acr run`, or trigger based execution of tasks created through `az acr task create`, has a unique ID. The ID represents the Run currently being executed.

Typically used for a uniquely tagging an image:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Run.Registry

The fully qualified server name of the registry. Typically used to generically reference the registry where the task is being run.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Run.RegistryName

The name of the container registry. Typically used in task steps that don't require a fully qualified server name, for example, `cmd` steps that run Azure CLI commands on registries.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run.Date

The current UTC time the run began.

### <a name="runcommit"></a>Run.Commit

For a task triggered by a commit to a GitHub repository, the commit identifier.

### <a name="runbranch"></a>Run.Branch

For a task triggered by a commit to a GitHub repository, the branch name.

## <a name="aliases"></a>Aliases

As of `v1.1.0`, ACR Tasks supports aliases that are available to task steps when they execute. Aliases are similar in concept to aliases (command shortcuts) supported in bash and some other command shells. 

With an alias, you can launch any command or group of commands (including options and filenames) by entering a single word.

ACR Tasks supports several predefined aliases and also custom aliases you create.

### <a name="predefined-aliases"></a>Predefined aliases

The following task aliases are available to use in place of [run variables](#run-variables):

| Alias | Run variable |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

In task steps, precede an alias with the `$` directive, as in this example:

```yaml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Image aliases

Each of the following aliases points to a stable image in Microsoft Container Registry (MCR). You can refer to each of them in the `cmd` section of a Task file without using a directive.

| Alias | Imagem |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:d0725bc` |
| `bash` | `mcr.microsoft.com/acr/bash:d0725bc` |
| `curl` | `mcr.microsoft.com/acr/curl:d0725bc` |

The following example task uses several aliases to [purge](container-registry-auto-purge.md) image tags older than 7 days in the repo `samples/hello-world` in the run registry:

```yaml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Custom alias

Define a custom alias in your YAML file and use it as shown in the following example. An alias can contain only alphanumeric characters. The default directive to expand an alias is the `$` character.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

You can link to a remote or local YAML file for custom alias definitions. The following example links to a YAML file in Azure blob storage:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Passos seguintes

For an overview of multi-step tasks, see the [Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md).

For single-step builds, see the [ACR Tasks overview](container-registry-tasks-overview.md).



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure

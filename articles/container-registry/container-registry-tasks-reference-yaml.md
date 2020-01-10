---
title: Referência de YAML – tarefas de ACR
description: Referência para definir tarefas em YAML para tarefas ACR, incluindo propriedades de tarefa, tipos de etapa, propriedades de etapa e variáveis internas.
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: da1b1613d880b9edf6ec6d6018011f43a7ac69a5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445684"
---
# <a name="acr-tasks-reference-yaml"></a>Referência de tarefas de ACR: YAML

A definição de tarefa de várias etapas em tarefas ACR fornece um primitivo de computação centrado em contêiner voltado para criar, testar e aplicar patches a contêineres. Este artigo aborda os comandos, parâmetros, propriedades e sintaxe para os arquivos YAML que definem as tarefas de várias etapas.

Este artigo contém referência para a criação de arquivos de YAML de tarefas de várias etapas para tarefas ACR. Se você quiser uma introdução às tarefas do ACR, consulte a [visão geral das tarefas do ACR](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>ACR-formato de arquivo. YAML de tarefa

As tarefas ACR dão suporte à declaração de tarefa de várias etapas na sintaxe YAML padrão. Você define as etapas de uma tarefa em um arquivo YAML. Em seguida, você pode executar a tarefa manualmente passando o arquivo para o comando [AZ ACR Run][az-acr-run] . Ou use o arquivo para criar uma tarefa com [AZ ACR tarefa Create][az-acr-task-create] que é disparada automaticamente em uma confirmação git ou em uma atualização de imagem base. Embora este artigo se refira a `acr-task.yaml` como o arquivo que contém as etapas, as tarefas ACR dão suporte a qualquer nome de arquivo válido com uma [extensão com suporte](#supported-task-filename-extensions).

Os primitivos de `acr-task.yaml` de nível superior são **Propriedades da tarefa**, tipos de **etapa**e propriedades da **etapa**:

* As [Propriedades da tarefa](#task-properties) se aplicam a todas as etapas durante a execução da tarefa. Há várias propriedades globais da tarefa, incluindo:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* Os [tipos de etapa de tarefa](#task-step-types) representam os tipos de ações que podem ser executadas em uma tarefa. Há três tipos de etapa:
  * `build`
  * `push`
  * `cmd`
* [As propriedades da etapa de tarefa](#task-step-properties) são parâmetros que se aplicam a uma etapa individual. Há várias propriedades da etapa, incluindo:
  * `startDelay`
  * `timeout`
  * `when`
  * ... e muito mais.

O formato base de um arquivo de `acr-task.yaml`, incluindo algumas propriedades de etapa comuns, a seguir. Embora não seja uma representação exaustiva de todas as propriedades da etapa disponíveis ou do uso do tipo de etapa, ele fornece uma visão geral rápida do formato de arquivo básico.

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

### <a name="supported-task-filename-extensions"></a>Extensões de nome de arquivo de tarefa com suporte

As tarefas ACR reservaram várias extensões de nome de arquivo, incluindo `.yaml`, que serão processadas como um arquivo de tarefa. Qualquer extensão que *não* esteja na lista a seguir é considerada por tarefas de ACR para ser um Dockerfile:. YAML,. yml,. toml,. JSON,. sh,. bash,. zsh,. ps1,. PS,. cmd,. bat,. TS,. js,. php,. py,. rb,. lua

YAML é o único formato de arquivo com suporte no momento por tarefas de ACR. As outras extensões de nome de arquivo são reservadas para possíveis suporte futuro.

## <a name="run-the-sample-tasks"></a>Executar as tarefas de exemplo

Há vários arquivos de tarefas de exemplo referenciados nas seções a seguir deste artigo. As tarefas de exemplo estão em um repositório GitHub público, [Azure-Samples/ACR-Tasks][acr-tasks]. Você pode executá-los com o comando de CLI do Azure [AZ ACR Run][az-acr-run]. Os comandos de exemplo são semelhantes a:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

A formatação dos comandos de exemplo pressupõe que você tenha configurado um registro padrão no CLI do Azure, para que omitam o parâmetro `--registry`. Para configurar um registro padrão, use o comando [AZ configure][az-configure] com o parâmetro `--defaults`, que aceita um valor de `acr=REGISTRY_NAME`.

Por exemplo, para configurar o CLI do Azure com um registro padrão chamado "myregistry":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Propriedades da tarefa

Normalmente, as propriedades da tarefa aparecem na parte superior de um arquivo de `acr-task.yaml` e são propriedades globais que se aplicam em toda a execução das etapas da tarefa. Algumas dessas propriedades globais podem ser substituídas em uma etapa individual.

| Propriedade | Tipo | Opcional | Descrição | Substituição com suporte | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | Sim | A versão do arquivo de `acr-task.yaml` conforme analisado pelo serviço de tarefas ACR. Embora as tarefas do ACR se esforçam para manter a compatibilidade com versões anteriores, esse valor permite que as tarefas do ACR mantenham a compatibilidade em uma versão definida. Se não for especificado, o padrão será a versão mais recente. | Não | Nenhuma |
| `stepTimeout` | int (segundos) | Sim | O número máximo de segundos que uma etapa pode executar. Se a propriedade for especificada em uma tarefa, ela definirá a propriedade de `timeout` padrão de todas as etapas. Se a propriedade `timeout` for especificada em uma etapa, ela substituirá a propriedade fornecida pela tarefa. | Sim | 600 (10 minutos) |
| `workingDirectory` | string | Sim | O diretório de trabalho do contêiner durante o tempo de execução. Se a propriedade for especificada em uma tarefa, ela definirá a propriedade de `workingDirectory` padrão de todas as etapas. Se especificado em uma etapa, ele substituirá a propriedade fornecida pela tarefa. | Sim | `$HOME` |
| `env` | [cadeia de caracteres, Cadeia de caracteres,...] | Sim |  Matriz de cadeias de caracteres no formato `key=value` que define as variáveis de ambiente para a tarefa. Se a propriedade for especificada em uma tarefa, ela definirá a propriedade de `env` padrão de todas as etapas. Se especificado em uma etapa, ele substituirá as variáveis de ambiente herdadas da tarefa. | Nenhuma |
| `secrets` | [segredo, segredo,...] | Sim | Matriz de objetos [secretos](#secret) . | Nenhuma |
| `networks` | [rede, rede,...] | Sim | Matriz de objetos de [rede](#network) . | Nenhuma |

### <a name="secret"></a>segredo

O objeto secreto tem as propriedades a seguir.

| Propriedade | Tipo | Opcional | Descrição | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | string | Não | O identificador do segredo. | Nenhuma |
| `keyvault` | string | Sim | A URL do segredo do Azure Key Vault. | Nenhuma |
| `clientID` | string | Sim | A ID do cliente da [identidade gerenciada atribuída pelo usuário](container-registry-tasks-authentication-managed-identity.md) para recursos do Azure. | Nenhuma |

### <a name="network"></a>rede

O objeto de rede tem as propriedades a seguir.

| Propriedade | Tipo | Opcional | Descrição | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | string | Não | O nome da rede. | Nenhuma |
| `driver` | string | Sim | O driver para gerenciar a rede. | Nenhuma |
| `ipv6` | booleano | Sim | Se a rede IPv6 está habilitada. | `false` |
| `skipCreation` | booleano | Sim | Se a criação da rede deve ser ignorada. | `false` |
| `isDefault` | booleano | Sim | Se a rede é uma rede padrão fornecida com o registro de contêiner do Azure | `false` |

## <a name="task-step-types"></a>Tipos de etapas de tarefa

As tarefas ACR dão suporte a três tipos de etapa. Cada tipo de etapa dá suporte a várias propriedades, detalhadas na seção para cada tipo de etapa.

| Tipo de etapa | Descrição |
| --------- | ----------- |
| [`build`](#build) | Cria uma imagem de contêiner usando a sintaxe `docker build` familiar. |
| [`push`](#push) | Executa uma `docker push` de imagens recentemente compiladas ou remarcadas para um registro de contêiner. Há suporte para o registro de contêiner do Azure, outros registros privados e o Hub do Docker público. |
| [`cmd`](#cmd) | Executa um contêiner como um comando, com parâmetros passados para o `[ENTRYPOINT]`do contêiner. O tipo de etapa `cmd` dá suporte a parâmetros como `env`, `detach`e outras opções de comando `docker run` familiares, permitindo testes de unidade e funcionais com a execução simultânea de contêiner. |

## <a name="build"></a>compilar

Crie uma imagem de contêiner. O tipo de etapa `build` representa um meio seguro multilocatário de executar `docker build` na nuvem como um primitivo de primeira classe.

### <a name="syntax-build"></a>Sintaxe: Build

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

O tipo de etapa de `build` dá suporte aos parâmetros na tabela a seguir. O tipo de etapa de `build` também dá suporte a todas as opções de Build do comando de [Build do Docker](https://docs.docker.com/engine/reference/commandline/build/) , como `--build-arg` para definir variáveis de tempo de compilação.

| Parâmetro | Descrição | Opcional |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Define o `image:tag` totalmente qualificado da imagem interna.<br /><br />Como as imagens podem ser usadas para validações de tarefas internas, como testes funcionais, nem todas as imagens exigem `push` a um registro. No entanto, para fazer uma instância de uma imagem em uma execução de tarefa, a imagem precisa de um nome para referenciar.<br /><br />Ao contrário de `az acr build`, a execução de tarefas ACR não fornece o comportamento de push padrão. Com as tarefas ACR, o cenário padrão assume a capacidade de criar, validar e enviar por push uma imagem. Consulte [Push](#push) para saber como opcionalmente enviar imagens criadas. | Sim |
| `-f` &#124; `--file` | Especifica o Dockerfile passado para `docker build`. Se não for especificado, o Dockerfile padrão na raiz do contexto será assumido. Para especificar um Dockerfile, passe o nome de arquivo relativo à raiz do contexto. | Sim |
| `context` | O diretório raiz passado para `docker build`. O diretório raiz de cada tarefa é definido como um [workingDirectory](#task-step-properties)compartilhado e inclui a raiz do diretório clonado do git associado. | Não |

### <a name="properties-build"></a>Propriedades: Build

O tipo de etapa de `build` dá suporte às propriedades a seguir. Encontre detalhes dessas propriedades na seção [Propriedades da etapa da tarefa](#task-step-properties) deste artigo.

| | | |
| -------- | ---- | -------- |
| `detach` | booleano | Opcional |
| `disableWorkingDirectoryOverride` | booleano | Opcional |
| `entryPoint` | string | Opcional |
| `env` | [cadeia de caracteres, Cadeia de caracteres,...] | Opcional |
| `expose` | [cadeia de caracteres, Cadeia de caracteres,...] | Opcional |
| `id` | string | Opcional |
| `ignoreErrors` | booleano | Opcional |
| `isolation` | string | Opcional |
| `keep` | booleano | Opcional |
| `network` | objeto | Opcional |
| `ports` | [cadeia de caracteres, Cadeia de caracteres,...] | Opcional |
| `pull` | booleano | Opcional |
| `repeat` | int | Opcional |
| `retries` | int | Opcional |
| `retryDelay` | int (segundos) | Opcional |
| `secret` | objeto | Opcional |
| `startDelay` | int (segundos) | Opcional |
| `timeout` | int (segundos) | Opcional |
| `when` | [cadeia de caracteres, Cadeia de caracteres,...] | Opcional |
| `workingDirectory` | string | Opcional |

### <a name="examples-build"></a>Exemplos: Build

#### <a name="build-image---context-in-root"></a>Compilar imagem-contexto na raiz

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Compilar o contexto de imagem no subdiretório

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

Envie por push uma ou mais imagens criadas ou remarcadas para um registro de contêiner. Dá suporte ao envio por push para registros privados, como o registro de contêiner do Azure, ou para o Hub do Docker público.

### <a name="syntax-push"></a>Sintaxe: enviar por push

O tipo de etapa de `push` dá suporte a uma coleção de imagens. A sintaxe da coleção YAML dá suporte a formatos embutidos e aninhados. O envio por push de uma única imagem normalmente é representado usando a sintaxe embutida:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Para maior legibilidade, use a sintaxe aninhada ao enviar várias imagens:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Propriedades: enviar por push

O tipo de etapa de `push` dá suporte às propriedades a seguir. Encontre detalhes dessas propriedades na seção [Propriedades da etapa da tarefa](#task-step-properties) deste artigo.

| | | |
| -------- | ---- | -------- |
| `env` | [cadeia de caracteres, Cadeia de caracteres,...] | Opcional |
| `id` | string | Opcional |
| `ignoreErrors` | booleano | Opcional |
| `startDelay` | int (segundos) | Opcional |
| `timeout` | int (segundos) | Opcional |
| `when` | [cadeia de caracteres, Cadeia de caracteres,...] | Opcional |

### <a name="examples-push"></a>Exemplos: enviar por push

#### <a name="push-multiple-images"></a>Enviar várias imagens por push

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Compilar, enviar por push e executar

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

O tipo de etapa de `cmd` executa um contêiner.

### <a name="syntax-cmd"></a>Sintaxe: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Propriedades: cmd

O tipo de etapa de `cmd` dá suporte às seguintes propriedades:

| | | |
| -------- | ---- | -------- |
| `detach` | booleano | Opcional |
| `disableWorkingDirectoryOverride` | booleano | Opcional |
| `entryPoint` | string | Opcional |
| `env` | [cadeia de caracteres, Cadeia de caracteres,...] | Opcional |
| `expose` | [cadeia de caracteres, Cadeia de caracteres,...] | Opcional |
| `id` | string | Opcional |
| `ignoreErrors` | booleano | Opcional |
| `isolation` | string | Opcional |
| `keep` | booleano | Opcional |
| `network` | objeto | Opcional |
| `ports` | [cadeia de caracteres, Cadeia de caracteres,...] | Opcional |
| `pull` | booleano | Opcional |
| `repeat` | int | Opcional |
| `retries` | int | Opcional |
| `retryDelay` | int (segundos) | Opcional |
| `secret` | objeto | Opcional |
| `startDelay` | int (segundos) | Opcional |
| `timeout` | int (segundos) | Opcional |
| `when` | [cadeia de caracteres, Cadeia de caracteres,...] | Opcional |
| `workingDirectory` | string | Opcional |

Você pode encontrar detalhes dessas propriedades na seção [Propriedades da etapa da tarefa](#task-step-properties) deste artigo.

### <a name="examples-cmd"></a>Exemplos: cmd

#### <a name="run-hello-world-image"></a>Executar imagem do Hello-World

Esse comando executa o arquivo de tarefa `hello-world.yaml`, que faz referência à imagem [Hello-World](https://hub.docker.com/_/hello-world/) no Hub do Docker.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Executar imagem bash e Echo "Olá mundo"

Esse comando executa o arquivo de tarefa `bash-echo.yaml`, que faz referência à imagem [bash](https://hub.docker.com/_/bash/) no Hub do Docker.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Executar marca de imagem de bash específica

Para executar uma versão de imagem específica, especifique a marca no `cmd`.

Esse comando executa o arquivo de tarefa `bash-echo-3.yaml`, que faz referência à imagem [bash: 3.0](https://hub.docker.com/_/bash/) no Hub do Docker.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Executar imagens personalizadas

O tipo de etapa `cmd` faz referência a imagens usando o formato de `docker run` padrão. Supõe-se que as imagens não precedidas com um registro sejam originadas em docker.io. O exemplo anterior poderia ser igualmente representado como:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Usando a Convenção de referência de imagem de `docker run` padrão, `cmd` pode executar imagens de qualquer registro privado ou do Hub do Docker público. Se você estiver fazendo referência a imagens no mesmo registro no qual a tarefa ACR está em execução, não será necessário especificar nenhuma credencial de registro.

* Execute uma imagem que seja de um registro de contêiner do Azure. O exemplo a seguir pressupõe que você tenha um registro chamado `myregistry`e uma imagem personalizada `myimage:mytag`.

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Generalizar a referência de registro com uma variável de execução ou alias

    Em vez de embutir o nome do registro em um arquivo `acr-task.yaml`, você pode torná-lo mais portátil usando uma [variável de execução](#run-variables) ou [alias](#aliases). A variável `Run.Registry` ou o alias de `$Registry` expande em tempo de execução para o nome do registro no qual a tarefa está sendo executada.

    Por exemplo, para generalizar a tarefa anterior para que ela funcione em qualquer registro de contêiner do Azure, faça referência à variável $Registry no nome da imagem:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>Propriedades da etapa da tarefa

Cada tipo de etapa dá suporte a várias propriedades apropriadas para seu tipo. A tabela a seguir define todas as propriedades da etapa disponível. Nem todos os tipos de etapa dão suporte a todas as propriedades. Para ver quais dessas propriedades estão disponíveis para cada tipo de etapa, consulte as seções de referência de tipo de etapa [cmd](#cmd), [Build](#build)e [Push](#push) .

| Propriedade | Tipo | Opcional | Descrição | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | booleano | Sim | Se o contêiner deve ser desanexado durante a execução. | `false` |
| `disableWorkingDirectoryOverride` | booleano | Sim | Se a funcionalidade de substituição de `workingDirectory` deve ser desabilitada. Use isso em combinação com `workingDirectory` para ter controle total sobre o diretório de trabalho do contêiner. | `false` |
| `entryPoint` | string | Sim | Substitui a `[ENTRYPOINT]` do contêiner de uma etapa. | Nenhuma |
| `env` | [cadeia de caracteres, Cadeia de caracteres,...] | Sim | Matriz de cadeias de caracteres no formato `key=value` que define as variáveis de ambiente para a etapa. | Nenhuma |
| `expose` | [cadeia de caracteres, Cadeia de caracteres,...] | Sim | Matriz de portas que são expostas do contêiner. |  Nenhuma |
| [`id`](#example-id) | string | Sim | Identifica exclusivamente a etapa dentro da tarefa. Outras etapas na tarefa podem fazer referência a `id`de uma etapa, como para a verificação de dependência com `when`.<br /><br />O `id` também é o nome do contêiner em execução. Os processos em execução em outros contêineres na tarefa podem se referir ao `id` como seu nome de host DNS ou para acessá-lo com os logs do Docker [ID], por exemplo. | `acb_step_%d`, em que `%d` é o índice baseado em 0 da etapa de cima para baixo no arquivo YAML |
| `ignoreErrors` | booleano | Sim | Indica se a etapa será marcada como bem-sucedida, independentemente de ocorrer um erro durante a execução do contêiner. | `false` |
| `isolation` | string | Sim | O nível de isolamento do contêiner. | `default` |
| `keep` | booleano | Sim | Se o contêiner da etapa deve ser mantido após a execução. | `false` |
| `network` | objeto | Sim | Identifica uma rede na qual o contêiner é executado. | Nenhuma |
| `ports` | [cadeia de caracteres, Cadeia de caracteres,...] | Sim | Matriz de portas que são publicadas do contêiner para o host. |  Nenhuma |
| `pull` | booleano | Sim | Se deve forçar um pull do contêiner antes de executá-lo para evitar qualquer comportamento de cache. | `false` |
| `privileged` | booleano | Sim | Se o contêiner deve ser executado no modo privilegiado. | `false` |
| `repeat` | int | Sim | O número de tentativas para repetir a execução de um contêiner. | 0 |
| `retries` | int | Sim | O número de tentativas para tentar se um contêiner falhar na sua execução. Uma nova tentativa só será tentada se o código de saída de um contêiner for diferente de zero. | 0 |
| `retryDelay` | int (segundos) | Sim | O atraso em segundos entre as tentativas da execução de um contêiner. | 0 |
| `secret` | objeto | Sim | Identifica um segredo Azure Key Vault ou [identidade gerenciada para recursos do Azure](container-registry-tasks-authentication-managed-identity.md). | Nenhuma |
| `startDelay` | int (segundos) | Sim | Número de segundos para atrasar a execução de um contêiner. | 0 |
| `timeout` | int (segundos) | Sim | Número máximo de segundos que uma etapa pode executar antes de ser encerrada. | 600 |
| [`when`](#example-when) | [cadeia de caracteres, Cadeia de caracteres,...] | Sim | Configura a dependência de uma etapa em uma ou mais etapas dentro da tarefa. | Nenhuma |
| `user` | string | Sim | O nome de usuário ou UID de um contêiner | Nenhuma |
| `workingDirectory` | string | Sim | Define o diretório de trabalho para uma etapa. Por padrão, as tarefas ACR criam um diretório raiz como o diretório de trabalho. No entanto, se sua compilação tiver várias etapas, as etapas anteriores poderão compartilhar artefatos com etapas posteriores especificando o mesmo diretório de trabalho. | `$HOME` |

### <a name="examples-task-step-properties"></a>Exemplos: Propriedades da etapa da tarefa

#### <a name="example-id"></a>Exemplo: ID

Crie duas imagens, instanciando uma imagem de teste funcional. Cada etapa é identificada por um `id` exclusivo que outras etapas na referência de tarefa em sua propriedade `when`.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Exemplo: quando

A propriedade `when` especifica a dependência de uma etapa em outras etapas dentro da tarefa. Ele dá suporte a dois valores de parâmetro:

* `when: ["-"]`-indica nenhuma dependência de outras etapas. Uma etapa que especifica `when: ["-"]` iniciará a execução imediatamente e habilitará a execução simultânea da etapa.
* `when: ["id1", "id2"]`-indica que a etapa depende das etapas com `id` "ID1" e `id` "ID2". Esta etapa não será executada até que as etapas "ID1" e "ID2" sejam concluídas.

Se `when` não for especificado em uma etapa, essa etapa dependerá da conclusão da etapa anterior no arquivo de `acr-task.yaml`.

Execução de etapa sequencial sem `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Execução de etapa sequencial com `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Build de imagens paralelas:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Compilação de imagem paralela e teste dependente:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Executar variáveis

As tarefas do ACR incluem um conjunto padrão de variáveis que estão disponíveis para etapas da tarefa quando elas são executadas. Essas variáveis podem ser acessadas usando o formato `{{.Run.VariableName}}`, em que `VariableName` é um dos seguintes:

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

Os nomes de variáveis são geralmente auto-explicativos. Os detalhes a seguir para as variáveis usadas com frequência. A partir da versão YAML `v1.1.0`, você pode usar um [alias de tarefa](#aliases) Abreviado e predefinido no lugar da maioria das variáveis de execução. Por exemplo, no lugar de `{{.Run.Registry}}`, use o alias `$Registry`.

### <a name="runid"></a>Run.ID

Cada execução, por meio de `az acr run`ou a execução baseada em gatilho de tarefas criadas por meio de `az acr task create`, tem uma ID exclusiva. A ID representa a execução que está sendo executada no momento.

Normalmente usado para uma marcação exclusiva de uma imagem:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Run.Registry

O nome do servidor totalmente qualificado do registro. Normalmente usado para referenciar genericamente o registro em que a tarefa está sendo executada.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Execute. Registryname

O nome do registro de contêiner. Normalmente usado em etapas de tarefas que não exigem um nome de servidor totalmente qualificado, por exemplo, `cmd` etapas que executam CLI do Azure comandos em registros.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Executar. Data

A hora UTC atual em que a execução começou.

### <a name="runcommit"></a>Executar. Commit

Para uma tarefa disparada por uma confirmação para um repositório GitHub, o identificador de confirmação.

### <a name="runbranch"></a>Executar. Branch

Para uma tarefa disparada por uma confirmação para um repositório GitHub, o nome da ramificação.

## <a name="aliases"></a>Aliases

A partir de `v1.1.0`, as tarefas ACR dão suporte a aliases que estão disponíveis para etapas de tarefa quando eles são executados. Os aliases são semelhantes em conceito a aliases (atalhos de comando) com suporte no bash e em outros shells de comando. 

Com um alias, você pode iniciar qualquer comando ou grupo de comandos (incluindo opções e nomes de texto) digitando uma única palavra.

As tarefas ACR dão suporte a vários aliases predefinidos e também a aliases personalizados criados por você.

### <a name="predefined-aliases"></a>Aliases predefinidos

Os aliases de tarefa a seguir estão disponíveis para uso no lugar de [variáveis de execução](#run-variables):

| Alias | Executar variável |
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

Em etapas da tarefa, preceda um alias com a diretiva `$`, como neste exemplo:

```yaml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Aliases de imagem

Cada um dos aliases a seguir aponta para uma imagem estável no registro de contêiner da Microsoft (MCR). Você pode fazer referência a cada um deles na seção `cmd` de um arquivo de tarefa sem usar uma diretiva.

| Alias | Imagem |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

A seguinte tarefa de exemplo usa vários aliases para [limpar](container-registry-auto-purge.md) marcas de imagem com mais de 7 dias no repositório `samples/hello-world` no registro de execução:

```yaml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Alias personalizado

Defina um alias personalizado no arquivo YAML e use-o conforme mostrado no exemplo a seguir. Um alias só pode conter caracteres alfanuméricos. A diretiva padrão para expandir um alias é o `$` caractere.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

Você pode vincular a um arquivo YAML remoto ou local para obter definições de alias personalizadas. O exemplo a seguir contém links para um arquivo YAML no armazenamento de BLOBs do Azure:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Passos seguintes

Para obter uma visão geral das tarefas de várias etapas, consulte [executar tarefas de compilação, teste e patch de várias etapas em tarefas de ACR](container-registry-tasks-multi-step.md).

Para compilações de uma única etapa, consulte a [visão geral das tarefas do ACR](container-registry-tasks-overview.md).



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure

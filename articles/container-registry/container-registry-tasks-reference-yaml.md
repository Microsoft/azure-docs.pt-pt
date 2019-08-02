---
title: Referência de tarefas do registro de contêiner do Azure-YAML
description: Referência para definir tarefas em YAML para tarefas ACR, incluindo propriedades de tarefa, tipos de etapa, propriedades de etapa e variáveis internas.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/12/2019
ms.author: danlep
ms.openlocfilehash: 27c38f51104dfb170c59860c96a8e3a86973bb1e
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638915"
---
# <a name="acr-tasks-reference-yaml"></a>Referência de tarefas ACR: YAML

A definição de tarefa de várias etapas em tarefas ACR fornece um primitivo de computação centrado em contêiner voltado para criar, testar e aplicar patches a contêineres. Este artigo aborda os comandos, parâmetros, propriedades e sintaxe para os arquivos YAML que definem as tarefas de várias etapas.

Este artigo contém referência para a criação de arquivos de YAML de tarefas de várias etapas para tarefas ACR. Se você quiser uma introdução às tarefas do ACR, consulte a [visão geral das tarefas do ACR](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>ACR-formato de arquivo. YAML de tarefa

As tarefas ACR dão suporte à declaração de tarefa de várias etapas na sintaxe YAML padrão. Você define as etapas de uma tarefa em um arquivo YAML. Em seguida, você pode executar a tarefa manualmente passando o arquivo para o comando [AZ ACR Run][az-acr-run] . Ou use o arquivo para criar uma tarefa com [AZ ACR tarefa Create][az-acr-task-create] que é disparada automaticamente em uma confirmação git ou em uma atualização de imagem base. Embora este artigo se refira `acr-task.yaml` como o arquivo que contém as etapas, as tarefas ACR dão suporte a qualquer nome de arquivo válido com uma extensão com [suporte](#supported-task-filename-extensions).

Os primitivos de `acr-task.yaml` nível superior são **Propriedades da tarefa**, **tipos de etapa**e **Propriedades da etapa**:

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

O formato base de um `acr-task.yaml` arquivo, incluindo algumas propriedades de etapa comuns, a seguir. Embora não seja uma representação exaustiva de todas as propriedades da etapa disponíveis ou do uso do tipo de etapa, ele fornece uma visão geral rápida do formato de arquivo básico.

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

As tarefas ACR reservaram várias extensões de nome `.yaml`de arquivo, incluindo, que serão processadas como um arquivo de tarefa. Qualquer extensão que *não* esteja na lista a seguir é considerada por tarefas de ACR para ser um Dockerfile:. YAML,. yml,. toml,. JSON,. sh,. bash,. zsh,. ps1,. PS,. cmd,. bat,. TS,. js,. php,. py,. rb,. lua

YAML é o único formato de arquivo com suporte no momento por tarefas de ACR. As outras extensões de nome de arquivo são reservadas para possíveis suporte futuro.

## <a name="run-the-sample-tasks"></a>Executar as tarefas de exemplo

Há vários arquivos de tarefas de exemplo referenciados nas seções a seguir deste artigo. As tarefas de exemplo estão em um repositório GitHub público, [Azure-Samples/ACR-Tasks][acr-tasks]. Você pode executá-los com o comando de CLI do Azure [AZ ACR Run][az-acr-run]. Os comandos de exemplo são semelhantes a:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

A formatação dos comandos de exemplo pressupõe que você configurou um registro padrão no CLI do Azure, portanto, ele omite `--registry` o parâmetro. Para configurar um registro padrão, use o comando [AZ configure][az-configure] com o `--defaults` parâmetro, que aceita um `acr=REGISTRY_NAME` valor.

Por exemplo, para configurar o CLI do Azure com um registro padrão chamado "myregistry":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Propriedades da tarefa

Normalmente, as propriedades da tarefa aparecem na parte `acr-task.yaml` superior de um arquivo e são propriedades globais que se aplicam durante a execução completa das etapas da tarefa. Algumas dessas propriedades globais podem ser substituídas em uma etapa individual.

| Propriedade | Type | Opcional | Descrição | Substituição com suporte | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | Cadeia de caracteres | Sim | A versão do `acr-task.yaml` arquivo conforme analisado pelo serviço de tarefas ACR. Embora as tarefas do ACR se esforçam para manter a compatibilidade com versões anteriores, esse valor permite que as tarefas do ACR mantenham a compatibilidade em uma versão definida. Se não for especificado, o padrão será a versão mais recente. | Não | Nenhum |
| `stepTimeout` | int (segundos) | Sim | O número máximo de segundos que uma etapa pode executar. Se a propriedade for especificada em uma tarefa, ela definirá a `timeout` propriedade padrão de todas as etapas. Se a `timeout` propriedade for especificada em uma etapa, ela substituirá a propriedade fornecida pela tarefa. | Sim | 600 (10 minutos) |
| `workingDirectory` | cadeia | Sim | O diretório de trabalho do contêiner durante o tempo de execução. Se a propriedade for especificada em uma tarefa, ela definirá a `workingDirectory` propriedade padrão de todas as etapas. Se especificado em uma etapa, ele substituirá a propriedade fornecida pela tarefa. | Sim | `$HOME` |
| `env` | [cadeia de caracteres, Cadeia de caracteres,...] | Sim |  Matriz de cadeias `key=value` de caracteres no formato que definem as variáveis de ambiente para a tarefa. Se a propriedade for especificada em uma tarefa, ela definirá a `env` propriedade padrão de todas as etapas. Se especificado em uma etapa, ele substituirá as variáveis de ambiente herdadas da tarefa. | Nenhuma |
| `secrets` | [segredo, segredo,...] | Sim | Matriz de [](#secret) objetos secretos. | Nenhum |
| `networks` | [rede, rede,...] | Sim | Matriz de objetos de [rede](#network) . | Nenhum |

### <a name="secret"></a>secret

O objeto secreto tem as propriedades a seguir.

| Propriedade | Type | Opcional | Descrição | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | Cadeia de caracteres | Não | O identificador do segredo. | Nenhum |
| `keyvault` | cadeia | Sim | A URL do segredo do Azure Key Vault. | Nenhum |
| `clientID` | cadeia | Sim | A ID do cliente da [identidade gerenciada atribuída pelo usuário](container-registry-tasks-authentication-managed-identity.md) para recursos do Azure. | Nenhuma |

### <a name="network"></a>rede

O objeto de rede tem as propriedades a seguir.

| Propriedade | Type | Opcional | Descrição | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | cadeia | Não | O nome da rede. | Nenhum |
| `driver` | cadeia | Sim | O driver para gerenciar a rede. | Nenhuma |
| `ipv6` | bool | Sim | Se a rede IPv6 está habilitada. | `false` |
| `skipCreation` | bool | Sim | Se a criação da rede deve ser ignorada. | `false` |
| `isDefault` | bool | Sim | Se a rede é uma rede padrão fornecida com o registro de contêiner do Azure | `false` |

## <a name="task-step-types"></a>Tipos de etapas de tarefa

As tarefas ACR dão suporte a três tipos de etapa. Cada tipo de etapa dá suporte a várias propriedades, detalhadas na seção para cada tipo de etapa.

| Tipo de etapa | Descrição |
| --------- | ----------- |
| [`build`](#build) | Cria uma imagem de contêiner usando `docker build` sintaxe familiar. |
| [`push`](#push) | Executa uma `docker push` das imagens recentemente compiladas ou remarcadas para um registro de contêiner. Há suporte para o registro de contêiner do Azure, outros registros privados e o Hub do Docker público. |
| [`cmd`](#cmd) | Executa um contêiner como um comando, com parâmetros passados para o contêiner `[ENTRYPOINT]`. O `cmd` tipo de etapa oferece suporte `env`a `detach`parâmetros como, e `docker run` outras opções de comando familiares, permitindo testes de unidade e funcionais com a execução simultânea de contêiner. |

## <a name="build"></a>integrado

Crie uma imagem de contêiner. O `build` tipo Step representa um meio de multilocatário e seguro de ser `docker build` executado na nuvem como um primitivo de primeira classe.

### <a name="syntax-build"></a>Sintaxe: Build

```yml
version: v1.0.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

O `build` tipo de etapa oferece suporte aos parâmetros na tabela a seguir. O `build` tipo de etapa também dá suporte a todas as opções de compilação do `--build-arg` comando de [Build](https://docs.docker.com/engine/reference/commandline/build/) do Docker, como para definir variáveis de tempo de compilação.

| Parâmetro | Descrição | Opcional |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Define o totalmente qualificado `image:tag` da imagem interna.<br /><br />Como as imagens podem ser usadas para validações de tarefas internas, como testes funcionais, nem todas as `push` imagens exigem um registro. No entanto, para fazer uma instância de uma imagem em uma execução de tarefa, a imagem precisa de um nome para referenciar.<br /><br />Ao `az acr build`contrário de, a execução de tarefas ACR não fornece o comportamento de push padrão. Com as tarefas ACR, o cenário padrão assume a capacidade de criar, validar e enviar por push uma imagem. Consulte [Push](#push) para saber como opcionalmente enviar imagens criadas. | Sim |
| `-f` &#124; `--file` | Especifica o Dockerfile passado para `docker build`. Se não for especificado, o Dockerfile padrão na raiz do contexto será assumido. Para especificar um Dockerfile, passe o nome de arquivo relativo à raiz do contexto. | Sim |
| `context` | O diretório raiz passado para `docker build`. O diretório raiz de cada tarefa é definido como um [workingDirectory](#task-step-properties)compartilhado e inclui a raiz do diretório clonado do git associado. | Não |

### <a name="properties-build"></a>Propriedades: Build

O `build` tipo de etapa oferece suporte às propriedades a seguir. Encontre detalhes dessas propriedades na seção [Propriedades da etapa da tarefa](#task-step-properties) deste artigo.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Opcional |
| `disableWorkingDirectoryOverride` | bool | Opcional |
| `entryPoint` | Cadeia de caracteres | Opcional |
| `env` | [cadeia de caracteres, Cadeia de caracteres,...] | Opcional |
| `expose` | [cadeia de caracteres, Cadeia de caracteres,...] | Opcional |
| `id` | cadeia | Opcional |
| `ignoreErrors` | bool | Opcional |
| `isolation` | Cadeia de caracteres | Opcional |
| `keep` | bool | Opcional |
| `network` | object | Opcional |
| `ports` | [cadeia de caracteres, Cadeia de caracteres,...] | Opcional |
| `pull` | bool | Opcional |
| `repeat` | int | Opcional |
| `retries` | int | Opcional |
| `retryDelay` | int (segundos) | Opcional |
| `secret` | object | Opcional |
| `startDelay` | int (segundos) | Opcional |
| `timeout` | int (segundos) | Opcional |
| `when` | [cadeia de caracteres, Cadeia de caracteres,...] | Opcional |
| `workingDirectory` | Cadeia de caracteres | Opcional |

### <a name="examples-build"></a>Exemplos: Build

#### <a name="build-image---context-in-root"></a>Compilar imagem-contexto na raiz

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Compilar o contexto de imagem no subdiretório

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>pressionado

Envie por push uma ou mais imagens criadas ou remarcadas para um registro de contêiner. Dá suporte ao envio por push para registros privados, como o registro de contêiner do Azure, ou para o Hub do Docker público.

### <a name="syntax-push"></a>Sintaxe: enviar por push

O `push` tipo de etapa dá suporte a uma coleção de imagens. A sintaxe da coleção YAML dá suporte a formatos embutidos e aninhados. O envio por push de uma única imagem normalmente é representado usando a sintaxe embutida:

```yml
version: v1.0.0
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Para maior legibilidade, use a sintaxe aninhada ao enviar várias imagens:

```yml
version: v1.0.0
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>Propriedades: enviar por push

O `push` tipo de etapa oferece suporte às propriedades a seguir. Encontre detalhes dessas propriedades na seção [Propriedades da etapa da tarefa](#task-step-properties) deste artigo.

| | | |
| -------- | ---- | -------- |
| `env` | [cadeia de caracteres, Cadeia de caracteres,...] | Opcional |
| `id` | cadeia | Opcional |
| `ignoreErrors` | bool | Opcional |
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

O `cmd` tipo Step executa um contêiner.

### <a name="syntax-cmd"></a>Sintaxe: cmd

```yml
version: v1.0.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Propriedades: cmd

O `cmd` tipo de etapa oferece suporte às seguintes propriedades:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Opcional |
| `disableWorkingDirectoryOverride` | bool | Opcional |
| `entryPoint` | Cadeia de caracteres | Opcional |
| `env` | [cadeia de caracteres, Cadeia de caracteres,...] | Opcional |
| `expose` | [cadeia de caracteres, Cadeia de caracteres,...] | Opcional |
| `id` | Cadeia de caracteres | Opcional |
| `ignoreErrors` | bool | Opcional |
| `isolation` | Cadeia de caracteres | Opcional |
| `keep` | bool | Opcional |
| `network` | object | Opcional |
| `ports` | [cadeia de caracteres, Cadeia de caracteres,...] | Opcional |
| `pull` | bool | Opcional |
| `repeat` | int | Opcional |
| `retries` | int | Opcional |
| `retryDelay` | int (segundos) | Opcional |
| `secret` | object | Opcional |
| `startDelay` | int (segundos) | Opcional |
| `timeout` | int (segundos) | Opcional |
| `when` | [cadeia de caracteres, Cadeia de caracteres,...] | Opcional |
| `workingDirectory` | Cadeia de caracteres | Opcional |

Você pode encontrar detalhes dessas propriedades na seção [Propriedades da etapa da tarefa](#task-step-properties) deste artigo.

### <a name="examples-cmd"></a>Exemplos: cmd

#### <a name="run-hello-world-image"></a>Executar imagem do Hello-World

Esse comando executa o arquivo `hello-world.yaml` de tarefa, que faz referência à imagem [Hello-World](https://hub.docker.com/_/hello-world/) no Hub do Docker.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Executar imagem bash e Echo "Olá mundo"

Esse comando executa o arquivo `bash-echo.yaml` de tarefa, que faz referência à imagem [bash](https://hub.docker.com/_/bash/) no Hub do Docker.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Executar marca de imagem de bash específica

Para executar uma versão de imagem específica, especifique a marca no `cmd`.

Esse comando executa o arquivo `bash-echo-3.yaml` de tarefa, que faz referência à imagem [bash: 3.0](https://hub.docker.com/_/bash/) no Hub do Docker.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Executar imagens personalizadas

O `cmd` tipo de etapa faz referência a imagens `docker run` usando o formato padrão. Supõe-se que as imagens não precedidas com um registro sejam originadas em docker.io. O exemplo anterior poderia ser igualmente representado como:

```yml
version: v1.0.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Usando a Convenção de `docker run` referência de imagem padrão `cmd` , o pode executar imagens de qualquer registro privado ou do Hub do Docker público. Se você estiver fazendo referência a imagens no mesmo registro no qual a tarefa ACR está em execução, não será necessário especificar nenhuma credencial de registro.

* Executar uma imagem que é de um registro de contêiner do Azure

    Substitua `[myregistry]` pelo nome do registro:

    ```yml
    version: v1.0.0
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* Generalizar a referência de registro com uma variável de execução

    Em vez de embutir em código o nome do `acr-task.yaml` registro em um arquivo, você pode torná-lo mais portátil usando uma [variável de execução](#run-variables). A `Run.Registry` variável expande em tempo de execução para o nome do registro no qual a tarefa está sendo executada.

    Para generalizar a tarefa anterior para que ela funcione em qualquer registro de contêiner do Azure, referencie a variável [Run. Registry](#runregistry) no nome da imagem:

    ```yml
    version: v1.0.0
    steps:
      - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>Propriedades da etapa da tarefa

Cada tipo de etapa dá suporte a várias propriedades apropriadas para seu tipo. A tabela a seguir define todas as propriedades da etapa disponível. Nem todos os tipos de etapa dão suporte a todas as propriedades. Para ver quais dessas propriedades estão disponíveis para cada tipo de etapa, consulte as seções de referência de tipo de etapa [cmd](#cmd), [Build](#build)e [Push](#push) .

| Propriedade | Type | Opcional | Descrição | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Sim | Se o contêiner deve ser desanexado durante a execução. | `false` |
| `disableWorkingDirectoryOverride` | bool | Sim | Se a funcionalidade `workingDirectory` de substituição deve ser desabilitada. Use isso em combinação com `workingDirectory` para ter controle total sobre o diretório de trabalho do contêiner. | `false` |
| `entryPoint` | Cadeia de caracteres | Sim | Substitui o `[ENTRYPOINT]` do contêiner de uma etapa. | Nenhum |
| `env` | [cadeia de caracteres, Cadeia de caracteres,...] | Sim | Matriz de cadeias `key=value` de caracteres no formato que definem as variáveis de ambiente para a etapa. | Nenhum |
| `expose` | [cadeia de caracteres, Cadeia de caracteres,...] | Sim | Matriz de portas que são expostas do contêiner. |  Nenhum |
| [`id`](#example-id) | Cadeia de caracteres | Sim | Identifica exclusivamente a etapa dentro da tarefa. Outras etapas na tarefa podem fazer referência a uma etapa `id`, como para a verificação de dependência `when`com.<br /><br />O `id` também é o nome do contêiner em execução. Os `id` processos em execução em outros contêineres na tarefa podem se referir ao como seu nome de host DNS ou para acessá-lo com os logs do Docker [ID], por exemplo. | `acb_step_%d`, em `%d` que é o índice baseado em 0 da etapa de cima para baixo no arquivo YAML |
| `ignoreErrors` | bool | Sim | Indica se a etapa será marcada como bem-sucedida, independentemente de ocorrer um erro durante a execução do contêiner. | `false` |
| `isolation` | Cadeia de caracteres | Sim | O nível de isolamento do contêiner. | `default` |
| `keep` | bool | Sim | Se o contêiner da etapa deve ser mantido após a execução. | `false` |
| `network` | object | Sim | Identifica uma rede na qual o contêiner é executado. | Nenhum |
| `ports` | [cadeia de caracteres, Cadeia de caracteres,...] | Sim | Matriz de portas que são publicadas do contêiner para o host. |  Nenhum |
| `pull` | bool | Sim | Se deve forçar um pull do contêiner antes de executá-lo para evitar qualquer comportamento de cache. | `false` |
| `privileged` | bool | Sim | Se o contêiner deve ser executado no modo privilegiado. | `false` |
| `repeat` | int | Sim | O número de tentativas para repetir a execução de um contêiner. | 0 |
| `retries` | int | Sim | O número de tentativas para tentar se um contêiner falhar na sua execução. Uma nova tentativa só será tentada se o código de saída de um contêiner for diferente de zero. | 0 |
| `retryDelay` | int (segundos) | Sim | O atraso em segundos entre as tentativas da execução de um contêiner. | 0 |
| `secret` | object | Sim | Identifica um segredo Azure Key Vault ou [identidade gerenciada para recursos do Azure](container-registry-tasks-authentication-managed-identity.md). | Nenhum |
| `startDelay` | int (segundos) | Sim | Número de segundos para atrasar a execução de um contêiner. | 0 |
| `timeout` | int (segundos) | Sim | Número máximo de segundos que uma etapa pode executar antes de ser encerrada. | 600 |
| [`when`](#example-when) | [cadeia de caracteres, Cadeia de caracteres,...] | Sim | Configura a dependência de uma etapa em uma ou mais etapas dentro da tarefa. | Nenhum |
| `user` | cadeia | Sim | O nome de usuário ou UID de um contêiner | Nenhum |
| `workingDirectory` | Cadeia de caracteres | Sim | Define o diretório de trabalho para uma etapa. Por padrão, as tarefas ACR criam um diretório raiz como o diretório de trabalho. No entanto, se sua compilação tiver várias etapas, as etapas anteriores poderão compartilhar artefatos com etapas posteriores especificando o mesmo diretório de trabalho. | `$HOME` |

### <a name="examples-task-step-properties"></a>Exemplos: Propriedades da etapa da tarefa

#### <a name="example-id"></a>Exemplo: ID

Crie duas imagens, instanciando uma imagem de teste funcional. Cada etapa é identificada por um `id` exclusivo que outras etapas na referência de tarefa em `when` sua propriedade.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Exemplo: quando

A `when` propriedade especifica a dependência de uma etapa em outras etapas dentro da tarefa. Ele dá suporte a dois valores de parâmetro:

* `when: ["-"]`-Indica nenhuma dependência de outras etapas. Uma etapa especificando `when: ["-"]` iniciará A execução imediatamente e habilitará A execução simultânea da etapa.
* `when: ["id1", "id2"]`-Indica que a etapa depende das etapas com `id` "ID1" e `id` "ID2". Esta etapa não será executada até que as etapas "ID1" e "ID2" sejam concluídas.

Se `when` não for especificado em uma etapa, essa etapa dependerá da conclusão da etapa anterior `acr-task.yaml` no arquivo.

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

As tarefas do ACR incluem um conjunto padrão de variáveis que estão disponíveis para etapas da tarefa quando elas são executadas. Essas variáveis podem ser acessadas usando o `{{.Run.VariableName}}`formato, `VariableName` onde é um dos seguintes:

* `Run.ID`
* `Run.Registry`
* `Run.Date`
* `Run.Commit`
* `Run.Branch`

### <a name="runid"></a>Run.ID

Cada execução, por `az acr run`meio do ou gatilho com base em execução de `az acr task create` tarefas criadas por meio do têm uma ID exclusiva. A ID representa a execução que está sendo executada no momento.

Normalmente usado para uma marcação exclusiva de uma imagem:

```yml
version: v1.0.0
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Run.Registry

O nome do servidor totalmente qualificado do registro. Normalmente usado para referenciar genericamente o registro em que a tarefa está sendo executada.

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="rundate"></a>Executar. Data

A hora UTC atual em que a execução começou.

### <a name="runcommit"></a>Executar. Commit

Para uma tarefa disparada por uma confirmação para um repositório GitHub, o identificador de confirmação.

### <a name="runbranch"></a>Executar. Branch

Para uma tarefa disparada por uma confirmação para um repositório GitHub, o nome da ramificação.

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

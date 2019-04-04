---
title: Referência de tarefas de registo de contentor do Azure - YAML
description: Referência para definir tarefas em YAML para tarefas de ACR, incluindo propriedades da tarefa, tipos de passo, propriedades do passo e variáveis incorporadas.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/28/2019
ms.author: danlep
ms.openlocfilehash: b2398e7db7ed91dee8d85c0c50058bb15b9f4c7e
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894137"
---
# <a name="acr-tasks-reference-yaml"></a>Referência de tarefas do ACR: YAML

Definição de tarefa de várias etapas nas tarefas de ACR fornece um primitivo de centrada no contêiner de computação focado na criação, teste e contentores de aplicação de patches. Este artigo abrange os comandos, parâmetros, propriedades e sintaxe para os ficheiros YAML que definem suas tarefas de vários passos.

Este artigo contém uma referência para a criação de arquivos YAML de tarefas de vários passos para tarefas do ACR. Se desejar uma introdução às tarefas do ACR, consulte a [descrição geral de tarefas de ACR](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>formato de ficheiro ACR task.yaml

Tarefas de ACR oferece suporte a declaração de tarefa de várias etapas na sintaxe YAML padrão. Definir os passos de uma tarefa num ficheiro YAML. Pode, em seguida, executar a tarefa manualmente, transmitindo o ficheiro para o [acr az execute] [ az-acr-run] comando. Em alternativa, utilize o ficheiro para criar uma tarefa com [az acr tarefa criar] [ az-acr-task-create] que é acionada automaticamente numa atualização de imagem de consolidação ou a base de Git. Embora este artigo refere-se ao `acr-task.yaml` como o ficheiro que contém os passos, tarefas de ACR suporta qualquer nome de ficheiro válido com um [suportado extensão](#supported-task-filename-extensions).

O nível superior `acr-task.yaml` primitivos são **propriedades da tarefa**, **passo tipos**, e **passo propriedades**:

* [Propriedades da tarefa](#task-properties) aplicam-se a todos os passos em toda a execução da tarefa. Existem várias propriedades de tarefa global, incluindo:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Passo tipos de tarefas](#task-step-types) representam os tipos de ações que podem ser executadas numa tarefa. Existem três tipos de passo:
  * `build`
  * `push`
  * `cmd`
* [Propriedades do passo da tarefa](#task-step-properties) são parâmetros que se aplicam a um passo individual. Existem várias propriedades do passo, incluindo:
  * `startDelay`
  * `timeout`
  * `when`
  * ... e muito mais.

O formato de base de um `acr-task.yaml` ficheiro, incluindo algumas propriedades comuns do passo, segue-se. Ao não uma representação completa de todas as propriedades do passo disponíveis ou utilização de tipo de passo, ele fornece uma rápida visão geral do formato de arquivo básico.

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

### <a name="supported-task-filename-extensions"></a>Extensões de nome de ficheiro de tarefa suportados

Tarefas de ACR reservou várias extensões de nome de ficheiro, incluindo `.yaml`, que irá processar como um ficheiro de tarefa. Qualquer extensão *não* na lista seguinte é considerado pelas tarefas de ACR um Dockerfile: .yaml,. yml, .toml,. JSON,. SH, .bash, .zsh,. ps1, .ps,. cmd,. bat,. TS,. js, PHP,. PY,. RB, .lua

YAML é o único formato de ficheiro suportado atualmente por tarefas do ACR. Outras extensões de nome de ficheiro estão reservados para eventual suporte futuros.

## <a name="run-the-sample-tasks"></a>Executar as tarefas de exemplo

Existem vários ficheiros de tarefas de exemplo referenciados nas secções seguintes deste artigo. As tarefas de exemplo estão num repositório público do GitHub [Azure-amostras/acr-tarefas][acr-tasks]. Pode executá-los com o comando da CLI do Azure [acr az execute][az-acr-run]. Os comandos de exemplo são semelhantes a:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

A formatação dos comandos de exemplo parte do princípio de que configurou um registro padrão na CLI do Azure, para que eles omitir o `--registry` parâmetro. Para configurar um registro padrão, utilize o [az configure] [ az-configure] comando com o `--defaults` parâmetro, que aceita um `acr=REGISTRY_NAME` valor.

Por exemplo, para configurar a CLI do Azure com um registro padrão chamada "myregistry":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Propriedades da tarefa

Propriedades da tarefa, normalmente, são apresentados na parte superior de um `acr-task.yaml` de ficheiros e são propriedades globais que se aplicam durante toda a execução completa dos passos de tarefas. Algumas dessas propriedades global podem ser substituídas dentro de um passo individual.

| Propriedade | Type | Opcional | Descrição | Suportada de substituição | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | Sim | A versão do `acr-task.yaml` de ficheiros como analisado pelo serviço de tarefas do ACR. Enquanto as tarefas de ACR tenta manter a compatibilidade com versões anteriores, este valor permite que tarefas de ACR manter a compatibilidade dentro de uma versão definida. Se não for especificado, por predefinição, a versão mais recente. | Não | Nenhuma |
| `stepTimeout` | Int (segundos) | Sim | O número máximo de segundos que pode executar uma etapa. Se a propriedade é especificada numa tarefa, ele define a predefinição `timeout` propriedade de todos os passos. Se o `timeout` é especificada a propriedade numa etapa, substitui a propriedade fornecida pela tarefa. | Sim | 600 (10 minutos) |
| `workingDirectory` | string | Sim | O diretório de trabalho do contentor durante o tempo de execução. Se a propriedade é especificada numa tarefa, ele define a predefinição `workingDirectory` propriedade de todos os passos. Se for especificado num passo, substitui a propriedade fornecida pela tarefa. | Sim | `$HOME` |
| `env` | [string, string, ...] | Sim |  Matriz de cadeias de caracteres em `key=value` formato que definem as variáveis de ambiente para a tarefa. Se a propriedade é especificada numa tarefa, ele define a predefinição `env` propriedade de todos os passos. Se for especificado num passo, substitui quaisquer variáveis de ambiente herdados da tarefa. | Nenhuma |
| `secrets` | [SECRETO, segredo,...] | Sim | Matriz de [segredo](#secret) objetos. | Nenhuma |
| `networks` | [rede, rede,...] | Sim | Matriz de [rede](#network) objetos. | Nenhuma |

### <a name="secret"></a>segredo

O objeto secreto tem as seguintes propriedades.

| Propriedade | Type | Opcional | Descrição | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | string | Não | O identificador do segredo do. | Nenhuma |
| `akv` | string | Sim | O URL de segredo do Cofre de chaves do Azure (AKV). | Nenhuma |
| `clientID` | string | Sim | O ID de cliente do atribuído ao utilizador a identidade de recursos do Azure gerido. | Nenhuma |

### <a name="network"></a>rede

O objeto de rede tem as seguintes propriedades.

| Propriedade | Type | Opcional | Descrição | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | string | Não | O nome da rede. | Nenhuma |
| `driver` | string | Sim | O driver para gerir a rede. | Nenhuma |
| `ipv6` | booleano | Sim | Se o sistema de rede IPv6 está ativado. | `false` |
| `skipCreation` | booleano | Sim | Se ignorar a criação de rede. | `false` |
| `isDefault` | booleano | Sim | Se a rede está uma rede predefinida fornecida com o Azure Container Registry | `false` |

## <a name="task-step-types"></a>Tipos de passo de tarefa

Tarefas de ACR suporta três tipos de passo. Cada tipo de passo suporta várias propriedades, detalhadas na secção para cada tipo de passo.

| Tipo de passo | Descrição |
| --------- | ----------- |
| [`build`](#build) | Cria uma imagem de contentor através de familiar `docker build` sintaxe. |
| [`push`](#push) | Executa um `docker push` de recentemente criadas, ou retagged imagens para um registo de contentor. O Azure Container Registry, outros registos privados e o Docker Hub público são suportadas. |
| [`cmd`](#cmd) | Execuções de um contentor como um comando, com parâmetros passado para o contentor `[ENTRYPOINT]`. O `cmd` parâmetros como oferece suporte a tipo de passo `env`, `detach`e outro familiar `docker run` as opções, permitindo que a unidade e testes funcionais com execução em simultâneo de contentor do comando. |

## <a name="build"></a>Compilação

Crie uma imagem de contentor. O `build` tipo de passo representa um meio seguro e de multi-inquilino de execução `docker build` na cloud como um primitivo de primeira classe.

### <a name="syntax-build"></a>Sintaxe: criar

```yml
version: v1.0.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

O `build` tipo de passo suporta os parâmetros na tabela seguinte. O `build` tipo de passo também oferece suporte a todas as opções de compilação do [compilação do docker](https://docs.docker.com/engine/reference/commandline/build/) comando, tal como `--build-arg` para definir as variáveis de tempo de compilação.

| Parâmetro | Descrição | Opcional |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Define o completamente qualificado `image:tag` da imagem incorporada.<br /><br />Como as imagens podem ser utilizadas para validações de tarefas interna, como testes funcionais, nem todas as imagens exigem `push` para um registo. No entanto, para uma imagem dentro de uma execução de tarefa de instância, a imagem tem um nome para fazer referência.<br /><br />Ao contrário de `az acr build`, execução de tarefas do ACR não fornece o comportamento de push padrão. Com tarefas do ACR, o cenário de predefinição pressupõe que a capacidade de criar, validar, em seguida, enviar uma imagem. Ver [push](#push) para saber como opcionalmente as imagens de push incorporado. | Sim |
| `-f` &#124; `--file` | Especifica o Dockerfile passado para `docker build`. Se não for especificado, a predefinição Dockerfile na raiz do contexto é assumido. Para especificar um Dockerfile, passe o nome de ficheiro relativo à raiz do contexto. | Sim |
| `context` | O diretório de raiz passado para `docker build`. O diretório de raiz de cada tarefa está definido para um partilhada [workingDirectory](#task-step-properties)e inclui a raiz do diretório clonado Git associado. | Não |

### <a name="properties-build"></a>Propriedades: criar

O `build` tipo de passo suporta as seguintes propriedades. Encontrar detalhes sobre estas propriedades no [propriedades do passo de tarefa](#task-step-properties) seção deste artigo.

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
| `retryDelay` | Int (segundos) | Opcional |
| `secret` | objeto | Opcional |
| `startDelay` | Int (segundos) | Opcional |
| `timeout` | Int (segundos) | Opcional |
| `when` | [string, string, ...] | Opcional |
| `workingDirectory` | string | Opcional |

### <a name="examples-build"></a>Exemplos: criar

#### <a name="build-image---context-in-root"></a>Criar imagem - contexto na raiz

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Criar imagem - contexto no subdiretório

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>Push

Push um ou mais criados ou retagged imagens para um registo de contentor. Suporta o envio de registos privados, como o Azure Container Registry ou o Docker Hub público.

### <a name="syntax-push"></a>Sintaxe: push

O `push` suporta o tipo de passo uma coleção de imagens. Sintaxe de coleção de YAML suporta formatos aninhados e inline. Envio de uma única imagem normalmente é representado através de sintaxe de inline:

```yml
version: v1.0.0
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Para maior legibilidade, utilize sintaxe aninhada quando enviar várias imagens:

```yml
version: v1.0.0
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>Propriedades: push

O `push` tipo de passo suporta as seguintes propriedades. Encontrar detalhes sobre estas propriedades no [propriedades do passo de tarefa](#task-step-properties) seção deste artigo.

| | | |
| -------- | ---- | -------- |
| `env` | [string, string, ...] | Opcional |
| `id` | string | Opcional |
| `ignoreErrors` | booleano | Opcional |
| `startDelay` | Int (segundos) | Opcional |
| `timeout` | Int (segundos) | Opcional |
| `when` | [string, string, ...] | Opcional |

### <a name="examples-push"></a>Exemplos: push

#### <a name="push-multiple-images"></a>Push várias imagens

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Criar, push e executar

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

O `cmd` tipo de passo é executado um contentor.

### <a name="syntax-cmd"></a>Sintaxe: cmd

```yml
version: v1.0.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Propriedades: cmd

O `cmd` tipo de passo suporta as seguintes propriedades:

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
| `retryDelay` | Int (segundos) | Opcional |
| `secret` | objeto | Opcional |
| `startDelay` | Int (segundos) | Opcional |
| `timeout` | Int (segundos) | Opcional |
| `when` | [string, string, ...] | Opcional |
| `workingDirectory` | string | Opcional |

Pode encontrar os detalhes destas propriedades no [propriedades do passo de tarefa](#task-step-properties) seção deste artigo.

### <a name="examples-cmd"></a>Exemplos: cmd

#### <a name="run-hello-world-image"></a>Execute a imagem hello-world

Este comando executa o `hello-world.yaml` ficheiro de tarefa, o que faz referência a [hello-world](https://hub.docker.com/_/hello-world/) imagem no Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Execute o bash imagem e eco "hello world"

Este comando executa o `bash-echo.yaml` ficheiro de tarefa, o que faz referência a [bash](https://hub.docker.com/_/bash/) imagem no Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Execute o bash específico tag de imagem

Para executar uma versão de imagem específica, especifique a marca no `cmd`.

Este comando executa o `bash-echo-3.yaml` ficheiro de tarefa, o que faz referência a [bash: 3.0](https://hub.docker.com/_/bash/) imagem no Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Executar imagens personalizadas

O `cmd` referencia o tipo de passo imagens usando o padrão `docker run` formato. Se originar de docker.io devem imagens não prefaciadas com um registo. O exemplo anterior igualmente pode ser representado como:

```yml
version: v1.0.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Utilizando o padrão `docker run` Convenção de referência, de imagem `cmd` pode executar imagens a partir de qualquer registo privado ou o Docker Hub público. Se está a referenciar o imagens no mesmo registo em que a tarefa de ACR está em execução, não precisa de especificar quaisquer credenciais de registo.

* Executar uma imagem que é a partir de um Azure container registry

    Substitua `[myregistry]` com o nome do seu registo:

    ```yml
    version: v1.0.0
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* Generalizar a referência de registro com uma variável de execução

    Em vez de embutir o nome do seu registo numa `acr-task.yaml` arquivo, pode torná-lo mais portátil, usando um [executar variável](#run-variables). O `Run.Registry` variável expande-se em tempo de execução para o nome do registo em que a tarefa está em execução.

    Para generalizar a tarefa anterior para que ele funcione em qualquer registo de contentor do Azure, fazer referência a [Run.Registry](#runregistry) variável no nome da imagem:

    ```yml
    version: v1.0.0
    steps:
      - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>Propriedades do passo de tarefa

Cada tipo de passo suporta várias propriedades adequadas para o respetivo tipo. A tabela seguinte define todas as propriedades do passo disponíveis. Nem todos os tipos de passo suportam todas as propriedades. Para ver qual destas propriedades estão disponíveis para cada tipo de passo, consulte a [cmd](#cmd), [crie](#build), e [push](#push) passo secções de referência de tipo.

| Propriedade | Type | Opcional | Descrição | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | booleano | Sim | Se o contêiner deve ser desligado quando em execução. | `false` |
| `disableWorkingDirectoryOverride` | booleano | Sim | Se pretende desativar `workingDirectory` substituir a funcionalidade. Utilize esta opção em combinação com `workingDirectory` ter controlo total sobre o diretório de trabalho do contentor. | `false` |
| `entryPoint` | string | Sim | Substitui o `[ENTRYPOINT]` do contentor de um passo. | Nenhuma |
| `env` | [string, string, ...] | Sim | Matriz de cadeias de caracteres em `key=value` formato que definem as variáveis de ambiente para o passo. | Nenhuma |
| `expose` | [string, string, ...] | Sim | Matriz de portas que são expostas a partir do contentor. |  Nenhuma |
| [`id`](#example-id) | string | Sim | Identifica exclusivamente a etapa na tarefa. Outros passos da tarefa podem fazer referência a uma etapa `id`, por exemplo, para a dependência a verificar com `when`.<br /><br />O `id` também é o nome do contentor em execução. Processos em execução nos outros contentores na tarefa podem consultar o `id` como seu nome de anfitrião DNS, ou para aceder ao mesmo com os registos do docker [id], por exemplo. | `acb_step_%d`, onde `%d` é o índice baseado em 0 do passo de cima para baixo no ficheiro YAML |
| `ignoreErrors` | booleano | Sim | Se marcar o passo como concluída com êxito, independentemente de se Ocorreu um erro durante a execução do contentor. | `false` |
| `isolation` | string | Sim | O nível de isolamento do contentor. | `default` |
| `keep` | booleano | Sim | Se o contentor de um passo deve ser mantida após a execução. | `false` |
| `network` | objeto | Sim | Identifica uma rede no qual o contentor é executado. | Nenhuma |
| `ports` | [string, string, ...] | Sim | Matriz de portas que são publicados a partir do contentor para o anfitrião. |  Nenhuma |
| `pull` | booleano | Sim | Se forçar uma solicitação do contentor antes de executá-la para impedir que qualquer comportamento de colocação em cache. | `false` |
| `privileged` | booleano | Sim | Se é necessário executar o contentor no modo privilegiado. | `false` |
| `repeat` | int | Sim | O número de tentativas para repetir a execução de um contentor. | 0 |
| `retries` | int | Sim | O número de tentativas para tentar se sua execução de um contentor falha. Apenas é tentada uma repetição se o código de saída de um contentor é diferente de zero. | 0 |
| `retryDelay` | Int (segundos) | Sim | O atraso em segundos, entre tentativas de execução de um contentor. | 0 |
| `secret` | objeto | Sim | Identifica um segredo do Cofre de chaves do Azure ou uma identidade gerida para recursos do Azure. | Nenhuma |
| `startDelay` | Int (segundos) | Sim | Número de segundos para atrasar a execução de um contentor. | 0 |
| `timeout` | Int (segundos) | Sim | Número máximo de segundos que um passo pode executar antes de a ser terminadas. | 600 |
| [`when`](#example-when) | [string, string, ...] | Sim | Configura a dependência de um passo num ou mais outros passos na tarefa. | Nenhuma |
| `user` | string | Sim | O nome de utilizador ou o UID de um contentor | Nenhuma |
| `workingDirectory` | string | Sim | Define o diretório de trabalho de um passo. Por predefinição, as tarefas de ACR cria um diretório de raiz como o diretório de trabalho. No entanto, se sua compilação possui várias etapas, os passos anteriores podem partilhar artefactos com passos posteriores ao especificar o mesmo diretório de trabalho. | `$HOME` |

### <a name="examples-task-step-properties"></a>Exemplos: Propriedades do passo de tarefa

#### <a name="example-id"></a>Exemplo: id

Crie duas imagens, criação de instância de uma imagem de teste funcional. Cada passo é identificado por um exclusivo `id` que fazem referência a outros passos da tarefa no seu `when` propriedade.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Exemplo: quando

O `when` propriedade especifica a dependência de um passo em outros passos na tarefa. Ele oferece suporte a dois valores de parâmetro:

* `when: ["-"]` -Não indica a nenhuma dependência de outros passos. Especificar um passo `when: ["-"]` irá começar imediatamente a execução e permite a execução do passo em simultâneo.
* `when: ["id1", "id2"]` -Indica a etapa é dependente de passos com `id` "id1" e `id` "id2". Este passo não será executado até concluir os passos "id1" e "id2".

Se `when` não for especificado um passo, essa etapa é dependente da conclusão do passo anterior no `acr-task.yaml` ficheiro.

Execução do passo seqüencial sem `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Execução do passo seqüencial com `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Compilação de imagens paralela:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Compilação de imagem e dependentes de teste em paralelo:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Executar variáveis

Tarefas de ACR inclui um conjunto predefinido de variáveis que estão disponíveis para passos de tarefas quando eles são executados. Estas variáveis podem ser acedidas utilizando o formato `{{.Run.VariableName}}`, onde `VariableName` é um dos seguintes:

* `Run.ID`
* `Run.Registry`
* `Run.Date`

### <a name="run46id"></a>Execute&#46;ID

Cada execução, através de `az acr run`, ou a acionar a execução com base de tarefas criada por meio de `az acr task create` tem um ID exclusivo. O ID representa a execução atualmente a ser executada.

Normalmente utilizado para uma forma exclusiva de marcar uma imagem:

```yml
version: v1.0.0
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Run.Registry

O nome de servidor completamente qualificado do Registro. Normalmente utilizado para referenciar genericamente o registo em que a tarefa está a ser executada.

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="rundate"></a>Run.Date

A hora UTC atual a execução começou.

## <a name="next-steps"></a>Passos Seguintes

Para uma descrição geral das tarefas de vários passos, consulte a [executar vários passo compilação, teste e as tarefas de patch no ACR tarefas](container-registry-tasks-multi-step.md).

Para as compilações do passo a passo, consulte a [descrição geral de tarefas de ACR](container-registry-tasks-overview.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure

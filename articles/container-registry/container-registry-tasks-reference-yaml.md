---
title: Referência YAML - Tarefas ACR
description: Referência para a definição de tarefas em YAML para Tarefas ACR, incluindo propriedades de tarefas, tipos de etapas, propriedades de passo e variáveis incorporadas.
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: 9558f698b4a9dbca46431fc02ced6ae30de29121
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246984"
---
# <a name="acr-tasks-reference-yaml"></a>Referência de tarefas ACR: YAML

A definição de tarefa sem etapas em Tarefas ACR fornece uma computação centrada em contentores primitiva focada na construção, teste e remendar contentores. Este artigo cobre os comandos, parâmetros, propriedades e sintaxe para os ficheiros YAML que definem as suas tarefas em várias etapas.

Este artigo contém referência para a criação de ficheiros YAML de tarefas em várias etapas para tarefas ACR. Se quiser apresentar as Tarefas ACR, consulte a visão geral das [Tarefas ACR.](container-registry-tasks-overview.md)

## <a name="acr-taskyaml-file-format"></a>formato de ficheiro acr-task.yaml

A ACR Tasks apoia a declaração de tarefas em várias etapas na sintaxe padrão yAML. Define os passos de uma tarefa num ficheiro YAML. Em seguida, pode executar a tarefa manualmente, passando o ficheiro para o comando [de execução az acr.][az-acr-run] Ou, use o ficheiro para criar uma tarefa com a criação de [tarefas az acr][az-acr-task-create] que é desencadeada automaticamente numa atualização de imagem git ou base. Embora este artigo `acr-task.yaml` se refira como o ficheiro que contém as etapas, as Tarefas ACR suportam qualquer nome de ficheiro válido com uma [extensão suportada](#supported-task-filename-extensions).

Os primitivos `acr-task.yaml` de alto nível são propriedades de **tarefas,** **tipos de etapas**e **propriedades de etapas:**

* [As propriedades](#task-properties) de tarefa aplicam-se a todas as etapas ao longo da execução da tarefa. Existem várias propriedades de tarefas globais, incluindo:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* Os tipos de [etapas](#task-step-types) de tarefa representam os tipos de ações que podem ser realizadas numa tarefa. Existem três tipos de passos:
  * `build`
  * `push`
  * `cmd`
* [As propriedades](#task-step-properties) do passo de tarefa são parâmetros que se aplicam a um passo individual. Existem várias propriedades de passo, incluindo:
  * `startDelay`
  * `timeout`
  * `when`
  * ... e muitos mais.

Segue-se o `acr-task.yaml` formato base de um ficheiro, incluindo algumas propriedades comuns do passo. Embora não seja uma representação exaustiva de todas as propriedades de passo disponíveis ou utilização do tipo de passo, fornece uma visão geral rápida do formato de ficheiro básico.

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

### <a name="supported-task-filename-extensions"></a>Extensões de nome de ficheiro de tarefa suportada

A ACR Tasks reservou várias `.yaml`extensões de nome de ficheiro, incluindo, que irá processar como ficheiro de tarefas. Qualquer extensão *que não* esteja na lista seguinte é considerada pela ACR Tasks como um Dockerfile: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua.

O YAML é o único formato de ficheiro atualmente suportado por Tarefas ACR. As outras extensões de nome de ficheiro estão reservadas para um possível suporte futuro.

## <a name="run-the-sample-tasks"></a>Executar as tarefas da amostra

Existem vários ficheiros de tarefas de amostra referenciados nas seguintes secções deste artigo. As tarefas da amostra estão num repositório público do GitHub, [Azure-Samples/acr-tasks][acr-tasks]. Pode executá-los com o comando Azure CLI [az acr run][az-acr-run]. Os comandos da amostra são semelhantes a:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

A formatação dos comandos da amostra pressupõe que configuraste um registo predefinido no ClI Azure, por isso omitem o `--registry` parâmetro. Para configurar um registo predefinido, utilize o `--defaults` comando [az configurar][az-configure] com o parâmetro, que aceita um `acr=REGISTRY_NAME` valor.

Por exemplo, configurar o Azure CLI com um registo predefinido chamado "myregistry":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Propriedades de tarefas

As propriedades de tarefa saem tipicamente no topo de um `acr-task.yaml` ficheiro, e são propriedades globais que se aplicam ao longo de toda a execução dos passos de tarefa. Algumas destas propriedades globais podem ser ultrapassadas dentro de um passo individual.

| Propriedade | Tipo | Opcional | Descrição | Anulação suportada | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | Sim | A versão `acr-task.yaml` do ficheiro como analisado pelo serviço ACR Tasks. Enquanto as Tasks ACR se esforçam para manter a compatibilidade retrógrada, este valor permite que as Tarefas ACR mantenham a compatibilidade dentro de uma versão definida. Se não especificado, a versão mais recente. | Não | Nenhuma |
| `stepTimeout` | int (segundos) | Sim | O número máximo de segundos que um passo pode correr. Se a propriedade for especificada numa tarefa, `timeout` define a propriedade padrão de todos os passos. Se `timeout` a propriedade for especificada num degrau, substitui a propriedade fornecida pela tarefa. | Sim | 600 (10 minutos) |
| `workingDirectory` | string | Sim | O diretório de trabalho do recipiente durante o tempo de funcionamento. Se a propriedade for especificada numa tarefa, `workingDirectory` define a propriedade padrão de todos os passos. Se especificado num degrau, substitui a propriedade fornecida pela tarefa. | Sim | `/workspace` |
| `env` | [corda, corda, ...] | Sim |  Conjunto de cordas `key=value` em formato que definem as variáveis ambientais para a tarefa. Se a propriedade for especificada numa tarefa, `env` define a propriedade padrão de todos os passos. Se especificado num degrau, substitui quaisquer variáveis ambientais herdadas da tarefa. | Nenhuma |
| `secrets` | [segredo, segredo, ...] | Sim | Uma série de objetos [secretos.](#secret) | Nenhuma |
| `networks` | [rede, rede, ...] | Sim | Conjunto de objetos de [rede.](#network) | Nenhuma |

### <a name="secret"></a>segredo

O objeto secreto tem as seguintes propriedades.

| Propriedade | Tipo | Opcional | Descrição | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | string | Não | O identificador do segredo. | Nenhuma |
| `keyvault` | string | Sim | O URL Secreto do Cofre de Chaves Azure. | Nenhuma |
| `clientID` | string | Sim | A identificação do cliente da [identidade gerida atribuída](container-registry-tasks-authentication-managed-identity.md) ao utilizador para os recursos Azure. | Nenhuma |

### <a name="network"></a>network

O objeto de rede tem as seguintes propriedades.

| Propriedade | Tipo | Opcional | Descrição | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | string | Não | O nome da rede. | Nenhuma |
| `driver` | string | Sim | O condutor para gerir a rede. | Nenhuma |
| `ipv6` | bool | Sim | Se a rede IPv6 está ativada. | `false` |
| `skipCreation` | bool | Sim | Se falta a criação de rede. | `false` |
| `isDefault` | bool | Sim | Se a rede é uma rede predefinida fornecida com registo de contentores Azure | `false` |

## <a name="task-step-types"></a>Tipos de passo sonantes de tarefas

As Tarefas ACR suportam três tipos de etapas. Cada tipo de passo suporta várias propriedades, detalhadas na secção para cada tipo de passo.

| Tipo de passo | Descrição |
| --------- | ----------- |
| [`build`](#build) | Constrói uma imagem de `docker build` recipiente usando sintaxe familiar. |
| [`push`](#push) | Executa `docker push` uma de imagens recém-construídas ou remarcadas para um registo de contentores. O Registo de Contentores Azure, outros registos privados e o Centro Público de Docker são apoiados. |
| [`cmd`](#cmd) | Executa um recipiente como um comando, com `[ENTRYPOINT]`parâmetros passados para o recipiente . O `cmd` tipo de passo `env`suporta `detach`parâmetros `docker run` como, e outras opções de comando familiares, permitindo a unidade e os testes funcionais com execução simultânea do contentor. |

## <a name="build"></a>compilar

Construa uma imagem de recipiente. O `build` tipo de passo representa um multi-inquilino, meios seguros de correr `docker build` na nuvem como um primitivo de primeira classe.

### <a name="syntax-build"></a>Sintaxe: construir

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

O `build` tipo de passo suporta os parâmetros na tabela seguinte. O `build` tipo de passo também suporta todas as opções de construção do comando de construção de [estivadores,](https://docs.docker.com/engine/reference/commandline/build/) tais como `--build-arg` definir variáveis de tempo de construção.

| Parâmetro | Descrição | Opcional |
| --------- | ----------- | :-------: |
| `-t`&#124;`--image` | Define o totalmente `image:tag` qualificado da imagem construída.<br /><br />Como as imagens podem ser usadas para validações de `push` tarefas interiores, tais como testes funcionais, nem todas as imagens requerem para um registo. No entanto, para exemplo uma imagem dentro de uma execução de Tarefa, a imagem precisa de um nome para referência.<br /><br />Ao `az acr build`contrário, executar tarefas ACR não fornece comportamento de pressão padrão. Com as Tarefas ACR, o cenário padrão assume a capacidade de construir, validar e, em seguida, empurrar uma imagem. Veja [push](#push) como empurrar opcionalmente imagens construídas. | Sim |
| `-f`&#124;`--file` | Especifica o Dockerfile `docker build`passado para . Se não especificado, assume-se o Dockerfile predefinido na raiz do contexto. Para especificar um Dockerfile, passe o nome de ficheiro em relação à raiz do contexto. | Sim |
| `context` | O diretório de `docker build`raiz passou para . O diretório raiz de cada tarefa é definido para um [diretório](#task-step-properties)de trabalho partilhado , e inclui a raiz do diretório clonado git associado. | Não |

### <a name="properties-build"></a>Propriedades: construir

O `build` tipo de passo suporta as seguintes propriedades. Encontre detalhes destas propriedades na secção de propriedades da [etapa de Tarefa](#task-step-properties) deste artigo.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Opcional |
| `disableWorkingDirectoryOverride` | bool | Opcional |
| `entryPoint` | string | Opcional |
| `env` | [corda, corda, ...] | Opcional |
| `expose` | [corda, corda, ...] | Opcional |
| `id` | string | Opcional |
| `ignoreErrors` | bool | Opcional |
| `isolation` | string | Opcional |
| `keep` | bool | Opcional |
| `network` | objeto | Opcional |
| `ports` | [corda, corda, ...] | Opcional |
| `pull` | bool | Opcional |
| `repeat` | int | Opcional |
| `retries` | int | Opcional |
| `retryDelay` | int (segundos) | Opcional |
| `secret` | objeto | Opcional |
| `startDelay` | int (segundos) | Opcional |
| `timeout` | int (segundos) | Opcional |
| `when` | [corda, corda, ...] | Opcional |
| `workingDirectory` | string | Opcional |

### <a name="examples-build"></a>Exemplos: construir

#### <a name="build-image---context-in-root"></a>Construir imagem - contexto na raiz

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Construir imagem - contexto no subdiretório

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>empurrar

Empurre uma ou mais imagens construídas ou remarcadas para um registo de contentores. Suporta a pressão para registos privados como o Registo de Contentores Azure, ou para o Centro Público de Docker.

### <a name="syntax-push"></a>Sintaxe: empurre

O `push` tipo de passo suporta uma coleção de imagens. A sintaxe de recolha YAML suporta formatos inline e aninhados. Empurrar uma única imagem é tipicamente representado usando sintaxe inline:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Para uma maior legibilidade, utilize a sintaxe aninhada ao empurrar várias imagens:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Propriedades: empurrar

O `push` tipo de passo suporta as seguintes propriedades. Encontre detalhes destas propriedades na secção de propriedades da [etapa de Tarefa](#task-step-properties) deste artigo.

| | | |
| -------- | ---- | -------- |
| `env` | [corda, corda, ...] | Opcional |
| `id` | string | Opcional |
| `ignoreErrors` | bool | Opcional |
| `startDelay` | int (segundos) | Opcional |
| `timeout` | int (segundos) | Opcional |
| `when` | [corda, corda, ...] | Opcional |

### <a name="examples-push"></a>Exemplos: empurrar

#### <a name="push-multiple-images"></a>Empurre várias imagens

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Construir, empurrar e correr

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

O `cmd` tipo de passo funciona com um recipiente.

### <a name="syntax-cmd"></a>Sintaxe: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Propriedades: cmd

O `cmd` tipo de passo suporta as seguintes propriedades:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Opcional |
| `disableWorkingDirectoryOverride` | bool | Opcional |
| `entryPoint` | string | Opcional |
| `env` | [corda, corda, ...] | Opcional |
| `expose` | [corda, corda, ...] | Opcional |
| `id` | string | Opcional |
| `ignoreErrors` | bool | Opcional |
| `isolation` | string | Opcional |
| `keep` | bool | Opcional |
| `network` | objeto | Opcional |
| `ports` | [corda, corda, ...] | Opcional |
| `pull` | bool | Opcional |
| `repeat` | int | Opcional |
| `retries` | int | Opcional |
| `retryDelay` | int (segundos) | Opcional |
| `secret` | objeto | Opcional |
| `startDelay` | int (segundos) | Opcional |
| `timeout` | int (segundos) | Opcional |
| `when` | [corda, corda, ...] | Opcional |
| `workingDirectory` | string | Opcional |

Pode encontrar detalhes destas propriedades na secção de propriedades da [etapa task](#task-step-properties) deste artigo.

### <a name="examples-cmd"></a>Exemplos: cmd

#### <a name="run-hello-world-image"></a>Executar imagem do mundo do olá

Este comando executa o `hello-world.yaml` ficheiro de tarefas, que faz referência à imagem do mundo [olá](https://hub.docker.com/_/hello-world/) no Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Executar imagem de festa e ecoar "olá mundo"

Este comando executa o `bash-echo.yaml` ficheiro de tarefa, que faz referência à imagem de [festa](https://hub.docker.com/_/bash/) no Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Executar etiqueta de imagem de festa específica

Para executar uma versão de imagem `cmd`específica, especifique a etiqueta no .

Este comando executa o `bash-echo-3.yaml` ficheiro de tarefa, que faz referência à imagem de [bash:3.0](https://hub.docker.com/_/bash/) no Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Executar imagens personalizadas

O `cmd` tipo de passo refere `docker run` as imagens utilizando o formato padrão. As imagens não prefaciadas com um registo são assumidas como originárias de docker.io. O exemplo anterior poderia igualmente ser representado como:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Ao utilizar `docker run` a convenção `cmd` padrão de referência de imagem, pode executar imagens de qualquer registo privado ou do centro público de Docker. Se estiver a referir imagens no mesmo registo em que a Tarefa ACR está a executar, não precisa de especificar quaisquer credenciais de registo.

* Executa uma imagem que é de um registo de contentores Azure. O exemplo que se segue pressupõe que tem um registo chamado `myregistry`e uma imagem `myimage:mytag`personalizada.

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Generalizar a referência do registo com uma variável de execução ou pseudónimo

    Em vez de codificar duramente `acr-task.yaml` o seu nome de registo num ficheiro, pode torná-lo mais portátil utilizando uma [variável de Execução](#run-variables) ou [pseudónimo](#aliases). A `Run.Registry` variável ou `$Registry` pseudónimo expande-se em tempo de execução para o nome do registo em que a tarefa está a executar.

    Por exemplo, para generalizar a tarefa anterior de modo a que funcione em qualquer registo de contentores Azure, fazer referência à variável $Registry no nome da imagem:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>Propriedades do passo de tarefa

Cada tipo de passo suporta várias propriedades apropriadas para o seu tipo. A tabela que se segue define todas as propriedades de passo disponíveis. Nem todos os tipos de passosuportam todas as propriedades. Para ver quais destas propriedades estão disponíveis para cada tipo de passo, consulte as secções de referência do tipo [cmd,](#cmd) [construção](#build)e [impulso.](#push)

| Propriedade | Tipo | Opcional | Descrição | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Sim | Se o recipiente deve ser separado durante a execução. | `false` |
| `disableWorkingDirectoryOverride` | bool | Sim | Se deve `workingDirectory` desativar a funcionalidade de substituição. Utilize-o em `workingDirectory` combinação com o controlo total sobre o diretório de trabalho do recipiente. | `false` |
| `entryPoint` | string | Sim | Sobrepõe-se ao `[ENTRYPOINT]` recipiente de um degrau. | Nenhuma |
| `env` | [corda, corda, ...] | Sim | Conjunto de cordas `key=value` em formato que definem as variáveis ambientais para o passo. | Nenhuma |
| `expose` | [corda, corda, ...] | Sim | Conjunto de portas expostas do recipiente. |  Nenhuma |
| [`id`](#example-id) | string | Sim | Identifica exclusivamente o passo dentro da tarefa. Outros passos na tarefa podem `id`fazer referência a um passo, como por exemplo, a verificação da dependência com `when`.<br /><br />O `id` é também o nome do contentor de corrida. Os processos em execução noutros contentores na tarefa podem referir-se ao `id` nome de anfitrião do DNS, ou para aceder a ele com registos de estivadores [id], por exemplo. | `acb_step_%d`, `%d` onde está o índice de 0 baseado seleções do degrau superior-down no ficheiro YAML |
| `ignoreErrors` | bool | Sim | Se para marcar o passo como bem sucedido, independentemente de ter ocorrido um erro durante a execução do contentor. | `false` |
| `isolation` | string | Sim | O nível de isolamento do contentor. | `default` |
| `keep` | bool | Sim | Se o contentor do passo deve ser mantido após a execução. | `false` |
| `network` | objeto | Sim | Identifica uma rede em que o contentor funciona. | Nenhuma |
| `ports` | [corda, corda, ...] | Sim | Conjunto de portas que são publicadas do contentor para o hospedeiro. |  Nenhuma |
| `pull` | bool | Sim | Se forçar uma força do recipiente antes de executá-lo para evitar qualquer comportamento de cache. | `false` |
| `privileged` | bool | Sim | Se deve executar o recipiente em modo privilegiado. | `false` |
| `repeat` | int | Sim | O número de tentativas para repetir a execução de um contentor. | 0 |
| `retries` | int | Sim | O número de tentativas de tentativa se um contentor falhar a sua execução. Uma nova tentativa só é tentada se o código de saída de um contentor não for zero. | 0 |
| `retryDelay` | int (segundos) | Sim | O atraso em segundos entre as tentativas de execução de um contentor. | 0 |
| `secret` | objeto | Sim | Identifica um segredo azure key vault ou [identidade gerida para os recursos Azure](container-registry-tasks-authentication-managed-identity.md). | Nenhuma |
| `startDelay` | int (segundos) | Sim | Número de segundos para atrasar a execução de um contentor. | 0 |
| `timeout` | int (segundos) | Sim | O número máximo de segundos que um passo pode executar antes de ser terminado. | 600 |
| [`when`](#example-when) | [corda, corda, ...] | Sim | Confunde a dependência de um passo em um ou mais passos dentro da tarefa. | Nenhuma |
| `user` | string | Sim | O nome do utilizador ou UID de um recipiente | Nenhuma |
| `workingDirectory` | string | Sim | Define o diretório de trabalho para um passo. Por padrão, as Tarefas ACR criam um diretório de raiz como o diretório de trabalho. No entanto, se a sua construção tiver vários passos, passos anteriores podem partilhar artefactos com passos posteriores, especificando o mesmo diretório de trabalho. | `/workspace` |

### <a name="examples-task-step-properties"></a>Exemplos: Propriedades de passo de tarefa

#### <a name="example-id"></a>Exemplo: id

Construa duas imagens, intentando uma imagem funcional de teste. Cada passo é identificado `id` por um único que outros `when` passos na referência de tarefa na sua propriedade.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Exemplo: quando

A `when` propriedade especifica a dependência de um passo em outros passos dentro da tarefa. Suporta dois valores de parâmetro:

* `when: ["-"]`- Não indica dependência de outros passos. Um passo `when: ["-"]` especificado começará a execução imediatamente, e permitirá a execução de etapas simultâneas.
* `when: ["id1", "id2"]`- Indica que o passo `id` depende de `id` passos com "id1" e "id2". Este passo não será executado até que os passos "id1" e "id2" estejam completos.

Se `when` não for especificado num passo, esse passo depende da `acr-task.yaml` conclusão do passo anterior no ficheiro.

Execução sequencial sem: `when`

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Execução sequencial de `when`etapas com:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Imagens paralelas constroem:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Construção de imagem paralela e testes dependentes:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Executar variáveis

As Tarefas ACR incluem um conjunto padrão de variáveis que estão disponíveis para os passos de tarefa quando executam. Estas variáveis podem ser acedidas utilizando o formato, `{{.Run.VariableName}}`onde `VariableName` é uma das seguintes:

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

Os nomes variáveis são geralmente autoexplicativos. Os detalhes seguem-se a variáveis comumente usadas. A partir da `v1.1.0`versão YAML, pode utilizar um [pseudónimo](#aliases) de tarefa abreviado e predefinido no lugar da maioria das variáveis de execução. Por exemplo, no `{{.Run.Registry}}`lugar `$Registry` de , use o pseudónimo.

### <a name="runid"></a>Run.ID

Cada Run, `az acr run`através de , ou desencadear `az acr task create`a execução baseada em tarefas criadas através , tem um ID único. O ID representa a Execução que está a ser executada.

Normalmente usado para uma imagem exclusivamente marcada:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Executar.Registo

O nome do servidor totalmente qualificado do registo. Normalmente usado para referenciar genericamente o registo onde a tarefa está sendo executada.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Executar.RegistryName

O nome do registo do contentor. Normalmente usado em passos de tarefa que não requerem `cmd` um nome de servidor totalmente qualificado, por exemplo, passos que executam comandos Azure CLI em registos.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Executar.Data

A atual hora utc começou a corrida.

### <a name="runcommit"></a>Executar.Comprometer

Para uma tarefa desencadeada por um compromisso com um repositório GitHub, o identificador de compromisso.

### <a name="runbranch"></a>Executar.Branch

Para uma tarefa desencadeada por um compromisso com um repositório GitHub, o nome do ramo.

## <a name="aliases"></a>Aliases

A `v1.1.0`partir de , ACR Tasks suporta pseudónimos que estão disponíveis para os passos de tarefa quando executam. Os pseudónimos são semelhantes no conceito aos pseudónimos (atalhos de comando) suportados em bash e em alguns outros cartuchos de comando. 

Com um pseudónimo, pode lançar qualquer comando ou grupo de comandos (incluindo opções e nomes de ficheiros) inserindo uma única palavra.

A ACR Tasks suporta vários pseudónimos predefinidos e também pseudónimos personalizados que cria.

### <a name="predefined-aliases"></a>Pseudónimos predefinidos

Os seguintes pseudónimos de tarefa estão disponíveis para utilização no lugar de variáveis de [execução:](#run-variables)

| Alias | Variável de execução |
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

Nas etapas de tarefa, precede um pseudónimo com a `$` diretiva, como neste exemplo:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Pseudónimos de imagem

Cada um dos seguintes pseudónimos aponta para uma imagem estável no Registo de Contentores da Microsoft (MCR). Pode consultar cada um deles `cmd` na secção de um ficheiro Task sem utilizar uma diretiva.

| Alias | Imagem |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

A seguinte tarefa de exemplo utiliza vários pseudónimos para [expurgar](container-registry-auto-purge.md) etiquetas de imagem com mais de 7 dias no repo no registo de execução: `samples/hello-world`

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Pseudónimo personalizado

Defina um pseudónimo personalizado no seu ficheiro YAML e use-o como mostrado no exemplo seguinte. Um pseudónimo só pode conter caracteres alfanuméricos. A diretiva padrão para expandir `$` um pseudónimo é o carácter.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

Pode ligar-se a um ficheiro YAML remoto ou local para definições personalizadas de pseudónimos. O exemplo seguinte liga-se a um ficheiro YAML no armazenamento de blob Azure:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Passos seguintes

Para uma visão geral das tarefas em várias etapas, consulte as tarefas de [construção, teste e patch em várias etapas em Tarefas ACR](container-registry-tasks-multi-step.md).

Para construções de um só passo, consulte a visão geral das [Tarefas ACR](container-registry-tasks-overview.md).



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure

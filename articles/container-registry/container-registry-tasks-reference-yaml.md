---
title: Referência YAML - Tarefas ACR
description: Referência para a definição de tarefas em YAML para Tarefas ACR, incluindo propriedades de tarefas, tipos de etapas, propriedades de passo e variáveis incorporadas.
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: 9558f698b4a9dbca46431fc02ced6ae30de29121
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668499"
---
# <a name="acr-tasks-reference-yaml"></a>Referência de tarefas ACR: YAML

A definição de tarefa sem etapas em Tarefas ACR fornece uma computação centrada em contentores primitiva focada na construção, teste e remendar contentores. Este artigo cobre os comandos, parâmetros, propriedades e sintaxe para os ficheiros YAML que definem as suas tarefas em várias etapas.

Este artigo contém referência para a criação de ficheiros YAML de tarefas em várias etapas para tarefas ACR. Se quiser apresentar as Tarefas ACR, consulte a visão geral das [Tarefas ACR.](container-registry-tasks-overview.md)

## <a name="acr-taskyaml-file-format"></a>formato de ficheiro acr-task.yaml

A ACR Tasks apoia a declaração de tarefas em várias etapas na sintaxe padrão yAML. Define os passos de uma tarefa num ficheiro YAML. Em seguida, pode executar a tarefa manualmente, passando o ficheiro para o comando [de execução az acr.][az-acr-run] Ou, use o ficheiro para criar uma tarefa com a criação de [tarefas az acr][az-acr-task-create] que é desencadeada automaticamente numa atualização de imagem git ou base. Embora este artigo se refira a `acr-task.yaml` como ficheiro que contém as etapas, as Tarefas ACR suportam qualquer nome de ficheiro válido com uma [extensão suportada](#supported-task-filename-extensions).

Os primitivos de `acr-task.yaml` de alto nível são propriedades de **tarefas,** **tipos de etapas**e **propriedades de etapas:**

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

Segue-se o formato base de um ficheiro `acr-task.yaml`, incluindo algumas propriedades comuns do passo. Embora não seja uma representação exaustiva de todas as propriedades de passo disponíveis ou utilização do tipo de passo, fornece uma visão geral rápida do formato de ficheiro básico.

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

A ACR Tasks reservou várias extensões de nome de ficheiro, incluindo `.yaml`, que irá processar como ficheiro de tarefas. Qualquer extensão *que não* esteja na lista seguinte é considerada pela ACR Tasks como um Dockerfile: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua.

O YAML é o único formato de ficheiro atualmente suportado por Tarefas ACR. As outras extensões de nome de ficheiro estão reservadas para um possível suporte futuro.

## <a name="run-the-sample-tasks"></a>Executar as tarefas da amostra

Existem vários ficheiros de tarefas de amostra referenciados nas seguintes secções deste artigo. As tarefas da amostra estão num repositório público do GitHub, [Azure-Samples/acr-tasks][acr-tasks]. Pode executá-los com o comando Azure CLI [az acr run][az-acr-run]. Os comandos da amostra são semelhantes a:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

A formatação dos comandos da amostra pressupõe que configuraum registo predefinido no ClI Azure, por isso omitem o parâmetro `--registry`. Para configurar um registo predefinido, utilize o comando [az configurar][az-configure] com o parâmetro `--defaults`, que aceita um valor `acr=REGISTRY_NAME`.

Por exemplo, configurar o Azure CLI com um registo predefinido chamado "myregistry":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Propriedades de tarefas

As propriedades de tarefa saem tipicamente no topo de um ficheiro `acr-task.yaml`, e são propriedades globais que se aplicam ao longo de toda a execução dos passos de tarefa. Algumas destas propriedades globais podem ser ultrapassadas dentro de um passo individual.

| Propriedade | Tipo | Opcional | Descrição | Anulação suportada | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | Sim | A versão do ficheiro `acr-task.yaml` analisado pelo serviço ACR Tasks. Enquanto as Tasks ACR se esforçam para manter a compatibilidade retrógrada, este valor permite que as Tarefas ACR mantenham a compatibilidade dentro de uma versão definida. Se não especificado, a versão mais recente. | Não | Nenhum |
| `stepTimeout` | int (segundos) | Sim | O número máximo de segundos que um passo pode correr. Se a propriedade for especificada numa tarefa, define o predefinido `timeout` propriedade de todos os passos. Se a propriedade `timeout` for especificada num degrau, substitui a propriedade fornecida pela tarefa. | Sim | 600 (10 minutos) |
| `workingDirectory` | string | Sim | O diretório de trabalho do recipiente durante o tempo de funcionamento. Se a propriedade for especificada numa tarefa, define o predefinido `workingDirectory` propriedade de todos os passos. Se especificado num degrau, substitui a propriedade fornecida pela tarefa. | Sim | `/workspace` |
| `env` | [corda, corda, ...] | Sim |  Conjunto de cordas em formato `key=value` que definem as variáveis ambientais para a tarefa. Se a propriedade for especificada numa tarefa, define o predefinido `env` propriedade de todos os passos. Se especificado num degrau, substitui quaisquer variáveis ambientais herdadas da tarefa. | Nenhum |
| `secrets` | [segredo, segredo, ...] | Sim | Uma série de objetos [secretos.](#secret) | Nenhum |
| `networks` | [rede, rede, ...] | Sim | Conjunto de objetos de [rede.](#network) | Nenhum |

### <a name="secret"></a>segredo

O objeto secreto tem as seguintes propriedades.

| Propriedade | Tipo | Opcional | Descrição | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | string | Não | O identificador do segredo. | Nenhum |
| `keyvault` | string | Sim | O URL Secreto do Cofre de Chaves Azure. | Nenhum |
| `clientID` | string | Sim | A identificação do cliente da [identidade gerida atribuída](container-registry-tasks-authentication-managed-identity.md) ao utilizador para os recursos Azure. | Nenhum |

### <a name="network"></a>rede

O objeto de rede tem as seguintes propriedades.

| Propriedade | Tipo | Opcional | Descrição | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | string | Não | O nome da rede. | Nenhum |
| `driver` | string | Sim | O condutor para gerir a rede. | Nenhum |
| `ipv6` | bool | Sim | Se a rede IPv6 está ativada. | `false` |
| `skipCreation` | bool | Sim | Se falta a criação de rede. | `false` |
| `isDefault` | bool | Sim | Se a rede é uma rede predefinida fornecida com registo de contentores Azure | `false` |

## <a name="task-step-types"></a>Tipos de passo sonantes de tarefas

As Tarefas ACR suportam três tipos de etapas. Cada tipo de passo suporta várias propriedades, detalhadas na secção para cada tipo de passo.

| Tipo de passo | Descrição |
| --------- | ----------- |
| [`build`](#build) | Constrói uma imagem de recipiente usando uma sintaxe familiar `docker build`. |
| [`push`](#push) | Executa uma `docker push` de imagens recém-construídas ou remarcadas para um registo de contentores. O Registo de Contentores Azure, outros registos privados e o Centro Público de Docker são apoiados. |
| [`cmd`](#cmd) | Executa um recipiente como comando, com parâmetros passados para o `[ENTRYPOINT]`do contentor . O tipo de passo `cmd` suporta parâmetros como `env`, `detach`, e outras opções de comando de `docker run` familiares, permitindo a unidade e os testes funcionais com execução de contentores simultâneos. |

## <a name="build"></a>construir

Construa uma imagem de recipiente. O `build` tipo de passo representa um meio multi-inquilino, seguro de correr `docker build` na nuvem como um primitivo de primeira classe.

### <a name="syntax-build"></a>Sintaxe: construir

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

O `build` tipo de passo suporta os parâmetros na tabela seguinte. O `build` tipo de passo também suporta todas as opções de construção do comando de construção de [estivadores,](https://docs.docker.com/engine/reference/commandline/build/) tais como `--build-arg` para definir variáveis de tempo de construção.

| Parâmetro | Descrição | Opcional |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Define o `image:tag` totalmente qualificado da imagem construída.<br /><br />Como as imagens podem ser usadas para validações de tarefas internas, como testes funcionais, nem todas as imagens requerem `push` a um registo. No entanto, para exemplo uma imagem dentro de uma execução de Tarefa, a imagem precisa de um nome para referência.<br /><br />Ao contrário `az acr build`, executar tarefas ACR não fornece comportamento de pressão padrão. Com as Tarefas ACR, o cenário padrão assume a capacidade de construir, validar e, em seguida, empurrar uma imagem. Veja [](#push) como empurrar opcionalmente imagens construídas. | Sim |
| `-f` &#124; `--file` | Especifica o Dockerfile passado para `docker build`. Se não especificado, assume-se o Dockerfile predefinido na raiz do contexto. Para especificar um Dockerfile, passe o nome de ficheiro em relação à raiz do contexto. | Sim |
| `context` | O diretório de raiz passou para `docker build`. O diretório raiz de cada tarefa é definido para um [diretório](#task-step-properties)de trabalho partilhado , e inclui a raiz do diretório clonado git associado. | Não |

### <a name="properties-build"></a>Propriedades: construir

O tipo `build` passo suporta as seguintes propriedades. Encontre detalhes destas propriedades na secção de propriedades da [etapa de Tarefa](#task-step-properties) deste artigo.

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

O tipo `push` passo suporta as seguintes propriedades. Encontre detalhes destas propriedades na secção de propriedades da [etapa de Tarefa](#task-step-properties) deste artigo.

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

O tipo de passo `cmd` suporta as seguintes propriedades:

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

Este comando executa o ficheiro de tarefas `hello-world.yaml`, que faz referência à imagem [do hello-world](https://hub.docker.com/_/hello-world/) no Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Executar imagem de festa e ecoar "olá mundo"

Este comando executa o ficheiro de tarefa seletiva `bash-echo.yaml`, que faz referência à imagem de [festa](https://hub.docker.com/_/bash/) no Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Executar etiqueta de imagem de festa específica

Para executar uma versão de imagem específica, especifique a etiqueta no `cmd`.

Este comando executa o ficheiro de tarefa `bash-echo-3.yaml`, que faz referência à imagem [bash:3.0](https://hub.docker.com/_/bash/) no Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Executar imagens personalizadas

O `cmd` tipo de passo refere imagens utilizando o formato padrão `docker run`. As imagens não prefaciadas com um registo são assumidas como originárias de docker.io. O exemplo anterior poderia igualmente ser representado como:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Ao utilizar a convenção padrão `docker run` de referência de imagem, `cmd` pode executar imagens de qualquer registo privado ou do centro público de Docker. Se estiver a referir imagens no mesmo registo em que a Tarefa ACR está a executar, não precisa de especificar quaisquer credenciais de registo.

* Executa uma imagem que é de um registo de contentores Azure. O exemplo que se segue pressupõe que tem um registo chamado `myregistry`e uma imagem personalizada `myimage:mytag`.

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Generalizar a referência do registo com uma variável de execução ou pseudónimo

    Em vez de codificar duramente o seu nome de registo num ficheiro `acr-task.yaml`, pode torná-lo mais portátil utilizando uma [variável ou](#run-variables) [pseudónimo](#aliases)run . O `Run.Registry` variável ou `$Registry` pseudónimo expande-se em tempo de execução para o nome do registo em que a tarefa está a executar.

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
| `disableWorkingDirectoryOverride` | bool | Sim | Se deve desativar `workingDirectory` sobrepor a funcionalidade. Utilize isto em combinação com `workingDirectory` para ter controlo total sobre o diretório de trabalho do recipiente. | `false` |
| `entryPoint` | string | Sim | Substitui o `[ENTRYPOINT]` de um contentor de um degrau. | Nenhum |
| `env` | [corda, corda, ...] | Sim | Conjunto de cordas em formato `key=value` que definem as variáveis ambientais para o passo. | Nenhum |
| `expose` | [corda, corda, ...] | Sim | Conjunto de portas expostas do recipiente. |  Nenhum |
| [`id`](#example-id) | string | Sim | Identifica exclusivamente o passo dentro da tarefa. Outros passos na tarefa podem fazer referência a um passo `id`, como por exemplo, a verificação da dependência com `when`.<br /><br />O `id` é também o nome do contentor de corrida. Os processos em execução noutros contentores na tarefa podem referir-se à `id` como o seu nome de anfitrião DNS, ou para aceder a ele com registos de estivadores [id], por exemplo. | `acb_step_%d`, onde `%d` é o índice baseado em 0-base do degrau superior no ficheiro YAML |
| `ignoreErrors` | bool | Sim | Se para marcar o passo como bem sucedido, independentemente de ter ocorrido um erro durante a execução do contentor. | `false` |
| `isolation` | string | Sim | O nível de isolamento do contentor. | `default` |
| `keep` | bool | Sim | Se o contentor do passo deve ser mantido após a execução. | `false` |
| `network` | objeto | Sim | Identifica uma rede em que o contentor funciona. | Nenhum |
| `ports` | [corda, corda, ...] | Sim | Conjunto de portas que são publicadas do contentor para o hospedeiro. |  Nenhum |
| `pull` | bool | Sim | Se forçar uma força do recipiente antes de executá-lo para evitar qualquer comportamento de cache. | `false` |
| `privileged` | bool | Sim | Se deve executar o recipiente em modo privilegiado. | `false` |
| `repeat` | int | Sim | O número de tentativas para repetir a execução de um contentor. | 0 |
| `retries` | int | Sim | O número de tentativas de tentativa se um contentor falhar a sua execução. Uma nova tentativa só é tentada se o código de saída de um contentor não for zero. | 0 |
| `retryDelay` | int (segundos) | Sim | O atraso em segundos entre as tentativas de execução de um contentor. | 0 |
| `secret` | objeto | Sim | Identifica um segredo azure key vault ou [identidade gerida para os recursos Azure](container-registry-tasks-authentication-managed-identity.md). | Nenhum |
| `startDelay` | int (segundos) | Sim | Número de segundos para atrasar a execução de um contentor. | 0 |
| `timeout` | int (segundos) | Sim | O número máximo de segundos que um passo pode executar antes de ser terminado. | 600 |
| [`when`](#example-when) | [corda, corda, ...] | Sim | Confunde a dependência de um passo em um ou mais passos dentro da tarefa. | Nenhum |
| `user` | string | Sim | O nome do utilizador ou UID de um recipiente | Nenhum |
| `workingDirectory` | string | Sim | Define o diretório de trabalho para um passo. Por padrão, as Tarefas ACR criam um diretório de raiz como o diretório de trabalho. No entanto, se a sua construção tiver vários passos, passos anteriores podem partilhar artefactos com passos posteriores, especificando o mesmo diretório de trabalho. | `/workspace` |

### <a name="examples-task-step-properties"></a>Exemplos: Propriedades de passo de tarefa

#### <a name="example-id"></a>Exemplo: id

Construa duas imagens, intentando uma imagem funcional de teste. Cada passo é identificado por um `id` único que outros passos na referência de tarefa na sua propriedade `when`.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Exemplo: quando

A propriedade `when` especifica a dependência de um passo em outros passos dentro da tarefa. Suporta dois valores de parâmetro:

* `when: ["-"]` - Não indica dependência de outros passos. Um passo que especifica `when: ["-"]` iniciará a execução imediatamente, e permite a execução de etapas simultâneas.
* `when: ["id1", "id2"]` - Indica que o passo depende de passos com `id` "id1" e `id` "id2". Este passo não será executado até que os passos "id1" e "id2" estejam completos.

Se `when` não for especificado num passo, esse passo depende da conclusão do passo anterior no ficheiro `acr-task.yaml`.

Execução sequencial sem `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Execução sequencial com `when`:

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

As Tarefas ACR incluem um conjunto padrão de variáveis que estão disponíveis para os passos de tarefa quando executam. Estas variáveis podem ser acedidas utilizando o formato `{{.Run.VariableName}}`, onde `VariableName` é uma das seguintes:

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

Os nomes variáveis são geralmente autoexplicativos. Os detalhes seguem-se a variáveis comumente usadas. A partir da versão YAML `v1.1.0`, pode utilizar um pseudónimo de [tarefa](#aliases) abreviado e predefinido no lugar da maioria das variáveis de execução. Por exemplo, no lugar de `{{.Run.Registry}}`, use o pseudónimo `$Registry`.

### <a name="runid"></a>Run.ID

Cada Run, através de `az acr run`, ou desencadear a execução baseada em tarefas criadas através de `az acr task create`, tem um ID único. O ID representa a Execução que está a ser executada.

Normalmente usado para uma imagem exclusivamente marcada:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Run.Registry

O nome do servidor totalmente qualificado do registo. Normalmente usado para referenciar genericamente o registo onde a tarefa está sendo executada.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Executar.RegistryName

O nome do registo do contentor. Normalmente usado em passos de tarefa que não requerem um nome de servidor totalmente qualificado, por exemplo, `cmd` passos que executam comandos Azure CLI em registos.

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

A partir de `v1.1.0`, a ACR Tasks suporta pseudónimos que estão disponíveis para os passos de tarefa quando executam. Os pseudónimos são semelhantes no conceito aos pseudónimos (atalhos de comando) suportados em bash e em alguns outros cartuchos de comando. 

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

Nas etapas de tarefa, precede um pseudónimo com a diretiva `$`, como neste exemplo:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Pseudónimos de imagem

Cada um dos seguintes pseudónimos aponta para uma imagem estável no Registo de Contentores da Microsoft (MCR). Pode consultar cada um deles na secção `cmd` de um ficheiro Task sem utilizar uma diretiva.

| Alias | Imagem |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

A seguinte tarefa de exemplo utiliza vários pseudónimos para [expurgar](container-registry-auto-purge.md) etiquetas de imagem com mais de 7 dias no repo `samples/hello-world` no registo de execução:

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Pseudónimo personalizado

Defina um pseudónimo personalizado no seu ficheiro YAML e use-o como mostrado no exemplo seguinte. Um pseudónimo só pode conter caracteres alfanuméricos. A diretiva predefinida para expandir um pseudónimo é o carácter `$`.

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

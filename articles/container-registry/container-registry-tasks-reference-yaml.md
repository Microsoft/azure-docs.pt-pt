---
title: Referência YAML - Tarefas ACR
description: Referência para definir tarefas em YAML para tarefas ACR, incluindo propriedades de tarefas, tipos de passos, propriedades de passos e variáveis incorporadas.
ms.topic: article
ms.date: 07/08/2020
ms.openlocfilehash: 042310d29f5561c2cd77b0b9cccfc587ca4aa767
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88067588"
---
# <a name="acr-tasks-reference-yaml"></a>Referência de tarefas ACR: YAML

A definição de tarefas em várias etapas em ACR Tasks fornece uma primitiva computacional centrada em contentores focados na construção, teste e remendação de recipientes. Este artigo abrange os comandos, parâmetros, propriedades e sintaxe para os ficheiros YAML que definem as suas tarefas em várias etapas.

Este artigo contém referência para a criação de ficheiros YAML de tarefas de tarefas em várias etapas para tarefas ACR. Se quiser apresentar as tarefas do ACR, consulte a [visão geral](container-registry-tasks-overview.md)das tarefas da ACR .

## <a name="acr-taskyaml-file-format"></a>formato de ficheiro acr-task.yaml

A ACR Tasks suporta a declaração de tarefa em várias etapas na sintaxe YAML padrão. Define os passos de uma tarefa num ficheiro YAML. Em seguida, pode executar a tarefa manualmente, passando o ficheiro para o comando [de execução az acr.][az-acr-run] Ou, use o ficheiro para criar uma tarefa com [a az acr task create][az-acr-task-create] que é ativada automaticamente em um compromisso Git, uma atualização de imagem base ou um horário. Embora este artigo se refira a `acr-task.yaml` um ficheiro que contém os passos, a ACR Tasks suporta qualquer nome de ficheiro válido com uma [extensão suportada](#supported-task-filename-extensions).

Os primitivos de alto nível são propriedades de `acr-task.yaml` **tarefa,** **tipos de passos,** e propriedades **de passos:**

* [As propriedades de tarefa](#task-properties) aplicam-se a todas as etapas ao longo da execução da tarefa. Existem várias propriedades de tarefas globais, incluindo:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Os tipos de etapas](#task-step-types) de tarefa representam os tipos de ações que podem ser executadas numa tarefa. Existem três tipos de passos:
  * `build`
  * `push`
  * `cmd`
* [As propriedades do passo de tarefa](#task-step-properties) são parâmetros que se aplicam a um passo individual. Existem várias propriedades de etapas, incluindo:
  * `startDelay`
  * `timeout`
  * `when`
  * ... e muitos mais.

Segue-se o formato base de um `acr-task.yaml` ficheiro, incluindo algumas propriedades comuns do passo. Embora não seja uma representação exaustiva de todas as propriedades de passo disponíveis ou utilização do tipo passo, fornece uma visão geral rápida do formato de ficheiro básico.

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

### <a name="supported-task-filename-extensions"></a>Extensões de nome de ficheiro de tarefa suportadas

A ACR Tasks reservou várias extensões de nome de `.yaml` ficheiros, incluindo, que irá processar como um ficheiro de tarefa. Qualquer extensão *não* na lista seguinte é considerada pela ACR Tasks como um Dockerfile: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua

YAML é o único formato de ficheiro suportado atualmente por Tarefas ACR. As outras extensões de nome de ficheiros estão reservadas para um possível suporte futuro.

## <a name="run-the-sample-tasks"></a>Executar as tarefas da amostra

Existem vários ficheiros de tarefas de amostra referenciados nas seguintes secções deste artigo. As tarefas da amostra estão num repositório público do GitHub, [Azure-Samples/acr-tasks][acr-tasks]. Pode executá-los com o comando Azure CLI [az acr run][az-acr-run]. Os comandos da amostra são semelhantes a:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

A formatação dos comandos da amostra pressupõe que configuraste um registo predefinido no CLI Azure, por isso eles omitem o `--registry` parâmetro. Para configurar um registo predefinido, utilize o comando [de configuração az][az-configure] com o `--defaults` parâmetro, que aceita um `acr=REGISTRY_NAME` valor.

Por exemplo, para configurar o CLI Azure com um registo predefinido denominado "mioretria":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Propriedades de tarefa

As propriedades de tarefas normalmente aparecem no topo de um `acr-task.yaml` ficheiro, e são propriedades globais que se aplicam ao longo da execução completa dos passos de tarefa. Algumas destas propriedades globais podem ser ultrapassadas dentro de um passo individual.

| Propriedade | Tipo | Opcional | Description | Substituição suportada | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | Yes | A versão do `acr-task.yaml` ficheiro analisada pelo serviço ACR Tasks. Enquanto a ACR Tasks se esforça para manter a retrocompatibilidade, este valor permite que as Tarefas ACR mantenham a compatibilidade dentro de uma versão definida. Se não for especificado, predefine a versão mais recente. | No | Nenhum |
| `stepTimeout` | int (segundos) | Yes | O número máximo de segundos que um passo pode correr. Se a propriedade for especificada numa tarefa, define a propriedade padrão `timeout` de todos os degraus. Se a `timeout` propriedade for especificada num degrau, substitui a propriedade fornecida pela tarefa. | Yes | 600 (10 minutos) |
| `workingDirectory` | string | Yes | O diretório de trabalho do contentor durante o tempo de funcionamento. Se a propriedade for especificada numa tarefa, define a propriedade padrão `workingDirectory` de todos os degraus. Se especificado em um passo, substitui a propriedade fornecida pela tarefa. | Yes | `c:\workspace` em Janelas ou `/workspace` em Linux |
| `env` | [corda, corda, ...] | Yes |  Matriz de cordas em `key=value` formato que definem as variáveis ambientais para a tarefa. Se a propriedade for especificada numa tarefa, define a propriedade padrão `env` de todos os degraus. Se especificado num passo, substitui quaisquer variáveis ambientais herdadas da tarefa. | Yes | Nenhum |
| `secrets` | [segredo, segredo, ...] | Yes | Conjunto de objetos [secretos.](#secret) | No | Nenhum |
| `networks` | [rede, rede, ...] | Yes | Conjunto de objetos de [rede.](#network) | No | Nenhum |
| `volumes` | [volume, volume, ...] | Yes | Conjunto de objetos de [volume.](#volume) Especifica volumes com conteúdo de origem para montar a um passo. | No | Nenhum |

### <a name="secret"></a>segredo

O objeto secreto tem as seguintes propriedades.

| Propriedade | Tipo | Opcional | Description | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | cadeia (de carateres) | No | O identificador do segredo. | Nenhum |
| `keyvault` | string | Yes | A URL secreta do cofre da chave azul. | Nenhum |
| `clientID` | string | Yes | Identificação do cliente da [identidade gerida atribuída pelo utilizador](container-registry-tasks-authentication-managed-identity.md) para os recursos da Azure. | Nenhum |

### <a name="network"></a>network

O objeto de rede tem as seguintes propriedades.

| Propriedade | Tipo | Opcional | Description | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | cadeia (de carateres) | No | O nome da rede. | Nenhum |
| `driver` | string | Yes | O motorista para gerir a rede. | Nenhum |
| `ipv6` | bool | Yes | Se a rede IPv6 está ativada. | `false` |
| `skipCreation` | bool | Yes | Se saltar a criação de rede. | `false` |
| `isDefault` | bool | Yes | Se a rede é uma rede predefinida fornecida com registo de contentores Azure. | `false` |

### <a name="volume"></a>volume

O objeto de volume tem as seguintes propriedades.

| Propriedade | Tipo | Opcional | Description | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | cadeia (de carateres) | No | O nome do volume a montar. Pode conter apenas caracteres alfanuméricos, '-', e '_'. | Nenhum |
| `secret` | mapa[cadeia]cadeia | No | Cada chave do mapa é o nome de um ficheiro criado e povoado no volume. Cada valor é a versão de corda do segredo. Os valores secretos devem ser codificados na Base64. | Nenhum |

## <a name="task-step-types"></a>Tipos de etapas de tarefa

As tarefas ACR suportam três tipos de passos. Cada tipo de passo suporta várias propriedades, detalhadas na secção para cada tipo de passo.

| Tipo de passo | Description |
| --------- | ----------- |
| [`build`](#build) | Constrói uma imagem de recipiente utilizando `docker build` uma sintaxe familiar. |
| [`push`](#push) | Executa uma `docker push` de imagens recém-construídas ou retasadas para um registo de contentores. O Registo de Contentores Azure, outros registos privados e o público Docker Hub são apoiados. |
| [`cmd`](#cmd) | Executa um contentor como comando, com parâmetros passados para o do contentor `[ENTRYPOINT]` . O `cmd` tipo de passo suporta parâmetros `env` `detach` como, e outras opções de `docker run` comando familiares, permitindo a unidade e testes funcionais com a execução simultânea do contentor. |

## <a name="build"></a>compilar

Construa uma imagem de recipiente. O `build` tipo de passo representa um multi-inquilino, meios seguros de correr na nuvem como um primitivo de primeira `docker build` classe.

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
| `-t` &#124; `--image` | Define a plena qualificação `image:tag` da imagem construída.<br /><br />Como as imagens podem ser usadas para validações de tarefas internas, tais como testes funcionais, nem todas as imagens requerem `push` um registo. No entanto, para exemplo de uma imagem dentro de uma execução de Tarefa, a imagem precisa de um nome para referência.<br /><br />Ao contrário de `az acr build` , executar tarefas ACR não fornece um comportamento de pressão padrão. Com as Tarefas ACR, o cenário padrão assume a capacidade de construir, validar e, em seguida, empurrar uma imagem. Veja [o impulso](#push) para como empurrar opcionalmente imagens construídas. | Yes |
| `-f` &#124; `--file` | Especifica que o Dockerfile passou para `docker build` . Se não for especificado, assume-se o Ficheiro Docker na raiz do contexto. Para especificar um Dockerfile, passe o nome de ficheiro relativo à raiz do contexto. | Yes |
| `context` | O diretório de raiz passou para `docker build` . O diretório de raiz de cada tarefa está definido para uma [direção](#task-step-properties)de trabalho partilhada, e inclui a raiz do diretório clonado de Git associado. | No |

### <a name="properties-build"></a>Propriedades: construir

O `build` tipo de passo suporta as seguintes propriedades. Encontre detalhes destas propriedades na secção de propriedades do [passo de tarefa](#task-step-properties) deste artigo.

| Propriedades | Tipo | Necessário |
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
| `volumeMount` | objeto | Opcional |
| `when` | [corda, corda, ...] | Opcional |
| `workingDirectory` | string | Opcional |

### <a name="examples-build"></a>Exemplos: construir

#### <a name="build-image---context-in-root"></a>Construa imagem - contexto na raiz

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Construa imagem - contexto em subdireção

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>empurrar

Empurre uma ou mais imagens construídas ou retasadas para um registo de contentores. Suporta empurrar para registos privados como o Registo de Contentores Azure, ou para o público Docker Hub.

### <a name="syntax-push"></a>Sintaxe: empurrar

O `push` tipo de passo suporta uma coleção de imagens. A sintaxe de coleção YAML suporta formatos inline e aninhados. Empurrar uma única imagem é tipicamente representado usando sintaxe inline:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Para uma maior legibilidade, utilize sintaxe aninhada ao empurrar várias imagens:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Propriedades: empurrar

O `push` tipo de passo suporta as seguintes propriedades. Encontre detalhes destas propriedades na secção de propriedades do [passo de tarefa](#task-step-properties) deste artigo.

| Propriedade | Tipo | Necessário |
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

O `cmd` tipo de passo passa por um recipiente.

### <a name="syntax-cmd"></a>Sintaxe: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Propriedades: cmd

O `cmd` tipo de passo suporta as seguintes propriedades:

| Propriedade | Tipo | Necessário |
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
| `volumeMount` | objeto | Opcional |
| `when` | [corda, corda, ...] | Opcional |
| `workingDirectory` | string | Opcional |

Pode encontrar detalhes destas propriedades na secção de propriedades do [passo de tarefa](#task-step-properties) deste artigo.

### <a name="examples-cmd"></a>Exemplos: cmd

#### <a name="run-hello-world-image"></a>Executar imagem de olá mundo

Este comando executa o `hello-world.yaml` ficheiro de tarefa, que faz referência à imagem [do mundo-olá](https://hub.docker.com/_/hello-world/) no Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Executar imagem de bash e eco "olá mundo"

Este comando executa o `bash-echo.yaml` ficheiro de tarefa, que faz referência à imagem [de bash](https://hub.docker.com/_/bash/) no Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Executar etiqueta de imagem de bash específica

Para executar uma versão de imagem específica, especifique a etiqueta no `cmd` .

Este comando executa o `bash-echo-3.yaml` ficheiro de tarefa, que faz referência à [imagem bash:3.0](https://hub.docker.com/_/bash/) no Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Executar imagens personalizadas

O `cmd` tipo de passo refere as imagens utilizando o formato padrão. `docker run` Presume-se que as imagens não prefaciadas com um registo são originárias de docker.io. O exemplo anterior poderia igualmente ser representado como:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Ao utilizar a convenção de referência de `docker run` imagem padrão, pode executar `cmd` imagens de qualquer registo privado ou do estivador público. Se estiver a referir imagens no mesmo registo em que a ACR Task está a executar, não precisa de especificar quaisquer credenciais de registo.

* Executar uma imagem que é de um registo de contentores Azure. O exemplo a seguir pressupõe que tem um registo `myregistry` nomeado, e uma imagem `myimage:mytag` personalizada.

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Generalize a referência de registo com uma variável ou pseudónimo de Run

    Em vez de codificar duramente o seu nome de registo num `acr-task.yaml` ficheiro, pode torná-lo mais portátil utilizando uma [variável](#run-variables) run ou [pseudónimo](#aliases). A `Run.Registry` variável ou `$Registry` pseudónimo expande-se em tempo de execução para o nome do registo em que a tarefa está a ser executada.

    Por exemplo, para generalizar a tarefa anterior de modo a funcionar em qualquer registo de contentores Azure, consulte a variável $Registry no nome da imagem:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

#### <a name="access-secret-volumes"></a>Aceder a volumes secretos

A `volumes` propriedade permite que volumes e seus conteúdos secretos sejam especificados e `build` `cmd` passos em uma tarefa. Dentro de cada etapa, uma propriedade opcional `volumeMounts` lista os volumes e os caminhos correspondentes do contentor para montar no recipiente nesse degrau. Os segredos são fornecidos como ficheiros no caminho de montagem de cada volume.

Execute uma tarefa e monte dois segredos a um passo: um armazenado num cofre chave e outro especificado na linha de comando:

```azurecli
az acr run -f mounts-secrets.yaml --set-secret mysecret=abcdefg123456 https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/mounts-secrets.yaml -->
[!code-yml[task](~/acr-tasks/mounts-secrets.yaml)]

## <a name="task-step-properties"></a>Propriedades do passo de tarefa

Cada tipo de passo suporta várias propriedades adequadas para o seu tipo. A tabela a seguir define todas as propriedades de passo disponíveis. Nem todos os tipos de passo suportam todas as propriedades. Para ver quais destas propriedades estão disponíveis para cada tipo de passo, consulte o [cmd,](#cmd) [construa](#build)e [empurre](#push) as secções de referência do tipo passo.

| Propriedade | Tipo | Opcional | Description | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Yes | Se o recipiente deve ser desligado durante o funcionamento. | `false` |
| `disableWorkingDirectoryOverride` | bool | Yes | Se desativar a `workingDirectory` funcionalidade de substituição. Utilize isto em combinação com `workingDirectory` o controlo total sobre o diretório de trabalho do contentor. | `false` |
| `entryPoint` | string | Yes | Sobrepõe-se ao `[ENTRYPOINT]` contentor de um degrau. | Nenhum |
| `env` | [corda, corda, ...] | Yes | Matriz de cordas em `key=value` formato que definem as variáveis ambientais para o passo. | Nenhum |
| `expose` | [corda, corda, ...] | Yes | Matriz de portas expostas do recipiente. |  Nenhum |
| [`id`](#example-id) | string | Yes | Identifica exclusivamente o passo dentro da tarefa. Outros passos na tarefa podem fazer referência a um `id` passo, como por exemplo, para verificação de dependência com `when` .<br /><br />É `id` também o nome do recipiente de funcionamento. Os processos em execução em outros contentores na tarefa podem referir-se ao `id` nome de anfitrião do DNS, ou para aceder a ele com registos de estivadores [id], por exemplo. | `acb_step_%d`, onde `%d` está o índice baseado em 0 do degrau de cima para baixo no ficheiro YAML |
| `ignoreErrors` | bool | Yes | Se marcar o passo como bem sucedido, independentemente de ter ocorrido um erro durante a execução do contentor. | `false` |
| `isolation` | string | Yes | O nível de isolamento do contentor. | `default` |
| `keep` | bool | Yes | Se o contentor do degrau deve ser mantido após a execução. | `false` |
| `network` | objeto | Yes | Identifica uma rede em que o contentor funciona. | Nenhum |
| `ports` | [corda, corda, ...] | Yes | Conjunto de portas que são publicadas do recipiente para o hospedeiro. |  Nenhum |
| `pull` | bool | Yes | Se forçar um puxão do recipiente antes de executá-lo para evitar qualquer comportamento de caching. | `false` |
| `privileged` | bool | Yes | Se deve executar o recipiente em modo privilegiado. | `false` |
| `repeat` | int | Yes | O número de retretes para repetir a execução de um contentor. | 0 |
| `retries` | int | Yes | O número de retrações para tentar se um contentor falhar a sua execução. Uma nova tentativa só é tentada se o código de saída de um contentor não for zero. | 0 |
| `retryDelay` | int (segundos) | Yes | O atraso em segundos entre as retretes da execução de um contentor. | 0 |
| `secret` | objeto | Yes | Identifica um cofre de chaves Azure ou [identidade gerida para os recursos da Azure.](container-registry-tasks-authentication-managed-identity.md) | Nenhum |
| `startDelay` | int (segundos) | Yes | Número de segundos para atrasar a execução de um contentor. | 0 |
| `timeout` | int (segundos) | Yes | O número máximo de segundos que um passo pode executar antes de ser terminado. | 600 |
| [`when`](#example-when) | [corda, corda, ...] | Yes | Configura a dependência de um passo em um ou mais passos dentro da tarefa. | Nenhum |
| `user` | string | Yes | O nome de utilizador ou UID de um recipiente | Nenhum |
| `workingDirectory` | string | Yes | Define o diretório de trabalho para um passo. Por predefinição, a ACR Tasks cria um diretório de raiz como diretório de trabalho. No entanto, se a sua construção tiver vários passos, os passos anteriores podem partilhar artefactos com passos posteriores especificando o mesmo diretório de trabalho. | `c:\workspace` em Janelas ou `/workspace` em Linux |

### <a name="volumemount"></a>volumeMont

O objeto volumeMount tem as seguintes propriedades.

| Propriedade | Tipo | Opcional | Description | Valor predefinido |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | cadeia (de carateres) | No | O nome do volume a montar. Deve corresponder exatamente o nome de uma `volumes` propriedade. | Nenhum |
| `mountPath`   | string | não | O caminho absoluto para montar ficheiros no recipiente.  | Nenhum |

### <a name="examples-task-step-properties"></a>Exemplos: Propriedades do passo de tarefa

#### <a name="example-id"></a>Exemplo: id

Construa duas imagens, inserindo uma imagem de teste funcional. Cada passo é identificado por um único `id` que outros passos na referência de tarefa na sua `when` propriedade.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Exemplo: quando

A `when` propriedade especifica a dependência de um passo em outros passos dentro da tarefa. Suporta dois valores de parâmetro:

* `when: ["-"]` - Indica que não há dependência de outros passos. Um passo especificando começará a `when: ["-"]` execução imediatamente, e permite a execução de passo simultâneo.
* `when: ["id1", "id2"]` - Indica que o passo depende de passos com `id` "id1" e `id` "id2". Este passo não será executado até que os passos "id1" e "id2" estejam completos.

Se `when` não for especificado num passo, esse passo depende da conclusão do passo anterior no `acr-task.yaml` ficheiro.

Execução de etapa sequencial `when` sem:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Execução de etapa sequencial `when` com:

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

Construção paralela de imagem e testes dependentes:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Executar variáveis

As Tarefas ACR incluem um conjunto predefinido de variáveis que estão disponíveis para etapas de tarefa quando executam. Estas variáveis podem ser acedidas utilizando o `{{.Run.VariableName}}` formato, onde `VariableName` é uma das seguintes:

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

Os nomes variáveis são geralmente autoexplicativos. Os detalhes seguem para variáveis comumente usadas. A partir da versão `v1.1.0` YAML, pode utilizar um pseudónimo de [tarefa](#aliases) abreviado e predefinido no lugar da maioria das variáveis executadas. Por exemplo, no lugar `{{.Run.Registry}}` de, use o `$Registry` pseudónimo.

### <a name="runid"></a>Run.ID

Cada Run, através `az acr run` de , ou execução baseada no gatilho de tarefas criadas através `az acr task create` , tem um ID único. O ID representa a Execução atualmente executada.

Tipicamente usado para uma imagem de marcação única:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runsharedvolume"></a>Run.SharedVolume

O identificador único para um volume partilhado que é acessível por todos os passos de tarefa. O volume é montado `c:\workspace` no Windows ou no `/workspace` Linux. 

### <a name="runregistry"></a>Run.Registry

O nome do servidor totalmente qualificado do registo. Normalmente usado para referenciar genericamente o registo onde a tarefa está a ser executada.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Run.RegistryName

O nome do registo do contentor. Normalmente usado em etapas de tarefa que não requerem um nome de servidor totalmente qualificado, por exemplo, `cmd` passos que executam comandos Azure CLI em registos.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Executar.Data

O tempo atual da UTC começou.

### <a name="runcommit"></a>Executar.Cometer

Para uma tarefa desencadeada por um compromisso com um repositório gitHub, o identificador de compromisso.

### <a name="runbranch"></a>Run.Branch

Para uma tarefa desencadeada por um compromisso com um repositório GitHub, o nome da filial.

## <a name="aliases"></a>Aliases

A partir de `v1.1.0` , ACR Tasks suporta pseudónimos disponíveis para tarefas quando executam. Os pseudónimos são semelhantes em conceito a pseudónimos (atalhos de comando) suportados em bash e algumas outras conchas de comando. 

Com um pseudónimo, pode lançar qualquer comando ou grupo de comandos (incluindo opções e nomes de ficheiros) introduzindo uma única palavra.

A ACR Tasks suporta vários pseudónimos predefinidos e também pseudónimos personalizados que cria.

### <a name="predefined-aliases"></a>Pseudónimos predefinidos

Os seguintes pseudónimos de tarefa estão disponíveis para utilizar em vez de [variáveis de execução:](#run-variables)

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

Nas etapas de tarefa, preceder um pseudónimo com a `$` diretiva, como neste exemplo:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Pseudónimos de imagem

Cada um dos pseudónimos seguintes aponta para uma imagem estável no Registo de Contentores da Microsoft (MCR). Pode consultar cada um deles na `cmd` secção de um ficheiro task sem utilizar uma diretiva.

| Alias | Imagem |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

A seguinte tarefa de exemplo utiliza vários pseudónimos para [purgar](container-registry-auto-purge.md) etiquetas de imagem com mais de 7 dias no repo `samples/hello-world` no registo de execução:

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Pseudónimo personalizado

Defina um pseudónimo personalizado no seu ficheiro YAML e use-o como mostrado no exemplo seguinte. Um pseudónimo pode conter apenas caracteres alfanuméricos. A diretiva por defeito para expandir um pseudónimo é o `$` personagem.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

Pode ligar-se a um ficheiro YAML remoto ou local para definições de pseudónimos personalizados. O exemplo a seguir liga-se a um ficheiro YAML no armazenamento de bolhas Azure:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Passos seguintes

Para obter uma visão geral das tarefas em várias etapas, consulte as [tarefas de construção, teste e correção em tarefas ACR](container-registry-tasks-multi-step.md).

Para construir um único passo, consulte a visão geral das [tarefas ACR](container-registry-tasks-overview.md).



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure

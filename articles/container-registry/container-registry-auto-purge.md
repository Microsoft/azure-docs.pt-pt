---
title: Remover automaticamente os recursos de imagem no registro de contêiner do Azure
description: Use um comando de limpeza para excluir várias marcas e manifestos de um registro de contêiner do Azure com base na idade e em um filtro de marca e, opcionalmente, agende operações de limpeza.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 08/14/2019
ms.author: danlep
ms.openlocfilehash: 6841bf18f57f514455f7680126c3cc58e7ebd7b1
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74148928"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Limpar automaticamente as imagens de um registro de contêiner do Azure

Quando você usa um registro de contêiner do Azure como parte de um fluxo de trabalho de desenvolvimento, o registro pode ser rapidamente preenchido com imagens ou outros artefatos que não são necessários após um curto período. Talvez você queira excluir todas as marcas que são mais antigas que uma determinada duração ou corresponder a um filtro de nome especificado. Para excluir vários artefatos rapidamente, este artigo apresenta o comando `acr purge` que você pode executar como uma tarefa de ACR sob demanda ou [agendada](container-registry-tasks-scheduled.md) . 

O comando `acr purge` está atualmente distribuído em uma imagem de contêiner pública (`mcr.microsoft.com/acr/acr-cli:0.1`), criada a partir do código-fonte no repositório [ACR-CLI](https://github.com/Azure/acr-cli) no github.

Você pode usar o Azure Cloud Shell ou uma instalação local do CLI do Azure para executar os exemplos de tarefa ACR neste artigo. Se você quiser usá-lo localmente, a versão 2.0.69 ou posterior será necessária. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install]. 

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

> [!WARNING]
> Use o comando `acr purge` com cuidado-os dados da imagem excluída são irrecuperáveis. Se você tiver sistemas que extraem imagens por Resumo do manifesto (em oposição ao nome da imagem), você não deve limpar imagens não marcadas. A exclusão de imagens não marcadas impedirá que esses sistemas extraiam as imagens do registro. Em vez de efetuar pull por manifesto, considere a adoção de um esquema de *marcação exclusivo* , uma [prática](container-registry-image-tag-version.md)recomendada.

Se você quiser excluir as marcas de imagem única ou os manifestos usando comandos CLI do Azure, consulte [Excluir imagens de contêiner no registro de contêiner do Azure](container-registry-delete.md).

## <a name="use-the-purge-command"></a>Usar o comando de limpeza

O comando de contêiner `acr purge` exclui imagens por marca em um repositório que corresponde a um filtro de nome e que são mais antigos do que uma duração especificada. Por padrão, somente referências de marca são excluídas, não os [manifestos](container-registry-concepts.md#manifest) subjacentes e os dados da camada. O comando tem uma opção para também excluir manifestos. 

> [!NOTE]
> `acr purge` não exclui uma marca de imagem ou repositório em que o atributo `write-enabled` está definido como `false`. Para obter informações, consulte [bloquear uma imagem de contêiner em um registro de contêiner do Azure](container-registry-image-lock.md).

`acr purge` é projetado para ser executado como um comando de contêiner em uma [tarefa ACR](container-registry-tasks-overview.md), para que ele seja autenticado automaticamente com o registro em que a tarefa é executada. 

No mínimo, especifique o seguinte ao executar `acr purge`:

* `--registry`-o registro de contêiner do Azure em que você executa o comando. 
* `--filter`-um repositório e uma *expressão regular* para filtrar as marcas no repositório. Exemplos: `--filter "hello-world:.*"` corresponde a todas as marcas no repositório de `hello-world` e `--filter "hello-world:^1.*"` corresponde a marcas que começam com `1`. Passe vários parâmetros `--filter` para limpar vários repositórios.
* `--ago`-uma [cadeia de caracteres de duração](https://golang.org/pkg/time/) em estilo de frente para indicar uma duração além da qual as imagens são excluídas. A duração consiste em uma sequência de um ou mais números decimais, cada um com um sufixo de unidade. As unidades de tempo válidas incluem "d" para dias, "h" para horas e "m" para minutos. Por exemplo, `--ago 2d3h6m` seleciona todas as imagens filtradas última modificação mais de 2 dias, 3 horas e 6 minutos atrás e `--ago 1.5h` seleciona as imagens pela última modificação há mais de 1,5 horas.

`acr purge` dá suporte a vários parâmetros opcionais. Os dois seguintes são usados em exemplos neste artigo:

* `--untagged`-especifica que os manifestos que não têm marcas associadas (*manifestos não marcados*) são excluídos.
* `--dry-run`-especifica que nenhum dado é excluído, mas a saída é a mesma que se o comando for executado sem esse sinalizador. Esse parâmetro é útil para testar um comando de limpeza para garantir que ele não exclua inadvertidamente os dados que você pretende preservar.

Para parâmetros adicionais, execute `acr purge --help`. 

o `acr purge` dá suporte a outros recursos de comandos de tarefas ACR, incluindo a [execução de variáveis](container-registry-tasks-reference-yaml.md#run-variables) e logs de [execução de tarefas](container-registry-tasks-overview.md#view-task-logs) que são transmitidos e também salvos para recuperação posterior.

### <a name="run-in-an-on-demand-task"></a>Executar em uma tarefa sob demanda

O exemplo a seguir usa o comando [AZ ACR Run][az-acr-run] para executar o comando `acr purge` sob demanda. Este exemplo exclui todas as marcas de imagem e os manifestos no repositório `hello-world` no *myregistry* que foram modificados há mais de 1 dia. O comando de contêiner é passado usando uma variável de ambiente. A tarefa é executada sem um contexto de origem.

Neste e nos exemplos a seguir, o registro em que o comando `acr purge` é executado é especificado usando o alias `$Registry`, que indica o registro que executa a tarefa.

```azurecli
# Environment variable for container command line
PURGE_CMD="mcr.microsoft.com/acr/acr-cli:0.1 purge \
  --registry {{.Run.Registry}} --filter 'hello-world:.*' --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Executar em uma tarefa agendada

O exemplo a seguir usa o comando [AZ ACR Task Create][az-acr-task-create] para criar uma [tarefa ACR agendada](container-registry-tasks-scheduled.md)diariamente. A tarefa limpa as marcas modificadas há mais de 7 dias no repositório `hello-world`. O comando de contêiner é passado usando uma variável de ambiente. A tarefa é executada sem um contexto de origem.

```azurecli
# Environment variable for container command line
PURGE_CMD="mcr.microsoft.com/acr/acr-cli:0.1 purge \
  --registry {{.Run.Registry}} --filter 'hello-world:.*' --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

Execute o comando [AZ ACR Task show][az-acr-task-show] para ver que o gatilho do temporizador está configurado.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Limpar grandes números de marcas e manifestos

A limpeza de um grande número de marcas e manifestos pode levar vários minutos ou mais. Para limpar milhares de marcas e manifestos, o comando pode precisar ser executado por mais tempo do que a hora de tempo limite padrão de 600 segundos para uma tarefa sob demanda ou 3600 segundos para uma tarefa agendada. Se o tempo limite for excedido, apenas um subconjunto de marcas e manifestos será excluído. Para garantir que uma limpeza em larga escala seja concluída, passe o parâmetro `--timeout` para aumentar o valor. 

Por exemplo, a seguinte tarefa sob demanda define um tempo limite de 3600 segundos (1 hora):

```azurecli
# Environment variable for container command line
PURGE_CMD="mcr.microsoft.com/acr/acr-cli:0.1 purge \
  --registry {{.Run.Registry}} --filter 'hello-world:.*' --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Exemplo: limpeza agendada de vários repositórios em um registro

Este exemplo percorre o uso de `acr purge` para limpar periodicamente vários repositórios em um registro. Por exemplo, você pode ter um pipeline de desenvolvimento que envia imagens por push para os repositórios `samples/devimage1` e `samples/devimage2`. Você importa periodicamente as imagens de desenvolvimento para um repositório de produção para suas implantações, de modo que você não precisa mais das imagens de desenvolvimento. Em uma base semanal, você limpa os repositórios `samples/devimage1` e `samples/devimage2`, em preparação para o trabalho da próxima semana.

### <a name="preview-the-purge"></a>Visualizar a limpeza

Antes de excluir dados, é recomendável executar uma tarefa de limpeza sob demanda usando o parâmetro `--dry-run`. Essa opção permite que você veja as marcas e os manifestos que o comando limpará, sem remover nenhum dado. 

No exemplo a seguir, o filtro em cada repositório seleciona todas as marcas. O parâmetro `--ago 0d` corresponde a imagens de todas as idades nos repositórios que correspondem aos filtros. Modifique os critérios de seleção conforme necessário para seu cenário. O parâmetro `--untagged` indica que os manifestos são excluídos, além das marcas. O comando de contêiner é passado para o comando [AZ ACR Run][az-acr-run] usando uma variável de ambiente.

```azurecli
# Environment variable for container command line
PURGE_CMD="mcr.microsoft.com/acr/acr-cli:0.1 purge \
  --registry {{.Run.Registry}} \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd  "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

Examine a saída do comando para ver as marcas e os manifestos que correspondem aos parâmetros de seleção. Como o comando é executado com `--dry-run`, nenhum dado é excluído.

Resultado do exemplo:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>Agendar a limpeza

Depois de verificar a execução seca, crie uma tarefa agendada para automatizar a limpeza. O exemplo a seguir agenda uma tarefa semanal em domingo às 1:00 UTC para executar o comando de limpeza anterior:

```azurecli
# Environment variable for container command line
PURGE_CMD="mcr.microsoft.com/acr/acr-cli:0.1 purge \
  --registry {{.Run.Registry}} \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

Execute o comando [AZ ACR Task show][az-acr-task-show] para ver que o gatilho do temporizador está configurado.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre outras opções para [excluir dados de imagem](container-registry-delete.md) no registro de contêiner do Azure.

Para obter mais informações sobre o armazenamento de imagens, consulte [contêiner de armazenamento de imagens no registro de contêiner do Azure](container-registry-storage.md).

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show


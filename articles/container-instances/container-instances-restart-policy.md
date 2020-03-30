---
title: Reiniciar a política para tarefas executadas
description: Aprenda a utilizar as Instâncias de Contentores Azure para executar tarefas que vão até à conclusão, tais como trabalhos de construção, teste ou renderização de imagem.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 8ef4ef228038242f53abc8041470f7f596ab1157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131500"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Executar tarefas contentorizadas com políticas de reinício

A facilidade e velocidade de implementação de contentores no Azure Container Instances oferece uma plataforma atraente para realizar tarefas de execução única, como a compilação, teste e composição de imagem numa instância de contentor.

Com uma política de reinício configurável, pode especificar que os contentores deverão ser parados quando os processos são concluídos. Uma vez que as instâncias de contentor são faturadas ao segundo, são cobrados apenas os recursos de computação utilizados enquanto o contentor executa a tarefa.

Os exemplos apresentados neste artigo utilizam o Azure CLI. Deve ter a versão Azure CLI 2.0.21 ou mais [instalada localmente,][azure-cli-install]ou utilizar o CLI na [Casca de Nuvem Azure](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Política de reinício de contentores

Quando criar um grupo de [contentores](container-instances-container-groups.md) em Casos de Contentores Azure, pode especificar uma das três definições de política de reinício.

| Política de reinício   | Descrição |
| ---------------- | :---------- |
| `Always` | Os contentores no grupo de contentores são sempre reiniciados. Esta é a **predefinição** aplicada quando nenhuma política de reinício é especificada durante a criação do contentor. |
| `Never` | Os contentores no grupo de contentores nunca são reiniciados. Os contentores são executados, no máximo, uma vez. |
| `OnFailure` | Os contentores no grupo de contentores são reiniciados apenas quando o processo executado no contentor falha (quando termina com um código de saída diferente de zero). Os contentores são executados, pelo menos, uma vez. |

## <a name="specify-a-restart-policy"></a>Especificar uma política de reinício

A forma como especifica uma política de reinício depende da forma como cria os seus casos de contentores, como é o caso dos cmdlets Azure CLI, Azure PowerShell ou no portal Azure. No Azure CLI, `--restart-policy` especifique o parâmetro quando chamar a criação do [recipiente Az][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Correr para o exemplo de conclusão

Para ver a política de reinício em ação, crie uma instância `OnFailure` de contentores a partir da imagem [aci-wordcount][aci-wordcount-image] da Microsoft e especifique a política de reinício. Este recipiente de exemplo executa um script Python que, por padrão, analisa o texto de Shakespeare's [Hamlet,](http://shakespeare.mit.edu/hamlet/full.html)escreve as 10 palavras mais comuns para O DSTOUT, e depois sai.

Executar o recipiente de exemplo com o seguinte [recipiente az criar][az-container-create] comando:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

O Azure Container Instances inicia o contentor e, em seguida, para-o quando a sua aplicação (ou script, neste caso) é fechada. Quando as instâncias de contentores azure `Never` `OnFailure`param um contentor cuja política de reinício é ou , o estado do contentor está definido para **terminar**. Pode verificar o estado de um contentor com o comando do [contentor az:][az-container-show]

```azurecli-interactive
az container show \
    --resource-group myResourceGroup \
    --name mycontainer \
    --query containers[0].instanceView.currentState.state
```

Exemplo de saída:

```bash
"Terminated"
```

Uma vez que o estado do recipiente de exemplo mostre *terminado,* pode ver a sua saída de tarefa visualizando os registos do recipiente. Executar o comando de registos de [recipientes az][az-container-logs] para ver a saída do script:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Saída:

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

Este exemplo mostra a saída que o script enviou para O DSTOUT. No entanto, as suas tarefas contentorizadas podem, em vez disso, escrever a sua saída para armazenamento persistente para posterior recuperação. Por exemplo, para uma partilha de [ficheiros Azure](container-instances-mounting-azure-files-volume.md).

## <a name="next-steps"></a>Passos seguintes

Cenários baseados em tarefas, como o processamento de lotes um grande conjunto de dados com vários contentores, podem tirar partido de [variáveis ambientais personalizadas](container-instances-environment-variables.md) ou linhas de [comando](container-instances-start-command.md) no tempo de execução.

Para obter mais detalhes sobre como persistir a saída dos seus contentores que se esgotam até à conclusão, consulte a montagem de uma partilha de [ficheiros Azure com](container-instances-mounting-azure-files-volume.md)as Instâncias de Contentores Azure .

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli

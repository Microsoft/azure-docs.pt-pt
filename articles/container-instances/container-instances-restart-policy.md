---
title: As políticas com tarefas em contentores de reinício de utilização no Azure Container Instances
description: Saiba como utilizar o Azure Container Instances para executar tarefas que são executadas até a conclusão, tal como na compilação, teste ou trabalhos de composição de imagem.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 06872eefd0d500a22214109ad5055dd236b5a6ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60608114"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Executar tarefas em contentores com políticas de reinício

A facilidade e velocidade de implementação de contentores no Azure Container Instances fornece uma plataforma atraente para executar tarefas de execução única, como a compilação, teste e composição de imagem numa instância de contentor.

Com uma política de reinício configuráveis, pode especificar que os contentores são parados quando seus processos forem concluídas. Uma vez que as instâncias de contentor são faturadas ao segundo, é-lhe cobrado apenas pelos recursos de computação utilizados enquanto o contentor a executar a tarefa está em execução.

Os exemplos apresentados neste artigo uso a CLI do Azure. Tem de ter a CLI do Azure da versão 2.0.21 ou superior [instalado localmente][azure-cli-install], ou utilizar a CLI no [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Política de reinício do contentor

Quando cria um [grupo de contentores](container-instances-container-groups.md) no Azure Container Instances, pode especificar uma das três definições de política de reinício.

| Política de reinício   | Descrição |
| ---------------- | :---------- |
| `Always` | Contentores no grupo de contentores são sempre reiniciados. Este é o **predefinição** definição aplicada quando nenhuma política de reinício é especificada durante a criação do contentor. |
| `Never` | Contentores no grupo de contentores nunca são reiniciadas. Os contentores executam no máximo uma vez. |
| `OnFailure` | Contentores no grupo de contentores são reiniciadas apenas quando o processo executado no contentor de falha (quando termina com um código de saída diferente de zero). Os contentores são executados, pelo menos, uma vez. |

## <a name="specify-a-restart-policy"></a>Especifique uma política de reinício

Como especificar uma política de reinício depende de como criar as instâncias de contentor, tal como com a CLI do Azure, cmdlets do PowerShell do Azure, ou no portal do Azure. Na CLI do Azure, especifique a `--restart-policy` parâmetro, quando chama [criar contentor de az][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Executar o exemplo de conclusão

Para ver a política de reinício em ação, crie uma instância de contentor da Microsoft [aci-wordcount] [ aci-wordcount-image] de imagem e especificar o `OnFailure` política de reinício. Este contentor de exemplo executa um script de Python que, por predefinição, analisa o texto do de Shakespeare [Mítico](http://shakespeare.mit.edu/hamlet/full.html), escreve as 10 palavras mais comuns para STDOUT e, em seguida, sai.

Execute o contentor de exemplo com o seguinte procedimento [criar contentor de az] [ az-container-create] comando:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

O Azure Container Instances inicia o contentor e, em seguida, pára-lo quando seu aplicativo (ou script, neste caso) é fechado. Quando o Azure Container Instances para um contentor de é cuja política de reinício `Never` ou `OnFailure`, estado do contentor é definido como **Terminated**. Pode verificar o estado de um contentor com o [show de contentor az] [ az-container-show] comando:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Exemplo de saída:

```bash
"Terminated"
```

Assim que o estado do contentor de exemplo mostra *Terminated*, pode ver o resultado da tarefa, visualizando os registos de contentor. Executar o [registos de contentor az] [ az-container-logs] o resultado de comando para ver o script:

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

Este exemplo mostra a saída que o script enviado para STDOUT. As tarefas em contentores, no entanto, poderão em vez disso, escrever o resultado no armazenamento persistente para recuperação posterior. Por exemplo, para um [partilha de ficheiros do Azure](container-instances-mounting-azure-files-volume.md).

## <a name="next-steps"></a>Passos Seguintes

Cenários e baseado em tarefas, como um grande conjunto de dados com vários contentores, de processamento em lotes podem tirar partido de personalizado [variáveis de ambiente](container-instances-environment-variables.md) ou [linhas de comando](container-instances-start-command.md) em tempo de execução.

Para obter detalhes sobre como manter a saída de seus contentores que são executados até a conclusão, consulte [montar uma partilha de ficheiros do Azure com o Azure Container Instances](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli

---
title: Reiniciar a política para tarefas de execução
description: Saiba como utilizar as Instâncias do Contentor Azure para executar tarefas que vão até à sua conclusão, tais como trabalhos de construção, teste ou de renderização de imagens.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 3bce208e3663ecfcebe520be92de3ac4443c0c8f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771160"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Executar tarefas em contentores com políticas de reinício

A facilidade e velocidade de implementação de contentores no Azure Container Instances oferece uma plataforma atraente para realizar tarefas de execução única, como a compilação, teste e composição de imagem numa instância de contentor.

Com uma política de reinício configurável, pode especificar que os contentores deverão ser parados quando os processos são concluídos. Uma vez que as instâncias de contentor são faturadas ao segundo, são cobrados apenas os recursos de computação utilizados enquanto o contentor executa a tarefa.

Os exemplos apresentados neste artigo utilizam o Azure CLI. Tem de ter a versão 2.0.21 do Azure CLI ou maior [instalada localmente,][azure-cli-install]ou utilizar o CLI na [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Política de reinício do contentor

Quando criar um [grupo de contentores](container-instances-container-groups.md) em Instâncias de Contentores Azure, pode especificar uma das três definições de política de reinício.

| Política de reinício   | Descrição |
| ---------------- | :---------- |
| `Always` | Os contentores no grupo de contentores são sempre reiniciados. Esta é a **predefinição** aplicada quando não é especificada nenhuma política de reinício durante a criação do contentor. |
| `Never` | Os contentores no grupo de contentores nunca são reiniciados. Os contentores são executados, no máximo, uma vez. |
| `OnFailure` | Os contentores no grupo de contentores são reiniciados apenas quando o processo executado no contentor falha (quando termina com um código de saída diferente de zero). Os contentores são executados, pelo menos, uma vez. |

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="specify-a-restart-policy"></a>Especificar uma política de reinício

A forma como especifica uma política de reinício depende da forma como cria as suas instâncias de contentores, tais como com os cmdlets Azure CLI, Azure PowerShell ou no portal Azure. No CLI Azure, especifique o `--restart-policy` parâmetro quando chamar a [az de configuração][az-container-create]do recipiente .

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Corra para o exemplo de conclusão

Para ver a política de reinício em ação, crie uma instância de contentor a partir da imagem de [contagem de palavras][aci-wordcount-image] da Microsoft e especifique a `OnFailure` política de reinício. Este recipiente de exemplo executa um script Python que, por padrão, analisa o texto de Shakespeare's [Hamlet,](http://shakespeare.mit.edu/hamlet/full.html)escreve as 10 palavras mais comuns para STDOUT, e depois sai.

Executar o recipiente de exemplo com o seguinte [recipiente az criar][az-container-create] comando:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

O Azure Container Instances inicia o contentor e, em seguida, para-o quando a sua aplicação (ou script, neste caso) é fechada. Quando o Azure Container Instances para um contentor cuja política de reinício é `Never` `OnFailure` ou, o estado do contentor está definido para **Terminado**. Pode verificar o estado de um contentor com o comando de exposição de [contentores az:][az-container-show]

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

Uma vez que o estado do recipiente de exemplo mostra *terminado,* pode ver a sua saída de tarefa visualizando os registos do contentor. Executar o comando [de registos de contentores az][az-container-logs] para visualizar a saída do script:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Resultado:

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

Este exemplo mostra a saída que o script enviou para STDOUT. No entanto, as suas tarefas contentorizadas podem, em vez disso, escrever a sua saída para armazenamento persistente para posterior recuperação. Por exemplo, para uma [partilha de ficheiros Azure](./container-instances-volume-azure-files.md).

## <a name="next-steps"></a>Passos seguintes

Cenários baseados em tarefas, como o processamento de um grande conjunto de dados com vários contentores, podem tirar partido de [variáveis ambientais personalizadas](container-instances-environment-variables.md) ou [linhas](container-instances-start-command.md) de comando em tempo de execução.

Para obter mais informações sobre como persistir a saída dos seus recipientes que vão até à sua conclusão, consulte [a montagem de uma partilha de ficheiros Azure com as Instâncias do Contentor Azure](./container-instances-volume-azure-files.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli

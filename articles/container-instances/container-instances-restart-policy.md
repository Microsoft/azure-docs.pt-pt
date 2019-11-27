---
title: Reinicie a política para tarefas de execução única
description: Saiba como usar as instâncias de contêiner do Azure para executar tarefas que são executadas até a conclusão, como nos trabalhos de processamento de compilação, teste ou imagem.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: f814b1c99827c07f8dadfb0cfd80c87a93377cdc
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533462"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Executar tarefas em contêineres com políticas de reinicialização

A facilidade e a velocidade de implantação de contêineres nas instâncias de contêiner do Azure fornecem uma plataforma atraente para executar tarefas de execução única, como compilação, teste e renderização de imagem em uma instância de contêiner.

Com uma política de reinicialização configurável, você pode especificar que seus contêineres sejam interrompidos quando seus processos forem concluídos. Como as instâncias de contêiner são cobradas por segundo, você será cobrado apenas pelos recursos de computação usados enquanto o contêiner que executa a tarefa estiver em execução.

Os exemplos apresentados neste artigo usam o CLI do Azure. Você deve ter CLI do Azure versão 2.0.21 ou superior [instalada localmente][azure-cli-install]ou usar a CLI no [Azure cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Política de reinicialização de contêiner

Ao criar um [grupo de contêineres](container-instances-container-groups.md) em instâncias de contêiner do Azure, você pode especificar uma das três configurações de política de reinicialização.

| Política de reinício   | Descrição |
| ---------------- | :---------- |
| `Always` | Os contêineres no grupo de contêineres são sempre reiniciados. Essa é a configuração **padrão** aplicada quando nenhuma política de reinicialização é especificada na criação do contêiner. |
| `Never` | Os contêineres no grupo de contêineres nunca são reiniciados. Os contêineres são executados no máximo uma vez. |
| `OnFailure` | Os contêineres no grupo de contêineres são reiniciados somente quando o processo executado no contêiner falha (quando ele termina com um código de saída diferente de zero). Os contêineres são executados pelo menos uma vez. |

## <a name="specify-a-restart-policy"></a>Especificar uma política de reinicialização

A maneira de especificar uma política de reinicialização depende de como você cria suas instâncias de contêiner, como com os cmdlets CLI do Azure, Azure PowerShell ou no portal do Azure. Na CLI do Azure, especifique o parâmetro `--restart-policy` ao chamar [AZ container Create][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Exemplo de execução até a conclusão

Para ver a política de reinicialização em ação, crie uma instância de contêiner a partir da imagem do Microsoft [ACI-WordCount][aci-wordcount-image] e especifique a política de reinicialização de `OnFailure`. Esse contêiner de exemplo executa um script Python que, por padrão, analisa o texto do [Hamlet](http://shakespeare.mit.edu/hamlet/full.html)de Shakespeare, grava as 10 palavras mais comuns em stdout e, em seguida, sai.

Execute o contêiner de exemplo com o seguinte comando [AZ container Create][az-container-create] :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

As instâncias de contêiner do Azure iniciam o contêiner e, em seguida, são interrompidas quando seu aplicativo (ou script, neste caso) é encerrado. Quando as instâncias de contêiner do Azure para um contêiner cuja política de reinicialização é `Never` ou `OnFailure`, o status do contêiner é definido como **encerrado**. Você pode verificar o status de um contêiner com o comando [AZ container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Exemplo de saída:

```bash
"Terminated"
```

Depois que o status do contêiner de exemplo mostrar *terminado*, você poderá ver a saída da tarefa exibindo os logs de contêiner. Execute o comando [AZ container logs][az-container-logs] para exibir a saída do script:

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

Este exemplo mostra a saída que o script enviou para STDOUT. As tarefas em contêineres, no entanto, podem gravar sua saída no armazenamento persistente para recuperação posterior. Por exemplo, para um [compartilhamento de arquivos do Azure](container-instances-mounting-azure-files-volume.md).

## <a name="next-steps"></a>Passos seguintes

Cenários baseados em tarefas, como processamento em lote de um grande conjunto de grandes com vários contêineres, podem tirar proveito de [variáveis de ambiente](container-instances-environment-variables.md) personalizadas ou [linhas de comando](container-instances-start-command.md) em tempo de execução.

Para obter detalhes sobre como persistir a saída de seus contêineres que são executados até a conclusão, consulte [montar um compartilhamento de arquivos do Azure com instâncias de contêiner do Azure](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli

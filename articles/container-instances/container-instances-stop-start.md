---
title: Parar ou iniciar manualmente grupo de contentores
description: Aprenda a parar manualmente ou a iniciar um grupo de contentores em Instâncias de Contentores Azure.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 1801dad463d478c754e621dad0ae9406899ae7e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198120"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Iniciar ou parar manualmente os contentores no Azure Container Instances

A definição de política de [reinício](container-instances-restart-policy.md) de um grupo de contentores determina como as instâncias de contentores iniciam ou param por defeito. Pode anular a definição predefinida, parando ou iniciando um grupo de contentores manualmente.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="stop"></a>Parar

Parar manualmente um grupo de recipientes em funcionamento - por exemplo, utilizando o comando de paragem do [contentor Az][az-container-stop] ou o portal Azure. Para determinadas cargas de trabalho dos contentores, é melhor parar um grupo de contentores de longa duração após um período definido para economizar custos. 

*Quando um grupo de contentores entra no estado stop, termina e recicla todos os recipientes do grupo. Não preserva o estado do contentor.*

Quando os contentores são reciclados, os [recursos](container-instances-container-groups.md#resource-allocation) são translocados e as paragens de faturação para o grupo de contentores.

A ação stop não tem qualquer efeito se o grupo de contentores já tiver terminado (encontra-se num estado bem sucedido ou falhado). Por exemplo, um grupo de contentores com tarefas de contentores que tenham terminado com sucesso no estado de Sucesso. As tentativas de deter o grupo nesse estado não mudam o Estado. 

## <a name="start"></a>Iniciar

Quando um grupo de contentores é parado - seja porque os contentores terminaram por conta própria ou para o grupo manualmente - pode iniciar os recipientes. Por exemplo, utilize o comando de arranque do [recipiente az][az-container-start] ou o portal Azure para iniciar manualmente os recipientes do grupo. Se a imagem do recipiente for atualizada, é retirada uma nova imagem. 

Iniciar um grupo de contentores inicia uma nova implantação com a mesma configuração do recipiente. Esta ação pode ajudá-lo a reutilizar rapidamente uma configuração conhecida do grupo de contentores que funciona como espera. Não é preciso criar um novo grupo de contentores para executar a mesma carga de trabalho.

Todos os contentores de um grupo de contentores são iniciados por esta ação. Não se pode abrir um contentor específico no grupo.

Depois de iniciar ou reiniciar manualmente um grupo de contentores, o grupo de contentores funciona de acordo com a política de reinício configurada.
  
## <a name="restart"></a>Reiniciar

Pode reiniciar um grupo de contentores enquanto está em funcionamento - por exemplo, utilizando o comando de reinício do [recipiente az.][az-container-restart] Esta ação reinicia todos os contentores do grupo de contentores. Se a imagem do recipiente for atualizada, é retirada uma nova imagem. 

Reiniciar um grupo de contentores é útil quando se pretende resolver um problema de implantação. Por exemplo, se uma limitação temporária de recursos impedir que os seus recipientes correm com sucesso, reiniciar o grupo pode resolver o problema.

Todos os contentores de um grupo de contentores são reiniciados por esta ação. Não se pode reiniciar um contentor específico do grupo.

Depois de reiniciar manualmente um grupo de contentores, o grupo de contentores funciona de acordo com a política de reinício configurada.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o reinício das definições de política](container-instances-restart-policy.md) em Instâncias de Contentores Azure.

Além de parar e iniciar manualmente um grupo de contentores com a configuração existente, pode [atualizar as definições](container-instances-update.md) de um grupo de contentores em funcionamento.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container#az-container-restart
[az-container-start]: /cli/azure/container#az-container-start
[az-container-stop]: /cli/azure/container#az-container-stop

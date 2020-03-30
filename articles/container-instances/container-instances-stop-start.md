---
title: Paragem manual ou grupo de contentores de início
description: Aprenda a parar manualmente ou a iniciar um grupo de contentores em Instâncias de Contentores Azure.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: c9f8afea33c65df940d02823ec394697d2786d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533426"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Paragem manual ou start de contentores em instâncias de contentores de Azure

A definição da política de [reinício](container-instances-restart-policy.md) de um grupo de contentores determina como as instâncias de contentores iniciam ou param por defeito. Pode anular a definição predefinida parando ou iniciando um grupo de contentores manualmente.

## <a name="stop"></a>Parar

Pare manualmente um grupo de contentores em funcionamento - por exemplo, utilizando o comando de paragem do [contentor az][az-container-stop] ou o portal Azure. Para certas cargas de trabalho de contentores, é possível que deva parar um grupo de contentores de longa duração após um período definido para economizar nos custos. 

*Quando um grupo de contentores entra no estado de parada, termina e recicla todos os contentores do grupo. Não preserva o estado do contentor.*

Quando os contentores são reciclados, os [recursos](container-instances-container-groups.md#resource-allocation) são distribuídos e as paragens de faturação para o grupo de contentores.

A ação stop não tem efeito se o grupo de contentores já terminou (está em estado bem sucedido ou falhado). Por exemplo, um grupo de contentores com tarefas de contentores de corrida que funcionavam com sucesso termina no estado bem sucedido. As tentativas de deter o grupo naquele estado não mudam o Estado. 

## <a name="start"></a>Iniciar

Quando um grupo de contentores é parado - seja porque os contentores terminaram por conta própria ou que parou manualmente o grupo - pode ligar os contentores. Por exemplo, utilize o comando de arranque do [recipiente az][az-container-start] ou o portal Azure para iniciar manualmente os contentores do grupo. Se a imagem do recipiente para qualquer recipiente for atualizada, é puxada uma nova imagem. 

Iniciar um grupo de contentores inicia uma nova implantação com a mesma configuração do recipiente. Esta ação pode ajudá-lo a reutilizar rapidamente uma configuração conhecida do grupo de contentores que funciona como você espera. Não é preciso criar um novo grupo de contentores para gerir a mesma carga de trabalho.

Todos os contentores de um grupo de contentores são iniciados por esta ação. Não se pode iniciar um contentor específico no grupo.

Depois de iniciar ou reiniciar manualmente um grupo de contentores, o grupo de contentores funciona de acordo com a política de reinício configurada.
  
## <a name="restart"></a>Reiniciar

Pode reiniciar um grupo de contentores enquanto estiver em funcionamento - por exemplo, utilizando o comando de reinício do [recipiente az.][az-container-restart] Esta ação reinicia todos os contentores do grupo de contentores. Se a imagem do recipiente para qualquer recipiente for atualizada, é puxada uma nova imagem. 

Reiniciar um grupo de contentores é útil quando se pretende resolver um problema de implantação. Por exemplo, se uma limitação temporária de recursos impedir que os seus contentores funcionassem com sucesso, reiniciar o grupo pode resolver o problema.

Todos os contentores de um grupo de contentores são reiniciados por esta ação. Não se pode reiniciar um contentor específico do grupo.

Depois de reiniciar manualmente um grupo de contentores, o grupo de contentores funciona de acordo com a política de reinício configurada.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [as definições](container-instances-restart-policy.md) de política de reinício em Casos de Contentores Azure.

Além de parar manualmente e iniciar um grupo de contentores com a configuração existente, pode [atualizar as definições](container-instances-update.md) de um grupo de contentores em execução.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop

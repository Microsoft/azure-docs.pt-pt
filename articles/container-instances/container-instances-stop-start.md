---
title: Parar ou iniciar manualmente o grupo de contêineres
description: Saiba como parar manualmente ou iniciar um grupo de contêineres em instâncias de contêiner do Azure.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: c9f8afea33c65df940d02823ec394697d2786d6a
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533426"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Parar ou iniciar os contêineres manualmente nas instâncias de contêiner do Azure

A configuração de [diretiva de reinicialização](container-instances-restart-policy.md) de um grupo de contêineres determina como as instâncias de contêiner iniciam ou param por padrão. Você pode substituir a configuração padrão interrompendo ou iniciando manualmente um grupo de contêineres.

## <a name="stop"></a>Parar

Interrompa manualmente um grupo de contêineres em execução-por exemplo, usando o comando [AZ container Stop][az-container-stop] ou portal do Azure. Para determinadas cargas de trabalho de contêiner, talvez você queira interromper um grupo de contêineres de execução longa após um período definido para economizar nos custos. 

*Quando um grupo de contêineres entra no estado parado, ele termina e recicla todos os contêineres no grupo. Ele não preserva o estado do contêiner.*

Quando os contêineres são reciclados, os [recursos](container-instances-container-groups.md#resource-allocation) são desalocados e a cobrança é interrompida para o grupo de contêineres.

A ação de parada não terá efeito se o grupo de contêineres já tiver sido encerrado (está em um estado de êxito ou falha). Por exemplo, um grupo de contêineres com tarefas de contêiner de execução única que foram executadas com êxito termina no estado com êxito. As tentativas de parar o grupo nesse estado não alteram o estado. 

## <a name="start"></a>Iniciar

Quando um grupo de contêineres é interrompido – porque os contêineres terminam sozinhos ou você interrompeu manualmente o grupo – você pode iniciar os contêineres. Por exemplo, use o comando [AZ container Start][az-container-start] ou portal do Azure para iniciar manualmente os contêineres no grupo. Se a imagem de contêiner de qualquer contêiner for atualizada, uma nova imagem será retirada. 

Iniciar um grupo de contêineres inicia uma nova implantação com a mesma configuração de contêiner. Essa ação pode ajudá-lo a reutilizar rapidamente uma configuração de grupo de contêineres conhecida que funcione conforme o esperado. Você não precisa criar um novo grupo de contêineres para executar a mesma carga de trabalho.

Todos os contêineres em um grupo de contêineres são iniciados por esta ação. Não é possível iniciar um contêiner específico no grupo.

Depois de iniciar ou reiniciar um grupo de contêineres manualmente, o grupo de contêineres é executado de acordo com a política de reinicialização configurada.
  
## <a name="restart"></a>Reiniciar

Você pode reiniciar um grupo de contêineres enquanto ele está em execução-por exemplo, usando o comando [AZ container Restart][az-container-restart] . Esta ação reinicia todos os contêineres no grupo de contêineres. Se a imagem de contêiner de qualquer contêiner for atualizada, uma nova imagem será retirada. 

A reinicialização de um grupo de contêineres é útil quando você deseja solucionar um problema de implantação. Por exemplo, se uma limitação de recursos temporários impedir que os contêineres sejam executados com êxito, reiniciar o grupo poderá resolver o problema.

Todos os contêineres em um grupo de contêineres são reiniciados por essa ação. Não é possível reiniciar um contêiner específico no grupo.

Depois de reiniciar manualmente um grupo de contêineres, o grupo de contêineres é executado de acordo com a política de reinicialização configurada.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [as configurações de política de reinicialização](container-instances-restart-policy.md) nas instâncias de contêiner do Azure.

Além de parar e iniciar manualmente um grupo de contêineres com a configuração existente, você pode [atualizar as configurações](container-instances-update.md) de um grupo de contêineres em execução.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop

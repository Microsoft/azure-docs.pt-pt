---
title: Manualmente parar ou iniciar contentores no Azure Container Instances
description: Saiba como manualmente parar ou iniciar um grupo de contentores no Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 8e62d106a42dfbec897e5e14cf68fd3d7fd823c4
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65070823"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Manualmente parar ou iniciar contentores no Azure Container Instances

O [política de reinício](container-instances-restart-policy.md) definição de um grupo de contentores determina como instâncias de contentor iniciar ou parar por predefinição. É possível substituir a predefinição por manualmente a parar ou a partir de um grupo de contentores.

## <a name="stop"></a>Parar

Parar manualmente um grupo de contentor em execução - por exemplo, utilizando o [stop do contentor de az] [ az-container-stop] comando ou o portal do Azure. Para determinadas cargas de trabalho do contentor, poderá querer parar um grupo de contentores de longa execução após um período de tempo definido para poupar nos custos. 

*Quando um grupo de contentor entra no estado parado, ele termina e recicla todos os contentores no grupo. Ele não preservar o estado do contentor.*

Quando os contentores são reciclados, o [recursos](container-instances-container-groups.md#resource-allocation) são desalocadas e pára para o grupo de contentores de faturação.

A ação de paragem não tem qualquer efeito se o grupo de contentores já terminada (está em estado de um com êxito ou falha). Por exemplo, um grupo de contentores com tarefas de execução única de contêiner que foi executado com êxito termina no Estado com êxito. Tenta parar o grupo em que o estado de não alterar o estado. 

## <a name="start"></a>Iniciar

Quando um grupo de contentores está parado - ou porque os contentores terminaram por conta própria ou parar manualmente o grupo - pode iniciar os contentores. Por exemplo, utilizar o [início do contentor de az] [ az-container-start] comando ou o portal do Azure para iniciar manualmente os contentores no grupo. Se a imagem de contentor para qualquer contentor for atualizada, uma nova imagem é extraída. 

A partir de um grupo de contentores começa uma nova implementação com a mesma configuração de contentor. Esta ação pode ajudá-lo a reutilizar rapidamente uma configuração de grupo do contentor conhecidos que funciona como esperado. Não precisa de criar um novo grupo de contentor para executar a mesma carga de trabalho.

Todos os contentores no grupo de contentores são iniciados por esta ação. Não é possível iniciar um contentor específico no grupo.

Depois de iniciar manualmente ou reinicie um grupo de contentores, o grupo de contentor é executado, de acordo com o configurado política de reinício.
  
## <a name="restart"></a>Reiniciar

Pode reiniciar um grupo de contentores durante a execução - por exemplo, utilizando o [reinício do contentor de az] [ az-container-restart] comando. Esta ação reinicia todos os contentores no grupo de contentores. Se a imagem de contentor para qualquer contentor for atualizada, uma nova imagem é extraída. 

Reiniciar um grupo de contentores é útil quando deseja resolver um problema de implementação. Por exemplo, se uma limitação de recursos temporário impede que os contentores em execução com êxito, reiniciar o grupo poderá ajudar a resolver o problema.

Todos os contentores num grupo de contentor são reiniciados por esta ação. Não é possível reiniciar um contentor específico no grupo.

Depois de reiniciar manualmente um grupo de contentores, o grupo de contentor é executado, de acordo com o configurado política de reinício.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [definições de política de reinício](container-instances-restart-policy.md) no Azure Container Instances.

Além de manualmente parar e iniciar um grupo de contentores com a configuração existente, pode [atualizar as definições de](container-instances-update.md) de um grupo de contentor em execução.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop

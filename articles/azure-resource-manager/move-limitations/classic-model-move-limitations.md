---
title: Mover os recursos de implantação clássica do Azure
description: Use Azure Resource Manager para mover recursos de implantação clássicos para um novo grupo de recursos ou assinatura.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 783fcdca7637f3f67cf146bb827760cb4cdd7cbe
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533476"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Mover diretrizes para recursos do modelo de implantação clássico

As etapas para mover os recursos implantados por meio do modelo clássico diferem se você estiver movendo os recursos em uma assinatura ou em uma nova assinatura.

## <a name="move-in-the-same-subscription"></a>Mover na mesma assinatura

Ao mover recursos de um grupo de recursos para outro dentro da mesma assinatura, as seguintes restrições se aplicam:

* As redes virtuais (clássicas) não podem ser movidas.
* As máquinas virtuais (clássicas) devem ser movidas com o serviço de nuvem.
* O serviço de nuvem só pode ser movido quando a movimentação inclui todas as suas máquinas virtuais.
* Somente um serviço de nuvem pode ser movido de cada vez.
* Somente uma conta de armazenamento (clássica) pode ser movida de cada vez.
* A conta de armazenamento (clássica) não pode ser movida na mesma operação com uma máquina virtual ou um serviço de nuvem.

Para mover recursos clássicos para um novo grupo de recursos dentro da mesma assinatura, use as [operações de movimentação padrão](../resource-group-move-resources.md) por meio do portal, Azure PowerShell, CLI do Azure ou API REST. Você usa as mesmas operações que usa para mover os recursos do Resource Manager.

## <a name="move-across-subscriptions"></a>Mover entre assinaturas

Ao mover recursos para uma nova assinatura, as seguintes restrições se aplicam:

* Todos os recursos clássicos na assinatura devem ser movidos na mesma operação.
* A assinatura de destino não deve ter outros recursos clássicos.
* A movimentação só pode ser solicitada por meio de uma API REST separada para movimentações clássicas. Os comandos de movimentação padrão do Gerenciador de recursos não funcionam ao mover recursos clássicos para uma nova assinatura.

Para mover recursos clássicos para uma nova assinatura, use as operações REST específicas para os recursos clássicos. Para usar o REST, execute as seguintes etapas:

1. Verifique se a assinatura de origem pode participar de uma movimentação entre assinaturas. Use a seguinte operação:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     No corpo da solicitação, inclua:

   ```json
   {
    "role": "source"
   }
   ```

     A resposta para a operação de validação está no seguinte formato:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Verifique se a assinatura de destino pode participar de uma movimentação entre assinaturas. Use a seguinte operação:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     No corpo da solicitação, inclua:

   ```json
   {
    "role": "target"
   }
   ```

     A resposta está no mesmo formato que a validação da assinatura de origem.
1. Se ambas as assinaturas passarem na validação, mova todos os recursos clássicos de uma assinatura para outra assinatura com a seguinte operação:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    No corpo da solicitação, inclua:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

A operação pode ser executada por vários minutos.

## <a name="next-steps"></a>Passos seguintes

Se você tiver problemas para mover os recursos clássicos, entre em contato com o [suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Para obter comandos para mover recursos, consulte [mover recursos para um novo grupo de recursos ou assinatura](../resource-group-move-resources.md).

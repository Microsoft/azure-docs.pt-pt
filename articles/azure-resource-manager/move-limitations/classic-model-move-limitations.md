---
title: Mover os recursos de implantação clássica do Azure
description: Use Azure Resource Manager para mover recursos de implantação clássicos para um novo grupo de recursos ou assinatura.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: b97496e4abfdf248b9f5010417e9284c643a74ad
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150848"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Mover diretrizes para recursos do modelo de implantação clássico

As etapas para mover os recursos implantados por meio do modelo clássico diferem se você estiver movendo os recursos em uma assinatura ou em uma nova assinatura.

## <a name="move-in-the-same-subscription"></a>Mover na mesma assinatura

Ao mover recursos do grupo de recursos para outro grupo de recursos dentro da mesma subscrição, aplicam-se as seguintes restrições:

* Não não possível mover a redes virtuais (clássicas).
* Máquinas virtuais (clássicas) têm de ser movidas com o serviço cloud.
* Só pode ser movido serviço em nuvem quando a mudança inclui todas as suas máquinas virtuais.
* Apenas um serviço cloud pode ser movido por vez.
* Apenas uma conta de armazenamento (clássica) pode ser movida de cada vez.
* Não é possível mover a conta de armazenamento (clássica) na mesma operação com uma máquina virtual ou um serviço em nuvem.

Para mover recursos clássicos para um novo grupo de recursos dentro da mesma assinatura, use as [operações de movimentação padrão](../resource-group-move-resources.md) por meio do portal, Azure PowerShell, CLI do Azure ou API REST. Utilize as mesmas operações que utiliza para mover os recursos do Resource Manager.

## <a name="move-across-subscriptions"></a>Mover entre assinaturas

Quando mover recursos para uma nova subscrição, aplicam-se as seguintes restrições:

* Todos os recursos clássicos na subscrição têm de ser movidos na mesma operação.
* A assinatura de destino não deve ter outros recursos clássicos.
* A mudança só pode ser pedida através da API REST separado para move clássico. Os comandos de movimentação do Gestor de recursos padrão não funcionam quando mover recursos clássicos para uma nova subscrição.

Para mover recursos clássicos para uma nova subscrição, utilize as operações REST que são específicas para recursos clássicos. Para usar o REST, execute as seguintes etapas:

1. Verifique se a subscrição de origem pode participar de uma movimentação entre subscrições. Utilize a seguinte operação:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     No corpo do pedido, incluem:

   ```json
   {
    "role": "source"
   }
   ```

     A resposta para a operação de validação é o seguinte formato:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Verifique se a subscrição de destino pode participar de uma movimentação entre subscrições. Utilize a seguinte operação:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     No corpo do pedido, incluem:

   ```json
   {
    "role": "target"
   }
   ```

     A resposta está no mesmo formato que a validação de subscrição de origem.
1. Se ambas as subscrições passam na validação, mova todos os recursos clássicos de uma subscrição para outra subscrição com a seguinte operação:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    No corpo do pedido, incluem:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

A operação pode ser executada durante vários minutos.

## <a name="next-steps"></a>Passos seguintes

Se você tiver problemas para mover os recursos clássicos, entre em contato com o [suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Para obter comandos para mover recursos, consulte [mover recursos para um novo grupo de recursos ou assinatura](../resource-group-move-resources.md).

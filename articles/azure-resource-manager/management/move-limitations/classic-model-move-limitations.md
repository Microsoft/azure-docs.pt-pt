---
title: Move Azure Classic recursos de implantação
description: Use o Gestor de Recursos Azure para mover recursos de implantação clássicos para um novo grupo de recursos ou subscrição.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 78b9769a31fa0c96c12e18d05cb9c484aa52a1d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75485289"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Mover orientação para recursos do modelo de implementação clássico

As etapas para movimentar recursos implementados através do modelo clássico diferem com base na mudança dos recursos dentro de uma subscrição ou numa nova subscrição.

## <a name="move-in-the-same-subscription"></a>Mover-se na mesma subscrição

Ao transferir recursos de um grupo de recursos para outro grupo de recursos dentro da mesma subscrição, aplicam-se as seguintes restrições:

* As redes virtuais (clássicas) não podem ser movidas.
* As máquinas virtuais (clássicas) devem ser movidas com o serviço de nuvem.
* O serviço de nuvem só pode ser movido quando o movimento inclui todas as suas máquinas virtuais.
* Apenas um serviço de nuvem pode ser movido de cada vez.
* Apenas uma conta de armazenamento (clássica) pode ser movida de cada vez.
* A conta de armazenamento (clássica) não pode ser movida na mesma operação com uma máquina virtual ou um serviço de nuvem.

Para mover recursos clássicos para um novo grupo de recursos dentro da mesma subscrição, utilize as [operações](../move-resource-group-and-subscription.md) de movimento padrão através do portal, Azure PowerShell, Azure CLI ou REST API. Usa as mesmas operações que utiliza para movimentar recursos do Gestor de Recursos.

## <a name="move-across-subscriptions"></a>Mover-se através de subscrições

Ao mover recursos para uma nova subscrição, aplicam-se as seguintes restrições:

* Todos os recursos clássicos da subscrição devem ser movidos na mesma operação.
* A subscrição-alvo não deve ter outros recursos clássicos.
* O movimento só pode ser solicitado através de uma API REST separada para movimentos clássicos. Os comandos de movimento padrão do Gestor de Recursos não funcionam ao mover recursos clássicos para uma nova subscrição.

Para mover recursos clássicos para uma nova subscrição, use as operações REST que são específicas dos recursos clássicos. Para utilizar o REST, faça os seguintes passos:

1. Verifique se a subscrição de origem pode participar num movimento de subscrição cruzada. Utilize a seguinte operação:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     No órgão de pedido, inclua:

   ```json
   {
    "role": "source"
   }
   ```

     A resposta para a operação de validação encontra-se no seguinte formato:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Verifique se a subscrição do destino pode participar num movimento de subscrição cruzada. Utilize a seguinte operação:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     No órgão de pedido, inclua:

   ```json
   {
    "role": "target"
   }
   ```

     A resposta encontra-se no mesmo formato que a validação de subscrição de origem.
1. Se ambas as subscrições passarem a validação, mova todos os recursos clássicos de uma subscrição para outra subscrição com a seguinte operação:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    No órgão de pedido, inclua:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

A operação pode decorrer durante vários minutos.

## <a name="next-steps"></a>Passos seguintes

Se tiver dificuldade em mover recursos clássicos, contacte [o Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Para que os comandos movam recursos, consulte [mover recursos para um novo grupo de recursos ou subscrição](../move-resource-group-and-subscription.md).

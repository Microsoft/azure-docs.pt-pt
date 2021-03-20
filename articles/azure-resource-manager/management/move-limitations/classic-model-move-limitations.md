---
title: Mover recursos de implantação clássicos do Move Azure
description: Utilize o Azure Resource Manager para mover recursos de implantação clássicos para um novo grupo de recursos ou subscrição.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 78b9769a31fa0c96c12e18d05cb9c484aa52a1d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75485289"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Orientar para recursos de modelo de implantação clássica

Os passos para mover recursos implementados através do modelo clássico diferem com base no facto de estar a mover os recursos dentro de uma subscrição ou de uma nova subscrição.

## <a name="move-in-the-same-subscription"></a>Mova-se na mesma subscrição

Ao transferir recursos de um grupo de recursos para outro grupo de recursos dentro da mesma subscrição, aplicam-se as seguintes restrições:

* As redes virtuais (clássicas) não podem ser movidas.
* As máquinas virtuais (clássicas) devem ser movidas com o serviço de nuvem.
* O serviço em nuvem só pode ser movido quando o movimento inclui todas as suas máquinas virtuais.
* Apenas um serviço de nuvem pode ser movido de cada vez.
* Apenas uma conta de armazenamento (clássica) pode ser movida de cada vez.
* A conta de armazenamento (clássica) não pode ser movida na mesma operação com uma máquina virtual ou um serviço de nuvem.

Para mover recursos clássicos para um novo grupo de recursos dentro da mesma subscrição, utilize as [operações de movimento padrão](../move-resource-group-and-subscription.md) através do portal, Azure PowerShell, Azure CLI ou REST API. Utiliza as mesmas operações que utiliza para mover recursos do Gestor de Recursos.

## <a name="move-across-subscriptions"></a>Mover assinaturas

Ao transferir recursos para uma nova subscrição, aplicam-se as seguintes restrições:

* Todos os recursos clássicos da subscrição devem ser movidos na mesma operação.
* A subscrição-alvo não deve ter quaisquer outros recursos clássicos.
* O movimento só pode ser solicitado através de uma API REST separada para movimentos clássicos. Os comandos standard de movimento do Gestor de Recursos não funcionam quando se movem recursos clássicos para uma nova subscrição.

Para mover recursos clássicos para uma nova subscrição, utilize as operações REST específicas aos recursos clássicos. Para utilizar o REST, faça os seguintes passos:

1. Verifique se a subscrição de origem pode participar num movimento de subscrição cruzada. Utilize a seguinte operação:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     No órgão de pedido, incluem::

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

     No órgão de pedido, incluem::

   ```json
   {
    "role": "target"
   }
   ```

     A resposta está no mesmo formato que a validação da subscrição de origem.
1. Se ambas as subscrições passarem na validação, mova todos os recursos clássicos de uma subscrição para outra subscrição com a seguinte operação:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    No órgão de pedido, incluem::

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

A operação pode decorrer durante alguns minutos.

## <a name="next-steps"></a>Passos seguintes

Se tiver problemas em mover recursos clássicos, contacte [o Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Para que os comandos movimentem recursos, consulte [mover recursos para novo grupo de recursos ou subscrição](../move-resource-group-and-subscription.md).

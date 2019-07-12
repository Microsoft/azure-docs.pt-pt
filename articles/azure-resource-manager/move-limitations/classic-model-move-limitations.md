---
title: Mover os recursos de implementação clássico do Azure para a nova subscrição ou grupo de recursos
description: Utilize o Azure Resource Manager para mover os recursos de implantação do clássico para um novo grupo de recursos ou subscrição.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 4770f957b6b9eea75b50776a7491b1ca479e50e2
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723509"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Mover a documentação de orientação para os recursos de modelo de implementação clássico

Os passos para mover recursos implementados por meio do modelo clássico divergir com base em se estiver a mover os recursos numa subscrição ou para uma nova subscrição.

## <a name="move-in-the-same-subscription"></a>Mover na mesma subscrição

Ao mover recursos do grupo de recursos para outro grupo de recursos dentro da mesma subscrição, aplicam-se as seguintes restrições:

* Não não possível mover a redes virtuais (clássicas).
* Máquinas virtuais (clássicas) têm de ser movidas com o serviço cloud.
* Só pode ser movido serviço em nuvem quando a mudança inclui todas as suas máquinas virtuais.
* Apenas um serviço cloud pode ser movido por vez.
* Apenas uma conta de armazenamento (clássica) pode ser movida de cada vez.
* Não é possível mover a conta de armazenamento (clássica) na mesma operação com uma máquina virtual ou um serviço em nuvem.

Para mover recursos clássicos para um novo grupo de recursos dentro da mesma subscrição, utilize o [operações de movimentação padrão](../resource-group-move-resources.md) através do portal, o Azure PowerShell, a CLI do Azure ou a REST API. Utilize as mesmas operações que utiliza para mover os recursos do Resource Manager.

## <a name="move-across-subscriptions"></a>Mover entre subscrições

Quando mover recursos para uma nova subscrição, aplicam-se as seguintes restrições:

* Todos os recursos clássicos na subscrição têm de ser movidos na mesma operação.
* A subscrição de destino não tem de ter outros recursos clássicos.
* A mudança só pode ser pedida através da API REST separado para move clássico. Os comandos de movimentação do Gestor de recursos padrão não funcionam quando mover recursos clássicos para uma nova subscrição.

Para mover recursos clássicos para uma nova subscrição, utilize as operações REST que são específicas para recursos clássicos. Para utilizar o REST, siga os passos abaixo:

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

## <a name="next-steps"></a>Passos Seguintes

Se tiver dificuldades em mover recursos clássicos, contacte [suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Para obter comandos mover os recursos, veja [mover recursos para um novo grupo de recursos ou subscrição](../resource-group-move-resources.md).

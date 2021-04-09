---
ms.openlocfilehash: 2e92d150851c74a84f785d1f5f0ebe2e5870a54e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "97936769"
---


| Funcionalidade |[Plano de consumo](../articles/azure-functions/consumption-plan.md)|[Plano Premium](../articles/azure-functions/functions-premium-plan.md)|[Plano dedicado](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Restrições ip de entrada e acesso ao site privado](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integração de rede virtual](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌No|✅Sim (Regional)|✅Sim (Regional e Gateway)|✅Yes| ✅Yes|
|[Gatilhos de rede virtuais (não HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌No| ✅Yes |✅Yes|✅Yes|✅Yes|
|[Ligações híbridas (apenas](../articles/azure-functions/functions-networking-options.md#hybrid-connections) para Windows)|❌No|✅Yes|✅Yes|✅Yes|✅Yes|
|[Restrições ip de saída](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌No| ✅Yes|✅Yes|✅Yes|✅Yes|
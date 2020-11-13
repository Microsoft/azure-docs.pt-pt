---
ms.openlocfilehash: 82571d1a0e651f638dec29184f0ecdc88562b3ad
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578846"
---


| Funcionalidade |[Plano de consumo](../articles/azure-functions/functions-scale.md#consumption-plan)|[Plano Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Plano dedicado](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Restrições ip de entrada e acesso ao site privado](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integração de rede virtual](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Não|✅Sim (Regional)|✅Sim (Regional e Gateway)|✅Yes| ✅Yes|
|[Gatilhos de rede virtuais (não HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Não| ✅Yes |✅Yes|✅Yes|✅Yes|
|[Ligações híbridas (apenas](../articles/azure-functions/functions-networking-options.md#hybrid-connections) para Windows)|❌Não|✅Yes|✅Yes|✅Yes|✅Yes|
|[Restrições ip de saída](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Não| ✅Yes|✅Yes|✅Yes|✅Yes|
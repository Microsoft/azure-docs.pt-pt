---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84906731"
---


| Funcionalidade |[Plano de consumo](../articles/azure-functions/functions-scale.md#consumption-plan)|[Plano Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Plano dedicado](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Utilizar o Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Restrições ip de entrada e acesso ao site privado](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integração de rede virtual](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌No|✅Sim (Regional)|✅Sim (Regional e Gateway)|✅Yes| ✅Yes|
|[Gatilhos de rede virtuais (não HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌No| ✅Yes |✅Yes|✅Yes|✅Yes|
|[Ligações híbridas (apenas](../articles/azure-functions/functions-networking-options.md#hybrid-connections) para Windows)|❌No|✅Yes|✅Yes|✅Yes|✅Yes|
|[Restrições ip de saída](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌No| ✅Yes|✅Yes|✅Yes|✅Yes|
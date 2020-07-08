---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84906731"
---


| Funcionalidade |[Plano de consumo](../articles/azure-functions/functions-scale.md#consumption-plan)|[Plano Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Plano dedicado](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Utilizar o Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Restrições ip de entrada e acesso ao site privado](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅Sim|✅Sim|✅Sim|✅Sim|✅Sim|
|[Integração de rede virtual](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Não|✅Sim (Regional)|✅Sim (Regional e Gateway)|✅Sim| ✅Sim|
|[Gatilhos de rede virtuais (não HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Não| ✅Sim |✅Sim|✅Sim|✅Sim|
|[Ligações híbridas (apenas](../articles/azure-functions/functions-networking-options.md#hybrid-connections) para Windows)|❌Não|✅Sim|✅Sim|✅Sim|✅Sim|
|[Restrições ip de saída](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Não| ✅Sim|✅Sim|✅Sim|✅Sim|
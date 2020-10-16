---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84906731"
---


| Funcionalidade |[Plano de consumo](../articles/azure-functions/functions-scale.md#consumption-plan)|[Plano Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Plano dedicado](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Restrições ip de entrada e acesso ao site privado](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅Sim, é o seu|✅Sim, é o seu|✅Sim, é o seu|✅Sim, é o seu|✅Sim, é o seu|
|[Integração da rede virtual](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Não|✅Sim (Regional)|✅Sim (Regional e Gateway)|✅Sim, é o seu| ✅Sim, é o seu|
|[Gatilhos de rede virtuais (não HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Não| ✅Sim, é o seu |✅Sim, é o seu|✅Sim, é o seu|✅Sim, é o seu|
|[Ligações híbridas (apenas](../articles/azure-functions/functions-networking-options.md#hybrid-connections) para Windows)|❌Não|✅Sim, é o seu|✅Sim, é o seu|✅Sim, é o seu|✅Sim, é o seu|
|[Restrições ip de saída](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Não| ✅Sim, é o seu|✅Sim, é o seu|✅Sim, é o seu|✅Sim, é o seu|
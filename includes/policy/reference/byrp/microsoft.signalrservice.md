---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 949979afb3c2cc0d04dc1e9b189570e5bf1f2c07
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99214410"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Serviço Azure SignalR deve utilizar link privado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |O Azure Private Link permite-lhe ligar a sua rede virtual aos serviços Azure sem um endereço IP público na fonte ou destino. A plataforma de ligação privada trata da conectividade entre o consumidor e os serviços através da rede de espinha dorsal Azure. Ao mapear pontos finais privados para os seus recursos SignalR em vez de todo o serviço, também estará protegido contra riscos de fuga de dados . Saiba mais em: [https://aka.ms/asrs/privatelink](https://aka.ms/asrs/privatelink) . |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |

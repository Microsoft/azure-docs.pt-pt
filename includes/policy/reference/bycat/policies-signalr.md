---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 293a32a52419d281409dcdfc7b234d8cdb5e6942
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611287"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Serviço Azure SignalR deve utilizar link privado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |O Azure Private Link permite-lhe ligar a sua rede virtual aos serviços Azure sem um endereço IP público na fonte ou destino. A plataforma de ligação privada trata da conectividade entre o consumidor e os serviços através da rede de espinha dorsal Azure. Ao mapear pontos finais privados para os seus recursos SignalR em vez de todo o serviço, também estará protegido contra riscos de fuga de dados . Saiba mais em: [https://aka.ms/asrs/privatelink](https://aka.ms/asrs/privatelink) . |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |

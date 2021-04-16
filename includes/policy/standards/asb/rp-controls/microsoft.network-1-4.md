---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3e4004eac01d223ff7b5894c410b233ba6885aa6
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511591"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Todo o tráfego de Internet deve ser encaminhado através do seu Azure Firewall implantado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |O Centro de Segurança Azure identificou que algumas das suas sub-redes não estão protegidas com uma firewall de próxima geração. Proteja as suas sub-redes de potenciais ameaças restringindo-lhes o acesso com o Azure Firewall ou com uma firewall de próxima geração suportada |AuditIfNotExists, Desativado |[3.0.0-pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[A Padrão de Proteção Azure DDoS deve ser ativado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |A norma de proteção DDoS deve ser ativada para todas as redes virtuais com uma sub-rede que faça parte de um gateway de aplicações com um IP público. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |

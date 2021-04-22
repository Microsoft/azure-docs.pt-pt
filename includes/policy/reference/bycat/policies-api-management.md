---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6c54ddf232d16b0555ec8e1f5fee57e1f597488f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866942"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O serviço de Gestão API deve utilizar um SKU que suporte redes virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73ef9241-5d81-4cd4-b483-8443d1730fe5) |Com SKUs suportados da API Management, a implantação do serviço numa rede virtual desbloqueia funcionalidades avançadas de networking e segurança da API Management que lhe proporcionam um maior controlo sobre a configuração de segurança da rede. Saiba mais em: [https://aka.ms/apimvnet](https://aka.ms/apimvnet) . |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_AllowedVNETSkus_AuditDeny.json) |
|[Os serviços de Gestão da API devem utilizar uma rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |A implementação da Rede Virtual Azure proporciona uma maior segurança, isolamento e permite-lhe colocar o seu serviço de Gestão API numa rede de encaminhamento não internet a que controla o acesso. Estas redes podem então ser ligadas às suas redes no local utilizando várias tecnologias VPN, o que permite o acesso aos seus serviços de backend dentro da rede e/ou no local. O portal de desenvolvedores e gateway API, pode ser configurado para ser acessível a partir da Internet ou apenas dentro da rede virtual. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |

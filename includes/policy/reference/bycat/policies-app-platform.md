---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3bbfc9d2045367d014ffc2c3cbc3f5696f9c1d6f
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "90005434"
---
|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar instâncias da Cloud Azure Spring onde o rastreio distribuído não está habilitado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f2d8593-4667-4932-acca-6a9f187af109) |As ferramentas de rastreio distribuídas na Nuvem de primavera de Azure permitem depurar e monitorizar as complexas interligações entre microserviços numa aplicação. As ferramentas de rastreio distribuídas devem ser ativadas e em estado saudável. |Auditoria, Deficientes |[1.0.0-pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_DistributedTracing_Audit.json) |
|[Azure Spring Cloud deve usar injeção de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |As instâncias Azure Spring Cloud devem utilizar a injeção de rede virtual para os seguintes fins: 1. Isole a nuvem de primavera de Azure da Internet. 2. Permita ao Azure Spring Cloud interagir com sistemas em centros de dados ou em Azure em outras redes virtuais. 3. Capacitar os clientes para controlar as comunicações de rede de entrada e saída para a Azure Spring Cloud. |Auditoria, Deficientes, Negar |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |

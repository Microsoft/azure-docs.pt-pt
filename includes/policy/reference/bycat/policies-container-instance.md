---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 116b1ca47c9fa7c5f2448499c14b6da56a8b02c9
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105033550"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O grupo de contentores Azure Container Instance deve implantar-se numa rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8af8f826-edcb-4178-b35f-851ea6fea615) |Comunicação segura entre os seus contentores com redes virtuais Azure. Quando especifica uma rede virtual, os recursos dentro da rede virtual podem comunicar de forma segura e privada entre si. |Auditoria, Deficientes, Negar |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_VNET_Audit.json) |
|[O grupo de contentores Azure Container Instance deve utilizar a chave gerida pelo cliente para encriptação](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0aa61e00-0a01-4a3c-9945-e93cffedf0e6) |Fixe os seus recipientes com maior flexibilidade utilizando as chaves geridas pelo cliente. Quando especifica uma chave gerida pelo cliente, essa chave é utilizada para proteger e controlar o acesso à chave que encripta os seus dados. A utilização de teclas geridas pelo cliente fornece capacidades adicionais para controlar a rotação da chave de encriptação ou apagar os dados criptograficamente. |Auditoria, Deficientes, Negar |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_CMK_Audit.json) |

---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5884817091ec7ad5eb0ff58a8aefc8acafb173d3
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429369"
---
|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A encriptação Azure Data Explorer em repouso deve usar uma chave gerida pelo cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |Ativar a encriptação em repouso utilizando uma chave gerida pelo cliente no seu cluster Azure Data Explorer fornece controlo adicional sobre a chave que está a ser utilizada pela encriptação em repouso. Esta funcionalidade é frequentemente aplicável aos clientes com requisitos especiais de conformidade e requer um Cofre-Chave para gerir as chaves. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[A encriptação do disco deve ser ativada no Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |Permitir a encriptação do disco ajuda a proteger e salvaguardar os seus dados para cumprir os seus compromissos de segurança organizacional e conformidade. |Auditoria, Negar, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[A dupla encriptação deve ser ativada no Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |Permitir a dupla encriptação ajuda a proteger e salvaguardar os seus dados para cumprir os seus compromissos de segurança organizacional e conformidade. Quando a dupla encriptação foi ativada, os dados na conta de armazenamento são encriptados duas vezes, uma ao nível de serviço e outra ao nível da infraestrutura, utilizando dois algoritmos de encriptação diferentes e duas teclas diferentes. |Auditoria, Negar, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[A injeção de rede virtual deve ser ativada para o Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |Proteja o perímetro da sua rede com a injeção de rede virtual que lhe permite impor as regras do grupo de segurança de rede, ligar no local e proteger as suas fontes de ligação de dados com pontos finais de serviço. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |

---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: fb5f6dbb410a15bf88fccc1ed3f9c3ca0f830c70
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "96296097"
---
|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os espaços de trabalho do Azure Synapse devem usar chaves geridas pelo cliente para encriptar dados em repouso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d52b2d-e161-4dfa-a82b-55e564167385) |Utilize chaves geridas pelo cliente para controlar a encriptação no resto dos dados armazenados nos espaços de trabalho do Azure Synapse. As teclas geridas pelo cliente fornecem encriptação dupla adicionando uma segunda camada de encriptação em cima da encriptação padrão com teclas geridas pelo serviço. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceCMK_Audit.json) |
|[As regras de firewall ip nos espaços de trabalho do Azure Synapse devem ser removidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56fd377d-098c-4f02-8406-81eb055902b8) |A remoção de todas as regras de firewall IP melhora a segurança garantindo que o seu espaço de trabalho Azure Synapse só pode ser acedido a partir de um ponto final privado. Esta configuração audita a criação de regras de firewall que permitem o acesso da rede pública no espaço de trabalho. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceFirewallRules_Audit.json) |
|[Rede virtual gerida do espaço de trabalho nos espaços de trabalho do Azure Synapse deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d9dbfa3-927b-4cf0-9d0f-08747f971650) |Ativar uma rede virtual gerida do espaço de trabalho garante que o seu espaço de trabalho está isolado de outros espaços de trabalho. A integração de dados e os recursos de Faíscas implantados nesta rede virtual também proporcionam isolamento ao nível do utilizador para as atividades da Spark. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceManagedVnet_Audit.json) |
|[As ligações privadas de ponto final nos espaços de trabalho da Azure Synapse devem ser ativadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |Os pontos finais privados podem ser configurados para se ligarem privadamente a um espaço de trabalho Azure Synapse. Isto é usado para impor um canal de comunicação seguro ao espaço de trabalho Azure Synapse. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Os pontos finais privados geridos pela Sinaapse só devem ligar-se aos recursos dos inquilinos aprovados do Azure Ative Directory](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a003702-13d2-4679-941b-937e58c443f0) |Proteja o seu espaço de trabalho synapse apenas permitindo ligações a recursos em inquilinos aprovados do Azure Ative Directory (Azure AD). Os inquilinos aprovados da Azure AD podem ser definidos durante a atribuição de políticas. |Auditoria, Deficientes, Negar |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/Workspace_DataExfiltrationPrevention_Deny.json) |

---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 7aa6abb1a5fa9c969ca5e6d0730bed3b461fe81b
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172885"
---
|Nome |Descrição |Efeitos(s) |Versão |
|---|---|---|---|
|[SKUs de contas de armazenamento permitidos](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/AllowedStorageSkus_Deny.json) |Esta política permite especificar um conjunto de skus de conta de armazenamento que a sua organização pode implementar. |Negar |1.0.0 |
|[Auditoria sem restrições ao acesso da rede às contas de armazenamento](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |Audite o acesso sem restrições à rede nas definições de firewall da sua conta de armazenamento. Em vez disso, configurar as regras da rede para que apenas as aplicações de redes permitidas possam aceder à conta de armazenamento. Para permitir ligações de clientes específicos da Internet ou no local, o acesso pode ser concedido ao tráfego de redes virtuais específicas do Azure ou a gamas de endereços IP da Internet pública |Auditoria, Deficientes |1.0.0 |
|[Implementar proteção avançada de ameaças em contas de armazenamento](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAdvancedThreatProtection_Deploy.json) |Esta política permite proteção avançada de ameaças em contas de armazenamento. |ImplementaçãoIfNotExists, Desativado |1.0.0 |
|[Armazenamento geo-redundante deve ser ativado para contas de armazenamento](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |Esta política audita qualquer Conta de Armazenamento com armazenamento geo-redundante não ativado. |Auditoria, Deficientes |1.0.0 |
|[Transferência segura para contas de armazenamento deve ser ativada](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |Pedido de auditoria da transferência segura na sua conta de armazenamento. A transferência segura é uma opção que obriga a sua conta de armazenamento a aceitar pedidos apenas de ligações seguras (HTTPS). A utilização do HTTPS garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camadas de rede, tais como o homem no meio, escutas e sequestro de sessão |Auditoria, Negação, Deficientes |1.0.0 |
|[As contas de armazenamento devem permitir o acesso a partir de serviços fidedignos da Microsoft](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |Alguns serviços da Microsoft que interagem com contas de armazenamento operam de redes que não não possível conceder acesso por meio de regras de rede. Para ajudar a este tipo de trabalho do serviço conforme pretendido, permitir que o conjunto de serviços Microsoft fidedignos para ignorar as regras de rede. Estes serviços, em seguida, irão utilizar a autenticação segura para aceder à conta de armazenamento. |Auditoria, Negação, Deficientes |1.0.0 |
|[As contas de armazenamento devem ser migradas para novos recursos do Gestor de Recursos do Azure](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |Utilize o novo Gestor de Recursos Azure para as suas contas de armazenamento para fornecer melhorias de segurança tais como: controlo de acesso mais forte (RBAC), melhor auditoria, implantação e governação baseadaem em Recursos Azure, acesso a identidades geridas, acesso a cofre chave para segredos, autenticação baseada em Azure AD e suporte para tags e grupos de recursos para uma gestão mais fácil da segurança |Auditoria, Negação, Deficientes |1.0.0 |

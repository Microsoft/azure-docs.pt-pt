---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ce13484aa085cef89ba34151824ee843dd3bd90e
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "103419901"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure File Sync deve usar link privado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |A criação de um ponto final privado para o recurso indicado do Storage Sync Service permite-lhe abordar o seu recurso de Serviço de Sincronização de Armazenamento a partir do espaço de endereço IP privado da rede da sua organização, em vez de através do ponto final público acessível à Internet. A criação de um ponto final privado por si só não desativa o ponto final público. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Configure Azure File Sync com pontos finais privados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |É implantado um ponto final privado para o recurso indicado do Serviço de Sincronização de Armazenamento. Isto permite-lhe abordar o seu recurso Storage Sync Service a partir do espaço de endereço IP privado da rede da sua organização, em vez de através do ponto final público acessível à Internet. A existência de um ou mais pontos finais privados por si só não prejudica o ponto final público. |ImplementarIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[Modificar - Configurar o Azure File Sync para desativar o acesso à rede pública](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |O ponto final público acessível à Internet do Azure File Sync é desativado pela sua política organizacional. Pode ainda aceder ao Serviço de Sincronização de Armazenamento através dos seus pontos finais privados. |Modificar, Desativar |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[O acesso à rede pública deve ser desativado para O Azure File Sync](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |A desativação do ponto final público permite-lhe restringir o acesso ao seu recurso de Serviço de Sincronização de Armazenamento a pedidos destinados a pontos finais privados aprovados na rede da sua organização. Não há nada inerentemente inseguro em permitir pedidos ao ponto final público, no entanto, pode querer desativá-lo para satisfazer os requisitos de política regulamentar, legal ou organizacional. Pode desativar o ponto final público de um Serviço de Sincronização de Armazenamento, definindo a entradaTrafficPolicy do recurso para AllowVirtualNetworksOnly. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |

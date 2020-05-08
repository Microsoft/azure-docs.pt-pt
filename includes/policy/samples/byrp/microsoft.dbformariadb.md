---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d8d390256c44bc37070594596f627fe52f325c6a
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82966149"
---
|Name |Descrição |Efeitos(s) |Versão |GitHub |
|---|---|---|---|---|
|[Backup geo-redundante deve ser ativado para base de dados Azure para MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Esta política audita qualquer Base de Dados Azure para MariaDB com cópia de segurança georedundante não ativada. |Auditoria, Deficientes |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[O servidor MariaDB deve usar um ponto final do serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |Esta política audita servidores MariaDB não configurados para usar um ponto final de serviço de rede virtual. Para mais detalhes, visite. [https://aka.ms/mariadbvirtualnetwork](https://aka.ms/mariadbvirtualnetwork) |AuditoriaIfNotExists, Deficiente |1.0.1 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[Ponto final privado deve ser ativado para servidores MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Esta política audita servidores MariaDB não configurados para usar um ponto final privado. Para mais detalhes, visite. [https://aka.ms/mariadbprivatelink](https://aka.ms/mariadbprivatelink) |AuditoriaIfNotExists, Deficiente |1.0.1 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[O acesso à rede pública deve ser desativado para servidores MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Esta política audita servidores MariaDB no seu ambiente com acesso à rede pública ativado. Para mais detalhes, visite. [https://go.microsoft.com/fwlink/?linkid=2119542](https://go.microsoft.com/fwlink/?linkid=2119542) |Auditoria, Deficientes |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |

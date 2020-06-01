---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/29/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: caf5e604fe09dd34dbab84dbb66f220cd17b233e
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234735"
---
|Name |Descrição |Efeito(s) |Versão |GitHub |
|---|---|---|---|---|
|[Traga a sua própria proteção de dados chave deve ser ativada para servidores MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83cef61d-dbd1-4b20-a4fc-5fbc7da10833) |Esta política audita servidores MySQL no seu ambiente sem ativar a sua própria proteção de dados chave. Para mais detalhes, [https://aka.ms/mysqlbyok](https://aka.ms/mysqlbyok) visite. |AuditIfNotExists, Desativado |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableByok_Audit.json) |
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Esta política audita qualquer servidor MySQL que não esteja a impor a ligação SSL. A Azure Database for MySQL suporta ligar a sua Base de Dados Azure para o servidor MySQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. |Auditoria, Deficientes |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Backup geo-redundante deve ser ativado para Azure Database para MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Esta política audita qualquer Base de Dados Azure para o MySQL com cópia de segurança geo-redundante não ativada. |Auditoria, Deficientes |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[O servidor MySQL deve usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3375856c-3824-4e0e-ae6a-79e011dd4c47) |Esta política audita servidores MySQL não configurados para utilizar um ponto final de serviço de rede virtual. Para mais detalhes, [https://aka.ms/mysqlvnet](https://aka.ms/mysqlvnet) visite. |AuditIfNotExists, Desativado |1.0.1 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_VirtualNetworkServiceEndpoint_Audit.json) |
|[O ponto final privado deve ser ativado para servidores MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |Esta política audita servidores MySQL não configurados para utilizar um ponto final privado. Para mais detalhes, [https://aka.ms/mysqlprivatelink](https://aka.ms/mysqlprivatelink) visite. |AuditIfNotExists, Desativado |1.0.1 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[O acesso à rede pública deve ser desativado para servidores MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |Esta política audita servidores MySQL no seu ambiente com acesso à rede pública ativado. Para mais detalhes, [https://go.microsoft.com/fwlink/?linkid=2120014](https://go.microsoft.com/fwlink/?linkid=2120014) visite. |Auditoria, Deficientes |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |

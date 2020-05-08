---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 42d60c907fa0db8e04e121ce77e6cd50b34fdf03
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82966128"
---
|Name |Descrição |Efeitos(s) |Versão |GitHub |
|---|---|---|---|---|
|[Traga a sua própria proteção de dados chave deve ser ativada para servidores MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83cef61d-dbd1-4b20-a4fc-5fbc7da10833) |Esta política audita servidores MySQL no seu ambiente sem trazer a sua própria proteção de dados chave ativada. Para mais detalhes, visite. [https://aka.ms/mysqlbyok](https://aka.ms/mysqlbyok) |AuditoriaIfNotExists, Deficiente |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableByok_Audit.json) |
|[Impor a ligação SSL deve ser ativada para servidores de base de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Esta política audita qualquer servidor MySQL que não esteja a impor a ligação SSL. A Base de Dados Azure para MySQL suporta ligar a sua Base de Dados Azure para o servidor MySQL às aplicações do cliente utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o seu servidor de base de dados e as aplicações do seu cliente ajuda a proteger contra ataques "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. |Auditoria, Deficientes |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Backup geo-redundante deve ser ativado para Base de Dados Azure para MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Esta política audita qualquer Base de Dados Azure para O MySQL com cópia de segurança georedundante não ativada. |Auditoria, Deficientes |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[O servidor MySQL deve usar um ponto final do serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3375856c-3824-4e0e-ae6a-79e011dd4c47) |Esta política audita servidores MySQL não configurados para usar um ponto final de serviço de rede virtual. Para mais detalhes, visite. [https://aka.ms/mysqlvnet](https://aka.ms/mysqlvnet) |AuditoriaIfNotExists, Deficiente |1.0.1 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_VirtualNetworkServiceEndpoint_Audit.json) |
|[O ponto final privado deve ser ativado para servidores MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |Esta política audita servidores MySQL não configurados para usar um ponto final privado. Para mais detalhes, visite. [https://aka.ms/mysqlprivatelink](https://aka.ms/mysqlprivatelink) |AuditoriaIfNotExists, Deficiente |1.0.1 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[O acesso à rede pública deve ser desativado para servidores MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |Esta política audita servidores MySQL no seu ambiente com acesso à rede pública ativado. Para mais detalhes, visite. [https://go.microsoft.com/fwlink/?linkid=2120014](https://go.microsoft.com/fwlink/?linkid=2120014) |Auditoria, Deficientes |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |

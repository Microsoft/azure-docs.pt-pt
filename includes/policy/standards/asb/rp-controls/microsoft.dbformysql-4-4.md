---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 122bc50da4cef757a268a8c900836c3bec730839
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097867"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |A Azure Database for MySQL suporta ligar a sua Base de Dados Azure para o servidor MySQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. Esta configuração impõe que o SSL esteja sempre ativado para aceder ao servidor de base de dados. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

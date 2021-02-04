---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: eac282190469d2425443fadd92e5a852e85168a1
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99561626"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dashboards compartilhados não devem ter azulejos de marcação com conteúdo em linha](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Não é permitido criar um dashboard partilhado que tenha conteúdo inline em azulejos de marcação e impor que o conteúdo deve ser armazenado como um ficheiro de marcação que está hospedado online. Se utilizar conteúdo inline no azulejo de marcação, não poderá gerir a encriptação do conteúdo. Ao configurar o seu próprio armazenamento, pode encriptar, encriptar duas vezes e até trazer as suas próprias chaves. Permitir que esta política restringe os utilizadores a utilizarem a versão de pré-visualização 2020-09-01 ou acima dos dashboards partilhados REST API. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |

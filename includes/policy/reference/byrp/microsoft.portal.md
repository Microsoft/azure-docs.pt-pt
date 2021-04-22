---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d10090954162bdf614234d2bce863a0d9a1635af
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875738"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dashboards compartilhados não devem ter azulejos de marcação com conteúdo em linha](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Não é permitido criar um dashboard partilhado que tenha conteúdo inline em azulejos de marcação e impor que o conteúdo deve ser armazenado como um ficheiro de marcação que está hospedado online. Se utilizar conteúdo inline no azulejo de marcação, não poderá gerir a encriptação do conteúdo. Ao configurar o seu próprio armazenamento, pode encriptar, encriptar duas vezes e até trazer as suas próprias chaves. Permitir que esta política restringe os utilizadores a utilizarem a versão de pré-visualização 2020-09-01 ou acima dos dashboards partilhados REST API. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |

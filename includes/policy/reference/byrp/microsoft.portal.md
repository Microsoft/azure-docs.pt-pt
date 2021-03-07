---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4d419d8e338228132a4aa8a32b7e29d8fa2f46ca
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425464"
---
|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dashboards compartilhados não devem ter azulejos de marcação com conteúdo em linha](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Não é permitido criar um dashboard partilhado que tenha conteúdo inline em azulejos de marcação e impor que o conteúdo deve ser armazenado como um ficheiro de marcação que está hospedado online. Se utilizar conteúdo inline no azulejo de marcação, não poderá gerir a encriptação do conteúdo. Ao configurar o seu próprio armazenamento, pode encriptar, encriptar duas vezes e até trazer as suas próprias chaves. Permitir que esta política restringe os utilizadores a utilizarem a versão de pré-visualização 2020-09-01 ou acima dos dashboards partilhados REST API. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |

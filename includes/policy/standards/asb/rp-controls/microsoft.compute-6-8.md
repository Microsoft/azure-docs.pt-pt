---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f5b70c5da99fb4f8db32b234c032c13bd4585a0d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107510971"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os controlos de aplicação adaptativos para definir aplicações seguras devem ser ativados nas suas máquinas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Ative os controlos de aplicação para definir a lista de aplicações seguras conhecidas em execução nas suas máquinas e alerte-o quando outras aplicações são executadas. Isto ajuda a endurecer as suas máquinas contra malware. Para simplificar o processo de configuração e manutenção das suas regras, o Security Center utiliza machine learning para analisar as aplicações em funcionamento em cada máquina e sugerir a lista de aplicações conhecidas e seguras. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

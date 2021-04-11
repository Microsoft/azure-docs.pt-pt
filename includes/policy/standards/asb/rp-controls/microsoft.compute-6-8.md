---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e23274da02dcc7ae8c9835e0f70549c2059ee31c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "107284934"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os controlos de aplicação adaptativos para definir aplicações seguras devem ser ativados nas suas máquinas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Ative os controlos de aplicação para definir a lista de aplicações seguras conhecidas em execução nas suas máquinas e alerte-o quando outras aplicações são executadas. Isto ajuda a endurecer as suas máquinas contra malware. Para simplificar o processo de configuração e manutenção das suas regras, o Security Center utiliza machine learning para analisar as aplicações em funcionamento em cada máquina e sugerir a lista de aplicações conhecidas e seguras. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

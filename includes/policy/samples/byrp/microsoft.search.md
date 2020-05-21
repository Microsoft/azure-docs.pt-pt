---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 929869c1aff759c9ba4c13c736e000bcc53032b4
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658962"
---
|Name |Descrição |Efeitos(s) |Versão |GitHub |
|---|---|---|---|---|
|[Implementar definições de diagnóstico para serviços de pesquisa para o centro de eventos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d5da587-71bd-41f5-ac95-dd3330c2d58d) |Implementa as definições de diagnóstico dos Serviços de Pesquisa para transmitir para um Centro regional de Eventos quando quaisquer Serviços de Pesquisa que não estão presentes nesta definição de diagnóstico são criados ou atualizados. |ImplementaçãoIfNotExists, Desativado |2.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Search_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Implementar definições de diagnóstico para serviços de pesquisa para registar espaço de trabalho de Analytics](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08ba64b8-738f-4918-9686-730d2ed79c7d) |Implementa as definições de diagnóstico dos Serviços de Pesquisa para transmitir para um espaço de trabalho regional log Analytics quando quaisquer Serviços de Pesquisa que não estão a ser criados ou atualizados. |ImplementaçãoIfNotExists, Desativado |1.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Search_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Os registos de diagnóstico nos serviços de pesquisa devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Auditoria que permite registos de diagnóstico. Isto permite-lhe recriar trilhas de atividade para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditoriaIfNotExists, Deficiente |2.0.0 |[Ligação](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |

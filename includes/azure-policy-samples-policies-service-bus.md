---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: 993c1430757832b128266075105ed9447796f4a8
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192642"
---
|Nome |Descrição |Efeitos(s) |Versão |
|---|---|---|---|
|[Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do espaço de nome do Autocarro de Serviço](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json) |Os clientes do Service Bus não devem utilizar uma política de acesso de nível de espaço de nomes que fornece acesso a todas as filas e tópicos num espaço de nomes. Para se alinhar com o modelo de segurança menos privilegiado, deve criar políticas de acesso ao nível da entidade para filas e tópicos para fornecer acesso apenas à entidade específica |Auditoria, Negação, Deficientes |1.0.1 |
|[Os registos de diagnóstico no Ônibus de Serviço devem ser ativados](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |Auditoria que permite registos de diagnóstico. Isto permite-lhe recriar trilhas de atividade para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditoriaIfNotExists, Deficiente |2.0.0 |

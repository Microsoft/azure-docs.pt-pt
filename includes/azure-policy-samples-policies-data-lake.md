---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: 32cacb221f67a71cb2f3a15162712bf00897a26c
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193054"
---
|Nome |Descrição |Efeitos(s) |Versão |
|---|---|---|---|
|[Os registos de diagnóstico na Azure Data Lake Store devem ser ativados](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |Auditoria que permite registos de diagnóstico. Isto permite-lhe recriar trilhas de atividade para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditoriaIfNotExists, Deficiente |2.0.0 |
|[Os registos de diagnóstico no Data Lake Analytics devem ser ativados](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |Auditoria que permite registos de diagnóstico. Isto permite-lhe recriar trilhas de atividade para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditoriaIfNotExists, Deficiente |2.0.0 |
|[Exigir encriptação nas contas da Data Lake Store](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |Esta política garante que a encriptação está ativada em todas as contas da Data Lake Store |negar |1.0.0 |

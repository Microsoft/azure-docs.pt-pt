---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: 7b2179c0a924f7fc29aa70ff748d435e664980ae
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192631"
---
|Nome |Descrição |Efeitos(s) |Versão |
|---|---|---|---|
|[Implementar definições de diagnóstico para o cofre de chaves para o centro de eventos](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_DiagnosticLog_Deploy.json) |Implementa as definições de diagnóstico para o Key Vault transmitir para um Hub de Eventos regionais quando qualquer Cofre chave que esteja em falta nesta definição de diagnóstico é criado ou atualizado. |implementarIfNotExists |1.0.0 |
|[Os registos de diagnóstico no Cofre-Chave devem ser ativados](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |Auditoria que permite registos de diagnóstico. Isto permite-lhe recriar trilhas de atividade para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditoriaIfNotExists, Deficiente |1.0.0 |
|[Objetos do cofre da chave devem ser recuperáveis](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |Esta política audita se os objetos chave do cofre não forem recuperáveis. A função Soft Delete ajuda a manter eficazmente os recursos durante um determinado período de retenção (90 dias) mesmo após uma operação DELETE, ao mesmo tempo que dá a aparência de que o objeto é eliminado. Quando a "proteção da purga" estiver em funcionação, um cofre ou um objeto em estado apagado não podem ser purgados até que o período de retenção de 90 dias tenha passado. Estes cofres e objetos ainda podem ser recuperados, garantindo aos clientes que a política de retenção será seguida. |Auditoria, Deficientes |1.0.0 |

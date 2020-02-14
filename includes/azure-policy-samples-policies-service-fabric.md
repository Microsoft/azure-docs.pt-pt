---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: 221633fcb459e39a4c11e869b9214d985cd5ef0f
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192862"
---
|Nome |Descrição |Efeitos(s) |Versão |
|---|---|---|---|
|[Os clusters de tecido de serviço devem ter a propriedade ClusterProtectionLevel definida para encriptar AndSign](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |O Service Fabric fornece três níveis de proteção (nenhum, início de sessão e EncryptAndSign) para a comunicação de nó para nó a utilizar um certificado de cluster principal. Desestabeleça o nível de proteção para garantir que todas as mensagens de nós-a-nó são encriptadas e assinadas digitalmente |Auditoria, Deficientes |1.0.0 |
|[Os clusters de tecido de serviço só devem utilizar o Diretório Ativo Azure para a autenticação do cliente](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |Utilização auditada da autenticação do cliente apenas via Diretório Ativo Azure em Tecido de Serviço |Auditoria, Deficientes |1.0.0 |

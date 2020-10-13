---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: befe81f1c5d40e3bab8c7f0d02ff993f0217549e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91818695"
---
|Nome<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Definição de aplicação para Aplicação Gerida deve usar conta de armazenamento fornecida pelo cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633) |Utilize a sua própria conta de armazenamento para controlar os dados de definição de aplicação quando este é um requisito regulamentar ou de conformidade. Pode optar por armazenar a definição de aplicação gerida dentro de uma conta de armazenamento fornecida por si durante a criação, para que a sua localização e acesso possam ser totalmente geridos por si para cumprir os requisitos de conformidade regulamentar. |auditoria, negação, desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[Implementar associações para uma aplicação gerida](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |Implementa um recurso de associação que associa tipos de recursos selecionados à aplicação gerida especificada.  Esta implementação de políticas não suporta tipos de recursos aninhados. |implementarIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |

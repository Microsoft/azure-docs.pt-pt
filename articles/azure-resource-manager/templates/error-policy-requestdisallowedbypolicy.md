---
title: Erro RequestDisallowedByPolicy
description: Descreve a causa do erro RequestDisallowedByPolicy ao implementar recursos com o Gestor de Recursos Azure.
author: genlin
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 41581ba48da2f2e717c5abf2a749f8fd2b86ac06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75477671"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Erro solicitadoPorpolítica com a política de recursos do Azure

Este artigo descreve a causa do erro RequestDisallowedByPolicy, fornece também solução para este erro.

## <a name="symptom"></a>Sintoma

Durante a implementação, poderá receber um erro **RequestDisallowedByPolicy** que o impeça de criar os recursos. O exemplo que se segue mostra o erro:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Resolução de problemas

Para obter detalhes sobre a política que bloqueou a sua implantação, utilize um dos seguintes métodos:

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Na PowerShell, forneça esse identificador de política como parâmetro `Id` para recuperar detalhes sobre a política que bloqueou a sua implantação.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>CLI do Azure

No Azure CLI, forneça o nome da definição de política:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Solução

Para segurança ou conformidade, os seus administradores de subscrição podem atribuir políticas que limitem a forma como os recursos são implantados. Por exemplo, a sua subscrição pode ter uma política que impede a criação de endereços IP públicos, Grupos de Segurança de Rede, Rotas Definidas pelo Utilizador ou tabelas de rotas. A mensagem de erro na secção **Sintomas** mostra o nome da apólice.
Para resolver este problema, reveja as políticas de recursos e determine como implementar recursos que cumpram essas políticas.

Para obter mais informações, veja os artigos seguintes:

- [O que é o Azure Policy?](../../governance/policy/overview.md)
- [Criar e gerir políticas para impor a conformidade](../../governance/policy/tutorials/create-and-manage.md)

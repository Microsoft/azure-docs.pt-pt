---
title: Erro RequestDisallowedByPolicy
description: Descreve a causa do erro de Política de Solicitação ao implementar recursos com o Gestor de Recursos Azure.
author: genlin
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 41581ba48da2f2e717c5abf2a749f8fd2b86ac06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75477671"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Pedido Erro de Segurança Na Política com a política de recursos da Azure

Este artigo descreve a causa do erro de Política Dedesprovativo do Pedido, fornecendo também uma solução para este erro.

## <a name="symptom"></a>Sintoma

Durante a implementação, poderá receber um erro **de Política** Preventiva solicitado que o impeça de criar os recursos. O exemplo a seguir mostra o erro:

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

No PowerShell, forneça o identificador de política como o `Id` parâmetro para recuperar detalhes sobre a política que bloqueou a sua implantação.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>CLI do Azure

No Azure CLI, forneça o nome da definição de política:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Solução

Para segurança ou conformidade, os seus administradores de subscrição podem atribuir políticas que limitam a forma como os recursos são implantados. Por exemplo, a sua subscrição pode ter uma política que impede a criação de endereços IP públicos, grupos de segurança de rede, User-Defined Rotas ou tabelas de rotas. A mensagem de erro na secção **Sintomas** mostra o nome da apólice.
Para resolver este problema, reveja as políticas de recursos e determine como implementar recursos que cumpram essas políticas.

Para obter mais informações, veja os seguintes artigos:

- [O que é o Azure Policy?](../../governance/policy/overview.md)
- [Criar e gerir políticas para impor a conformidade](../../governance/policy/tutorials/create-and-manage.md)

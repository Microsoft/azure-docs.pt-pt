---
title: Erro de RequestDisallowedByPolicy
description: Descreve a causa do erro RequestDisallowedByPolicy ao implantar recursos com Azure Resource Manager.
author: genlin
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 41581ba48da2f2e717c5abf2a749f8fd2b86ac06
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477671"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Erro de RequestDisallowedByPolicy com a política de recursos do Azure

Este artigo descreve a causa do erro RequestDisallowedByPolicy, que também fornece a solução para esse erro.

## <a name="symptom"></a>Sintoma

Durante a implantação, você pode receber um erro **RequestDisallowedByPolicy** que impede a criação dos recursos. O exemplo a seguir mostra o erro:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Resolução de problemas

Para recuperar detalhes sobre a política que bloqueou a implantação, use um dos métodos a seguir:

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

No PowerShell, forneça esse identificador de política como o parâmetro `Id` para recuperar detalhes sobre a política que bloqueou a implantação.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>CLI do Azure

Em CLI do Azure, forneça o nome da definição de política:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Solução

Para segurança ou conformidade, os administradores de assinatura podem atribuir políticas que limitam como os recursos são implantados. Por exemplo, sua assinatura pode ter uma política que impede a criação de endereços IP públicos, grupos de segurança de rede, rotas definidas pelo usuário ou tabelas de rotas. A mensagem de erro na seção **sintomas** mostra o nome da política.
Para resolver esse problema, examine as políticas de recursos e determine como implantar recursos que estão em conformidade com essas políticas.

Para obter mais informações, veja os artigos seguintes:

- [O que é Azure Policy?](../../governance/policy/overview.md)
- [Criar e gerir políticas para impor a conformidade](../../governance/policy/tutorials/create-and-manage.md)

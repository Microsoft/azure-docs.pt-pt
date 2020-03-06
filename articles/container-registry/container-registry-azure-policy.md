---
title: Conformidade usando a política azure
description: Atribuir políticas incorporadas na Política Azure para auditar o cumprimento dos seus registos de contentores Azure
ms.topic: article
ms.date: 02/26/2020
ms.openlocfilehash: 012cd013de1c60fddcfb28e4bca96d761ada41ab
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330741"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Auditoria cumprimento dos registos de contentores azure utilizando a Política Azure

[A Azure Policy](../governance/policy/overview.md) é um serviço em Azure que utiliza para criar, atribuir e gerir políticas. Estas políticas impõem diferentes regras e efeitos aos recursos, de forma a que esses recursos se mantenham em conformidade com as normas empresariais e os contratos de nível de serviço.

Este artigo introduz políticas incorporadas (pré-visualização) para o Registo de Contentores Azure. Utilize estas políticas para auditar registos novos e existentes para o cumprimento.

Não há acusações por usar a Política Azure.

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="built-in-policy-definitions"></a>Definições políticas incorporadas

As seguintes definições políticas incorporadas são específicas do Registo de Contentores Azure:

[!INCLUDE [azure-policy-samples-policies-container-registry](../../includes/azure-policy-samples-policies-container-registry.md)]

Consulte também a definição de política de rede incorporada: [[Pré-visualização] O registo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78)de contentores deve utilizar um ponto final do serviço de rede virtual .

## <a name="assign-policies"></a>Políticas de atribuição

* Atribuir políticas utilizando o [portal Azure,](../governance/policy/assign-policy-portal.md) [Azure CLI,](../governance/policy/assign-policy-azurecli.md)um modelo de Gestor de [Recursos,](../governance/policy/assign-policy-template.md)ou os SDKs de Política Azure.
* Scope a policy assignment para um grupo de recursos, uma subscrição ou um grupo de [gestão Azure](../governance/management-groups/overview.md). As atribuições de política de registo de contentores aplicam-se aos registos existentes e novos de contentores no âmbito do âmbito.
* Ativar ou desativar [a aplicação da política](../governance/policy/concepts/assignment-structure.md#enforcement-mode) a qualquer momento.

> [!NOTE]
> Depois de atribuir ou atualizar uma apólice, leva algum tempo para que a atribuição seja aplicada aos recursos no âmbito definido. Consulte informações sobre os gatilhos de [avaliação de políticas](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Rever o cumprimento da política

Aceda às informações de conformidade geradas pelas suas atribuições políticas utilizando o portal Azure, ferramentas de linha de comando Azure ou os SDKs de Política Azure. Para mais detalhes, consulte [Obter dados de conformidade dos recursos do Azure.](../governance/policy/how-to/get-compliance-data.md)

Quando um recurso não é compatível, existem muitas razões possíveis. Para determinar a razão ou para encontrar a mudança responsável, consulte determinar o [incumprimento](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Conformidade política no portal:

1. Selecione **todos os serviços**e procure **Política.**
1. Selecione **Compliance**.
1. Utilize os filtros para limitar os estados de conformidade ou para procurar políticas ![conformidade política no portal](./media/container-registry-azure-policy/azure-policy-compliance.png).
1. Selecione uma política para rever detalhes e eventos agregados de conformidade. Se desejar, selecione um registo específico para a conformidade com os recursos.

### <a name="policy-compliance-in-the-azure-cli"></a>Conformidade política no Azure CLI

Também pode utilizar o Azure CLI para obter dados de conformidade. Por exemplo, utilize o comando da [lista de atribuição de políticas az](/cli/azure/policy/assignment#az-policy-assignment-list) no CLI para obter as identificações políticas das políticas de registo de contentores azure que são aplicadas:

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

Resultado do exemplo:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
[Preview]: Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

Em [seguida,](/cli/azure/policy/state#az-policy-state-list) executar a lista de estado de política az para devolver o estado de conformidade formado jSON para todos os recursos sob uma identificação de política específica:

```azurecli
az policy state list \
  --resource <policyID>
```

Ou executar a lista de estado de [política az](/cli/azure/policy/state#az-policy-state-list) para devolver o estado de conformidade formado pela JSON de um recurso específico de registo, como o *meu registo:*

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [definições](../governance/policy/concepts/definition-structure.md) e [efeitos](../governance/policy/concepts/effects.md) da Política Azure

* Criar uma [definição de política personalizada](../governance/policy/tutorials/create-custom-policy-definition.md)

* Saiba mais sobre [as capacidades](../governance/index.yml) de governação em Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
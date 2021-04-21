---
title: Conformidade usando a Política Azure
description: Atribua políticas incorporadas na Política Azure para auditar o cumprimento dos seus recursos de Serviço Azure SignalR.
author: JialinXin
ms.service: signalr
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: jixin
ms.openlocfilehash: c8776102602f5bdcf29139d808a6f603cc5c7473
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784578"
---
# <a name="audit-compliance-of-azure-signalr-service-resources-using-azure-policy"></a>Conformidade de auditoria dos recursos do Serviço Azure SignalR utilizando a Política Azure

[A Azure Policy](../governance/policy/overview.md) é um serviço em Azure que usa para criar, atribuir e gerir políticas. Estas políticas impõem diferentes regras e efeitos aos recursos, de forma a que esses recursos se mantenham em conformidade com as normas empresariais e os contratos de nível de serviço.

Este artigo introduz políticas incorporadas (pré-visualização) para o Serviço Azure SignalR. Utilize estas políticas para auditar novos recursos signalr existentes para o cumprimento.

Não há acusações por usar a Política Azure.

## <a name="built-in-policy-definitions"></a>Definições políticas incorporadas

As seguintes definições de política incorporadas são específicas do Serviço Azure SignalR:

[!INCLUDE [azure-policy-reference-policies-signalr](../../includes/policy/reference/bycat/policies-signalr.md)]

## <a name="assign-policy-definitions"></a>Atribuir definições de política

* Atribua definições de política utilizando o [portal Azure](../governance/policy/assign-policy-portal.md), [Azure CLI,](../governance/policy/assign-policy-azurecli.md)um [modelo de Gestor de Recursos](../governance/policy/assign-policy-template.md)ou os SDKs de política Azure.
* Âmbito de uma atribuição de políticas a um grupo de recursos, a uma subscrição ou a um [grupo de gestão Azure](../governance/management-groups/overview.md). As atribuições de política signalR aplicam-se aos recursos sinalizadores existentes e novos dentro do âmbito de aplicação.
* Ativar ou desativar [a aplicação da política a](../governance/policy/concepts/assignment-structure.md#enforcement-mode) qualquer momento.

> [!NOTE]
> Depois de atribuir ou atualizar uma apólice, leva algum tempo para que a atribuição seja aplicada aos recursos no âmbito definido. Consulte informações sobre [os gatilhos de avaliação de políticas](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Rever cumprimento da política

Aceder a informações de conformidade geradas pelas suas atribuições de política utilizando o portal Azure, ferramentas de linha de comando Azure ou os SDKs de política Azure. Para mais informações, consulte [obter dados de conformidade dos recursos da Azure.](../governance/policy/how-to/get-compliance-data.md)

Quando um recurso não é conforme, existem muitas razões possíveis. Para determinar a razão ou para encontrar a alteração responsável, consulte [determinar o incumprimento](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Conformidade da política no portal:

1. Selecione **Todos os serviços** e procure **por Política.**
1. Selecione **Compliance**.
1. Utilize os filtros para limitar os estados de conformidade ou para procurar políticas
   
    [![Conformidade de política no portal ](./media/signalr-howto-azure-policy/azure-policy-compliance.png)](./media/signalr-howto-azure-policy/azure-policy-compliance.png#lightbox)
2. Selecione uma política para rever detalhes e eventos de conformidade agregados. Se desejar, selecione um SignalR específico para a conformidade com os recursos.

### <a name="policy-compliance-in-the-azure-cli"></a>Conformidade da política no Azure CLI

Também pode utilizar o CLI Azure para obter dados de conformidade. Por exemplo, utilize o comando da [lista de atribuição de políticas az](/cli/azure/policy/assignment#az_policy_assignment_list) no CLI para obter os IDs de política das políticas do Serviço Azure SignalR que são aplicados:

```azurecli
az policy assignment list --query "[?contains(displayName,'SignalR')].{name:displayName, ID:id}" --output table
```

Resultado do exemplo:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure SignalR Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

Em [seguida, executar a lista de estados de política az](/cli/azure/policy/state#az_policy_state_list) para devolver o estado de conformidade formatado json para todos os recursos sob um grupo de recursos específico:

```azurecli
az policy state list --g <resourceGroup>
```

Ou executar [a lista de estado de política az](/cli/azure/policy/state#az_policy_state_list) para devolver o estado de conformidade formatado pelo JSON de um recurso signalr específico:

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/SignalR/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [definições](../governance/policy/concepts/definition-structure.md) e [efeitos](../governance/policy/concepts/effects.md) da Política Azure

* Criar uma [definição de política personalizada](../governance/policy/tutorials/create-custom-policy-definition.md)

* Saiba mais sobre [as capacidades de governação](../governance/index.yml) em Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
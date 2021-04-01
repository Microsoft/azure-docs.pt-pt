---
title: 'Quickstart: Nova atribuição de políticas com a REST API'
description: Neste quickstart, você usa a API REST para criar uma atribuição de Política Azure para identificar recursos não conformes.
ms.date: 01/29/2021
ms.topic: quickstart
ms.openlocfilehash: 438d8004cd50e6e2ef7586c51adc63257f37978b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99219982"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-rest-api"></a>Quickstart: Criar uma atribuição de política para identificar recursos não conformes com a API REST

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos.
Este início rápido acompanha-o ao longo do processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão a utilizar discos geridos.

No final deste processo, vai identificar com êxito as máquinas virtuais que não estão a utilizar discos geridos. Estão em _não conformidade_ com a atribuição de política.

REST A API é usada para criar e gerir recursos Azure. Este guia utiliza a API REST para criar uma atribuição de políticas e para identificar recursos não conformes no seu ambiente Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Se ainda não o fez, instale [o ARMClient](https://github.com/projectkudu/ARMClient). É uma ferramenta que envia pedidos HTTP para AZure Resource Manager-based REST APIs. Também pode utilizar a função "Try It" na documentação REST ou ferramentas como [a Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) ou [o Carteiro](https://www.postman.com)da PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste arranque rápido, cria-se uma atribuição de política e atribui os **VMs de auditoria que não utilizam discos geridos** `06a78e20-9358-41c9-923c-fb736d382a4d` () definição. Esta definição de política identifica recursos que não estão em conformidade com as condições definidas na definição de política.

Execute o seguinte comando para criar uma atribuição de política:

   - URI da API REST

     ```http
     PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
     ```

   - Corpo do Pedido

     ```json
     {
       "properties": {
         "displayName": "Audit VMs without managed disks Assignment",
         "description": "Shows all virtual machines not using managed disks",
         "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d",
         "nonComplianceMessages": [
             {
                 "message": "Virtual machines should use a managed disk"
             }
         ]
       }
     }
     ```

O organismo final e de pedido anteriores utiliza as seguintes informações:

REPOUSO API URI:
- **Âmbito** – Um âmbito determina que recursos ou agrupamento de recursos em que a atribuição de política é imposta. Pode ir de um grupo de gestão a um recurso individual. Certifique-se de que `{scope}` substitui por um dos seguintes padrões:
  - Grupo de gestão: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Subscrição: `/subscriptions/{subscriptionId}`
  - Grupo de recursos: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Recurso: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]{resourceType}/{resourceName}`
- **Nome** – O nome real da atribuição. Neste exemplo, foi utilizado _audit-vm-manageddisks_.

Corpo de Pedido:
- **DisplayName** – O nome da atribuição de política a apresentar. Neste caso, está a utilizar _VMs de auditoria sem serviço de discos geridos_.
- **Descrição** - Uma explicação mais profunda do que a apólice faz ou por que é atribuída a este âmbito.
- **policyDefinitionId** – O ID de definição de política, baseado no qual está a usar para criar a atribuição. Neste caso, é o ID de definição de política _Audit VMs que não usam discos geridos_.
- **NonComplianceMessages** - Desconfiem a mensagem vista quando um recurso é negado devido a incumprimento ou avaliado para não ser conforme. Para obter mais informações, consulte [as mensagens de incumprimento da atribuição](./concepts/assignment-structure.md#non-compliance-messages).

## <a name="identify-non-compliant-resources"></a>Identificar recursos que não estão em conformidade

Para visualizar os recursos que não estão em conformidade com esta nova atribuição, executar o seguinte comando para obter os IDs de recursos dos recursos não conformes que são output em um ficheiro JSON:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'&$apply=groupby((ResourceId))"
```

Os resultados assemelham-se ao seguinte exemplo:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3Id>"
        }

    ]
}
```

Os resultados são comparáveis aos que normalmente vê listados em **recursos que não estão em conformidade** na vista do portal do Azure.

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a atribuição criada, utilize o seguinte comando:

```http
DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
```

`{scope}`Substitua-o pelo âmbito utilizado quando criou a atribuição de política pela primeira vez.

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, atribuiu uma definição de política para identificar recursos incompatíveis no seu ambiente do Azure.

Para saber mais sobre a atribuição de políticas para validar que os novos recursos estão em conformidade, continue ao tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)

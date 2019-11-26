---
title: 'Quickstart: New policy assignment with Azure CLI'
description: In this quickstart, you use Azure CLI to create an Azure Policy assignment to identify non-compliant resources.
ms.date: 11/25/2019
ms.topic: quickstart
ms.openlocfilehash: 80dbccdb728da94d9f9fdd0aeb506ade40fd7394
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482632"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>Quickstart: Create a policy assignment to identify non-compliant resources with Azure CLI

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos.
Este início rápido acompanha-o ao longo do processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão a utilizar discos geridos.

No final deste processo, vai identificar com êxito as máquinas virtuais que não estão a utilizar discos geridos. Estão em _não conformidade_ com a atribuição de política.

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia utiliza a CLI do Azure para criar uma atribuição de política para identificar recursos que não estão em conformidade no seu ambiente do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- This quickstart requires that you run Azure CLI version 2.0.76 or later to install and use the CLI locally. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

- Register the Azure Policy Insights resource provider using Azure CLI. Registar o fornecedor de recursos assegura que a sua subscrição funciona com o mesmo. To register a resource provider, you must have permission to the register resource provider operation. Esta operação está incluída nas funções de Contribuinte e Proprietário. Execute o seguinte comando para registar o fornecedor de recursos:

  ```azurecli-interactive
  az provider register --namespace 'Microsoft.PolicyInsights'
  ```

  Para obter mais informações sobre como registar e ver os fornecedores de recursos, veja [Resource Providers and Types](../../azure-resource-manager/resource-manager-supported-services.md) (Fornecedores e Tipos de Recursos)

- Se ainda não o fez, instale o [ARMClient](https://github.com/projectkudu/ARMClient). É uma ferramenta que envia pedidos de HTTP para APIs baseadas no Azure Resource Manager.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste início rápido, vai criar uma atribuição de política e atribuir a definição **VMs de Auditoria que não utilizam discos geridos**. This policy definition identifies resources that aren't compliant to the conditions set in the policy definition.

Execute o seguinte comando para criar uma atribuição de política:

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

O comando anterior utiliza as seguintes informações:

- **Nome** – O nome real da atribuição. Neste exemplo, foi utilizado _audit-vm-manageddisks_.
- **DisplayName** – O nome da atribuição de política a apresentar. In this case, you're using _Audit VMs without managed disks Assignment_.
- **Política** – O ID de definição de política, com base no qual está a utilizar para criar a atribuição. In this case, it's the ID of policy definition _Audit VMs that do not use managed disks_. Para obter o ID de definição de política, execute este comando: `az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`
- **Âmbito** – Um âmbito determina que recursos ou agrupamento de recursos em que a atribuição de política é imposta. Pode ir de uma subscrição aos grupos de recursos. Não se esqueça de substituir &lt;âmbito&gt; pelo nome do seu grupo de recursos.

## <a name="identify-non-compliant-resources"></a>Identificar recursos não compatíveis

Para ver os recursos que não estão em conformidade com esta nova atribuição, obtenha o ID de atribuição de política ao executar os seguintes comandos:

```azurepowershell-interactive
$policyAssignment = Get-AzPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs without managed disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

For more information about policy assignment IDs, see [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment).

Em seguida, execute o seguinte comando para obter os ID dos recursos que não estão em conformidade produzidos num ficheiro de JSON:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
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
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

Os resultados são comparáveis aos que normalmente vê listados em **recursos que não estão em conformidade** na vista do portal do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

To remove the assignment created, use the following command:

```azurecli-interactive
az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, atribuiu uma definição de política para identificar recursos incompatíveis no seu ambiente do Azure.

To learn more about assigning policies to validate that new resources are compliant, continue to the tutorial for:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)
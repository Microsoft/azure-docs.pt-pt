---
title: Criar uma política para recursos não compatíveis com a CLI do Azure
description: Utilize a CLI do Azure para criar uma atribuição de política do Azure para identificar recursos incompatíveis.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: e30308ac2cda643cc0157f5e718157f6599751d6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59283550"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>Criar uma atribuição de política para identificar recursos incompatíveis com a CLI do Azure

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos.
Este início rápido acompanha-o ao longo do processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão a utilizar discos geridos.

No final deste processo, vai identificar com êxito as máquinas virtuais que não estão a utilizar discos geridos. Estão em *não conformidade* com a atribuição de política.

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia utiliza a CLI do Azure para criar uma atribuição de política para identificar recursos que não estão em conformidade no seu ambiente do Azure.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

O início rápido requer que execute a versão 2.0.4 da CLI do Azure ou posterior para instalar a utilizar a CLI local. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Pré-requisitos

Utilize a CLI do Azure para registar o fornecedor de recursos do Policy Insights. Registar o fornecedor de recursos assegura que a sua subscrição funciona com o mesmo. Para registar um fornecedor de recursos, tem de ter permissão para a operação de fornecedor de recursos de registo. Esta operação está incluída nas funções de Contribuinte e Proprietário. Execute o seguinte comando para registar o fornecedor de recursos:

```azurecli-interactive
az provider register --namespace 'Microsoft.PolicyInsights'
```

Para obter mais informações sobre como registar e ver os fornecedores de recursos, veja [Resource Providers and Types](../../azure-resource-manager/resource-manager-supported-services.md) (Fornecedores e Tipos de Recursos)

Se ainda não o fez, instale o [ARMClient](https://github.com/projectkudu/ARMClient). É uma ferramenta que envia pedidos de HTTP para APIs baseadas no Azure Resource Manager.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste início rápido, vai criar uma atribuição de política e atribuir a definição **VMs de Auditoria que não utilizam discos geridos**. Esta definição de política identifica os recursos que não estão em conformidade para as condições definidas na definição de política.

Execute o seguinte comando para criar uma atribuição de política:

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

O comando anterior utiliza as seguintes informações:

- **Nome** – O nome real da atribuição.  Neste exemplo, foi utilizado *audit-vm-manageddisks*.
- **DisplayName** – O nome da atribuição de política a apresentar. Neste caso, está usando *VMs de auditoria sem discos geridos atribuição*.
- **Política** – O ID de definição de política, com base no qual está a utilizar para criar a atribuição. Neste caso, é o ID de definição de política *VMs de auditoria que não utilizam discos geridos*. Para obter o ID de definição de política, execute este comando: `az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`
- **Âmbito** – Um âmbito determina que recursos ou agrupamento de recursos em que a atribuição de política é imposta. Pode ir de uma subscrição aos grupos de recursos. Não se esqueça de substituir &lt;âmbito&gt; pelo nome do seu grupo de recursos.

## <a name="identify-non-compliant-resources"></a>Identificar recursos não compatíveis

Para ver os recursos que não estão em conformidade com esta nova atribuição, obtenha o ID de atribuição de política ao executar os seguintes comandos:

```azurepowershell-interactive
$policyAssignment = Get-AzPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs without managed disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

Para obter mais informações sobre IDs de atribuição de política, consulte [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment).

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

Para remover a atribuição de criado, utilize o seguinte comando:

```azurecli-interactive
az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Passos Seguintes

Neste guia de introdução, atribuiu uma definição de política para identificar recursos incompatíveis no seu ambiente do Azure.

Para saber mais sobre a atribuição de políticas para validar que os novos recursos estão em conformidade, avance para o tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)
---
title: 'Quickstart: Nova atribuição de política com o Azure CLI'
description: Neste arranque rápido, utiliza o Azure CLI para criar uma missão da Política Azure para identificar recursos não conformes.
ms.date: 01/11/2020
ms.topic: quickstart
ms.openlocfilehash: b1d93cdb4dfa1c82e8004af4052c22bba60f3a3a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745747"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>Quickstart: Criar uma atribuição de políticas para identificar recursos não conformes com o Azure CLI

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos.
Este início rápido acompanha-o ao longo do processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão a utilizar discos geridos.

No final deste processo, vai identificar com êxito as máquinas virtuais que não estão a utilizar discos geridos. Estão em _não conformidade_ com a atribuição de política.

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia utiliza a CLI do Azure para criar uma atribuição de política para identificar recursos que não estão em conformidade no seu ambiente do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Este quickstart requer que execute a versão 2.0.76 do Azure CLI ou posteriormente para instalar e utilizar o CLI localmente. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

- Registe o fornecedor de recursos Azure Policy Insights utilizando o Azure CLI. Registar o fornecedor de recursos assegura que a sua subscrição funciona com o mesmo. Para registar um fornecedor de recursos, deve ter permissão para a operação do fornecedor de recursos de registo. Esta operação está incluída nas funções de Contribuinte e Proprietário. Execute o seguinte comando para registar o fornecedor de recursos:

  ```azurecli-interactive
  az provider register --namespace 'Microsoft.PolicyInsights'
  ```

  Para obter mais informações sobre como registar e ver os fornecedores de recursos, veja [Resource Providers and Types](../../azure-resource-manager/management/resource-providers-and-types.md) (Fornecedores e Tipos de Recursos)

- Se ainda não o fez, instale o [ARMClient](https://github.com/projectkudu/ARMClient). É uma ferramenta que envia pedidos de HTTP para APIs baseadas no Azure Resource Manager.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste início rápido, vai criar uma atribuição de política e atribuir a definição **VMs de Auditoria que não utilizam discos geridos**. Esta definição de política identifica recursos que não estão em conformidade com as condições estabelecidas na definição de política.

Execute o seguinte comando para criar uma atribuição de política:

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

O comando anterior utiliza as seguintes informações:

- **Nome** – O nome real da atribuição. Neste exemplo, foi utilizado _audit-vm-manageddisks_.
- **DisplayName** – O nome da atribuição de política a apresentar. Neste caso, está a utilizar _VMs de auditoria sem dispor de discos geridos_.
- **Política** – O ID de definição de política, com base no qual está a utilizar para criar a atribuição. Neste caso, é a identificação da definição de política _Audit VMs que não usam discos geridos_. Para obter o ID de definição de política, execute este comando: `az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`
- **Âmbito** – Um âmbito determina que recursos ou agrupamento de recursos em que a atribuição de política é imposta. Pode ir de uma subscrição aos grupos de recursos. Não se esqueça de substituir &lt;âmbito&gt; pelo nome do seu grupo de recursos.

## <a name="identify-non-compliant-resources"></a>Identificar recursos não compatíveis

Para ver os recursos que não estão em conformidade com esta nova atribuição, obtenha o ID de atribuição de política ao executar os seguintes comandos:

```azurecli-interactive
az policy assignment list --query "[?displayName=='Audit VMs without managed disks Assignment'].id"
```

Para obter mais informações sobre identificações de atribuição de políticas, consulte [az policy assignment](/cli/azure/policy/assignment).

Em seguida, execute o seguinte comando para obter os ID dos recursos que não estão em conformidade produzidos num ficheiro de JSON:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
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

Para remover a atribuição criada, utilize o seguinte comando:

```azurecli-interactive
az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, atribuiu uma definição de política para identificar recursos incompatíveis no seu ambiente do Azure.

Para saber mais sobre a atribuição de políticas para validar que os novos recursos estão em conformidade, continue o tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)
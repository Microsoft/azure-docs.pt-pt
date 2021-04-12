---
title: 'Quickstart: Nova atribuição de políticas com ficheiro Bicep (Preview)'
description: Neste arranque rápido, utiliza-se um ficheiro Bicep (Preview) para criar uma atribuição de política para identificar recursos não conformes.
ms.date: 04/01/2021
ms.topic: quickstart
ms.custom: subject-bicepqs
ms.openlocfilehash: 17460f9a06d7225d50933608645ea8aea2f5e8f6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223981"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-bicep-file"></a>Quickstart: Criar uma atribuição de política para identificar recursos não conformes usando um ficheiro Bicep

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos.
Este quickstart passo-o através do processo de utilização de um ficheiro [Bicep (Preview)](https://github.com/Azure/bicep) compilado para um modelo de Gestor de Recursos Azure (modelo ARM) para criar uma atribuição de política para identificar máquinas virtuais que não estão a usar discos geridos. No final deste processo, vai identificar com êxito as máquinas virtuais que não estão a utilizar discos geridos. Estão em _não conformidade_ com a atribuição de política.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo abre-se no portal Azure.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Botão para implantar o modelo ARM para atribuir uma Política de Azure ao Azure." border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
- Versão Bicep `0.3` ou mais alta instalada. Se ainda não tiver CLI Bicep ou precisar de atualizar, consulte [Instalar Bicep (Preview)](../../azure-resource-manager/templates/bicep-install.md).

## <a name="review-the-bicep-file"></a>Reveja o ficheiro Bicep

Neste arranque rápido, cria-se uma atribuição de política e atribui-se uma definição de política incorporada chamada _Audit VMs que não utilizam discos geridos_ ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Para obter uma lista parcial das políticas incorporadas disponíveis, consulte [as amostras da Política Azure](./samples/index.md).

Crie o seguinte ficheiro Bicep `assignment.bicep` como:

```bicep
param policyAssignmentName string = 'audit-vm-manageddisks'
param policyDefinitionID string = '/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d'

resource assignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
    name: policyAssignmentName
    properties: {
        scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
        policyDefinitionId: policyDefinitionID
    }
}

output assignmentId string = assignment.id
```

O recurso definido no ficheiro é:

- [Microsoft.Autorização/políticaAssins de assinaturas](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>Implementar o modelo

> [!NOTE]
> O serviço de política Azure é gratuito. Para mais informações, consulte [a Visão Geral da Política Azure.](./overview.md)

Depois de o Bicep CLI ser instalado e de criar ficheiro, pode implantar o ficheiro Bicep com:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name PolicyDeployment `
  -ResourceGroupName PolicyGroup `
  -TemplateFile assignment.bicep
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name PolicyDeployment \
  --resource-group PolicyGroup \
  --template-file assignment.bicep
```

---

Alguns recursos adicionais:

- Para encontrar mais modelos de amostras, consulte [o modelo Azure Quickstart.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)
- Para ver a referência do modelo, vá à [referência do modelo Azure](/azure/templates/microsoft.authorization/allversions).
- Para aprender a desenvolver modelos ARM, consulte a [documentação do Gestor de Recursos Azure](../../azure-resource-manager/management/overview.md).
- Para obter a implementação ao nível de subscrição, consulte [Criar grupos de recursos e recursos ao nível da subscrição](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Validar a implementação

Selecione **Conformidade** no lado esquerdo da página. Em seguida, localize os _VMs de auditoria que não utilizem_ a atribuição de política de discos geridos que criou.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="Screenshot dos detalhes de conformidade na página De Conformidade de Política." border="false":::

Se houver recursos existentes que não estejam em conformidade com esta nova atribuição, eles aparecem sob **recursos não conformes.**

Para mais informações, consulte [como funciona a conformidade.](./how-to/get-compliance-data.md#how-compliance-works)

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a atribuição criada, siga estes passos:

1. Selecione **Conformidade** (ou **Atribuições**) no lado esquerdo da página Azure Policy e localize a atribuição de política _Auditar VMs que não utilizam discos geridos_ que criou.

1. Clique com o direito nos _VMs de auditoria que não utilizam_ a atribuição de política de discos geridos e selecione a atribuição de **Eliminar**.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Screenshot de usar o menu de contexto para eliminar uma atribuição da página Compliance." border="false":::

1. Apague o `assignment.bicep` ficheiro.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, atribuiu uma definição de política incorporada a um âmbito e avaliou o seu relatório de conformidade. A definição de política valida que todos os recursos no âmbito são compatíveis e identifica quais não são.

Para saber mais sobre a atribuição de políticas para validar que os novos recursos estão em conformidade, continue ao tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)
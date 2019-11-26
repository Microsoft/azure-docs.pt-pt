---
title: 'Quickstart: New policy assignment with templates'
description: In this quickstart, you use a Resource Manager template to create a policy assignment to identify non-compliant resources.
ms.date: 11/25/2019
ms.topic: quickstart
ms.openlocfilehash: 61bffcdeb5d562fe18df98fda091d5d6aa4b4051
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482339"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Quickstart: Create a policy assignment to identify non-compliant resources by using a Resource Manager template

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos.
Este início rápido acompanha-o ao longo do processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão a utilizar discos geridos.

No final deste processo, vai identificar com êxito as máquinas virtuais que não estão a utilizar discos geridos. Estão em _não conformidade_ com a atribuição de política.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

In this quickstart, you create a policy assignment and assign a built-in policy definition called _Audit VMs that do not use managed disks_. For a partial list of available built-in policies, see [Azure Policy samples](./samples/index.md).

There are several methods for creating policy assignments. In this quickstart, you use a [quickstart template](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).
Here is a copy of the template:

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Azure Policy service is free. For more information, see [Overview of Azure Policy](./overview.md).

1. Select the following image to sign in to the Azure portal and open the template:

   [![Deploy the Policy template to Azure](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Select or enter the following values:

   | Nome | Valor |
   |------|-------|
   | Subscrição | Selecione a sua subscrição do Azure. |
   | Grupo de recursos | Select **Create new**, specify a name, and then select **OK**. In the screenshot, the resource group name is _mypolicyquickstart\<Date in MMDD\>rg_. |
   | Localização | Select a region. For example, **Central US**. |
   | Policy Assignment Name | Specify a policy assignment name. You can use the policy definition display if you want. For example, **Audit VMs that do not use managed disks**. |
   | Rg Name | Specify a resource group name where you want to assign the policy to. In this quickstart, use the default value **[resourceGroup().name]** . **[resourceGroup()](../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)** is a template function that retrieves the resource group. |
   | Policy Definition ID | Specify **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**. |
   | I agree to the terms and conditions stated above | (Select) |

1. Selecione **Comprar**.

Some additional resources:

- To find more samples templates, see [Azure Quickstart template](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- To see the template reference, go to [Azure template reference](/azure/templates/microsoft.authorization/allversions).
- To learn how to develop Resource Manager templates, see [Azure Resource Manager documentation](../../azure-resource-manager/resource-group-overview.md).
- To learn subscription-level deployment, see [Create resource groups and resources at the subscription level](../../azure-resource-manager/deploy-to-subscription.md).

## <a name="identify-non-compliant-resources"></a>Identificar recursos não compatíveis

Select **Compliance** in the left side of the page. Then locate the **Audit VMs that do not use managed disks** policy assignment you created.

![Policy compliance overview page](./media/assign-policy-template/policy-compliance.png)

If there are any existing resources that aren't compliant with this new assignment, they appear under **Non-compliant resources**.

For more information, see [How compliance works](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Limpar recursos

To remove the assignment created, follow these steps:

1. Selecione **Conformidade** (ou **Atribuições**) no lado esquerdo da página Azure Policy e localize a atribuição de política **Auditar VMs que não utilizam discos geridos** que criou.

1. Right-click the **Audit VMs that do not use managed disks** policy assignment and select **Delete assignment**.

   ![Delete an assignment from the compliance overview page](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Passos seguintes

In this quickstart, you assigned a built-in policy definition to a scope and evaluated its compliance report. The policy definition validates that all the resources in the scope are compliant and identifies which ones aren't.

To learn more about assigning policies to validate that new resources are compliant, continue to the tutorial for:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)
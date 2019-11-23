---
title: Tutorial - add outputs to template
description: Add outputs to your Azure Resource Manager template to simplify the syntax.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 654d3f7cbf6362d982549c86e6f54fea1e890cfc
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406003"
---
# <a name="tutorial-add-outputs-to-your-resource-manager-template"></a>Tutorial: Add outputs to your Resource Manager template

In this tutorial, you learn how to return a value from your template. You use outputs when you need a value from a deployed resource. This tutorial takes **7 minutes** to complete.

## <a name="prerequisites"></a>Pré-requisitos

We recommend that you complete the [tutorial about variables](template-tutorial-add-variables.md), but it's not required.

You must have Visual Studio Code with the Resource Manager Tools extension, and either Azure PowerShell or Azure CLI. For more information, see [template tools](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Review template

At the end of the previous tutorial, your template had the following JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json)]

It deploys a storage account, but it doesn't return any information about the storage account. You might need to capture properties from a new resource so they're available later for reference.

## <a name="add-outputs"></a>Add outputs

You can use outputs to return values from the template. For example, it might be helpful to get the endpoints for your new storage account.

The following example highlights the change to your template to add an output value. Copy the whole file and replace your template with its contents.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json?range=1-53&highlight=47-52)]

There are some important items to note about the output value you added.

The type of returned value is set to **object**, which means it returns a JSON object.

It uses the [reference](resource-group-template-functions-resource.md#reference) function to get the runtime state of the storage account. To get the runtime state of a resource, you pass in the name or ID of a resource. In this case, you use the same variable you used to create the name of the storage account.

Finally, it returns the **primaryEndpoints** property from the storage account

## <a name="deploy-template"></a>Implementar o modelo

You're ready to deploy the template and look at the returned value.

If you haven't created the resource group, see [Create resource group](template-tutorial-create-first-template.md#create-resource-group). The example assumes you've set the **templateFile** variable to the path to the template file, as shown in the [first tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

In the output for the deployment command, you'll see an object similar to:

```json
{
    "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
    "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
    "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
    "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
    "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
    "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

## <a name="review-your-work"></a>Review your work

You've done a lot in the last six tutorials. Let's take a moment to review what you have done. You created a template with parameters that are easy to provide. The template is reusable in different environments because it allows for customization and dynamically creates needed values. It also returns information about the storage account that you could use in your script.

Now, let's look at the resource group and deployment history.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. From the left menu, select **Resource groups**.
1. Select the resource group you deployed to.
1. Depending on the steps you did, you should have at least one and perhaps several storage accounts in the resource group.
1. You should also have several successful deployments listed in the history. Select that link.

   ![Select deployments](./media/template-tutorial-add-outputs/select-deployments.png)

1. You see all of your deployments in the history. Select the deployment called **addoutputs**.

   ![Show deployment history](./media/template-tutorial-add-outputs/show-history.png)

1. You can review the inputs.

   ![Show inputs](./media/template-tutorial-add-outputs/show-inputs.png)

1. You can review the outputs.

   ![Show outputs](./media/template-tutorial-add-outputs/show-outputs.png)

1. You can review the template.

   ![Show template](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Limpar recursos

If you're moving on to the next tutorial, you don't need to delete the resource group.

If you're stopping now, you might want to clean up the resources you deployed by deleting the resource group.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

In this tutorial, you added a return value to the template. In the next tutorial, you'll learn how to export a template and use parts of that exported template in your template.

> [!div class="nextstepaction"]
> [Use exported template](template-tutorial-export-template.md)

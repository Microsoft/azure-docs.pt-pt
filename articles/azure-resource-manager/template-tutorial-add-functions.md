---
title: Tutorial - add template functions
description: Add template functions to your Azure Resource Manager template to construct values.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 03a38178ec66c1c1a10934975d20778369d80dbe
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405965"
---
# <a name="tutorial-add-template-functions-to-your-resource-manager-template"></a>Tutorial: Add template functions to your Resource Manager template

In this tutorial, you learn how to add [template functions](resource-group-template-functions.md) to your template. You use functions to dynamically construct values. In addition to these system-provided template functions, you can also create [user-defined functions](./template-user-defined-functions.md). This tutorial takes **7 minutes** to complete.

## <a name="prerequisites"></a>Pré-requisitos

We recommend that you complete the [tutorial about parameters](template-tutorial-add-parameters.md), but it's not required.

You must have Visual Studio Code with the Resource Manager Tools extension, and either Azure PowerShell or Azure CLI. For more information, see [template tools](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Review template

At the end of the previous tutorial, your template had the following JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json)]

The location of the storage account is hard-coded to **East US**. However, you may need to deploy the storage account to other regions. You're again facing an issue of your template lacking flexibility. You could add a parameter for location, but it would be great if its default value made more sense than just a hard-coded value.

## <a name="use-function"></a>Use function

If you've completed the previous tutorial in this series, you've already used a function. When you added **"[parameters('storageName')]"** , you used the [parameters](resource-group-template-functions-deployment.md#parameters) function. The brackets indicate that the syntax inside the brackets is a [template expression](template-expressions.md). Resource Manager resolves the syntax rather than treating it as a literal value.

Functions add flexibility to your template by dynamically getting values during deployment. In this tutorial, you use a function to get the location of the resource group you're using for deployment.

The following example highlights the changes to add a parameter called **location**.  The parameter default value calls the [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) function. This function returns an object with information about the resource group being used for deployment. One of the properties on the object is a location property. When you use the default value, the storage account location has the same location as the resource group. The resources inside a resource group don't have to share the same location. You can also provide a different location when needed.

Copy the whole file and replace your template with its contents.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json?range=1-44&highlight=24-27,34)]

## <a name="deploy-template"></a>Implementar o modelo

In the previous tutorials, you created a storage account in East US, but your resource group was created in Central US. For this tutorial, your storage account is created in the same region as the resource group. Use the default value for location, so you don't need to provide that parameter value. You must provide a new name for the storage account because you're creating a storage account in a different location. For example, use **store2** as the prefix instead of **store1**.

If you haven't created the resource group, see [Create resource group](template-tutorial-create-first-template.md#create-resource-group). The example assumes you've set the **templateFile** variable to the path to the template file, as shown in the [first tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

## <a name="verify-deployment"></a>Verificar a implementação

You can verify the deployment by exploring the resource group from the Azure portal.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. From the left menu, select **Resource groups**.
1. Select the resource group you deployed to.
1. You see that a storage account resource has been deployed and has the same location as the resource group.

## <a name="clean-up-resources"></a>Limpar recursos

If you're moving on to the next tutorial, you don't need to delete the resource group.

If you're stopping now, you might want to clean up the resources you deployed by deleting the resource group.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

In this tutorial, you used a function when defining the default value for a parameter. In this tutorial series, you'll continue using functions. By the end of the series, you'll add functions to every section of the template.

> [!div class="nextstepaction"]
> [Add variables](template-tutorial-add-variables.md)

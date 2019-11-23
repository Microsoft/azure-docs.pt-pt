---
title: Tutorial - add tags to resources in template
description: Add tags to resources that you deploy in your Azure Resource Manager template. Tags let you logically organize resources.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 078d85b3120baf5b8bea9c305fb1f97763f8df59
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406070"
---
# <a name="tutorial-add-tags-in-your-resource-manager-template"></a>Tutorial: Add tags in your Resource Manager template

In this tutorial, you learn how to add tags to resources in your template. [Tags](resource-group-using-tags.md) help you logically organize your resources. The tag values show up in cost reports. This tutorial takes **8 minutes** to complete.

## <a name="prerequisites"></a>Pré-requisitos

We recommend that you complete the [tutorial about Quickstart templates](template-tutorial-quickstart-template.md), but it's not required.

You must have Visual Studio Code with the Resource Manager Tools extension, and either Azure PowerShell or Azure CLI. For more information, see [template tools](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Review template

Your previous template deployed a storage account, App Service plan, and web app.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json)]

After deploying these resources, you might need to track costs and find resources that belong to a category. You can add tags to help solve these issues.

## <a name="add-tags"></a>Adicionar etiquetas

You tag resources to add values that help you identify their use. For example, you can add tags that list the environment and the project. You could add tags that identify a cost center or the team that owns the resource. Add any values that make sense for your organization.

The following example highlights the changes to the template. Copy the whole file and replace your template with its contents.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json?range=1-118&highlight=46-52,64,78,100)]

## <a name="deploy-template"></a>Implementar o modelo

It's time to deploy the template and look at the results.

If you haven't created the resource group, see [Create resource group](template-tutorial-create-first-template.md#create-resource-group). The example assumes you've set the **templateFile** variable to the path to the template file, as shown in the [first tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="verify-deployment"></a>Verificar a implementação

You can verify the deployment by exploring the resource group from the Azure portal.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. From the left menu, select **Resource groups**.
1. Select the resource group you deployed to.
1. Select one of the resources, such as the storage account resource. You see that it now has tags.

   ![Show tags](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Limpar recursos

If you're moving on to the next tutorial, you don't need to delete the resource group.

If you're stopping now, you might want to clean up the resources you deployed by deleting the resource group.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

In this tutorial, you added tags to the resources. In the next tutorial, you'll learn how to use parameter files to simplify passing in values to the template.

> [!div class="nextstepaction"]
> [Use parameter file](template-tutorial-use-parameter-file.md)

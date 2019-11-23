---
title: Tutorial - Export template from the Azure portal
description: Learn how to use an exported template to complete your template development.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6c22a8e2b479ed7d3a225bc649d89f5302d0f536
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406017"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Tutorial: Use exported template from the Azure portal

In this tutorial series, you've created a template to deploy an Azure storage account. In the next two tutorials, you add an *App Service plan* and a *website*. Instead of creating templates from scratch, you learn how to export templates from the Azure portal and how to use sample templates from the [Azure Quickstart templates](https://azure.microsoft.com/resources/templates/). You customize those templates for your use. This tutorial focuses on exporting templates, and customizing the result for your template. It takes about **14 minutes** to complete.

## <a name="prerequisites"></a>Pré-requisitos

We recommend that you complete the [tutorial about outputs](template-tutorial-add-outputs.md), but it's not required.

You must have Visual Studio Code with the Resource Manager Tools extension, and either Azure PowerShell or Azure CLI. For more information, see [template tools](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Review template

At the end of the previous tutorial, your template had the following JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json)]

This template works well for deploying storage accounts, but you might want to add more resources to it. You can export a template from an existing resource to quickly get the JSON for that resource.

## <a name="create-app-service-plan"></a>Criar plano do App Service

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Select **Create a resource**.
1. In **Search the Marketplace**, enter **App Service plan**, and then select **App Service plan**.  Don’t select **App Service plan (classic)**
1. Selecione **Criar**.
1. Enter:

    - **Subscrição**: selecione a sua subscrição do Azure.
    - **Resource Group**: Select **Create new** and then specify a name. Provide a different resource group name than the one you have been using in this tutorial series.
    - **Name**: enter a name for the App service plan.
    - **Operating System**: select **Linux**.
    - **Region**: select an Azure location. For example, **Central US**.
    - **Pricing tier**: to save costs, change the SKU to **Basic B1** (under Dev/Test).

    ![Resource Manager template export template portal](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Select **Review and create**.
1. Selecione **Criar**. It takes a few moments to create the resource.

## <a name="export-template"></a>Exportar modelo

1. Select **Go to resource**.

    ![Ir para recurso](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Select **Export template**.

    ![Resource Manager template export template](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   The export template feature takes the current state of a resource and generates a template to deploy it. Exporting a template can be a helpful way of quickly getting the JSON you need to deploy a resource.

1. Copy the **Microsoft.Web/serverfarms** definition and the parameter definition to your template.

    ![Resource Manager template export template exported template](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> Typically, the exported template is more verbose than you might want when creating a template. For example, the SKU object in the exported template has five properties. This template works, but you could just use the **name** property. You can start with the exported template, and then modify it as you like to fit your requirements.

## <a name="revise-existing-template"></a>Revise existing template

The exported template gives you most of the JSON you need, but you need to customize it for your template. Pay particular attention to differences in parameters and variables between your template and the exported template. Obviously, the export process doesn't know the parameters and variables that you've already defined in your template.

The following example highlights the additions to your template. It contains the exported code plus some changes. First, it changes the name of the parameter to match your naming convention. Second, it uses your location parameter for the location of the app service plan. Third, it removes the **name** inside the **properties** object because this value is redundant with the **name** property at the resource level.

Copy the whole file and replace your template with its contents.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json?range=1-77&highlight=28-31,50-69)]

## <a name="deploy-template"></a>Implementar o modelo

Use either Azure CLI or Azure PowerShell to deploy a template.

If you haven't created the resource group, see [Create resource group](template-tutorial-create-first-template.md#create-resource-group). The example assumes you've set the **templateFile** variable to the path to the template file, as shown in the [first tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-deployment"></a>Verificar a implementação

You can verify the deployment by exploring the resource group from the Azure portal.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. From the left menu, select **Resource groups**.
1. Select the resource group you deployed to.
1. The resource group contains a storage account and an App Service plan.

## <a name="clean-up-resources"></a>Limpar recursos

If you're moving on to the next tutorial, you don't need to delete the resource group.

If you're stopping now, you might want to clean up the resources you deployed by deleting the resource group.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

You learned how to export a template from the Azure portal, and how to use the exported template for your template development. You can also use the Azure Quickstart templates to simplify template development.

> [!div class="nextstepaction"]
> [Use Azure Quickstart templates](template-tutorial-quickstart-template.md)

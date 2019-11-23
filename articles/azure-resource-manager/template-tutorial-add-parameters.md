---
title: Tutorial - add parameters to template
description: Add parameters to your Azure Resource Manager template to make it reusable.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 28c171dfa067ec9b3eff2e0d7e5d5dd0a0c274c0
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406076"
---
# <a name="tutorial-add-parameters-to-your-resource-manager-template"></a>Tutorial: Add parameters to your Resource Manager template

In the [previous tutorial](template-tutorial-add-resource.md), you learned how to add a storage account to the template and deploy it. In this tutorial, you learn how to improve the template by adding parameters. This tutorial takes about **14 minutes** to complete.

## <a name="prerequisites"></a>Pré-requisitos

We recommend that you complete the [tutorial about resources](template-tutorial-add-resource.md), but it's not required.

You must have Visual Studio Code with the Resource Manager Tools extension, and either Azure PowerShell or Azure CLI. For more information, see [template tools](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Review template

At the end of the previous tutorial, your template had the following JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json)]

You may have noticed that there's a problem with this template. The storage account name is hard-coded. You can only use this template to deploy the same storage account every time. To deploy a storage account with a different name, you would have to create a new template, which obviously isn't a practical way to automate your deployments.

## <a name="make-template-reusable"></a>Make template reusable

To make your template reusable, let's add a parameter that you can use to pass in a storage account name. The highlighted JSON in the following example shows what changed in your template. The **storageName** parameter is identified as a string. The max length is set to 24 characters to prevent any names that are too long.

Copy the whole file and replace your template with its contents.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json?range=1-26&highlight=4-10,15)]

## <a name="deploy-template"></a>Implementar o modelo

Let's deploy the template. The following example deploys the template with Azure CLI or PowerShell. Notice that you provide the storage account name as one of the values in the deployment command. For the storage account name, provide the same name you used in the previous tutorial.

If you haven't created the resource group, see [Create resource group](template-tutorial-create-first-template.md#create-resource-group). The example assumes you've set the **templateFile** variable to the path to the template file, as shown in the [first tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Understand resource updates

In the previous section, you deployed a storage account with the same name that you had created earlier. You may be wondering how the resource is affected by the redeployment.

If the resource already exists and no change is detected in the properties, no action is taken. If the resource already exists and a property has changed, the resource is updated. If the resource doesn't exist, it's created.

This way of handling updates means your template can include all of the resources you need for an Azure solution. You can safely redeploy the template and know that resources are changed or created only when needed. For example, if you have added files to your storage account, you can redeploy the storage account without losing those files.

## <a name="customize-by-environment"></a>Customize by environment

Os parâmetros permitem-lhe personalizar a implementação, ao fornecer valores que são adaptados para um determinado ambiente. For example, you can pass different values based on whether you're deploying to an environment for development, test, and production.

The previous template always deployed a Standard_LRS storage account. You might want the flexibility to deploy different SKUs depending on the environment. The following example shows the changes to add a parameter for SKU. Copy the whole file and paste over your template.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json?range=1-40&highlight=10-23,32)]

The **storageSKU** parameter has a default value. This value is used when a value isn't specified during the deployment. It also has a list of allowed values. These values match the values that are needed to create a storage account. You don't want users of your template to pass in SKUs that don't work.

## <a name="redeploy-template"></a>Reimplementar o modelo

You're ready to deploy again. Because the default SKU is set to **Standard_LRS**, you don't need to provide a value for that parameter.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

To see the flexibility of your template, let's deploy again. This time set the SKU parameter to **Standard_GRS**. You can either pass in a new name to create a different storage account, or use the same name to update your existing storage account. Both options work.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usedefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name usedefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Finally, let's run one more test and see what happens when you pass in a SKU that isn't one of the allowed values. In this case, we test the scenario where a user of your template thinks **basic** is one of the SKUs.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

The command fails immediately with an error message that states which values are allowed. Resource Manager identifies the error before the deployment starts.

## <a name="clean-up-resources"></a>Limpar recursos

If you're moving on to the next tutorial, you don't need to delete the resource group.

If you're stopping now, you might want to clean up the resources you deployed by deleting the resource group.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

You improved the template created in the [first tutorial](template-tutorial-create-first-template.md) by adding parameters. In the next tutorial, you'll learn about template functions.

> [!div class="nextstepaction"]
> [Add template functions](template-tutorial-add-functions.md)

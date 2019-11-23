---
title: Tutorial - Create & deploy template
description: Create your first Azure Resource Manager template. In the tutorial, you learn about the template file syntax and how to deploy a storage account.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 9650b8c67e3fd5c786b066c53e78b106935e11e1
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406026"
---
# <a name="tutorial-create-and-deploy-your-first-azure-resource-manager-template"></a>Tutorial: Create and deploy your first Azure Resource Manager template

This tutorial introduces you to Azure Resource Manager templates. It shows you how to create a starter template and deploy it to Azure. You'll learn about the structure of the template and the tools you'll need for working with templates. It takes about **12 minutes** to complete this tutorial, but the actual time will vary based on how many tools you need to install.

This tutorial is the first of a series. As you progress through the series, you modify the starting template step-by-step until you've explored all of the core parts of a Resource Manager template. These elements are the building blocks for much more complex templates. We hope by the end of the series you're confident creating your own templates and ready to automate your deployments with templates.

If you want to learn about the benefits of using templates and why you should automate deployment with templates, see [Azure Resource Manager templates](template-deployment-overview.md).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="get-tools"></a>Get tools

Let's start by making sure you have the tools you need to create and deploy templates.

### <a name="editor"></a>Editor

Templates are JSON files. To create templates, you need a good JSON editor. We recommend Visual Studio Code with the Resource Manager Tools extension. If you need to install these tools, see [Use Visual Studio Code to create Azure Resource Manager templates](./resource-manager-tools-vs-code.md).

### <a name="command-line-deployment"></a>Command-line deployment

You also need either Azure PowerShell or Azure CLI to deploy the template. For the installation instructions, see:

- [Instalar o Azure PowerShell](/powershell/azure/install-az-ps)
- [Install Azure CLI on Windows](/cli/azure/install-azure-cli-windows)
- [Install Azure CLI on Linux](/cli/azure/install-azure-cli-linux)

After installing either Azure PowerShell or Azure CLI, make sure you sign in for the first time. For help, see [Sign in - PowerShell](/powershell/azure/install-az-ps#sign-in) or [Sign in - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

Okay, you're ready to start learning about templates.

## <a name="create-your-first-template"></a>Create your first template

1. Open Visual Studio Code with the Resource Manager Tools extension installed.
1. From the **File** menu, select **New File** to create a new file.
1. From the **File** menu, select **Save as**.
1. Name the file **azuredeploy** and select the **JSON** file extension. The complete name of the file **azuredeploy.json**.
1. Save the file to your workstation. Select a path that is easy to remember because you'll provide that path later when deploying the template.
1. Copy and paste the following JSON into the file:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Here's what your VS Code environment looks like:

    ![Resource Manager template visual studio code first template](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    This template doesn't deploy any resources. We're starting with a blank template so you can get familiar with the steps to deploy a template while minimizing the chance of something going wrong.

    The JSON file has these elements:

    - **$schema**: Specifies the location of the JSON schema file. The schema file describes the properties that are available within a template. For example, the schema defines **resources** as one of the valid properties for a template. Don't worry that the date for the schema is 2015-01-01. This schema version is up-to-date and includes all of the latest features. The schema date hasn't been changed because there have been no breaking changes since its introduction.
    - **contentVersion**: Specifies the version of the template (such as 1.0.0.0). You can provide any value for this element. Use this value to document significant changes in your template. When deploying resources using the template, this value can be used to make sure that the right template is being used.
    - **resources**: Contains the resources you want to deploy or update. Currently, it's empty, but you'll add resources later.

1. Guarde o ficheiro.

Congratulations, you've created your first template.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

To start working with Azure PowerShell/Azure CLI, sign in with your Azure credentials.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az login
```

---
## <a name="create-resource-group"></a>Criar grupo de recursos

When you deploy a template, you specify a resource group that will contain the resources. Before running the deployment command, create the resource group with either Azure CLI or Azure PowerShell. Select the tabs in the following code section to choose between Azure PowerShell and Azure CLI. The CLI examples in this article are written for the Bash shell.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Implementar o modelo

To deploy the template, use either Azure CLI or Azure PowerShell. Use the resource group you created. Give a name to the deployment so you can easily identify it in the deployment history. For convenience, also create a variable that stores the path to the template file. This variable makes it easier for you to run the deployment commands because you don't have to retype the path every time you deploy.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az group deployment create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

The deployment command returns results. Look for `ProvisioningState` to see whether the deployment succeeded.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

![PowerShell deployment provisioning state](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

![Azure CLI deployment provisioning state](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

## <a name="verify-deployment"></a>Verificar a implementação

You can verify the deployment by exploring the resource group from the Azure portal.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. From the left menu, select **Resource groups**.

1. Select the resource group deploy in the last procedure. The default name is **myResourceGroup**. You shall see no resource deployed within the resource group.

1. Notice in the upper right of the overview, the status of the deployment is displayed. Select **1 Succeeded**.

   ![View deployment status](./media/template-tutorial-create-first-template/deployment-status.png)

1. You see a history of deployment for the resource group. Select **blanktemplate**.

   ![Select deployment](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. You see a summary of the deployment. In this case, there's not a lot to see because no resources were deployed. Later in this series you might find it helpful to review the summary in the deployment history. Notice on the left you can view inputs, outputs, and the template used during deployment.

   ![View deployment summary](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Limpar recursos

If you're moving on to the next tutorial, you don't need to delete the resource group.

If you're stopping now, you might want to delete the resource group.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

You created a simple template to deploy to Azure. In the next tutorial, you'll add a storage account to the template and deploy it to your resource group.

> [!div class="nextstepaction"]
> [Add resource](template-tutorial-add-resource.md)

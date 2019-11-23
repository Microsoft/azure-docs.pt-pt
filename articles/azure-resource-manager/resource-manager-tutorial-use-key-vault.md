---
title: Use Azure Key Vault in templates
description: Saiba como utilizar o Azure Key Vault para transmitir valores de parâmetros seguros durante a implementação de modelos do Resource Manager
author: mumian
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: ebb61bfd0fd1d39a9c1b17126fdd0e576e5629a2
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326399"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-resource-manager-template-deployment"></a>Tutorial: Integrate Azure Key Vault in your Resource Manager template deployment

Learn how to retrieve secrets from an Azure key vault and pass the secrets as parameters when you deploy Azure Resource Manager. The parameter value is never exposed, because you reference only its key vault ID. For more information, see [Use Azure Key Vault to pass secure parameter value during deployment](./resource-manager-keyvault-parameter.md).

In the [Set resource deployment order](./resource-manager-tutorial-create-templates-with-dependent-resources.md) tutorial, you create a virtual machine (VM). You need to provide the VM administrator username and password. Instead of providing the password, you can pre-store the password in an Azure key vault and then customize the template to retrieve the password from the key vault during the deployment.

![Diagram displaying the integration of a Resource Manager template with a key vault](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Prepare a key vault
> * Abrir um modelo de início rápido
> * Editar o ficheiro de parâmetros
> * Implementar o modelo
> * Validar a implementação
> * Limpar recursos

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Visual Studio Code with Resource Manager Tools extension. See [Use Visual Studio Code to create Azure Resource Manager templates](./resource-manager-tools-vs-code.md).
* To increase security, use a generated password for the VM administrator account. Here's a sample for generating a password:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Verify that the generated password meets the VM password requirements. Cada serviço do Azure tem requisitos de palavra-passe específicos. For the VM password requirements, see [What are the password requirements when you create a VM?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Prepare a key vault

In this section, you create a key vault and add a secret to it, so that you can retrieve the secret when you deploy your template. There are many ways to create a key vault. In this tutorial, you use Azure PowerShell to deploy a [Resource Manager template](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). This template does the following:

* Creates a key vault with the `enabledForTemplateDeployment` property enabled. This property must be *true* before the template deployment process can access the secrets that are defined in the key vault.
* Adds a secret to the key vault. The secret stores the VM administrator password.

> [!NOTE]
> As the user who's deploying the virtual machine template, if you're not the Owner of or a Contributor to the key vault, the Owner or a Contributor must grant you access to the *Microsoft.KeyVault/vaults/deploy/action* permission for the key vault. For more information, see [Use Azure Key Vault to pass a secure parameter value during deployment](./resource-manager-keyvault-parameter.md).

To run the following Azure PowerShell script, select **Try it** to open Azure Cloud Shell. To paste the script, right-click the shell pane, and then select **Paste**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue
```

> [!IMPORTANT]
> * The resource group name is the project name, but with **rg** appended to it. To make it easier to [clean up the resources that you created in this tutorial](#clean-up-resources), use the same project name and resource group name when you [deploy the next template](#deploy-the-template).
> * The default name for the secret is **vmAdminPassword**. It's hardcoded in the template.
> * To enable the template to retrieve the secret, you must enable an access policy called "Enable access to Azure Resource Manager for template deployment" for the key vault. This policy is enabled in the template. For more information about the access policy, see [Deploy key vaults and secrets](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets).

The template has one output value, called *keyVaultId*. Write down the ID value for later use, when you deploy the virtual machine. The resource ID format is:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

When you copy and paste the ID, it might be broken into multiple lines. Merge the lines and trim the extra spaces.

To validate the deployment, run the following PowerShell command in the same shell pane to retrieve the secret in clear text. The command works only in the same shell session, because it uses the variable *$keyVaultName*, which is defined in the preceding PowerShell script.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Now you've prepared a key vault and a secret. The following sections show you how to customize an existing template to retrieve the secret during the deployment.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de início rápido

Azure Quickstart Templates is a repository for Resource Manager templates. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. The template that's used in this tutorial is called [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. In Visual Studio Code, select **File** > **Open File**.

1. In the **File name** box, paste the following URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Selecione **Abrir** para abrir o ficheiro. The scenario is the same as the one that's used in [Tutorial: Create Azure Resource Manager templates with dependent resources](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
   The template defines five resources:

   * `Microsoft.Storage/storageAccounts`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

   It's helpful to have some basic understanding of the template before you customize it.

1. Select **File** > **Save As**, and then save a copy of the file to your local computer with the name *azuredeploy.json*.

1. Repeat steps 1-3 to open the following URL, and then save the file as *azuredeploy.parameters.json*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Editar o ficheiro de parâmetros

Não precisa de fazer quaisquer alterações ao ficheiro de modelo.

1. In Visual Studio Code, open *azuredeploy.parameters.json* if it's not already open.
1. Update the `adminPassword` parameter to:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > Replace the value for **id** with the resource ID of the key vault that you created in the previous procedure.

    ![Integrate key vault and Resource Manager template virtual machine deployment parameters file](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Update the following values:

    * **adminUsername**: The name of the virtual machine administrator account.
    * **dnsLabelPrefix**: Name the dnsLabelPrefix value.

    For examples of names, see the preceding image.

1. Guarde as alterações.

## <a name="deploy-the-template"></a>Implementar o modelo

Follow the instructions in [Deploy the template](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Upload both *azuredeploy.json* and *azuredeploy.parameters.json* to Cloud Shell, and then use the following PowerShell script to deploy the template:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

When you deploy the template, use the same resource group that you used in the key vault. This approach makes it easier for you to clean up the resources, because you need to delete only one resource group instead of two.

## <a name="validate-the-deployment"></a>Validar a implementação

After you've successfully deployed the virtual machine, test the sign-in credentials by using the password that's stored in the key vault.

1. Abra o [Portal do Azure](https://portal.azure.com).

1. Select **Resource groups** >  **\<*YourResourceGroupName*>**  > **simpleWinVM**.
1. Select **connect** at the top.
1. Select **Download RDP File**, and then follow the instructions to sign in to the virtual machine by using the password that's stored in the key vault.

## <a name="clean-up-resources"></a>Limpar recursos

When you no longer need your Azure resources, clean up the resources that you deployed by deleting the resource group.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

In this tutorial, you retrieved a secret from your Azure key vault. You then used the secret in your template deployment. Para saber como criar modelos ligados, veja:

> [!div class="nextstepaction"]
> [Criar modelos ligados](./resource-manager-tutorial-create-linked-templates.md)

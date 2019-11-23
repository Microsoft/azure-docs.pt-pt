---
title: Deploy VM extensions with template
description: Saiba como implementar extensões de máquina virtual com modelos do Azure Resource Manager
author: mumian
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: c18e5959a1ec52d9f10f27f456a466669e7b99c9
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325365"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>Tutorial: Implementar extensões de máquina virtual com modelos do Azure Resource Manager

Aprenda a utilizar [extensões de máquina virtual do Azure](../virtual-machines/extensions/features-windows.md) para fazer tarefas de automatização e configuração de pós-implementação em VMs do Azure. Estão disponíveis muitas VMs diferentes para utilização com as VMs do Azure. In this tutorial, you deploy a Custom Script extension from an Azure Resource Manager template to run a PowerShell script on a Windows VM.  O script instala o Servidor Web na VM.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Preparar o script do Powershell
> * Abrir um modelo de início rápido
> * Editar o modelo
> * Implementar o modelo
> * Verificar a implementação

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Visual Studio Code with Resource Manager Tools extension. See [Use Visual Studio Code to create Azure Resource Manager templates](./resource-manager-tools-vs-code.md).
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador da máquina virtual. Eis um exemplo para gerar uma palavra-passe:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para obter mais informações, veja [Tutorial: Integrar o Azure Key Vault na implementação de modelos do Resource Manager](./resource-manager-tutorial-use-key-vault.md). We also recommend that you update your password every three months.

## <a name="prepare-a-powershell-script"></a>Preparar o script do Powershell

A PowerShell script with the following content is shared from [Github](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

If you choose to publish the file to your own location, you must update the `fileUri` element in the template later in the tutorial.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de início rápido

Azure Quickstart Templates is a repository for Resource Manager templates. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo utilizado neste tutorial é denominado [Implementar uma VM do Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. In Visual Studio Code, select **File** > **Open File**.
1. In the **File name** box, paste the following URL: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json

1. To open the file, select **Open**.
    The template defines five resources:

   * **Microsoft.Storage/storageAccounts**. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * **Microsoft.Network/publicIPAddresses**. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * **Microsoft.Network/virtualNetworks**. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * **Microsoft.Network/networkInterfaces**. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * **Microsoft.Compute/virtualMachines**. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     It's helpful to get some basic understanding of the template before you customize it.

1. Save a copy of the file to your local computer with the name *azuredeploy.json* by selecting **File** > **Save As**.

## <a name="edit-the-template"></a>Editar o modelo

Adicione um recurso de extensão de máquina virtual ao modelo existente com o seguinte conteúdo:

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "autoUpgradeMinorVersion":true,
        "settings": {
            "fileUris": [
                "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1"
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
        }
    }
}
```

For more information about this resource definition, see the [extension reference](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions). Seguem alguns elementos importantes:

* **nome**: uma vez que o recurso de extensão é um recurso subordinado do objeto de máquina virtual, o nome tem de ter o prefixo do nome da máquina virtual. See [Set name and type for child resources](child-resource-name-type.md).
* **dependsOn**: Create the extension resource after you've created the virtual machine.
* **fileUris**: The locations where the script files are stored. If you choose not to use the provided location, you need to update the values.
* **commandToExecute**: This command invokes the script.

## <a name="deploy-the-template"></a>Implementar o modelo

For the deployment procedure, see the "Deploy the template" section of [Tutorial: Create Azure Resource Manager templates with dependent resources](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). We recommended that you use a generated password for the virtual machine administrator account. See this article's [Prerequisites](#prerequisites) section.

## <a name="verify-the-deployment"></a>Verificar a implementação

1. In the Azure portal, select the VM.
1. In the VM overview, copy the IP address by selecting **Click to copy**, and then paste it in a browser tab. The default Internet Information Services (IIS) welcome page opens:

![The Internet Information Services welcome page](./media/resource-manager-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Limpar recursos

When you no longer need the Azure resources you deployed, clean them up by deleting the resource group.

1. In the Azure portal, in the left pane, select **Resource group**.
2. In the **Filter by name** box, enter the resource group name.
3. Selecione o nome do grupo de recursos.
    Six resources are displayed in the resource group.
4. In the top menu, select **Delete resource group**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou uma máquina virtual e uma extensão de máquina virtual. A extensão instalou o servidor web dos IIS na máquina virtual. To learn how to use the Azure SQL Database extension to import a BACPAC file, see:

> [!div class="nextstepaction"]
> [Deploy SQL extensions](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)

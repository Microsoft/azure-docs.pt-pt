---
title: Implementar extensões de máquina virtual com modelos do Azure Resource Manager | Microsoft Docs
description: Saiba como implementar extensões de máquina virtual com modelos do Azure Resource Manager
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a1df94c7157823a4d7dcdcf652b1c7c926f4dbba
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57406512"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>Tutorial: Implementar extensões de máquina virtual com modelos Azure Resource Manager

Aprenda a utilizar [extensões de máquina virtual do Azure](../virtual-machines/extensions/features-windows.md) para fazer tarefas de automatização e configuração de pós-implementação em VMs do Azure. Estão disponíveis muitas VMs diferentes para utilização com as VMs do Azure. Neste tutorial, vai implementar uma extensão de Script personalizado a partir de um modelo Azure Resource Manager para executar um script do PowerShell numa VM do Windows.  O script instala o Servidor Web na VM.

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

* [Visual Studio Code](https://code.visualstudio.com/) com a extensão Ferramentas do Resource Manager. Ver [instalar a extensão](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador da máquina virtual. Eis um exemplo para gerar uma palavra-passe:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para obter mais informações, consulte [Tutorial: Integrar o Azure Key Vault na implementação de modelo do Resource Manager](./resource-manager-tutorial-use-key-vault.md). Também recomendamos que Atualize a sua palavra-passe a cada três meses.

## <a name="prepare-a-powershell-script"></a>Preparar o script do Powershell

Um script do PowerShell com o seguinte conteúdo partilhado a partir um [conta de armazenamento do Azure com o acesso público](https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Se optar por publicar o ficheiro para a sua própria localização, tem de atualizar o `fileUri` elemento no modelo mais tarde no tutorial.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de início rápido

Modelos de início rápido do Azure é um repositório de modelos do Resource Manager. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo utilizado neste tutorial é denominado [Implementar uma VM do Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. No Visual Studio Code, selecione **arquivo** > **abrir o ficheiro**.
1. Na **nome de ficheiro** caixa, cole o seguinte URL: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json

1. Para abrir o ficheiro, selecione **abra**.  
    O modelo define cinco recursos:

    * **Microsoft.Storage/storageAccounts**. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
    * **Microsoft.Network/publicIPAddresses**. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
    * **Microsoft.Network/virtualNetworks**. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
    * **Microsoft.Network/networkInterfaces**. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
    * **Microsoft.Compute/virtualMachines**. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    É útil obter algumas noções básicas sobre o modelo antes de personalizá-lo.

1. Guardar uma cópia do ficheiro no seu computador local com o nome *azuredeploy. JSON* ao selecionar **ficheiro** > **guardar como**.

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
                "https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1"
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
        }
    }
}
```

Para obter mais informações sobre esta definição do recurso, consulte a [referência de extensão](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions). Seguem alguns elementos importantes:

* **name**: Como o recurso de extensão é um recurso filho do objeto de máquina virtual, o nome tem de ter o prefixo de nome de máquina virtual. Veja [Recursos subordinados](./resource-group-authoring-templates.md#child-resources).
* **dependsOn**: Crie o recurso de extensão depois de criar a máquina virtual.
* **fileUris**: As localizações onde os ficheiros de script são armazenados. Se optar por não utilizar a localização fornecida, tem de atualizar os valores.
* **commandToExecute**: Este comando invoca o script.  

## <a name="deploy-the-template"></a>Implementar o modelo

Para o procedimento de implementação, consulte a secção "Implementar o modelo" [Tutorial: Criar modelos Azure Resource Manager com recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Recomendamos que utilize uma palavra-passe gerada para a conta de administrador da máquina virtual. Veja este artigo [pré-requisitos](#prerequisites) secção.

## <a name="verify-the-deployment"></a>Verificar a implementação

1. No portal do Azure, selecione a VM.
1. Na descrição geral de VM, copie o endereço IP, selecionando **clique para copiar**e, em seguida, cole-o num separador do browser.  
   A predefinição de serviços de informação Internet (IIS) de boas-vindas é aberta a página:

![A página de boas-vindas do IIS](./media/resource-manager-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos do Azure que implementou, limpe-o ao eliminar o grupo de recursos.

1. No portal do Azure, no painel esquerdo, selecione **grupo de recursos**.
2. Na **filtrar por nome** , introduza o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.  
    Recursos de seis são exibidos no grupo de recursos.
4. No menu superior, selecione **eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, implementou uma máquina virtual e uma extensão de máquina virtual. A extensão instalou o servidor web dos IIS na máquina virtual. Para saber como utilizar a extensão de base de dados do Azure SQL para importar um ficheiro BACPAC, veja:

> [!div class="nextstepaction"]
> [Implementar extensões SQL](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)

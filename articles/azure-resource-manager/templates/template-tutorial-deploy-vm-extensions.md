---
title: Implantar extensões de VM com o modelo
description: Saiba como implementar extensões de máquina virtual com modelos do Azure Resource Manager
author: mumian
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 5379bf3c0a5127e5114ac819bd3e0e2ad12e8d69
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045265"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>Tutorial: Implementar extensões de máquina virtual com modelos do Azure Resource Manager

Aprenda a utilizar [extensões de máquina virtual do Azure](../../virtual-machines/extensions/features-windows.md) para fazer tarefas de automatização e configuração de pós-implementação em VMs do Azure. Estão disponíveis muitas VMs diferentes para utilização com as VMs do Azure. Neste tutorial, você implanta uma extensão de script personalizado de um modelo de Azure Resource Manager para executar um script do PowerShell em uma VM do Windows.  O script instala o Servidor Web na VM.

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

* Visual Studio Code com a extensão de ferramentas do Resource Manager. Consulte [usar Visual Studio Code para criar modelos de Azure Resource Manager](use-vs-code-to-create-template.md).
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador da máquina virtual. Eis um exemplo para gerar uma palavra-passe:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para obter mais informações, veja [Tutorial: Integrar o Azure Key Vault na implementação de modelos do Resource Manager](./template-tutorial-use-key-vault.md). Também recomendamos que você atualize sua senha a cada três meses.

## <a name="prepare-a-powershell-script"></a>Preparar o script do Powershell

Um script do PowerShell com o seguinte conteúdo é compartilhado do [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Se você optar por publicar o arquivo em seu próprio local, deverá atualizar o elemento `fileUri` no modelo posteriormente no tutorial.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de início rápido

Os modelos de início rápido do Azure são um repositório para modelos do Resource Manager. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo utilizado neste tutorial é denominado [Implementar uma VM do Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. Em Visual Studio Code, selecione **arquivo** > **Abrir arquivo**.
1. Na caixa **nome do arquivo** , Cole a seguinte URL: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json

1. Para abrir o arquivo, selecione **abrir**.
    O modelo define cinco recursos:

   * **Microsoft.Storage/storageAccounts**. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * **Microsoft. Network/publicIPAddresses**. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * **Microsoft.Network/virtualNetworks**. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * **Microsoft.Network/networkInterfaces**. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * **Microsoft.Compute/virtualMachines**. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     É útil obter alguma compreensão básica do modelo antes de personalizá-lo.

1. Salve uma cópia do arquivo em seu computador local com o nome *azuredeploy. JSON* selecionando **arquivo** > **salvar como**.

## <a name="edit-the-template"></a>Editar o modelo

Adicione um recurso de extensão de máquina virtual ao modelo existente com o seguinte conteúdo:

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2018-06-01",
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

Para obter mais informações sobre essa definição de recurso, consulte a [referência de extensão](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions). Seguem alguns elementos importantes:

* **nome**: uma vez que o recurso de extensão é um recurso subordinado do objeto de máquina virtual, o nome tem de ter o prefixo do nome da máquina virtual. Consulte [definir nome e tipo para recursos filho](child-resource-name-type.md).
* **depende**: Crie o recurso de extensão depois de criar a máquina virtual.
* **fileuris**: os locais onde os arquivos de script são armazenados. Se você optar por não usar o local fornecido, precisará atualizar os valores.
* **commandToExecute**: esse comando invoca o script.

## <a name="deploy-the-template"></a>Implementar o modelo

Para o procedimento de implantação, consulte a seção "implantar o modelo" do [tutorial: criar modelos de Azure Resource Manager com recursos dependentes](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Recomendamos que você use uma senha gerada para a conta de administrador da máquina virtual. Consulte a seção [pré-requisitos](#prerequisites) deste artigo.

## <a name="verify-the-deployment"></a>Verificar a implementação

1. Na portal do Azure, selecione a VM.
1. Na visão geral da VM, copie o endereço IP selecionando **clicar para copiar**e, em seguida, Cole-o em uma guia do navegador. A página de boas-vindas do Serviços de Informações da Internet padrão (IIS) é aberta:

![A página de boas-vindas do Serviços de Informações da Internet](./media/template-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais dos recursos do Azure implantados, limpe-os excluindo o grupo de recursos.

1. No portal do Azure, no painel esquerdo, selecione grupo de **recursos**.
2. Na caixa **Filtrar por nome** , insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
    Seis recursos são exibidos no grupo de recursos.
4. No menu superior, selecione **excluir grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou uma máquina virtual e uma extensão de máquina virtual. A extensão instalou o servidor web dos IIS na máquina virtual. Para saber como usar a extensão do banco de dados SQL do Azure para importar um arquivo BACPAC, consulte:

> [!div class="nextstepaction"]
> [Implantar extensões do SQL](./template-tutorial-deploy-sql-extensions-bacpac.md)

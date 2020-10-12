---
title: Implementar extensões VM com modelo
description: Saiba como implementar extensões de máquina virtual com modelos do Azure Resource Manager
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f82e0eb45f4bc7c3260554b1b1120025029336bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89073647"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-arm-templates"></a>Tutorial: Implementar extensões de máquinas virtuais com modelos ARM

Aprenda a utilizar [extensões de máquina virtual do Azure](../../virtual-machines/extensions/features-windows.md) para fazer tarefas de automatização e configuração de pós-implementação em VMs do Azure. Estão disponíveis muitas VMs diferentes para utilização com as VMs do Azure. Neste tutorial, você implementa uma extensão de Script Personalizado a partir de um modelo Azure Resource Manager (ARM) para executar um script PowerShell em um VM do Windows.  O script instala o Servidor Web na VM.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Preparar o script do Powershell
> * Abrir um modelo de início rápido
> * Editar o modelo
> * Implementar o modelo

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Visual Studio Code com extensão Ferramentas do Resource Manager. Consulte [Quickstart: Crie modelos de Gestor de Recursos Azure com Código de Estúdio Visual](quickstart-create-templates-use-visual-studio-code.md).
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador da máquina virtual. Eis um exemplo para gerar uma palavra-passe:

    ```console
    openssl rand -base64 32
    ```

    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para obter mais informações, consulte [Tutorial: Integre o cofre da chave azul na implementação do modelo ARM](./template-tutorial-use-key-vault.md). Recomendamos também que atualize a sua palavra-passe de três em três meses.

## <a name="prepare-a-powershell-script"></a>Preparar o script do Powershell

Pode utilizar o script inline PowerShell ou um ficheiro de script.  Este tutorial mostra como usar um ficheiro de script. Um script PowerShell com o seguinte conteúdo é partilhado no [GitHub:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1)

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Se optar por publicar o ficheiro para a sua própria localização, atualize o `fileUri` elemento no modelo mais tarde no tutorial.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de início rápido

Azure Quickstart Templates é um repositório para modelos ARM. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo utilizado neste tutorial é denominado [Implementar uma VM do Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. No Código do Estúdio Visual, selecione Ficheiro Aberto **de**  >  **Ficheiros**.
1. Na caixa **de nomes do Ficheiro,** cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Para abrir o ficheiro, selecione **Abrir**.
    O modelo define cinco recursos:

   * [**Microsoft.Storage/storageAcounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     É útil obter alguma compreensão básica do modelo antes de personalizá-lo.

1. Guarde uma cópia do ficheiro para o seu computador local com o nome *azuredeploy.js* selecionando **File**  >  **Save As**.

## <a name="edit-the-template"></a>Editar o modelo

Adicione um recurso de extensão de máquina virtual ao modelo existente com o seguinte conteúdo:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "apiVersion": "2019-12-01",
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

Para obter mais informações sobre esta definição de recursos, consulte a [referência de extensão](/azure/templates/microsoft.compute/virtualmachines/extensions). Seguem alguns elementos importantes:

* **nome**: uma vez que o recurso de extensão é um recurso subordinado do objeto de máquina virtual, o nome tem de ter o prefixo do nome da máquina virtual. Consulte [o nome definido e o tipo para obter recursos para crianças.](child-resource-name-type.md)
* **depende:** Crie o recurso de extensão depois de ter criado a máquina virtual.
* **fileUris**: As localizações onde os ficheiros de script são armazenados. Se optar por não utilizar a localização fornecida, tem de atualizar os valores.
* **commandToExecute**: Este comando invoca o script.

Para utilizar o script inline, remova **os fileUris**e atualize **o comandoToExecute** para:

```powershell
powershell.exe Install-WindowsFeature -name Web-Server -IncludeManagementTools && powershell.exe remove-item 'C:\\inetpub\\wwwroot\\iisstart.htm' && powershell.exe Add-Content -Path 'C:\\inetpub\\wwwroot\\iisstart.htm' -Value $('Hello World from ' + $env:computername)
```

Este script inline também atualiza o conteúdo iisstart.html.

Também tem de abrir a porta HTTP para que possa aceder ao servidor web.

1. Encontre **regras de segurança** no modelo.
1. Adicione a seguinte regra ao lado **do padrão-permitir-3389**.

    ```json
    {
      "name": "AllowHTTPInBound",
      "properties": {
        "priority": 1010,
        "access": "Allow",
        "direction": "Inbound",
        "destinationPortRange": "80",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*"
      }
    }
    ```

## <a name="deploy-the-template"></a>Implementar o modelo

Para o procedimento de implementação, consulte a secção "Implementar o modelo" do [Tutorial: Criar modelos ARM com recursos dependentes](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Recomendamos que utilize uma palavra-passe gerada para a conta de administrador de máquina virtual. Consulte a secção [Pré-Requisitos](#prerequisites) deste artigo.

A partir da Cloud Shell, executar o seguinte comando para recuperar o endereço IP público do VM:

```azurepowershell
(Get-AzPublicIpAddress -ResourceGroupName $resourceGroupName).IpAddress
```

Cole o endereço IP num navegador Web. A página de boas-vindas dos Serviços de Informação da Internet (IIS) por defeito abre:

![A página de boas-vindas dos Serviços de Informação da Internet](./media/template-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos Azure que implementou, limpe-os eliminando o grupo de recursos.

1. No portal Azure, no painel esquerdo, selecione **Grupo de Recursos**.
2. Na caixa **de identificação filter by name,** insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
    Seis recursos são apresentados no grupo de recursos.
4. No menu superior, selecione **Eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou uma máquina virtual e uma extensão de máquina virtual. A extensão instalou o servidor web dos IIS na máquina virtual. Para aprender a utilizar a extensão da Base de Dados Azure SQL para importar um ficheiro BACPAC, consulte:

> [!div class="nextstepaction"]
> [Implementar extensões de SQL](./template-tutorial-deploy-sql-extensions-bacpac.md)

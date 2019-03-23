---
title: Criar uma VM do Windows a partir de um modelo no Azure | Documentos da Microsoft
description: Utilize um modelo do Resource Manager e o PowerShell para criar facilmente uma nova VM do Windows.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6bc578d931235623f6cfed45724ad408d3201c61
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58367937"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Criar uma máquina virtual do Windows a partir de um modelo do Resource Manager

Saiba como criar uma máquina virtual do Windows utilizando um modelo Azure Resource Manager e o Azure PowerShell a partir do Azure Cloud shell. O modelo utilizado neste artigo implementa uma única máquina virtual com o Windows Server numa rede virtual nova com uma única sub-rede. Para criar uma máquina virtual do Linux, consulte [como criar uma máquina virtual Linux com modelos Azure Resource Manager](../linux/create-ssh-secured-vm-from-template.md).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Normalmente, a criação de uma máquina virtual do Azure inclui duas etapas:

- Crie um grupo de recursos. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um grupo de recursos tem de ser criado antes de uma máquina virtual.
- Cria uma máquina virtual.

O exemplo seguinte cria uma VM a partir de um [modelo de início rápido do Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json). Esta é uma cópia do modelo:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

Para executar o script do PowerShell, selecione **experimente** para abrir o Azure Cloud shell. Colar o script, o shell com o botão direito e, em seguida, selecione **colar**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

Se optar por instalar e utilizar o PowerShell localmente, em vez de partir do Azure Cloud shell, este tutorial requer o Azure PowerShell versão 5.3 ou posterior do módulo. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/azurerm/install-azurerm-ps). Se estiver executando o PowerShell localmente, terá também de executar `Connect-AzAccount` para criar uma ligação com o Azure.

No exemplo anterior, que especificou um modelo armazenado no GitHub. Também pode transferir ou criar um modelo e especifique o caminho local com o `--template-file` parâmetro.

Aqui estão alguns recursos adicionais:

- Para saber como desenvolver modelos do Resource Manager, veja [documentação do Azure Resource Manager](/azure/azure-resource-manager/).
- Para ver os esquemas de máquina virtual do Azure, consulte [referência de modelo do Azure](/azure/templates/microsoft.compute/allversions).
- Para ver mais exemplos de modelos de máquina virtual, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual

O último comando do PowerShell do script anterior mostra o nome de máquina virtual. Para ligar à máquina virtual, veja [como ligar e iniciar sessão máquina virtual do Azure a executar o Windows](./connect-logon.md).

## <a name="next-steps"></a>Próximos Passos

- Se ocorreram problemas com a implementação, pode dar uma olhada [resolver erros comuns de implementação do Azure com o Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
- Saiba como criar e gerir uma máquina virtual no [criar e gerir VMs do Windows com o módulo Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para saber mais sobre a criação de modelos, veja a sintaxe JSON e propriedades para os tipos de recursos que implementou:

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)

---
title: Criar uma VM do Windows a partir de um modelo no Azure
description: Use um modelo do Resource Manager e o PowerShell para criar facilmente uma nova VM do Windows.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99e292930414ae027c9cbbf3a901d550041899d2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065553"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Criar uma máquina virtual do Windows a partir de um modelo do Resource Manager

Saiba como criar uma máquina virtual do Windows usando um modelo de Azure Resource Manager e Azure PowerShell do Azure cloud Shell. O modelo usado neste artigo implanta uma única máquina virtual executando o Windows Server em uma nova rede virtual com uma única sub-rede. Para criar uma máquina virtual do Linux, consulte [como criar uma máquina virtual Linux com modelos de Azure Resource Manager](../linux/create-ssh-secured-vm-from-template.md).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

A criação de uma máquina virtual do Azure geralmente inclui duas etapas:

- Crie um grupo de recursos. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um grupo de recursos tem de ser criado antes de uma máquina virtual.
- Cria uma máquina virtual.

O exemplo a seguir cria uma VM a partir de um [modelo de início rápido do Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json). Aqui está uma cópia do modelo:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

Para executar o script do PowerShell, selecione **Experimente-** o para abrir o Azure cloud Shell. Para colar o script, clique com o botão direito do mouse no Shell e selecione **colar**:

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

Se você optar por instalar e usar o PowerShell localmente em vez do Azure cloud Shell, este tutorial exigirá o módulo Azure PowerShell. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver executando o PowerShell localmente, terá também de executar `Connect-AzAccount` para criar uma ligação com o Azure.

No exemplo anterior, você especificou um modelo armazenado no GitHub. Você também pode baixar ou criar um modelo e especificar o caminho local com o parâmetro `--template-file`.

Aqui estão alguns recursos adicionais:

- Para saber como desenvolver modelos do Resource Manager, consulte a [documentação do Azure Resource Manager](/azure/azure-resource-manager/).
- Para ver os esquemas de máquina virtual do Azure, consulte [referência de modelo do Azure](/azure/templates/microsoft.compute/allversions).
- Para ver mais exemplos de modelo de máquina virtual, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual

O último comando do PowerShell do script anterior mostra o nome da máquina virtual. Para se conectar à máquina virtual, consulte [como se conectar e entrar em uma máquina virtual do Azure que executa o Windows](./connect-logon.md).

## <a name="next-steps"></a>Passos Seguintes

- Se houver problemas com a implantação, você poderá dar uma olhada em [solucionar erros comuns de implantação do Azure com o Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
- Saiba como criar e gerenciar uma máquina virtual em [criar e gerenciar VMs do Windows com o módulo Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para saber mais sobre como criar modelos, exiba a sintaxe e as propriedades JSON para os tipos de recursos implantados:

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)

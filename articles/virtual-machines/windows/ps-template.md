---
title: Criar um VM do Windows a partir de um modelo em Azure
description: Utilize um modelo de Gestor de Recursos e PowerShell para criar facilmente um novo VM do Windows.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 534a8480e783853f7497a0538b04e0302a9cda0c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553617"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Criar uma máquina virtual do Windows a partir de um modelo do Resource Manager

Aprenda a criar uma máquina virtual do Windows utilizando um modelo de Gestor de Recursos Azure e Azure PowerShell a partir da concha da Cloud Azure. O modelo utilizado neste artigo implementa uma única máquina virtual que executa o Windows Server numa nova rede virtual com uma única sub-rede. Para criar uma máquina virtual Linux, consulte [Como criar uma máquina virtual Linux com modelos Azure Resource Manager](../linux/create-ssh-secured-vm-from-template.md).

Uma alternativa é implementar o modelo a partir do portal Azure. Para abrir o modelo no portal, selecione o botão **Implementar para Azul.**

[![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json)

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

A criação de uma máquina virtual Azure geralmente inclui dois passos:

- Crie um grupo de recursos. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um grupo de recursos tem de ser criado antes de uma máquina virtual.
- Cria uma máquina virtual.

O exemplo a seguir cria um VM a partir de um [modelo Azure Quickstart](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json). Aqui está uma cópia do modelo:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

Para executar o script PowerShell, selecione **experimentá-lo** para abrir a casca de Azure Cloud. Para colar o script, clique com o botão direito da concha e, em seguida, **selecione Pasta**:

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

Se optar por instalar e utilizar o PowerShell localmente em vez de a partir da casca Azure Cloud, este tutorial requer o módulo Azure PowerShell. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar o PowerShell localmente, também precisa de correr `Connect-AzAccount` para criar uma ligação com o Azure.

No exemplo anterior, especificou um modelo armazenado no GitHub. Também pode descarregar ou criar um modelo e especificar o caminho local com o `--template-file` parâmetro.

Seguem-se alguns recursos adicionais:

- Para aprender a desenvolver modelos de Gestor de Recursos, consulte [a documentação do Gestor de Recursos Azure](../../azure-resource-manager/index.yml).
- Para ver os esquemas de máquina virtual Azure, consulte [a referência do modelo Azure](/azure/templates/microsoft.compute/allversions).
- Para ver mais amostras de modelos de máquinas virtuais, consulte [os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual

O último comando PowerShell do script anterior mostra o nome da máquina virtual. Para ligar à máquina virtual, consulte [como ligar e iniciar sôm numa máquina virtual Azure que executa o Windows](./connect-logon.md).

## <a name="next-steps"></a>Passos Seguintes

- Se houver problemas com a implementação, poderá dar uma olhada nos [erros comuns de implementação do Azure com o Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md).
- Aprenda a criar e gerir uma máquina virtual em [Criar e gerir VMs do Windows com o módulo Azure PowerShell](tutorial-manage-vm.md).

Para saber mais sobre a criação de modelos, consulte a sintaxe JSON e as propriedades para os tipos de recursos que implementou:

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)

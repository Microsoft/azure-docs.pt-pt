---
title: Criar VM do Windows com o cmdlet simplificada de New-AzVM no Azure Cloud Shell | Documentos da Microsoft
description: Aprenda rapidamente a criar as máquinas virtuais do Windows com o cmdlet New-AzVM simplificado no Azure Cloud Shell.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 02f2bd78ca5656534b106c6f7f18c05165b4b9ff
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444507"
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azvm-cmdlet-in-cloud-shell"></a>Criar uma máquina virtual do Windows com o cmdlet New-AzVM simplificado no Cloud Shell 

O [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet adicionou um conjunto simplificado de parâmetros para a criação de uma nova VM com o PowerShell. Este tópico mostra-lhe como utilizar o PowerShell no Azure Cloud Shell, com a versão mais recente do cmdlet New-AzureVM pré-instalado, para criar uma nova VM. Nós usaremos um conjunto de parâmetros simplificados que cria automaticamente todos os recursos necessários com predefinições inteligentes. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]


## <a name="create-the-vm"></a>Crie a VM

Pode utilizar o cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) para criar uma VM com predefinições inteligentes que incluem a utilização da imagem do Windows Server 2016 Datacenter do Azure Marketplace. Pode utilizar o cmdlet New-AzVM com apenas o **-nome** parâmetro e ele irão utilizar esse valor para todos os nomes de recursos. Neste exemplo, vamos definir o parâmetro **-Name** como *myVM*. 

Certifique-se de que o **PowerShell** está selecionado no Cloud Shell e escreva:

```azurepowershell-interactive
New-AzVm -Name myVM
```

É-lhe pedido para criar um nome de utilizador e uma palavra-passe para a VM, que serão utilizados quando ligar à VM mais adiante neste tópico. A palavra-passe tem de ter entre 12 e 123 carateres e cumprir três dos quatro requisitos de complexidade que se seguem: um caráter em letra minúscula, um caráter em letra maiúscula, um número e um caráter especial.

Basta um minuto para criar a VM e os recursos associados. Quando terminar, pode ver todos os recursos que foram criados utilizando o [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) cmdlet.

```azurepowershell-interactive
Get-AzResource `
    -ResourceGroupName myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Ligar à VM

Depois de a implementação estar concluída, crie uma ligação de ambiente de trabalho remoto à máquina virtual.

Utilize o [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) comando para devolver o endereço IP público da máquina virtual. Tome nota deste Endereço IP.

```azurepowershell-interactive
Get-AzPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

No seu computador local, abra um prompt de comando e utilize o **mstsc** comando para iniciar uma sessão de área de trabalho remota com a nova VM. Substitua &lt;publicIPAddress&gt; pelo endereço IP da máquina virtual. Quando lhe for pedido, introduza o nome de utilizador e a palavra-passe que atribuiu à VM quando esta foi criada.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>Especifique nomes de recursos diferente

Pode também fornecer nomes mais descritivos para os recursos e, ainda terá-los criado automaticamente. Eis um exemplo em que podemos ter chamado vários recursos para a nova VM, incluindo um novo grupo de recursos.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 3389
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) de comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myVM
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste tópico, implementou uma máquina virtual simples com o cmdlet New-AzVM e, em seguida, ligou-a via RDP. Para saber mais sobre as máquinas virtuais do Azure, continue com o tutorial para VMs do Windows.

> [!div class="nextstepaction"]
> [Tutoriais de máquinas virtuais do Windows do Azure](./tutorial-manage-vm.md)

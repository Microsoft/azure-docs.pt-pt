---
title: Comandos comuns do PowerShell para máquinas de virtuais do Azure | Documentos da Microsoft
description: Comandos comuns do PowerShell para começar a criar e gerir as suas VMs do Windows no Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 9d0e27c2427d53554b454e0c319ce9cf180f1633
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820795"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Comandos comuns do PowerShell para criar e gerir máquinas virtuais do Azure

Este artigo aborda alguns dos comandos do Azure PowerShell que pode utilizar para criar e gerir máquinas virtuais na sua subscrição do Azure.  Para obter mais ajuda com parâmetros de linha de comandos específicos e opções, pode utilizar o **Get-Help** *comando*.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

Estas variáveis podem ser útil para, se executar mais de um dos comandos neste artigo:

- $location - a localização da máquina virtual. Pode usar [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) para localizar um [região geográfica](https://azure.microsoft.com/regions/) que funciona melhor para si.
- $myResourceGroup - o nome do grupo de recursos que contém a máquina virtual.
- $myVM - o nome da máquina virtual.

## <a name="create-a-vm---simplified"></a>Criar uma VM - simplificada

| Tarefa | Comando |
| ---- | ------- |
| Criar uma VM simple | [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -Name $myVM <BR></BR><BR></BR> Novo-AzVM tem um conjunto de *simplificada* parâmetros, onde é tudo o que é necessário um nome único. O valor-nome será utilizado como o nome de todos os recursos necessários para criar uma nova VM. É possível especificar mais, mas isso é tudo o que é necessário.|
| Criar uma VM a partir de uma imagem personalizada | New-AzVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "myImage" -Location $location  <BR></BR><BR></BR>Tem de já ter criado seu próprio [imagem gerida](capture-image-resource.md). Pode utilizar uma imagem para tornar vários, VMs idênticas. |



## <a name="create-a-vm-configuration"></a>Criar uma configuração de VM

| Tarefa | Comando |
| ---- | ------- |
| Criar uma configuração de VM |$vm = [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>A configuração da VM é usada para definir ou atualizar as definições para a VM. A configuração é inicializada com o nome da VM e a respetiva [tamanho](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Adicionar definições de configuração |$vm = [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Definições de sistema operativo, incluindo [credenciais](https://technet.microsoft.com/library/hh849815.aspx) são adicionadas para o objeto de configuração que criou anteriormente com New-AzVMConfig. |
| Adicionar uma interface de rede |$vm = [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>Uma VM tem de ter uma [interface de rede](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para comunicar-se numa rede virtual. Também pode utilizar [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) para recuperar um objeto de interface de rede existente. |
| Especifique uma imagem de plataforma |$vm = [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) -VM $vm -PublisherName "publisher_name" -Offer "publisher_offer" -Skus "product_sku" -Version "latest"<BR></BR><BR></BR>[Informações de imagem](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) é adicionado ao objeto de configuração que criou anteriormente com New-AzVMConfig. O objeto devolvido deste comando só é utilizado ao definir o disco do SO a utilizar uma imagem de plataforma. |
| Criar uma VM |[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>Todos os recursos são criados num [grupo de recursos](../../azure-resource-manager/manage-resource-groups-powershell.md). Antes de executar este comando, execute New-AzVMConfig, AzVMOperatingSystem do conjunto, AzVMSourceImage do conjunto, AzVMNetworkInterface adicionar e AzVMOSDisk do conjunto. |
| Atualizar uma VM |[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Obter a configuração atual do VM utilizando o cmdlet Get-AzVM, alterar definições de configuração do objeto de VM e, em seguida, execute este comando. |

## <a name="get-information-about-vms"></a>Obter informações sobre VMs

| Tarefa | Comando |
| ---- | ------- |
| Lista de VMs numa subscrição |[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| Lista de VMs num grupo de recursos |Get-AzVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Para obter uma lista de grupos de recursos na sua subscrição, utilize [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup). |
| Obter informações sobre uma VM |Get-AzVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Gerir VMs
| Tarefa | Comando |
| --- | --- |
| Iniciar uma VM |[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Parar uma VM |[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Reiniciar uma VM em execução |[Restart-AzVM](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Eliminar uma VM |[Remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>Passos Seguintes
* Veja os passos básicos para a criação de uma máquina virtual no [criar uma VM do Windows com o Resource Manager e PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


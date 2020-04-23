---
title: Comandos Da PowerShell comuns para máquinas virtuais azure
description: O Common PowerShell ordena que você começou a criar e gerir VMs em Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 8b99b6dd62920ed17d79281b448089754613d4e1
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82098413"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Comandos comuns do PowerShell para criar e gerir Máquinas Virtuais do Azure

Este artigo cobre alguns dos comandos Azure PowerShell que pode utilizar para criar e gerir máquinas virtuais na sua subscrição Azure.  Para obter uma ajuda mais detalhada com interruptores e opções específicos da linha de comando, pode utilizar o *comando* **Get-Help** .

 

Estas variáveis podem ser úteis para si se executar mais do que um dos comandos neste artigo:

- $location - A localização da máquina virtual. Você pode usar [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) para encontrar uma [região geográfica](https://azure.microsoft.com/regions/) que funcione para si.
- $myResourceGroup - O nome do grupo de recursos que contém a máquina virtual.
- $myVM - O nome da máquina virtual.

## <a name="create-a-vm---simplified"></a>Criar um VM - simplificado

| Tarefa | Comando |
| ---- | ------- |
| Criar um VM simples | [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -Nome $myVM <BR></BR><BR></BR> A New-AzVM tem um conjunto de parâmetros *simplificados,* onde tudo o que é necessário é um único nome. O valor para -Nome será usado como o nome de todos os recursos necessários para a criação de um novo VM. Pode especificar mais, mas isto é tudo o que é necessário.|
| Criar uma VM a partir de uma imagem personalizada | New-AzVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "myImage" -Localização $location  <BR></BR><BR></BR>Precisa já ter criado a sua própria [imagem gerida.](capture-image-resource.md) Pode usar uma imagem para fazer mDs múltiplos e idênticos. |



## <a name="create-a-vm-configuration"></a>Criar uma configuração VM

| Tarefa | Comando |
| ---- | ------- |
| Criar uma configuração VM |$vm = [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>A configuração VM é utilizada para definir ou atualizar definições para o VM. A configuração é inicializada com o nome do VM e o seu [tamanho](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Adicionar definições de configuração |$vm = [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credencial $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>As definições do sistema operativo, incluindo [credenciais,](https://technet.microsoft.com/library/hh849815.aspx) são adicionadas ao objeto de configuração que criou anteriormente utilizando o New-AzVMConfig. |
| Adicione uma interface de rede |$vm = [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) -VM $vm -Id $nic. Id<BR></BR><BR></BR>Um VM deve ter uma interface de [rede](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para comunicar numa rede virtual. Também pode utilizar o [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) para recuperar um objeto de interface de rede existente. |
| Especificar uma imagem da plataforma |$vm = [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) -VM $vm -PublisherName "publisher_name" -Offer "publisher_offer" -Skus "product_sku" -Versão "mais recente"<BR></BR><BR></BR>[As informações de imagem](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) são adicionadas ao objeto de configuração que criou anteriormente usando o New-AzVMConfig. O objeto devolvido deste comando só é utilizado quando configura o disco OS para utilizar uma imagem da plataforma. |
| Criar uma VM |[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -ResourceGroupName $myResourceGroup -Localização $location -VM $vm<BR></BR><BR></BR>Todos os recursos são criados num grupo de [recursos.](../../azure-resource-manager/management/manage-resource-groups-powershell.md) Antes de executar este comando, execute New-AzVMConfig, Set-AzVMOperatingSystem, Set-AzVMSourceImage, Add-AzVMNetworkInterface e Set-AzVMOSDisk. |
| Atualizar um VM |[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Obtenha a configuração VM atual utilizando o Get-AzVM, altere as definições de configuração no objeto VM e, em seguida, execute este comando. |

## <a name="get-information-about-vms"></a>Obtenha informações sobre VMs

| Tarefa | Comando |
| ---- | ------- |
| Lista vMs numa subscrição |[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| Lista vMs em um grupo de recursos |Get-AzVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Para obter uma lista de grupos de recursos na sua subscrição, utilize o [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup). |
| Obter informações sobre uma VM |Get-AzVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Gerir VMs
| Tarefa | Comando |
| --- | --- |
| Iniciar uma VM |[Início-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) -Nome de grupo de recursos $myResourceGroup -Nome $myVM |
| Parar uma VM |[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Reiniciar um VM em execução |[Reiniciar-AzVM](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) -RecursoGroupName $myResourceGroup -Name $myVM |
| Eliminar uma VM |[Remover-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -Nome de grupo de recursos $myResourceGroup -Nome $myVM |


## <a name="next-steps"></a>Passos seguintes
* Consulte os passos básicos para criar uma máquina virtual em [Criar um VM windows utilizando o Gestor de Recursos e powerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


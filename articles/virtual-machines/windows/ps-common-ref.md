---
title: Comandos Common PowerShell para Máquinas Virtuais Azure
description: O Common PowerShell ordena que você começa a criar e gerir VMs em Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 6f7f2adb5c3e154c3910ee1082e9afad70de9758
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87836178"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Comandos comuns do PowerShell para criar e gerir Máquinas Virtuais do Azure

Este artigo cobre alguns dos comandos Azure PowerShell que pode utilizar para criar e gerir máquinas virtuais na sua subscrição Azure.  Para uma ajuda mais detalhada com comutadores e opções específicas da linha de comando, pode utilizar o *comando* **Get-Help** .

 

Estas variáveis podem ser úteis para si se executar mais do que um dos comandos neste artigo:

- $location - A localização da máquina virtual. Você pode usar [a Get-AzLocation](/powershell/module/az.resources/get-azlocation) para encontrar uma [região geográfica](https://azure.microsoft.com/regions/) que funcione para si.
- $myResourceGroup - O nome do grupo de recursos que contém a máquina virtual.
- $myVM - O nome da máquina virtual.

## <a name="create-a-vm---simplified"></a>Criar um VM - simplificado

| Tarefa | Comando |
| ---- | ------- |
| Criar um VM simples | [New-AzVM](/powershell/module/az.compute/new-azvm) - Nome $myVM <BR></BR><BR></BR> New-AzVM tem um conjunto de parâmetros *simplificados,* onde tudo o que é necessário é um único nome. O valor para -Nome será usado como o nome de todos os recursos necessários para a criação de um novo VM. Pode especificar mais, mas isto é tudo o que é necessário.|
| Criar uma VM a partir de uma imagem personalizada | New-AzVm -ResourceGroupName $myResourceGroup -Nome $myVM ImageName "myImage" -Localização $location  <BR></BR><BR></BR>Precisa de já ter criado a sua própria [imagem gerida.](capture-image-resource.md) Pode usar uma imagem para fazer VMs múltiplos e idênticos. |



## <a name="create-a-vm-configuration"></a>Criar uma configuração VM

| Tarefa | Comando |
| ---- | ------- |
| Criar uma configuração VM |$vm = [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>A configuração VM é utilizada para definir ou atualizar as definições para o VM. A configuração é inicializada com o nome do VM e o seu [tamanho.](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Adicionar configurações de configuração |$vm = [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>As definições do sistema operativo, incluindo [credenciais,](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1) são adicionadas ao objeto de configuração que criou anteriormente utilizando o New-AzVMConfig. |
| Adicionar uma interface de rede |$vm = [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) -VM $vm -Id $nic. ID<BR></BR><BR></BR>Um VM deve ter uma [interface de rede](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json) para comunicar numa rede virtual. Também pode utilizar [o Get-AzNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) para recuperar um objeto de interface de rede existente. |
| Especificar uma imagem de plataforma |$vm = [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) -VM $vm -PublisherName "publisher_name" -Offer "publisher_offer" -Skus "product_sku" -Versão "mais recente"<BR></BR><BR></BR>[A informação de imagem](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) é adicionada ao objeto de configuração que criou anteriormente usando New-AzVMConfig. O objeto devolvido deste comando só é utilizado quando se define o disco DE para utilizar uma imagem de plataforma. |
| Criar uma VM |[New-AzVM](/powershell/module/az.compute/new-azvm) -ResourceGroupName $myResourceGroup -Localização $location -VM $vm<BR></BR><BR></BR>Todos os recursos são criados num [grupo de recursos.](../../azure-resource-manager/management/manage-resource-groups-powershell.md) Antes de executar este comando, executar New-AzVMConfig, Set-AzVMOperatingSystem, Set-AzVMSourceImage, Add-AzVMNetworkInterface e Set-AzVMOSDisk. |
| Atualizar um VM |[Atualização-AzVM](/powershell/module/az.compute/update-azvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Obtenha a configuração VM atual utilizando o Get-AzVM, altere as definições de configuração no objeto VM e, em seguida, execute este comando. |

## <a name="get-information-about-vms"></a>Obtenha informações sobre VMs

| Tarefa | Comando |
| ---- | ------- |
| Lista de VMs em uma subscrição |[Get-AzVM](/powershell/module/az.compute/get-azvm) |
| Lista VMs em um grupo de recursos |Get-AzVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Para obter uma lista de grupos de recursos na sua subscrição, utilize [o Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup). |
| Obter informações sobre uma VM |Get-AzVM -ResourceGroupName $myResourceGroup -Nome $myVM |

## <a name="manage-vms"></a>Gerir VMs
| Tarefa | Comando |
| --- | --- |
| Iniciar uma VM |[Start-AzVM](/powershell/module/az.compute/start-azvm) -ResourceGroupName $myResourceGroup -Nome $myVM |
| Parar uma VM |[Stop-AzVM](/powershell/module/az.compute/stop-azvm) -ResourceGroupName $myResourceGroup -Nome $myVM |
| Reiniciar um VM em execução |[Reinicie-AzVM](/powershell/module/az.compute/restart-azvm) -ResourceGroupName $myResourceGroup -Nome $myVM |
| Eliminar uma VM |[Remover-AzVM](/powershell/module/az.compute/remove-azvm) -ResourceGroupName $myResourceGroup -Nome $myVM |


## <a name="next-steps"></a>Passos seguintes
* Consulte os passos básicos para criar uma máquina virtual na [Criar um VM do Windows utilizando o Gestor de Recursos e o PowerShell.](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json)
---
title: Comandos comuns do PowerShell para máquinas virtuais do Azure | Microsoft Docs
description: Comandos comuns do PowerShell para ajudá-lo a começar a criar e gerenciar suas VMs do Windows no Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 24cb9db9dff670ece75db24df873d24c08919722
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749258"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Comandos comuns do PowerShell para criar e gerenciar máquinas virtuais do Azure

Este artigo aborda alguns dos comandos Azure PowerShell que você pode usar para criar e gerenciar máquinas virtuais em sua assinatura do Azure.  Para obter ajuda mais detalhada com opções de linha de comando específicas, você pode usar o *comando* **Get-Help** .

 

Essas variáveis podem ser úteis para você se executar mais de um dos comandos neste artigo:

- $location-o local da máquina virtual. Você pode usar [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) para localizar uma [região geográfica](https://azure.microsoft.com/regions/) que funcione para você.
- $myResourceGroup-o nome do grupo de recursos que contém a máquina virtual.
- $myVM-o nome da máquina virtual.

## <a name="create-a-vm---simplified"></a>Criar uma VM-simplificada

| Tarefa | Comando |
| ---- | ------- |
| Criar uma VM simples | [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -Name $myVM <BR></BR><BR></BR> New-AzVM tem um conjunto de parâmetros *simplificados* , onde tudo o que é necessário é um único nome. O valor de-Name será usado como o nome de todos os recursos necessários para criar uma nova VM. Você pode especificar mais, mas isso é tudo o que é necessário.|
| Criar uma VM a partir de uma imagem personalizada | New-AzVm-ResourceGroupName $myResourceGroup-Name $myVM ImageName "MYIMAGE" – Location $location  <BR></BR><BR></BR>Você precisa já ter criado sua própria [imagem gerenciada](capture-image-resource.md). Você pode usar uma imagem para fazer várias VMs idênticas. |



## <a name="create-a-vm-configuration"></a>Criar uma configuração de VM

| Tarefa | Comando |
| ---- | ------- |
| Criar uma configuração de VM |$vm = [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) -VMName $MyVM-VMSize "Standard_D1_v1"<BR></BR><BR></BR>A configuração da VM é usada para definir ou atualizar as configurações da VM. A configuração é inicializada com o nome da VM e seu [tamanho](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Adicionar definições de configuração |$vm = [set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) -VM $VM-Windows-ComputerName $MyVM-Credential $cred-ProvisionVMAgent-EnableAutoUpdate<BR></BR><BR></BR>As configurações do sistema operacional, incluindo [as credenciais](https://technet.microsoft.com/library/hh849815.aspx) , são adicionadas ao objeto de configuração que você criou anteriormente usando New-AzVMConfig. |
| Adicionar uma interface de rede |$vm = [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) -VM $VM-ID $NIC. Sessão<BR></BR><BR></BR>Uma VM deve ter uma [interface de rede](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para se comunicar em uma rede virtual. Você também pode usar [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) para recuperar um objeto de interface de rede existente. |
| Especificar uma imagem de plataforma |$vm = [set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) -VM $VM-PublisherName "publisher_name"-oferta "publisher_offer"-Skus "product_sku"-Version "mais recente"<BR></BR><BR></BR>[As informações da imagem](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) são adicionadas ao objeto de configuração que você criou anteriormente usando New-AzVMConfig. O objeto retornado por esse comando é usado apenas quando você define o disco do sistema operacional para usar uma imagem de plataforma. |
| Criar uma VM |[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -ResourceGroupName $MyResourceGroup-Location $Location-VM $VM<BR></BR><BR></BR>Todos os recursos são criados em um [grupo de recursos](../../azure-resource-manager/manage-resource-groups-powershell.md). Antes de executar esse comando, execute New-AzVMConfig, Set-AzVMOperatingSystem, Set-AzVMSourceImage, Add-AzVMNetworkInterface e Set-AzVMOSDisk. |
| Atualizar uma VM |[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) -ResourceGroupName $MYRESOURCEGROUP-VM $VM<BR></BR><BR></BR>Obtenha a configuração atual da VM usando Get-AzVM, altere as definições de configuração no objeto da VM e, em seguida, execute este comando. |

## <a name="get-information-about-vms"></a>Obter informações sobre VMs

| Tarefa | Comando |
| ---- | ------- |
| Listar VMs em uma assinatura |[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| Listar VMs em um grupo de recursos |Get-AzVM-ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Para obter uma lista de grupos de recursos em sua assinatura, use [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup). |
| Obter informações sobre uma VM |Get-AzVM-ResourceGroupName $myResourceGroup-Name $myVM |

## <a name="manage-vms"></a>Gerir VMs
| Tarefa | Comando |
| --- | --- |
| Iniciar uma VM |[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |
| Parar uma VM |[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |
| Reiniciar uma VM em execução |[Restart-AzVM](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |
| Eliminar uma VM |[Remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -ResourceGroupName $MyResourceGroup-Name $myVM |


## <a name="next-steps"></a>Passos seguintes
* Consulte as etapas básicas para criar uma máquina virtual em [criar uma VM do Windows usando o Gerenciador de recursos e o PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


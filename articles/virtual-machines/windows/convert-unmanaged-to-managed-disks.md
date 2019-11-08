---
title: Converter uma máquina virtual do Windows de discos não gerenciados em discos gerenciados – Azure Managed Disks | Microsoft Docs
description: Como converter uma VM do Windows de discos não gerenciados em discos gerenciados usando o PowerShell no modelo de implantação do Gerenciador de recursos
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: 64ff47f1dc4b06d1407497adf41981c670ea9064
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749527"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Converter uma máquina virtual do Windows de discos não gerenciados em discos gerenciados

Se você tiver VMs (máquinas virtuais) do Windows existentes que usam discos não gerenciados, poderá converter as VMs para usar discos gerenciados por meio do serviço de [Managed disks do Azure](managed-disks-overview.md) . Esse processo converte o disco do sistema operacional e todos os discos de dados anexados.

 

## <a name="before-you-begin"></a>Antes de começar


* [Plano de revisão para a migração para Managed disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Examine [as perguntas frequentes sobre migração para Managed disks](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Os VHDs originais e a conta de armazenamento utilizada pela VM antes da conversão não são eliminados. Continuam a incorrer em custos. Para evitar a cobrança destes artefactos, elimine os blobs de VHD originais depois de verificar que a conversão está concluída. Se você precisar encontrar esses discos desanexados para excluí-los, consulte nosso artigo [Localizar e excluir discos gerenciados e não geridos do Azure desconectados](find-unattached-disks.md).


## <a name="convert-single-instance-vms"></a>Converter VMs de instância única
Esta seção aborda como converter VMs do Azure de instância única de discos não gerenciados em discos gerenciados. (Se suas VMs estiverem em um conjunto de disponibilidade, consulte a próxima seção.) 

1. Desaloque a VM usando o cmdlet [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) . O exemplo a seguir Desaloca a VM denominada `myVM` no grupo de recursos chamado `myResourceGroup`: 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. Converta a VM em Managed disks usando o cmdlet [ConvertTo-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk) . O processo a seguir converte a VM anterior, incluindo o disco do sistema operacional e os discos de dados, e inicia a máquina virtual:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>Converter VMs em um conjunto de disponibilidade

Se as VMs que você deseja converter em discos gerenciados estiverem em um conjunto de disponibilidade, primeiro você precisará converter o conjunto de disponibilidade em um conjunto de disponibilidade gerenciado.

1. Converta o conjunto de disponibilidade usando o cmdlet [Update-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/update-azavailabilityset) . O exemplo a seguir atualiza o conjunto de disponibilidade chamado `myAvailabilitySet` no grupo de recursos chamado `myResourceGroup`:

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   Se a região em que seu conjunto de disponibilidade está localizado tiver apenas 2 domínios de falha gerenciados, mas o número de domínios de falha não gerenciado for 3, esse comando mostrará um erro semelhante a "a contagem de domínio de falha especificada 3 deve estar no intervalo de 1 a 2." Para resolver o erro, atualize o domínio de falha para 2 e atualize `Sku` para `Aligned` da seguinte maneira:

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. Desaloque e converta as VMs no conjunto de disponibilidade. O script a seguir desaloca cada VM usando o cmdlet [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) , converte-a usando [ConvertTo-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk)e a reinicia automaticamente no processo de conversão:

   ```azurepowershell-interactive
   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

   foreach($vmInfo in $avSet.VirtualMachinesReferences)
   {
     $vm = Get-AzVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   }
   ```


## <a name="troubleshooting"></a>Resolução de problemas

Se houver um erro durante a conversão ou se uma VM estiver em um estado de falha devido a problemas em uma conversão anterior, execute o cmdlet `ConvertTo-AzVMManagedDisk` novamente. Uma nova tentativa simples geralmente desbloqueia a situação.
Antes de converter, verifique se todas as extensões de VM estão no estado ' provisionamento bem-sucedido ' ou se a conversão falhará com o código de erro 409.

## <a name="convert-using-the-azure-portal"></a>Converter usando o portal do Azure

Você também pode converter discos não gerenciados em discos gerenciados usando o portal do Azure.

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
2. Selecione a VM na lista de VMs no Portal.
3. Na folha da VM, selecione **discos** no menu.
4. Na parte superior da folha **discos** , selecione **migrar para Managed disks**.
5. Se sua VM estiver em um conjunto de disponibilidade, haverá um aviso na folha **migrar para discos gerenciados** que você precisa para converter o conjunto de disponibilidade primeiro. O aviso deve ter um link no qual você pode clicar para converter o conjunto de disponibilidade. Depois que o conjunto de disponibilidade for convertido ou a VM não estiver em um conjunto de disponibilidade, clique em **migrar** para iniciar o processo de migração de seus discos para o Managed disks.

A VM será interrompida e reiniciada após a conclusão da migração.

## <a name="next-steps"></a>Passos seguintes

[Converter Managed disks Standard para Premium](convert-disk-storage.md)

Faça uma cópia somente leitura de uma VM usando [instantâneos](snapshot-copy-managed-disk.md).


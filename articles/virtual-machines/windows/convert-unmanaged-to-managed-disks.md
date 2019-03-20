---
title: Converter uma máquina virtual do Windows de discos não geridos para managed disks – Managed Disks do Azure | Documentos da Microsoft
description: Como converter uma VM do Windows de discos não geridos para discos geridos com o PowerShell no modelo de implementação do Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.openlocfilehash: d848fdd23f459d7e95e85fe38f2272f4d67c32be
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58120793"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Converter uma máquina virtual do Windows de discos não geridos para managed disks

Se tiver existentes máquinas de virtuais de Windows (VMs) que utilizam discos não geridos, pode converter as VMs para utilizar discos geridos através da [Managed Disks do Azure](managed-disks-overview.md) serviço. Este processo converte o disco do SO e qualquer discos de dados anexados.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="before-you-begin"></a>Antes de começar


* Revisão [planear a migração para os Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Revisão [as FAQ sobre a migração para os Managed Disks](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Converter VMs de instância única
Esta secção abrange como converter VMs do Azure de instância única de discos não geridos para discos geridos. (Se forem as suas VMs num conjunto de disponibilidade, consulte a secção seguinte.) 

1. Desaloque a VM ao utilizar o [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) cmdlet. O exemplo seguinte desaloca a VM com o nome `myVM` no grupo de recursos com o nome `myResourceGroup`: 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. Converter VM para managed disks, utilizando o [ConvertTo-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk) cmdlet. O processo a seguir converte a VM anterior, incluindo o disco do SO e qualquer discos de dados e inicia a Máquina Virtual:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>Converter VMs num conjunto de disponibilidade

Se as VMs que pretende converter para gerido discos estão num conjunto de disponibilidade, tem primeiro de converter o conjunto de disponibilidade para um conjunto de disponibilidade gerido.

1. Converter a conjunto de disponibilidade, utilizando o [AzAvailabilitySet atualização](https://docs.microsoft.com/powershell/module/az.compute/update-azavailabilityset) cmdlet. O exemplo seguinte atualiza a conjunto de disponibilidade designado `myAvailabilitySet` no grupo de recursos com o nome `myResourceGroup`:

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   Se a região onde definir a sua disponibilidade está localizada tem apenas 2 domínios de falha gerido, mas o número de domínios de falha não gerenciado é 3, este comando mostra um erro semelhante a "a contagem de domínios de falhas especificado 3 deve estar no intervalo de 1 a 2." Para resolver o erro, atualize o domínio de falhas para 2 e de atualização `Sku` para `Aligned` da seguinte forma:

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. Desalocar e converter as VMs no conjunto de disponibilidade. O seguinte script desaloca cada VM utilizando o [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) cmdlet, converte-o utilizando [ConvertTo-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk)e reinicia automaticamente como outra parte do processo de conversão:

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

Se existir um erro durante a conversão, ou se uma VM está num Estado de falha devido a problemas de uma conversão anterior, execute o `ConvertTo-AzVMManagedDisk` cmdlet novamente. Uma repetição simple normalmente desbloqueia a situação.
Antes de converter, certificar-se de que todas as extensões VM estão no estado "A aprovisionar foi concluída com êxito" ou a conversão irá falhar com o código de erro 409.


## <a name="convert-using-the-azure-portal"></a>Converter utilizando o portal do Azure

Também pode converter discos não geridos para managed disks através do portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione a VM a partir da lista de VMs no portal.
3. No painel da VM, selecione **discos** no menu.
4. Na parte superior a **discos** painel, selecione **para migrar para managed disks**.
5. Se a sua VM está num conjunto de disponibilidade, haverá um aviso sobre o **migrar para discos geridos** painel que tem de converter o primeiro conjunto de disponibilidade. O aviso deve ter uma ligação, pode clicar para converter o conjunto de disponibilidade. Depois do conjunto de disponibilidade é convertido, ou se a VM não está num conjunto de disponibilidade, clique em **migrar** para iniciar o processo de migrar os seus discos para discos geridos. 

A VM será parada e reiniciada após a conclusão da migração.

## <a name="next-steps"></a>Passos Seguintes

[Converter os discos geridos standard para premium](convert-disk-storage.md)

Ter uma cópia só de leitura de uma VM ao utilizar [instantâneos](snapshot-copy-managed-disk.md).


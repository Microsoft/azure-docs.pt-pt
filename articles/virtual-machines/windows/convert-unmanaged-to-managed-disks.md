---
title: Convert a Windows virtual machine from unmanaged disks to managed disks (Converter uma máquina virtual do Windows de discos não geridos em discos geridos)
description: Como converter um VM do Windows de discos não geridos para discos geridos utilizando o PowerShell no modelo de implementação do Gestor de Recursos
author: roygara
ms.service: virtual-machines-windows
ms.subservice: disks
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: d8069b174b7a69cc2e6c47171159569c56a15563
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82081953"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Convert a Windows virtual machine from unmanaged disks to managed disks (Converter uma máquina virtual do Windows de discos não geridos em discos geridos)

Se tiver máquinas virtuais windows existentes (VMs) que utilizam discos não geridos, pode converter os VMs para utilizar discos geridos através do serviço [De discos geridos azure.](managed-disks-overview.md) Este processo converte tanto o disco OS como quaisquer discos de dados anexados.

 

## <a name="before-you-begin"></a>Antes de começar


* Plano de Revisão [para a migração para Discos Geridos](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Reveja [as FAQ sobre a migração para Discos Geridos](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Os VHDs originais e a conta de armazenamento utilizada pela VM antes da conversão não são eliminados. Continuam a incorrer em custos. Para evitar a cobrança destes artefactos, elimine os blobs de VHD originais depois de verificar que a conversão está concluída. Se precisar de encontrar estes discos não ligados para os eliminar, consulte o nosso artigo [Encontre e elimine discos não ligados geridos e não geridos](find-unattached-disks.md).


## <a name="convert-single-instance-vms"></a>Converter VMs de instância única
Esta secção cobre a forma de converter VMs Azure de uma única instância de discos não geridos para discos geridos. (Se os seus VMs estiverem num conjunto de disponibilidade, consulte a secção seguinte.) 

1. Deslocar o VM utilizando o cmdlet [Stop-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) O exemplo seguinte desafeta `myVM` o VM `myResourceGroup`nomeado no grupo de recursos denominado: 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. Converta o VM em discos geridos utilizando o cmdlet [ConvertTo-AzVMManagedDisk.](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk) O seguinte processo converte o VM anterior, incluindo o disco OS e quaisquer discos de dados, e inicia a Máquina Virtual:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>Converter VMs num conjunto de disponibilidade

Se os VMs que pretende converter para discos geridos estiverem num conjunto de disponibilidade, primeiro precisa converter o conjunto de disponibilidade para um conjunto de disponibilidade gerido.

1. Converta a disponibilidade definida utilizando o cmdlet [Update-AzAvailabilitySet.](https://docs.microsoft.com/powershell/module/az.compute/update-azavailabilityset) O exemplo seguinte atualiza o `myAvailabilitySet` conjunto de disponibilidade `myResourceGroup`nomeado no grupo de recursos denominado:

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   Se a região onde o seu conjunto de disponibilidade está localizado tiver apenas 2 domínios de falha geridos, mas o número de domínios de falha não geridos é 3, este comando mostra um erro semelhante ao "A contagem de domínio de falha especificada 3 deve cair no intervalo 1 a 2." Para resolver o erro, atualize o `Sku` `Aligned` domínio da falha para 2 e atualize para o seguinte:

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. Deslocar e converter os VMs no conjunto de disponibilidade. O seguinte script desafeta cada VM utilizando o cmdlet [Stop-AzVM, converte-o](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) utilizando [convertto-AzVMManagedDisk,](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk)e reinicia-o automaticamente à parte do processo de conversão:

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

Se houver um erro durante a conversão, ou se um VM estiver em estado `ConvertTo-AzVMManagedDisk` de falha devido a problemas numa conversão anterior, volte a executar o cmdlet. Uma simples retentativa geralmente desbloqueia a situação.
Antes de converter, certifique-se de que todas as extensões VM estão no estado de "Provisioning succeeded" ou que a conversão falhará com o código de erro 409.

## <a name="convert-using-the-azure-portal"></a>Converter usando o portal Azure

Também pode converter discos não geridos para discos geridos utilizando o portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione o VM da lista de VMs no portal.
3. Na lâmina para o VM, selecione **Discos** do menu.
4. Na parte superior da lâmina **dos Discos,** selecione **Migrar para discos geridos**.
5. Se o seu VM estiver num conjunto de disponibilidade, haverá um aviso na lâmina de **discos gerido seletiva** que precisa para converter primeiro o conjunto de disponibilidade. O aviso deve ter um link que pode clicar para converter o conjunto de disponibilidade. Uma vez que o conjunto de disponibilidade é convertido ou se o seu VM não estiver num conjunto de disponibilidade, clique em **Migrar** para iniciar o processo de migração dos seus discos para discos geridos.

O VM será parado e reiniciado após a migração estar completa.

## <a name="next-steps"></a>Passos seguintes

[Converter discos geridos padrão para premium](convert-disk-storage.md)

Pegue uma cópia apenas de um VM através da utilização de [fotografias](snapshot-copy-managed-disk.md).


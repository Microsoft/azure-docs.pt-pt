---
title: Converter uma máquina virtual do Windows de discos não geridos em discos geridos
description: Como converter um VM do Windows de discos não geridos para discos geridos utilizando o PowerShell no modelo de implementação do Gestor de Recursos
author: roygara
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: 3f586c8907ded618b4cc4aaaadf3c87471cda0b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550727"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Converter uma máquina virtual do Windows de discos não geridos em discos geridos

Se tiver máquinas virtuais (VMs) existentes que utilizam discos não geridos, pode converter os VMs para utilizar discos geridos através do serviço [Azure Managed Disks.](../managed-disks-overview.md) Este processo converte tanto o disco de SO como quaisquer discos de dados anexados.

 

## <a name="before-you-begin"></a>Antes de começar


* Plano de Revisão [da migração para Discos Geridos.](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)

* [Reveja as FAQ sobre a migração para Discos Geridos.](../faq-for-disks.md#migrate-to-managed-disks)

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Os VHDs originais e a conta de armazenamento utilizada pela VM antes da conversão não são eliminados. Continuam a incorrer em custos. Para evitar a cobrança destes artefactos, elimine os blobs de VHD originais depois de verificar que a conversão está concluída. Se precisar de encontrar estes discos não ligados para os eliminar, consulte o nosso artigo [Encontre e elimine discos geridos e não geridos do Azure](find-unattached-disks.md)não ligados .


## <a name="convert-single-instance-vms"></a>Converter VMs de instância única
Esta secção abrange como converter VMs Azure de instância única de discos não geridos para discos geridos. (Se os seus VMs estiverem num conjunto de disponibilidade, consulte a secção seguinte.) 

1. Translocar o VM utilizando o [cmdlet Stop-AzVM.](/powershell/module/az.compute/stop-azvm) O exemplo a seguir é o VM nomeado `myVM` no grupo de recursos `myResourceGroup` denominado: 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. Converta o VM em discos geridos utilizando o [cmdlet ConvertTo-AzVMManagedDisk.](/powershell/module/az.compute/convertto-azvmmanageddisk) O processo seguinte converte o VM anterior, incluindo o disco de oss e quaisquer discos de dados, e inicia a Máquina Virtual:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>Converter VMs em um conjunto de disponibilidade

Se os VMs que pretende converter para discos geridos estiverem num conjunto de disponibilidade, primeiro tem de converter o conjunto de disponibilidade para um conjunto de disponibilidade gerida.

1. Converta a disponibilidade definida utilizando o [cmdlet Update-AzAvailabilitySet.](/powershell/module/az.compute/update-azavailabilityset) O exemplo a seguir atualiza o conjunto de disponibilidade nomeado `myAvailabilitySet` no grupo de recursos `myResourceGroup` denominado:

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   Se a região onde o seu conjunto de disponibilidade está localizado tem apenas 2 domínios de avariageridos, mas o número de domínios de avaria não geridos é 3, este comando mostra um erro semelhante ao "A contagem de domínio de avaria especificada 3 deve cair na faixa 1 a 2." Para resolver o erro, atualize o domínio da falha para 2 e atualize `Sku` da `Aligned` seguinte forma:

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. Translocar e converter os VMs no conjunto de disponibilidade. O seguinte script negoceia cada VM utilizando o cmdlet [Stop-AzVM,](/powershell/module/az.compute/stop-azvm) converte-o utilizando [o ConvertTo-AzVMManagedDisk](/powershell/module/az.compute/convertto-azvmmanageddisk), e reinicia-o automaticamente como separado do processo de conversão:

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

Se houver um erro durante a conversão, ou se um VM estiver em estado de falha devido a problemas numa conversão anterior, volte a executar o `ConvertTo-AzVMManagedDisk` cmdlet. Uma simples repetição geralmente desbloqueia a situação.
Antes de converter, certifique-se de que todas as extensões VM estão no estado de "Provisioning succeeded" ou que a conversão falhará com o código de erro 409.

## <a name="convert-using-the-azure-portal"></a>Converter usando o portal Azure

Também pode converter discos não geridos para discos geridos utilizando o portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione o VM da lista de VMs no portal.
3. Na lâmina para o VM, selecione **Discos** do menu.
4. Na parte superior da lâmina **dos Discos,** selecione **Migrar para discos geridos**.
5. Se o seu VM estiver num conjunto de disponibilidade, haverá um aviso no Migrar para a lâmina **de discos geridos** que precisa para converter primeiro o conjunto de disponibilidade. O aviso deve ter um link que pode clicar para converter o conjunto de disponibilidade. Uma vez que o conjunto de disponibilidade é convertido ou se o seu VM não estiver num conjunto de disponibilidade, clique em **Migrar** para iniciar o processo de migração dos seus discos para discos geridos.

O VM será interrompido e reiniciado após a conclusão da migração.

## <a name="next-steps"></a>Passos seguintes

[Converter discos geridos padrão para premium](convert-disk-storage.md)

Faça uma cópia apenas de leitura de um VM utilizando [instantâneos](snapshot-copy-managed-disk.md).
---
title: O Azure Site Recovery - exclusão de discos durante a replicação de máquinas virtuais do Azure com o Azure PowerShell | Documentos da Microsoft
description: Saiba como excluir discos de máquinas virtuais do Azure durante a recuperação de Site do Azure com o Azure PowerShell.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 54a32d7f7aa4bcab73f5828da3e7eba9d25276be
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678280"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Excluir discos da replicação do PowerShell de VMs do Azure

Este artigo descreve como excluir discos quando replicar VMs do Azure. Pode excluir discos para otimizar a largura de banda de replicação consumida ou os recursos do lado do destino que esses discos utilizam. Atualmente, esta capacidade está disponível apenas através do PowerShell do Azure.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

- Certifique-se de que compreende os [arquitetura de recuperação após desastre e os componentes](azure-to-azure-architecture.md).
- Reveja os [requisitos de suporte](azure-to-azure-support-matrix.md) de todos os componentes.
- Certifique-se de que tem AzureRm PowerShell "Az" módulo. Para instalar ou atualizar o PowerShell, veja [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Certifique-se de que criou um cofre dos serviços de recuperação e protegidas máquinas virtuais, pelo menos, uma vez. Se ainda não fez tudo isto, siga o processo na [configurar a recuperação após desastre para máquinas virtuais do Azure com o Azure PowerShell](azure-to-azure-powershell.md).

## <a name="why-exclude-disks-from-replication"></a>Porquê excluir discos da replicação
Poderá ter de excluir discos da replicação porque:

- Atingiu a sua máquina virtual [taxas de alteração de limites do Azure Site Recovery para replicar dados](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix).

- Os dados de alterações no disco excluído não não importantes ou não têm de ser replicados.

- Pretende guardar os recursos de armazenamento e de rede ao não replicar os dados.

## <a name="how-to-exclude-disks-from-replication"></a>Como excluir discos da replicação

No nosso exemplo, replicar uma máquina virtual que tenha um SO e de três discos de dados, ou seja, na região E.U.A. Leste para a região E.U.A. oeste 2. O nome da máquina virtual é *AzureDemoVM*. Vamos excluir o disco 1 e manter os discos de 2 e 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Obter os detalhes das máquinas virtuais a replicar

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Obtenha detalhes sobre os discos da máquina virtual. Estas informações serão utilizadas mais tarde, quando inicia a replicação da VM.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Replicar máquinas virtuais do Azure

Para o exemplo a seguir, partimos do princípio que já tem uma conta de armazenamento de cache, política de replicação e mapeamentos. Se não tiver essas coisas, siga o processo na [configurar a recuperação após desastre para máquinas virtuais do Azure com o Azure PowerShell](azure-to-azure-powershell.md).

Replicar máquinas virtuais do Azure, com *discos geridos*.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication. 

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Quando a operação de replicação inicial for concluída com êxito, os dados da VM são replicados para a região de recuperação.

Pode ir ao portal do Azure e ver as VMs replicadas em "itens replicados."

Inicia o processo de replicação, propagando uma cópia de discos de replicação da máquina virtual na região de recuperação. Nesta fase é chamada a fase de replicação inicial.

Depois de concluída a replicação inicial, replicação passa para a fase de sincronização diferencial. Neste momento, a máquina virtual está protegida. Selecione a máquina virtual protegida para ver se os discos são excluídos.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [executar uma ativação pós-falha de teste](site-recovery-test-failover-to-azure.md).

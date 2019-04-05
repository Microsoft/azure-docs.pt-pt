---
title: O Azure Site Recovery - excluir o disco durante a replicação de máquinas virtuais do Azure com o Azure PowerShell | Documentos da Microsoft
description: Saiba como excluir o disco para máquinas virtuais do Azure com o Azure Site Recovery com o Azure PowerShell.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 1c278d810df7e5ba8701529a59987c9bb16fa40c
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044130"
---
# <a name="exclude-disks-from-replication-of-azure-vms-to-azure-using-azure-powershell"></a>Excluir discos da replicação de VMs do Azure para o Azure com o Azure PowerShell

Este artigo descreve como excluir discos ao replicar VMs do Azure. Esta exclusão pode otimizar a largura de banda de replicação consumida ou otimizar os recursos do lado do destino que esses discos utilizam. Atualmente, esta capacidade está exposta apenas através do PowerShell do Azure.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

- Certifique-se de que compreende a [arquitetura e os componentes do cenário](azure-to-azure-architecture.md).
- Reveja os [requisitos de suporte](azure-to-azure-support-matrix.md) de todos os componentes.
- Tem o Azure PowerShell `Az` módulo. Se precisar de instalar ou atualizar o Azure PowerShell, siga este [guia para instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps).
- Já tiver criado o Cofre dos serviços de recuperação e o tiver feito a proteção de, pelo menos, de máquinas virtuais uma vez. Se não, em seguida, fazê-lo com a documentação mencionada [aqui](azure-to-azure-powershell.md).

## <a name="why-exclude-disks-from-replication"></a>Porquê excluir discos da replicação?
Muitas vezes, é necessário excluir discos da replicação porque:

- Atingiu a sua máquina virtual [limites do Azure Site Recovery para replicar dados de taxas de alteração](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)

- As alterações a dados no disco excluído não são importantes ou não têm de ser replicadas.

- Pretende poupar recursos de armazenamento e de rede ao não replicar estas alterações.


## <a name="how-to-exclude-disks-from-replication"></a>Como excluir discos da replicação?

O exemplo neste artigo, uma máquina virtual que tem 1 SO e discos de 3 dados na região E.U.A. Leste serão replicados para a região E.U.A. oeste 2. O nome da máquina virtual utilizada no exemplo é AzureDemoVM. e vamos irá excluir o disco 1 e manterá o disco 2 e 3

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Obter detalhes sobre as máquinas virtuais a serem replicados

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


Obtenha detalhes do disco para os discos da máquina virtual. Detalhes do disco serão utilizados mais tarde ao iniciar a replicação para a máquina virtual.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-azure-virtual-machine"></a>Replicar máquinas virtuais do Azure

No exemplo abaixo, pressupomos que já tem uma conta de armazenamento de cache, a política de replicação e mapeamentos. Se não, em seguida, fazê-lo com a documentação mencionada [aqui](azure-to-azure-powershell.md) 


Replicar a máquina virtual do Azure com **discos geridos**.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded so we will provide it during the time of replication 

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


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Assim que a operação de replicação inicial for concluída com êxito, os dados de máquina virtual são replicados para a região de recuperação.

Pode aceder ao portal do Azure e em itens replicados pode ver as máquinas virtuais replicadas de introdução.
Inicia o processo de replicação, propagando inicialmente uma cópia de discos de replicação da máquina virtual na região de recuperação. Nesta fase é chamada a fase de replicação inicial.

Uma vez concluída a replicação inicial, a replicação move para a fase de sincronização diferencial. Neste momento, a máquina virtual está protegida. Clique na máquina virtual protegida > discos para ver que, se o disco seja excluído ou não.

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre como executar uma ativação pós-falha de teste.

---
title: Excluir discos VM do Azure da replicação com Azure Site Recovery e Azure PowerShell
description: Saiba como excluir discos de máquinas virtuais Azure durante a Recuperação do Local Azure utilizando a Azure PowerShell.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 02/18/2019
ms.openlocfilehash: a21460279420c46b11c43615ae5ecc7bfa81de4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86135801"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Excluir discos da replicação powerShell de VMs Azure

Este artigo descreve como excluir discos quando replica VMs Azure. Pode excluir discos para otimizar a largura de banda de replicação consumida ou os recursos do lado alvo que esses discos usam. Atualmente, esta capacidade está disponível apenas através do Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

- Certifique-se de que compreende a [arquitetura e componentes de recuperação de desastres.](azure-to-azure-architecture.md)
- Reveja os [requisitos de suporte](azure-to-azure-support-matrix.md) para todos os componentes.
- Certifique-se de que tem o módulo AzureRm PowerShell "Az". Para instalar ou atualizar o PowerShell, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps).
- Certifique-se de que criou um cofre de serviços de recuperação e protegeu máquinas virtuais pelo menos uma vez. Se ainda não fez estas coisas, siga o processo de recuperação de [desastres para máquinas virtuais Azure utilizando a Azure PowerShell](azure-to-azure-powershell.md).
- Se procura informações sobre a adição de discos a um VM Azure habilitado para replicação, [reveja este artigo](azure-to-azure-enable-replication-added-disk.md).

## <a name="why-exclude-disks-from-replication"></a>Porquê excluir discos da replicação
Pode ser necessário excluir discos da replicação porque:

- A sua máquina virtual atingiu [os limites de recuperação do local de Azure para replicar as taxas de alteração de dados.](./azure-to-azure-support-matrix.md)

- Os dados que estão no disco excluído não são importantes ou não precisam de ser replicados.

- Pretende guardar recursos de armazenamento e rede não replicando os dados.

## <a name="how-to-exclude-disks-from-replication"></a>Como excluir discos da replicação

No nosso exemplo, replicamos uma máquina virtual que tem um SISTEMA e três discos de dados que está na região leste dos EUA para a região oeste dos EUA 2. O nome da máquina virtual é *AzureDemoVM*. Excluímos o disco 1 e mantemos os discos 2 e 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Obtenha detalhes das máquinas virtuais para replicar

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

Obtenha detalhes sobre os discos da máquina virtual. Esta informação será usada mais tarde quando começar a replicação do VM.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Replicar uma máquina virtual Azure

Para o exemplo seguinte, assumimos que já tem uma conta de armazenamento de cache, política de replicação e mapeamentos. Se não tiver estas coisas, siga o processo na [Configuração de recuperação de desastres para máquinas virtuais Azure usando Azure PowerShell](azure-to-azure-powershell.md).

Replicar uma máquina virtual Azure com *discos geridos*.

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

Quando a operação de replicação de arranque for bem sucedida, os dados VM são replicados na região de recuperação.

Pode ir ao portal Azure e ver os VMs replicados em "itens replicados".

O processo de replicação começa por semear uma cópia dos discos replicadores da máquina virtual na região de recuperação. Esta fase é chamada de fase inicial de replicação.

Após o final da replicação inicial, a replicação passa para a fase de sincronização diferencial. Neste ponto, a máquina virtual está protegida. Selecione a máquina virtual protegida para ver se algum disco está excluído.

## <a name="next-steps"></a>Passos seguintes

Saiba mais [sobre a execução de um teste falhado](site-recovery-test-failover-to-azure.md).

---
title: Recuperação de desastres para VMs Azure usando Azure PowerShell e Azure Site Recovery
description: Saiba como configurar a recuperação de desastres para máquinas virtuais Azure com a Recuperação do Site Azure usando a Azure PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: 1570bd9dfa62caa749d5a3983b93c2555be058ec
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348734"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Configurar a recuperação após desastre para as máquinas virtuais do Azure com o Azure PowerShell

Neste artigo, você vê como configurar e testar a recuperação de desastres para máquinas virtuais Azure usando Azure PowerShell.

Saiba como:

> [!div class="checklist"]
> - Crie um cofre dos Serviços de Recuperação.
> - Desa parte para a sessão PowerShell.
> - Prepare o cofre para começar a replicar máquinas virtuais Azure.
> - Crie mapeamentos de rede.
> - Criar contas de armazenamento para replicar máquinas virtuais para.
> - Replicar máquinas virtuais Azure para uma região de recuperação para recuperação de desastres.
> - Efetue um teste de failover, valide e teste de limpeza falhou.
> - Falha na região de recuperação.

> [!NOTE]
> Nem todas as capacidades de cenário disponíveis através do portal podem estar disponíveis através do Azure PowerShell. Algumas das capacidades do cenário que não são suportadas atualmente através do Azure PowerShell são:
> - A capacidade de especificar que todos os discos de uma máquina virtual devem ser replicados sem ter de especificar explicitamente cada disco da máquina virtual.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:
- Certifique-se de que compreende a [arquitetura e os componentes do cenário](azure-to-azure-architecture.md).
- Reveja os [requisitos de suporte](azure-to-azure-support-matrix.md) para todos os componentes.
- Tem o módulo Azure `Az` PowerShell. Se necessitar de instalar ou atualizar o Azure PowerShell, siga este [Guia para instalar e configurar a Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Inscreva-se na subscrição do Microsoft Azure

Inscreva-se na sua assinatura Azure com o `Connect-AzAccount` cmdlet.

```azurepowershell
Connect-AzAccount
```

Selecione a sua subscrição do Azure. Use o `Get-AzSubscription` cmdlet para obter a lista de subscrições Azure a que tem acesso. Selecione a subscrição Azure para trabalhar com a utilização do `Set-AzContext` cmdlet.

```azurepowershell
Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machine-to-be-replicated"></a>Obtenha detalhes da máquina virtual a ser replicada

Neste artigo, uma máquina virtual na região leste dos EUA é replicada e recuperada na região oeste dos EUA 2. A máquina virtual que está a ser replicada tem um disco de SO e um único disco de dados. O nome da máquina virtual utilizada no exemplo é `AzureDemoVM` .

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM
```

```Output
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

Obtenha detalhes do disco para os discos da máquina virtual. Os detalhes do disco serão usados mais tarde quando iniciar a replicação para a máquina virtual.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Criar um grupo de recursos para criar o cofre dos Serviços de Recuperação.

> [!IMPORTANT]
> * O cofre dos serviços de recuperação e as máquinas virtuais protegidas devem estar em diferentes locais do Azure.
> * O grupo de recursos do cofre dos serviços de recuperação, e as máquinas virtuais protegidas, devem estar em diferentes locais do Azure.
> * O cofre dos serviços de recuperação, e o grupo de recursos a que pertence, pode estar na mesma localização Azure.

No exemplo deste artigo, a máquina virtual que está a ser protegida está na região leste dos EUA. A região de recuperação selecionada para a recuperação de desastres é a região oeste dos EUA 2. O cofre dos serviços de recuperação, e o grupo de recursos do cofre, estão ambos na região de recuperação, West US 2.

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```

```Output
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Criar um cofre de serviços de recuperação. Neste exemplo, um cofre dos Serviços de Recuperação nomeado `a2aDemoRecoveryVault` é criado na região oeste dos EUA 2.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```

```Output
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="set-the-vault-context"></a>Desa parte para o contexto do cofre

Desa estaba o contexto do cofre para utilização na sessão PowerShell. Após o contexto do cofre ser definido, as operações de recuperação do local de Azure na sessão PowerShell são realizadas no contexto do cofre selecionado.

```azurepowershell
#Setting the vault context.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

```Output
ResourceName         ResourceGroupName ResourceNamespace          ResourceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```

Para uma migração Azure-to-Azure, você pode definir o contexto do cofre para o recém-criado cofre:

```azurepowershell
#Set the vault context for the PowerShell session.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Prepare o cofre para começar a replicar máquinas virtuais Azure

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>Criar um objeto de tecido de recuperação de local para representar a região primária (fonte)

O objeto de tecido no cofre representa uma região de Azure. O objeto de tecido primário é criado para representar a região de Azure a que pertencem as máquinas virtuais protegidas ao cofre. No exemplo deste artigo, a máquina virtual que está a ser protegida está na região leste dos EUA.

- Apenas um objeto de tecido pode ser criado por região.
- Se já ativou a replicação da Recuperação do Local para um VM no portal Azure, a Recuperação do Local cria automaticamente um objeto de tecido. Se um objeto de tecido existe para uma região, não se pode criar um novo.

Antes de começar, compreenda que as operações de Recuperação do Local são executadas assíncroticamente. Quando inicia uma operação, é submetido um trabalho de Recuperação do Local Azure e um objeto de rastreio de emprego é devolvido. Utilize o objeto de rastreio de trabalho para obter o estado mais recente do trabalho `Get-AzRecoveryServicesAsrJob` e para monitorizar o estado da operação.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-EastUS"
```

Se máquinas virtuais de várias regiões do Azure estiverem protegidas para o mesmo cofre, crie um objeto de tecido para cada fonte da região de Azure.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>Criar um objeto de tecido de recuperação de local para representar a região de recuperação

O objeto de tecido de recuperação representa a recuperação da localização do Azure. Se houver uma falha, as máquinas virtuais são replicadas e recuperadas para a região de recuperação representada pelo tecido de recuperação. A região de Azure de recuperação utilizada neste exemplo é o Oeste dos EUA 2.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-WestUS"
```

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>Criar um recipiente de proteção de recuperação do local no tecido primário

O recipiente de proteção é um recipiente utilizado para agrupar objetos replicados dentro de um tecido.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```

### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>Criar um recipiente de proteção de recuperação do local no tecido de recuperação

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

#### <a name="fabric-and-container-creation-when-enabling-zone-to-zone-replication"></a>Criação de tecidos e contentores ao permitir a zona de replicação da zona

Ao permitir a zona de replicação da zona, apenas será criado um tecido. Mas haverá dois contentores. Assumindo que a região é a Europa Ocidental, use as seguintes ordens para obter os recipientes primários e de proteção -

```azurepowershell
$primaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $fabric -Name "asr-a2a-default-westeurope-container"
$recoveryPprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $fabric -Name "asr-a2a-default-westeurope-t-container"
```

### <a name="create-a-replication-policy"></a>Criar uma política de replicação

```azurepowershell
#Create replication policy
$TempASRJob = New-AzRecoveryServicesAsrPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "A2APolicy"
```

### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>Criar um mapeamento de recipiente de proteção entre o recipiente de proteção primária e de recuperação

Um mapeamento de contentores de proteção mapeia o recipiente de proteção primária com um recipiente de proteção de recuperação e uma política de replicação. Crie um mapeamento para cada política de replicação que utilizará para replicar máquinas virtuais entre um par de recipientes de proteção.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

#### <a name="protection-container-mapping-creation-when-enabling-zone-to-zone-replication"></a>Criação de mapeamento de contentores de proteção ao permitir a replicação da zona

Ao permitir a zona de replicação da zona, utilize o comando abaixo para criar mapeamento de recipientes de proteção. Assumindo que a região é a Europa Ocidental, o comando será -

```azurepowershell
$protContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $PrimprotectionContainer -Name "westeurope-westeurope-24-hour-retention-policy-s"
```

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>Criar um mapeamento de recipiente de proteção para falhas (replicação inversa após uma falha)

Depois de um fracasso, quando estiveres pronto para trazer a máquina virtual falhada de volta à região original do Azure, fazes uma falha. Para falhar, a máquina virtual falhada é replicada inversamente da região falhada para a região original. Para a replicação inversa, os papéis da região original e a mudança da região de recuperação. A região original passa a ser a nova região de recuperação, e o que era originalmente a região de recuperação passa a ser a região primária. O mapeamento do contentor de proteção para a replicação inversa representa as funções comutados das regiões originais e de recuperação.

```azurepowershell
#Create Protection container mapping (for fail back) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$WusToEusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $RecoveryProtContainer -Name "A2ARecoveryToPrimary"
```

## <a name="create-cache-storage-account-and-target-storage-account"></a>Criar conta de armazenamento de cache e conta de armazenamento alvo

Uma conta de armazenamento de cache é uma conta de armazenamento padrão na mesma região de Azure que a máquina virtual que está sendo replicada. A conta de armazenamento de cache é usada para conter alterações de replicação temporariamente, antes que as alterações sejam transferidas para a região de Azure de recuperação. Pode optar, mas não é necessário, especificar diferentes contas de armazenamento de cache para os diferentes discos de uma máquina virtual.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

Para as máquinas virtuais **que não utilizam discos geridos,** a conta de armazenamento alvo é a conta de armazenamento na região de recuperação para a qual são replicados os discos da máquina virtual. A conta de armazenamento alvo pode ser uma conta de armazenamento padrão ou uma conta de armazenamento premium. Selecione o tipo de conta de armazenamento necessária com base na taxa de alteração de dados (taxa de escrita de IO) para os discos e a Recuperação do Local Azure suportada limites de churn para o tipo de armazenamento.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage
```

## <a name="create-network-mappings"></a>Criar mapeamentos de rede

Um mapeamento de rede mapeia redes virtuais na região primária para redes virtuais na região de recuperação. O mapeamento da rede especifica a rede virtual Azure na região de recuperação, que uma máquina virtual na rede virtual primária deve falhar. Uma rede virtual Azure pode ser mapeada para apenas uma única rede virtual Azure numa região de recuperação.

- Criar uma rede virtual Azure na região de recuperação para falhar:

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```

- Recupere a rede virtual primária. O VNet a que a máquina virtual está ligada:

   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resource name
    $NIC = Get-AzNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")
   ```

- Criar mapeamento de rede entre a rede virtual primária e a rede virtual de recuperação:

   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
   ```

- Criar mapeamento de rede para a direção inversa (falhar para trás):

    ```azurepowershell
    #Create an ASR network mapping for fail back between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Replicar máquina virtual Azure

Replicar a máquina virtual Azure com **discos geridos**.

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Replicar a máquina virtual Azure com **discos não geridos**.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

Uma vez que a operação de replicação inicial tenha sucesso, os dados da máquina virtual são replicados na região de recuperação.

O processo de replicação começa por sementeirar inicialmente uma cópia dos discos replicadores da máquina virtual na região de recuperação. Esta fase é chamada de fase inicial de replicação.

A replicação inicial do AFter completa-se, a replicação move-se para a fase de sincronização diferencial. Neste ponto, a máquina virtual está protegida e pode ser efetuada uma operação de falha de teste. O estado de replicação do item replicado que representa a máquina virtual vai para o estado **protegido** após a replicação inicial.

Monitorize o estado de replicação e a saúde de replicação para a máquina virtual, obtendo detalhes do item protegido de replicação correspondente à sua.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal
```

## <a name="do-a-test-failover-validate-and-cleanup-test-failover"></a>Faça um teste de failover, valide e teste de limpeza falhou

Após a replicação da máquina virtual ter atingido um estado protegido, pode ser efetuada uma operação de falha de teste na máquina virtual (no item protegido de replicação da máquina virtual).

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Faça um teste de failover.

```azurepowershell
$ReplicationProtectedItem = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery
```

Aguarde que a operação de falha do teste esteja concluída.

```azurepowershell
Get-AzRecoveryServicesAsrJob -Job $TFOJob
```

```Output
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```

Após o teste de failover trabalho concluído com sucesso, pode ligar-se ao teste falhado sobre a máquina virtual e validar o teste de falha.

Uma vez concluído o teste no teste falhou sobre a máquina virtual, limpe a cópia de teste iniciando a operação de falha do teste de limpeza. Esta operação elimina a cópia de teste da máquina virtual criada pela falha de teste.

```azurepowershell
$Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $Job_TFOCleanup | Select State
```

```Output
State
-----
Succeeded
```

## <a name="fail-over-to-azure"></a>Ativação pós-falha para o Azure

Falhe sobre a máquina virtual para um ponto de recuperação específico.

```azurepowershell
$RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```

```Output
CrashConsistent 4/24/2018 11:10:25 PM
```

```azurepowershell
#Start the fail over job
$Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-AzRecoveryServicesAsrJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```

```Output
Succeeded
```

Quando o trabalho de falha for bem sucedido, pode cometer a operação de failover.

```azurepowershell
$CommitFailoverJOb = Start-AzRecoveryServicesAsrCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $CommitFailoverJOb
```

```Output
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

## <a name="reprotect-and-fail-back-to-the-source-region"></a>Reprotegir e falhar de volta à região de origem

Depois de um failover, quando estiver pronto para voltar à região original, comece a replicar-se para o item protegido de replicação usando o `Update-AzRecoveryServicesAsrProtectionDirection` cmdlet.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage
```

```azurepowershell
#Use the recovery protection container, new cache storage account in West US and the source region VM resource group
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure
-ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId
```

Depois de a reproteção estar completa, pode falhar na direção inversa, oeste dos EUA para leste dos EUA, e falhar de volta à região de origem.

## <a name="disable-replication"></a>Desativar a replicação

Pode desativar a replicação com o `Remove-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet.

```azurepowershell
Remove-AzRecoveryServicesAsrReplicationProtectedItem -ReplicationProtectedItem $ReplicationProtectedItem
```

## <a name="next-steps"></a>Próximos passos

Consulte a [referência PowerShell de Recuperação do Site Azure](/powershell/module/az.RecoveryServices) para saber como pode fazer outras tarefas, tais como criar planos de recuperação e testar o failover dos planos de recuperação com o PowerShell.

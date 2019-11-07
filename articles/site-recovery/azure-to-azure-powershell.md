---
title: Recuperação de desastre para VMs do Azure usando Azure PowerShell e Azure Site Recovery
description: Saiba como configurar a recuperação de desastre para máquinas virtuais do Azure com Azure Site Recovery usando Azure PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: aa91725daf36113334849dd15dd01b6ce6ed4389
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621081"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Configurar a recuperação de desastre para máquinas virtuais do Azure usando Azure PowerShell


Neste artigo, você verá como configurar e testar a recuperação de desastre para máquinas virtuais do Azure usando Azure PowerShell.

Saiba como:

> [!div class="checklist"]
> - Crie um cofre dos Serviços de Recuperação.
> - Defina o contexto do cofre para a sessão do PowerShell.
> - Prepare o cofre para iniciar a replicação de máquinas virtuais do Azure.
> - Crie mapeamentos de rede.
> - Crie contas de armazenamento para as quais replicar máquinas virtuais.
> - Replique as máquinas virtuais do Azure para uma região de recuperação para recuperação de desastre.
> - Execute um failover de teste, valide e limpe o failover de teste.
> - Failover para a região de recuperação.

> [!NOTE]
> Nem todos os recursos de cenário disponíveis por meio do portal podem estar disponíveis por meio de Azure PowerShell. Alguns dos recursos de cenário que não têm suporte no momento por meio de Azure PowerShell são:
> - A capacidade de especificar que todos os discos em uma máquina virtual devem ser replicados sem a necessidade de especificar explicitamente cada disco da máquina virtual.  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:
- Certifique-se de que compreende a [arquitetura e os componentes do cenário](azure-to-azure-architecture.md).
- Reveja os [requisitos de suporte](azure-to-azure-support-matrix.md) de todos os componentes.
- Você tem o módulo Azure PowerShell `Az`. Se você precisar instalar ou atualizar Azure PowerShell, siga este [guia para instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Faça logon na sua assinatura do Microsoft Azure

Faça logon em sua assinatura do Azure usando o cmdlet Connect-AzAccount

```azurepowershell
Connect-AzAccount
```
Selecione a sua subscrição do Azure. Use o cmdlet Get-AzSubscription para obter a lista de assinaturas do Azure às quais você tem acesso. Selecione a assinatura do Azure com a qual trabalhar usando o cmdlet Select-AzSubscription.

```azurepowershell
Select-AzSubscription -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Obter detalhes das máquinas virtuais a serem replicadas

No exemplo neste artigo, uma máquina virtual na região leste dos EUA será replicada e recuperada na região oeste dos EUA 2. A máquina virtual que está sendo replicada é uma máquina virtual com um disco do sistema operacional e um único disco de dados. O nome da máquina virtual usada no exemplo é AzureDemoVM.

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

Obtenha detalhes do disco para os discos da máquina virtual. Os detalhes do disco serão usados posteriormente ao iniciar a replicação para a máquina virtual.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Crie um grupo de recursos no qual criar o cofre dos serviços de recuperação.

> [!IMPORTANT]
> * O cofre dos serviços de recuperação e as máquinas virtuais que estão sendo protegidas devem estar em locais diferentes do Azure.
> * O grupo de recursos do cofre dos serviços de recuperação e as máquinas virtuais que estão sendo protegidas devem estar em locais diferentes do Azure.
> * O cofre dos serviços de recuperação e o grupo de recursos ao qual ele pertence, podem estar no mesmo local do Azure.

No exemplo neste artigo, a máquina virtual que está sendo protegida está na região leste dos EUA. A região de recuperação selecionada para recuperação de desastre é a região oeste dos EUA 2. O cofre dos serviços de recuperação e o grupo de recursos do cofre estão ambos na região de recuperação (oeste dos EUA 2)

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```
```
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Crie um cofre dos serviços de recuperação. No exemplo abaixo, um cofre dos serviços de recuperação chamado a2aDemoRecoveryVault é criado na região oeste dos EUA 2.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```
```
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```
## <a name="set-the-vault-context"></a>Definir o contexto do cofre


Defina o contexto do cofre para uso na sessão do PowerShell. Uma vez definido, as operações de Azure Site Recovery subsequentes na sessão do PowerShell são executadas no contexto do cofre selecionado.

 ```azurepowershell
#Setting the vault context.
Set-AsrVaultSettings -Vault $vault

```
```
ResourceName         ResourceGroupName ResourceNamespace          ResourceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults     
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```

Para uma migração do Azure para o Azure, você pode definir o contexto do cofre para o cofre recém-criado: 

```azurepowershell

#Set the vault context for the PowerShell session.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault

```

## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Preparar o cofre para iniciar a replicação de máquinas virtuais do Azure

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>Criar um objeto de malha Site Recovery para representar a região primária (origem)

O objeto de malha no cofre representa uma região do Azure. O objeto de malha primário é criado para representar a região do Azure à qual as máquinas virtuais que estão sendo protegidas ao cofre pertencem. No exemplo neste artigo, a máquina virtual que está sendo protegida está na região leste dos EUA.

- Somente um objeto de malha pode ser criado por região.
- Se você tiver habilitado anteriormente Site Recovery replicação para uma VM na portal do Azure, Site Recovery criará um objeto de malha automaticamente. Se existir um objeto de malha para uma região, você não poderá criar um novo.


Antes de começar, observe que as operações de Site Recovery são executadas de forma assíncrona. Quando você inicia uma operação, um trabalho de Azure Site Recovery é enviado e um objeto de acompanhamento de trabalho é retornado. Use o objeto de acompanhamento de trabalho para obter o status mais recente do trabalho (Get-ASRJob) e para monitorar o status da operação.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AsrFabric -Name "A2Ademo-EastUS"
```
Se as máquinas virtuais de várias regiões do Azure estiverem sendo protegidas no mesmo cofre, crie um objeto de malha para cada região do Azure de origem.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>Criar um objeto de malha Site Recovery para representar a região de recuperação

O objeto de malha de recuperação representa o local do Azure de recuperação. As máquinas virtuais serão replicadas e recuperadas para (no caso de um failover) a região de recuperação representada pela malha de recuperação. A região do Azure de recuperação usada neste exemplo é oeste dos EUA 2.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AsrFabric -Name "A2Ademo-WestUS"

```

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>Criar um contêiner de proteção de Site Recovery na malha primária

O contêiner de proteção é um contêiner usado para agrupar itens replicados em uma malha.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-ASRProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```
### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>Criar um contêiner de proteção de Site Recovery na malha de recuperação

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-ASRProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

### <a name="create-a-replication-policy"></a>Criar uma política de replicação

```azurepowershell
#Create replication policy
$TempASRJob = New-ASRPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-ASRPolicy -Name "A2APolicy"
```
### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>Criar um mapeamento de contêiner de proteção entre o contêiner de proteção principal e de recuperação

Um mapeamento de contêiner de proteção mapeia o contêiner de proteção principal com um contêiner de proteção de recuperação e uma política de replicação. Crie um mapeamento para cada política de replicação que você usará para replicar máquinas virtuais entre um par de contêineres de proteção.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-ASRProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>Criar um mapeamento de contêiner de proteção para failback (replicação inversa após um failover)

Após um failover, quando você estiver pronto para trazer a máquina virtual de failover de volta para a região original do Azure, faça o failback. Para o failback, a máquina virtual com failover é revertida da região com failover para a região original. Para a replicação inversa, as funções da região original e a opção de região de recuperação. A região original agora se torna a nova região de recuperação e qual era originalmente a região de recuperação agora se torna a região primária. O mapeamento de contêiner de proteção para replicação inversa representa as funções comutadas das regiões original e de recuperação.

```azurepowershell
#Create Protection container mapping (for failback) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

## <a name="create-cache-storage-accounts-and-target-storage-accounts"></a>Criar conta (s) de armazenamento de cache e conta (s) de armazenamento de destino

Uma conta de armazenamento em cache é uma conta de armazenamento padrão na mesma região do Azure que a máquina virtual que está sendo replicada. A conta de armazenamento de cache é usada para manter as alterações de replicação temporariamente, antes que as alterações sejam movidas para a região de recuperação do Azure. Você pode escolher (mas não precisa) especificar contas de armazenamento de cache diferentes para os diferentes discos de uma máquina virtual.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

Para máquinas virtuais que **não usam discos gerenciados**, a conta de armazenamento de destino é a conta de armazenamento na região de recuperação na qual os discos da máquina virtual são replicados. A conta de armazenamento de destino pode ser uma conta de armazenamento Standard ou uma conta de armazenamento Premium. Selecione o tipo de conta de armazenamento necessário com base na taxa de alteração de dados (taxa de gravação de e/s) para os discos e o Azure Site Recovery limites de rotatividade com suporte para o tipo de armazenamento.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage

```

## <a name="create-network-mappings"></a>Criar mapeamentos de rede

Um mapeamento de rede mapeia redes virtuais na região primária para redes virtuais na região de recuperação. O mapeamento de rede especifica a rede virtual do Azure na região de recuperação, na qual uma máquina virtual na rede virtual primária deve fazer failover. Uma rede virtual do Azure pode ser mapeada para apenas uma única rede virtual do Azure em uma região de recuperação.

- Criar uma rede virtual do Azure na região de recuperação para failover para

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```
- Recuperar a rede virtual primária (a vnet à qual a máquina virtual está conectada)
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
- Criar mapeamento de rede entre a rede virtual primária e a rede virtual de recuperação
   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State

   ```
- Criar mapeamento de rede para a direção inversa (failback)
    ```azurepowershell
    #Create an ASR network mapping for failback between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Replicar máquina virtual do Azure

Replique a máquina virtual do Azure com **discos gerenciados**.

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

# Data disk
$datadiskId1  = $vm.StorageProfile.DataDisks[0].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[0].StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

```

Replique a máquina virtual do Azure com **discos não gerenciados**.

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
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}


#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

Depois que a operação iniciar replicação for concluída com sucesso, os dados da máquina virtual serão replicados para a região de recuperação.

O processo de replicação começa inicialmente propagando uma cópia dos discos de replicação da máquina virtual na região de recuperação. Essa fase é chamada de fase de replicação inicial.

Depois que a replicação inicial for concluída, a replicação será movida para a fase de sincronização diferencial. Neste ponto, a máquina virtual é protegida e uma operação de failover de teste pode ser executada nela. O estado de replicação do item replicado que representa a máquina virtual vai para o estado "protected" após a conclusão da replicação inicial.

Monitore o estado de replicação e a integridade da replicação para a máquina virtual obtendo detalhes do item protegido de replicação correspondente a ele.
```azurepowershell
 Get-ASRReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal           
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Executar um failover de teste, validar e limpar o failover de teste

Depois que a replicação da máquina virtual tiver atingido um estado protegido, uma operação de failover de teste poderá ser executada na máquina virtual (no item de replicação protegida da máquina virtual).

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Execute o failover de teste.
```azurepowershell
$ReplicationProtectedItem = Get-ASRReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery


```

Aguarde a conclusão da operação de failover de teste.
```azurepowershell
Get-ASRJob -Job $TFOJob
```

```
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
Depois que o trabalho de failover de teste for concluído com êxito, você poderá se conectar ao failover de teste na máquina virtual e validar o teste.

Após a conclusão do teste na máquina virtual do teste de failover, limpe a cópia de teste iniciando a operação de failover de teste de limpeza. Esta operação exclui a cópia de teste da máquina virtual que foi criada pelo failover de teste.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJob -Job $Job_TFOCleanup | Select State
```
```
State    
-----    
Succeeded
```

## <a name="failover-to-azure"></a>Ativação pós-falha para o Azure

Fazer failover da máquina virtual para um ponto de recuperação específico.

```azurepowershell
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```
```
CrashConsistent 4/24/2018 11:10:25 PM
```


```azurepowershell
#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```
```
Succeeded
```

Após o failover bem-sucedido, você pode confirmar a operação de failover.
```azurepowershell
$CommitFailoverJOb = Start-ASRCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJOb -Job $CommitFailoverJOb
```

```
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

## <a name="reprotect-and-failback-to-source-region"></a>Proteger novamente e failback para a região de origem

Após um failover, quando você estiver pronto para voltar para a região original, inicie a replicação inversa para o item de replicação protegida usando o cmdlet Update-AzRecoveryServicesAsrProtectionDirection.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage
```

```azurepowershell
#Use the recovery protection container, new cache storage accountin West US and the source region VM resource group
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure
-ProtectionContainerMapping $RecoveryProtContainer -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.Id
```

Depois que a nova proteção for concluída, você poderá iniciar o failover na direção inversa (oeste dos EUA para leste dos EUA) e failback para a região de origem.

## <a name="disable-replication"></a>Desativar a replicação

Você pode desabilitar a replicação usando o cmdlet Remove-ASRReplicationProtectedItem.

```azurepowershell
Remove-ASRReplicationProtectedItem -ReplicationProtectedItem $ReplicatedItem
```

## <a name="next-steps"></a>Passos seguintes
Exiba o [Azure site Recovery referência do PowerShell](https://docs.microsoft.com/powershell/module/az.RecoveryServices) para saber como você pode executar outras tarefas, como criar planos de recuperação e testar o failover de planos de recuperação por meio do PowerShell.

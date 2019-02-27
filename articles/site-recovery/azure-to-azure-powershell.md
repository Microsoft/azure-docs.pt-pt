---
title: O Azure Site Recovery - configuração e testar a recuperação após desastre para máquinas virtuais do Azure com o Azure PowerShell | Documentos da Microsoft
description: Saiba como configurar a recuperação após desastre para máquinas virtuais do Azure com o Azure Site Recovery com o Azure PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 9c4576633f98d38da7086711c24def88591ab71f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56869416"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Configurar a recuperação após desastre para máquinas virtuais do Azure com o Azure PowerShell


Neste artigo, verá como configurar e testar a recuperação após desastre para máquinas virtuais do Azure com o Azure PowerShell.

Saiba como:

> [!div class="checklist"]
> - Crie um cofre dos Serviços de Recuperação.
> - Defina o contexto do cofre para a sessão do PowerShell.
> - Prepare o cofre para iniciar a replicação de máquinas virtuais do Azure.
> - Crie mapeamentos de rede.
> - Crie contas de armazenamento para replicar as máquinas virtuais.
> - Replicar máquinas virtuais do Azure para uma região de recuperação para recuperação após desastre.
> - Executar uma ativação pós-falha de teste, validar e a ativação pós-falha de teste.
> - Ativação pós-falha para a região de recuperação.

> [!NOTE]
> Nem todas as capacidades de cenário disponíveis através do portal poderão estar disponíveis através do PowerShell do Azure. Algumas das capacidades de cenário não são atualmente suportadas através do PowerShell do Azure são:
> - A capacidade de especificar que todos os discos numa máquina virtual devem ser replicados sem ter de especificar explicitamente cada disco da máquina virtual.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:
- Certifique-se de que compreende a [arquitetura e os componentes do cenário](azure-to-azure-architecture.md).
- Reveja os [requisitos de suporte](azure-to-azure-support-matrix.md) de todos os componentes.
- Tem a versão 5.7.0 ou superior do módulo AzureRm PowerShell. Se precisar de instalar ou atualizar o Azure PowerShell, siga este [guia para instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Inicie sessão na sua subscrição do Microsoft Azure

Inicie sessão na sua subscrição do Azure com o cmdlet Connect-AzureRmAccount

```azurepowershell
Connect-AzureRmAccount
```
Selecione a sua subscrição do Azure. Utilize o cmdlet Get-AzureRmSubscription para obter a lista de subscrições do Azure que tem acesso a. Selecione a subscrição do Azure para trabalhar com o cmdlet Select-AzureRmSubscription a utilizar.

```azurepowershell
Select-AzureRmSubscription -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Obter detalhes sobre as máquinas virtuais a serem replicados

O exemplo neste artigo, uma máquina virtual na região E.U.A. Leste será replicada para e recuperada na região E.U.A. oeste 2. A máquina virtual a ser replicada é uma máquina virtual com um disco de SO e um disco de dados individual. O nome da máquina virtual utilizada no exemplo é AzureDemoVM.

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzureRmVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

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

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Crie um grupo de recursos no qual pretende criar o Cofre dos serviços de recuperação.

> [!IMPORTANT]
> * O Cofre dos serviços de recuperação e as máquinas virtuais a ser protegidas, tem de ser em diferentes localizações do Azure.
> * O grupo de recursos do cofre dos serviços de recuperação e as máquinas virtuais a ser protegidas, tem de ser em diferentes localizações do Azure.
> * O Cofre dos serviços de recuperação e o grupo de recursos a que pertence, podem ser na mesma localização do Azure.

O exemplo neste artigo, a máquina virtual que está a ser protegida está na região E.U.A. Leste. A região de recuperação selecionada para recuperação após desastre é a região EUA oeste 2. O Cofre dos serviços de recuperação e o grupo de recursos do cofre, são na região de recuperação (E.U.A. oeste 2)

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```
```
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Crie um cofre dos serviços de recuperação. No exemplo abaixo de um cofre de serviços de recuperação com o nome a2aDemoRecoveryVault é criado na região E.U.A. oeste 2.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzureRmRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

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
## <a name="set-the-vault-context"></a>Definir o contexto do Cofre

> [!TIP]
> O módulo do PowerShell do Azure Site Recovery (módulo AzureRm.RecoveryServices.SiteRecovery) vem com fácil de usar aliases para a maioria dos cmdlets. Os cmdlets no módulo assumir a forma  *\<operação >-**AzureRmRecoveryServicesAsr**\<objeto >* e possuem aliases equivalentes que assumir a forma  *\<Operação >-**ASR**\<objeto >*. Este artigo utiliza os aliases de cmdlet para facilitar a leitura.

Defina o contexto do cofre para utilização na sessão do PowerShell. Para fazer isso, transfira o ficheiro de definições do cofre para e importe o ficheiro transferido na sessão do PowerShell para definir o contexto do cofre.

Uma vez definido, as operações subsequentes do Azure Site Recovery na sessão do PowerShell são executadas no contexto do cofre selecionado.

 ```azurepowershell
#Download the vault settings file for the vault.
$Vaultsettingsfile = Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteRecovery -Path C:\users\user\Documents\

#Import the downloaded vault settings file to set the vault context for the PowerShell session.
Import-AzureRmRecoveryServicesAsrVaultSettingsFile -Path $Vaultsettingsfile.FilePath

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
## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Preparar o cofre para iniciar a replicação de máquinas virtuais do Azure

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>Criar um objeto de recursos de infraestrutura do Site Recovery para representar a região primária (origem)

O objeto de recursos de infraestrutura no cofre representa uma região do Azure. O objeto de recursos de infraestrutura principal é criado para representar a região do Azure que as máquinas virtuais que está a ser protegidas para o Cofre pertencem a. O exemplo neste artigo, a máquina virtual que está a ser protegida está na região E.U.A. Leste.

- Objeto de apenas um dos recursos de infraestrutura pode ser criado por região. 
- Se ativou anteriormente a replicação para uma VM no portal do Azure Site Recovery, o Site Recovery cria automaticamente um objeto de recursos de infraestrutura. Se existir um objeto de recursos de infraestrutura para uma região, não é possível criar um novo.


Antes de começar, tenha em atenção que as operações de recuperação de Site são executadas de forma assíncrona. Quando iniciar uma operação, é submetida uma tarefa de recuperação de sites do Azure e uma tarefa de objeto de controle é devolvida. Utilize a tarefa de objeto de controle para obter o estado mais recente para a tarefa (Get-ASRJob) e para monitorizar o estado da operação.

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
Se as máquinas virtuais de várias regiões do Azure estão a ser protegidas no mesmo cofre, crie um objeto de recursos de infraestrutura para cada região do Azure de origem.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>Criar um objeto de recursos de infraestrutura do Site Recovery para representar a região de recuperação

O objeto de recursos de infraestrutura de recuperação representa a recuperação de localização do Azure. Máquinas virtuais serão replicadas e recuperado (no caso de uma ativação pós-falha) a região de recuperação, representada pelos recursos de infraestrutura de recuperação. A região do Azure utilizado neste exemplo de recuperação é E.U.A. oeste 2.

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

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>Criar um contentor de proteção de recuperação de sites nos recursos de infraestrutura principal

O contentor de proteção é contentores utilizado para agrupar itens replicados dentro de um recurso de infraestrutura.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-ASRProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```
### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>Criar um contentor de proteção de recuperação de sites nos recursos de infraestrutura de recuperação

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

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
### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>Criar um mapeamento de contentor de proteção entre o contentor de proteção principal e de recuperação

Um mapeamento de contentor de proteção mapeia o contentor de proteção principal com um contentor de proteção de recuperação e uma política de replicação. Crie um mapeamento para cada política de replicação que irá utilizar para replicar máquinas virtuais entre um par de contentor de proteção.

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

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>Criar um mapeamento de contentor de proteção para a reativação pós-falha (a replicação inversa após uma ativação pós-falha)

Após uma ativação pós-falha, quando estiver pronto para que a máquina virtual com ativação pós-falha voltem a região do Azure original, executar a reativação pós-falha. A reativação pós-falha, a máquina virtual com ativação pós-falha é inversa replicadas a partir da falha por região para a região original. Para a replicação inversa alternar as funções de região original e a região de recuperação. A região original torna-se agora a nova região de recuperação e o que foi originalmente a região de recuperação agora torna-se a região primária. O mapeamento de contentor de proteção para a replicação inversa representa as funções comutadas das regiões original e a recuperação.

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

## <a name="create-cache-storage-accounts-and-target-storage-accounts"></a>Criar contas de armazenamento de cache e contas de armazenamento de destino

Uma conta de armazenamento de cache é uma conta de armazenamento standard na mesma região do Azure que a máquina virtual a ser replicada. A conta de armazenamento de cache é utilizada para armazenar as alterações de replicação temporariamente, antes das alterações são movidas para a recuperação de região do Azure. Pode optar por (mas não precisa) especificar contas de armazenamento de cache diferente para diferentes discos de uma máquina virtual.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzureRmStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

Para máquinas virtuais **não utilizar discos geridos**, a conta de armazenamento de destino é a conta de armazenamento (s) na região de recuperação para que os discos da máquina virtual são replicados. A conta de armazenamento de destino pode ser uma conta de armazenamento standard ou uma conta de armazenamento premium. Selecione o tipo de conta de armazenamento necessária, com base na taxa de alteração de dados (taxa de escrita de e/s) para os discos e os limites de alterações a dados do Azure Site Recovery suportados para o tipo de armazenamento.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzureRmStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage

```

## <a name="create-network-mappings"></a>Criar mapeamentos de rede

Um mapeamento de rede mapeia as redes virtuais na região primária para redes virtuais na região de recuperação. O mapeamento de rede Especifica a rede virtual do Azure na região de recuperação, o que uma máquina virtual na rede virtual principal deve ativação pós-falha. Uma rede virtual do Azure pode ser mapeada para apenas uma única rede virtual do Azure numa região de recuperação.

- Criar uma rede virtual do Azure na região de recuperação para ativação pós-falha para o

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzureRmVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzureRmVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```
- Obter a rede virtual principal (a que a máquina virtual está ligada à vnet)
   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resource name
    $NIC = Get-AzureRmNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")  
   ```
- Criar mapeamento da rede entre a rede virtual principal e a rede virtual de recuperação
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
- Criar o mapeamento de rede para a direção inversa (reativação pós-falha)
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

## <a name="replicate-azure-virtual-machine"></a>Replicar máquinas virtuais do Azure

Replicar a máquina virtual do Azure com **discos geridos**.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1  = $vm.StorageProfile.DataDisks[0].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[0]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0]. StorageAccountType

$DataDisk1ReplicationConfig  = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

```

Replicar a máquina virtual do Azure com **discos não geridos**.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

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

Assim que a operação de replicação inicial for concluída com êxito, os dados de máquina virtual são replicados para a região de recuperação.

Inicia o processo de replicação, propagando inicialmente uma cópia de discos de replicação da máquina virtual na região de recuperação. Nesta fase é chamada a fase de replicação inicial.

Uma vez concluída a replicação inicial, a replicação move para a fase de sincronização diferencial. Neste momento, a máquina virtual está protegida e uma operação de ativação pós-falha de teste pode ser executada no mesmo. O estado de replicação do item replicado que representa a máquina virtual vai para o estado "Protegido" após a conclusão da replicação inicial.

Monitorize o estado de replicação e o estado de funcionamento de replicação para a máquina virtual ao obter os detalhes do item protegido de replicação correspondente a ele.
```azurepowershell
 Get-ASRReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal           
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Executar uma ativação pós-falha de teste, validar e a ativação pós-falha de teste

Assim que a replicação para a máquina virtual atingiu um estado protegido, uma operação de ativação pós-falha de teste pode ser executada na máquina virtual (no item protegido de replicação da máquina virtual.)

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzureRmVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzureRmVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Efetue ativação pós-falha de teste.
```azurepowershell
$ReplicationProtectedItem = Get-ASRReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery


```

Aguarde pela conclusão da operação de ativação pós-falha de teste.
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
Uma vez a tarefa de ativação pós-falha de teste concluída com êxito, pode ligar ao efetuar a ativação pós-falha da máquina virtual de teste e validar a ativação pós-falha de teste.

Depois de testes são concluídos de efetuar a ativação pós-falha da máquina virtual de teste, a cópia de teste, começando a limpeza de limpeza teste operação de ativação pós-falha. Esta operação elimina a cópia de teste da máquina virtual criada através da ativação pós-falha de teste.

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

Ativação pós-falha da máquina virtual para um ponto de recuperação específico.

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

Assim que a ativação pós-falha com êxito, pode confirmar a operação de ativação pós-falha.
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

Após uma ativação pós-falha, quando estiver pronto para voltar para a região original, inicie a replicação inversa para o item protegido de replicação com o cmdlet Update-AzureRmRecoveryServicesAsrProtectionDirection.

## <a name="next-steps"></a>Passos Seguintes
Ver os [referência do PowerShell do Azure Site Recovery](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery) para saber como executar outras tarefas como criação de planos de recuperação e teste de ativação pós-falha dos planos de recuperação através do PowerShell.

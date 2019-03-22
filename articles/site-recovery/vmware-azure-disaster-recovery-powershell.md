---
title: Configurar a recuperação após desastre de VMs de VMware para o Azure com o PowerShell no Azure Site Recovery | Documentos da Microsoft
description: Saiba como configurar a replicação e ativação pós-falha do Azure para recuperação após desastre de VMs de VMware com o PowerShell no Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 11/27/2018
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: aa8292aac82f478422f9214c26d974825872eed6
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226340"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>Configurar a recuperação após desastre de VMs de VMware para o Azure com o PowerShell

Neste artigo, verá como replicar e máquinas de virtuais de VMware de ativação pós-falha para o Azure com o Azure PowerShell. 

Saiba como:

> [!div class="checklist"]
> - Crie um cofre dos serviços de recuperação e defina o contexto do cofre.
> - Valide o registo do servidor no cofre.
> - Configure a replicação, incluindo uma política de replicação. Adicione o servidor vCenter e detetar VMs. 
> - Adicionar um servidor vCenter e detetar 
> - Criar contas de armazenamento para armazenar dados de replicação e replicar as VMs.
> - Executar uma ativação pós-falha. Configurar definições de ativação pós-falha, efetue um definições para replicar máquinas virtuais.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

- Certifique-se de que compreende a [arquitetura e os componentes do cenário](vmware-azure-architecture.md).
- Reveja os [requisitos de suporte](site-recovery-support-matrix-to-azure.md) de todos os componentes.
- Tem a versão 5.0.1 ou superior do módulo AzureRm PowerShell. Se precisar de instalar ou atualizar o Azure PowerShell, siga este [guia para instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="log-into-azure"></a>Iniciar sessão no Azure

Iniciar sessão na sua subscrição do Azure com o cmdlet Connect-AzureRmAccount:

```azurepowershell
Connect-AzureRmAccount
```
Selecione a subscrição do Azure que pretende replicar as máquinas virtuais VMware. Utilize o cmdlet Get-AzureRmSubscription para obter a lista de subscrições do Azure que tem acesso a. Selecione a subscrição do Azure para trabalhar com o cmdlet Select-AzureRmSubscription a utilizar.

```azurepowershell
Select-AzureRmSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Configurar um cofre dos Serviços de Recuperação

1. Crie um grupo de recursos no qual pretende criar o Cofre dos serviços de recuperação. No exemplo abaixo, o grupo de recursos com o nome VMwareDRtoAzurePS e é criado na região Leste asiático.

   ```azurepowershell
   New-AzureRmResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
   ```
   ```
   ResourceGroupName : VMwareDRtoAzurePS
   Location          : eastasia
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
   ```
   
2. Crie um cofre dos serviços de recuperação. No exemplo abaixo, o Cofre dos serviços de recuperação com o nome VMwareDRToAzurePs e é criado na região Ásia Oriental e no grupo de recursos criado no passo anterior.

   ```azurepowershell
   New-AzureRmRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
   ```
   ```
   Name              : VMwareDRToAzurePs
   ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
   Type              : Microsoft.RecoveryServices/vaults
   Location          : eastasia
   ResourceGroupName : VMwareDRToAzurePs
   SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
   ``` 

3. Transferir a chave de registo do cofre para o cofre. A chave de registo do Cofre é utilizada para registar o servidor de configuração no local para este cofre. O registo é parte do processo de instalação de software do servidor de configuração.

   ```azurepowershell
   #Get the vault object by name and resource group and save it to the $vault PowerShell variable 
   $vault = Get-AzureRmRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

   #Download vault registration key to the path C:\Work
   Get-AzureRmRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   ```
   ```
   FilePath
   --------
   C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
   ```

4. Utilize a chave de registo do cofre transferido e siga os passos nos artigos indicados abaixo para concluir a instalação e registo do servidor de configuração.
   - [Escolha os seus objetivos de proteção](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [Configurar o ambiente de origem](vmware-azure-set-up-source.md#set-up-the-configuration-server) 

### <a name="set-the-vault-context"></a>Definir o contexto do Cofre

Defina o contexto do cofre com o cmdlet Set-ASRVaultContext. Uma vez definido, as operações subsequentes do Azure Site Recovery na sessão do PowerShell são executadas no contexto do cofre selecionado.

> [!TIP]
> O módulo do PowerShell do Azure Site Recovery (módulo AzureRm.RecoveryServices.SiteRecovery) vem com fácil de usar aliases para a maioria dos cmdlets. Os cmdlets no módulo assumir a forma  *\<operação >-**AzureRmRecoveryServicesAsr**\<objeto >* e possuem aliases equivalentes que assumir a forma  *\<Operação >-**ASR**\<objeto >*. Este artigo utiliza os aliases de cmdlet para facilitar a leitura.

No exemplo abaixo, os detalhes do cofre do $vault variável é utilizada para especificar o contexto do cofre para a sessão do PowerShell.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

Como alternativa para o cmdlet Set-ASRVaultContext, também é possível usar o cmdlet Import-AzureRmRecoveryServicesAsrVaultSettingsFile para definir o contexto do cofre. Especifique o caminho em que o ficheiro de chave de registo de Cofre está localizado como o parâmetro - path para o cmdlet Import-AzureRmRecoveryServicesAsrVaultSettingsFile. Por exemplo:

   ```azurepowershell
   Get-AzureRmRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzureRmRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
As seções subsequentes deste artigo partem do princípio de que o contexto do cofre para operações do Azure Site Recovery foi definido.

## <a name="validate-vault-registration"></a>Validar o registo do Cofre

Neste exemplo, podemos ter o seguinte:

- Um servidor de configuração (**ConfigurationServer**) foi registado neste cofre.
- Um servidor de processos adicionais (**ProcessServer de aumento horizontal**) ter sido registado *ConfigurationServer*
- Contas (**vCenter_account**, **WindowsAccount**, **LinuxAccount**) foram configurados no servidor de configuração. Essas contas são usadas para adicionar o servidor do vCenter, para detetar as máquinas virtuais e a instalação de emissão o software do serviço de mobilidade em servidores Windows e Linux que estão a ser replicadas.

1. Servidores de configuração registrados são representados por um objeto de recursos de infraestrutura no Site Recovery. Obter a lista de recursos de infraestrutura objetos no cofre e identificar o servidor de configuração.

   ```azurepowershell
   # Verify that the Configuration server is successfully registered to the vault
   $ASRFabrics = Get-ASRFabric
   $ASRFabrics.count
   ```
   ```
   1
   ```
   ```azurepowershell
   #Print details of the Configuration Server
   $ASRFabrics[0]
   ```
   ```
   Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   FriendlyName          : ConfigurationServer
   ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                           /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
   FabricType            : VMware
   SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
   FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
   ```

2. Identifique os servidores de processo que podem ser utilizados para replicar máquinas.

   ```azurepowershell
   $ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
   for($i=0; $i -lt $ProcessServers.count; $i++) {
    "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
   }
   ```
   ```
   0     ScaleOut-ProcessServer
   1     ConfigurationServer
   ```

   A partir da saída acima ***$ProcessServers [0]*** corresponde ao *ProcessServer de aumento horizontal* e ***$ProcessServers [1]*** corresponde à função de servidor de processos no *ConfigurationServer*

3. Identifica as contas que foram configuradas no servidor de configuração.

   ```azurepowershell
   $AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
   #Print the account details
   $AccountHandles
   ```
   ```
   AccountId AccountName
   --------- -----------
   1         vCenter_account
   2         WindowsAccount
   3         LinuxAccount
   ```

   A partir da saída acima ***$AccountHandles [0]*** corresponde à conta *vCenter_account*, ***$AccountHandles [1]*** conta *WindowsAccount*, e ***$AccountHandles [2]*** à conta *LinuxAccount*

## <a name="create-a-replication-policy"></a>Criar uma política de replicação

Neste passo, são criadas duas políticas de replicação. A ativação pós-falha uma diretiva para replicar máquinas virtuais VMware para o Azure e a outra para replicar máquinas virtuais em execução no Azure de volta para o site de VMware no local.

> [!NOTE]
> A maioria das operações de recuperação de sites do Azure são executados de forma assíncrona. Quando iniciar uma operação, é submetida uma tarefa de recuperação de sites do Azure e uma tarefa de objeto de controle é devolvida. Esta tarefa de objeto de controle pode ser utilizada para monitorizar o estado da operação.

1. Criar uma política de replicação com o nome *ReplicationPolicy* para replicar máquinas virtuais do VMware para o Azure com as propriedades especificadas.

   ```azurepowershell
   $Job_PolicyCreate = New-ASRPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

   # Track Job status to check for completion
   while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){ 
           sleep 10; 
           $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
   }

   #Display job status
   $Job_PolicyCreate
   ```
   ```
   Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
   ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                      9-479f-430d-b76b-6bc7eb2d0b3e
   Type             :
   JobType          : AddProtectionProfile
   DisplayName      : Create replication policy
   ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
   State            : Succeeded
   StateDescription : Completed
   StartTime        : 11/24/2017 2:49:24 AM
   EndTime          : 11/24/2017 2:49:23 AM
   TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
   TargetObjectType : ProtectionProfile
   TargetObjectName : ReplicationPolicy
   AllowedActions   :
   Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
   Errors           : {}
   ```

2. Crie uma política de replicação a utilizar para a reativação pós-falha do Azure para o site de VMware no local.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-ASRPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   Utilize os detalhes da tarefa na *$Job_FailbackPolicyCreate* para controlar a operação para conclusão.

   * Crie um mapeamento de contentor de proteção para mapear as políticas de replicação com o servidor de configuração.

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-ASRProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server. 

   $Job_AssociatePolicy = New-ASRProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

   # Check the job status
   while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){ 
           sleep 10; 
           $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
   }
   $Job_AssociatePolicy.State

   <# In the protection container mapping used for failback (replicating failed over virtual machines 
      running in Azure, to the primary VMware site.) the protection container corresponding to the 
      Configuration server acts as both the Primary protection container and the recovery protection
      container
   #>
    $Job_AssociateFailbackPolicy = New-ASRProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){ 
           sleep 10; 
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>Adicionar um servidor vCenter e detetar VMs

Adicione um vCenter Server por endereço IP ou nome de anfitrião. O **-porta** parâmetro especifica a porta no vCenter server para ligar, seja **-nome** parâmetro especifica um nome amigável a utilizar para o servidor vCenter e o **-conta** parâmetro especifica o identificador de conta no servidor de configuração para utilizar para detetar máquinas virtuais geridas pelo vCenter server.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-ASRvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) { 
        sleep 30; 
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts-for-replication"></a>Criar contas de armazenamento para replicação

Neste passo, são criadas as contas de armazenamento a ser utilizada para replicação. Estas contas de armazenamento são utilizadas mais tarde para replicar máquinas virtuais. Certifique-se de que as contas de armazenamento são criadas na mesma região do Azure que o cofre. Pode ignorar este passo se planeja usar uma conta de armazenamento existente para a replicação.

> [!NOTE]
> Ao replicar máquinas virtuais no local para uma conta de armazenamento premium, tem de especificar uma conta de armazenamento standard adicional (conta de armazenamento de registo). A conta de armazenamento de registo contém registos de replicação como um armazenamento intermediário até que os registos podem ser aplicados no destino de armazenamento premium.
>

```azurepowershell

$PremiumStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>Replicar VMs de VMware

Demora cerca de 15 a 20 minutos para máquinas virtuais para ser detetados a partir do vCenter server. Assim que detetado, é criado um objeto de item protegível no Azure Site Recovery para cada máquina virtual detetada. Neste passo, três das máquinas virtuais detetadas são replicados para as contas de armazenamento do Azure que criou no passo anterior.

Terá dos seguintes detalhes para proteger uma máquina virtual detetada:

* O item protegível a serem replicados.
* A conta de armazenamento para replicar a máquina virtual. Além disso, um armazenamento de registo é necessário para proteger máquinas virtuais para uma conta de armazenamento premium.
* O servidor de processo a ser utilizada para replicação. A lista de servidores de processos disponíveis foi recuperada e guardada na ***$ProcessServers [0]***  *(aumento horizontal-ProcessServer)* e ***$ProcessServers [1]*** *(ConfigurationServer)* variáveis.
* A conta a utilizar para o software do serviço de mobilidade em funcionamento nos computadores de instalação de emissão. A lista de contas disponíveis foi recuperada e armazenada no ***$AccountHandles*** variável.
* O mapeamento de contentor de proteção para a política de replicação para ser utilizada para replicação.
* O grupo de recursos em que as máquinas virtuais tem de ser criadas na ativação pós-falha.
* Opcionalmente, a rede virtual do Azure e a sub-rede à qual a máquina virtual com ativação pós-falha deverá estar ligado.

Agora replicar as seguintes máquinas virtuais com as definições especificadas nesta tabela


|Máquina virtual  |Process Server        |Conta de Armazenamento              |Conta de armazenamento de registo  |Política           |Conta para a instalação do serviço de mobilidade|Grupo de recursos de destino  | Rede virtual de destino  |Sub-rede de destino  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR-vnet                 |Sub-rede 1       |
|CentOSVM1       |ConfigurationServer   |replicationstdstorageaccount1| N/A                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Sub-rede 1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| N/A                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Sub-rede 1       |   

 
```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzureRmResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzureRmVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-ASRProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM1 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" 

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM2 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1
$Job_EnableReplication2 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" 

```

Uma vez a tarefa de replicação de ativação concluída com êxito, é iniciar a replicação inicial para as máquinas virtuais. A replicação inicial pode demorar algum tempo consoante a quantidade de dados a serem replicados e a largura de banda disponível para a replicação. Depois de concluída a replicação inicial, a máquina virtual é movida para um estado protegido. Assim que a máquina virtual atingir um estado protegido, que pode efetuar uma ativação pós-falha de teste para a máquina virtual, adicioná-lo aos planos de recuperação etc.

Pode verificar o estado de replicação e o estado de funcionamento de replicação da máquina virtual com o cmdlet Get-ASRReplicationProtectedItem.

```azurepowershell
Get-ASRReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>Configurar as definições de ativação pós-falha

As definições de ativação pós-falha para máquinas protegidas podem ser atualizadas com o cmdlet Set-ASRReplicationProtectedItem. Algumas das configurações que podem ser atualizadas através deste cmdlet são:
* Nome da máquina virtual a ser criada na ativação pós-falha
* Tamanho da VM da máquina virtual a ser criada na ativação pós-falha
* Rede virtual do Azure e a sub-rede à qual os NICs da máquina virtual devem estar ligados na ativação pós-falha
* Ativação pós-falha para os managed disks
* Aplicar o benefício de utilização híbrida do Azure
* Atribua um endereço IP estático a partir da rede virtual de destino a ser atribuída à máquina virtual na ativação pós-falha.

Neste exemplo, vamos atualizar o tamanho da VM da máquina virtual a ser criada na ativação pós-falha para a máquina virtual *Win2K12VM1* e especifique que a utilização de máquina virtual gerida discos na ativação pós-falha.

```azurepowershell
$ReplicatedVM1 = Get-ASRReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-ASRReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

1. Execute um teste de DR (ativação pós-falha de teste) da seguinte forma:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzureRmVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. Uma vez a tarefa de ativação pós-falha de teste concluída com êxito, irá reparar que uma máquina virtual com o sufixo *"-teste"* (teste Win2K12VM1 neste caso) para o seu nome é criado no Azure.
3. Pode agora ligar ao efetuar a ativação pós-falha da máquina virtual de teste e validar a ativação pós-falha de teste.
4. Limpe ativação pós-falha de teste com o cmdlet Start-ASRTestFailoverCleanupJob. Esta operação elimina a máquina virtual criada como parte da operação de ativação pós-falha de teste.

   ```azurepowershell
   $Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Efetuar a ativação pós-falha para o Azure

Neste passo, podemos fazer a ativação pós-falha da máquina virtual Win2K12VM1 para um ponto de recuperação específico.

1. Obter uma lista de pontos de recuperação disponíveis para utilizar para a ativação pós-falha:
   ```azurepowershell
   # Get the list of available recovery points for Win2K12VM1
   $RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
   "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
   ```
   ```
   CrashConsistent 11/24/2017 5:28:25 PM
   ```
   ```azurepowershell

   #Start the failover job
   $Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
   do {
           $Job_Failover = Get-ASRJob -Job $Job_Failover;
           sleep 60;
   } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
   $Job_Failover.State
   ```
   ```
   Succeeded
   ```

2. Depois de efetuar a ativação pós-falha com êxito, podem consolidar a operação de ativação pós-falha e configurar a replicação inversa do Azure de volta para o site de VMware no local.

## <a name="next-steps"></a>Passos Seguintes
Saiba como automatizar tarefas mais com o [referência do PowerShell do Azure Site Recovery](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery).

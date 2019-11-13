---
title: Configurar a recuperação de desastres do VMware usando o PowerShell no Azure site Revoery
description: Saiba como configurar a replicação e o failover para o Azure para recuperação de desastre de VMs VMware usando o PowerShell no Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 06/30/2019
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: 9546ae590918cdf6f3a6a95b9a68e9208054dcee
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953936"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>Configurar a recuperação de desastre de VMs do VMware para o Azure com o PowerShell

Neste artigo, você verá como replicar e fazer failover de máquinas virtuais VMware para o Azure usando o Azure PowerShell.

Saiba como:

> [!div class="checklist"]
> - Crie um cofre dos serviços de recuperação e defina o contexto do cofre.
> - Valide o registro do servidor no cofre.
> - Configure a replicação, incluindo uma política de replicação. Adicione seu servidor vCenter e descubra VMs.
> - Adicionar um vCenter Server e descobrir
> - Crie contas de armazenamento para manter os logs de replicação ou dados e replicar as VMs.
> - Executar uma ativação pós-falha. Defina as configurações de failover, execute as configurações para replicar máquinas virtuais.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

- Certifique-se de que compreende a [arquitetura e os componentes do cenário](vmware-azure-architecture.md).
- Reveja os [requisitos de suporte](site-recovery-support-matrix-to-azure.md) de todos os componentes.
- Você tem o módulo Azure PowerShell `Az`. Se você precisar instalar ou atualizar Azure PowerShell, siga este [guia para instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="log-into-azure"></a>Iniciar sessão no Azure

Faça logon em sua assinatura do Azure usando o cmdlet Connect-AzAccount:

```azurepowershell
Connect-AzAccount
```
Selecione a assinatura do Azure na qual você deseja replicar suas máquinas virtuais VMware. Use o cmdlet Get-AzSubscription para obter a lista de assinaturas do Azure às quais você tem acesso. Selecione a assinatura do Azure com a qual trabalhar usando o cmdlet Select-AzSubscription.

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Configurar um cofre dos Serviços de Recuperação

1. Crie um grupo de recursos no qual criar o cofre dos serviços de recuperação. No exemplo a seguir, o grupo de recursos é denominado VMwareDRtoAzurePS e é criado na região Ásia Oriental.

   ```azurepowershell
   New-AzResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
   ```
   ```
   ResourceGroupName : VMwareDRtoAzurePS
   Location          : eastasia
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
   ```

2. Crie um cofre dos serviços de recuperação. No exemplo a seguir, o cofre dos serviços de recuperação é denominado VMwareDRToAzurePs e é criado na região Ásia Oriental e no grupo de recursos criado na etapa anterior.

   ```azurepowershell
   New-AzRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
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

3. Baixe a chave de registro do cofre para o cofre. A chave de registro do cofre é usada para registrar o servidor de configuração local para esse cofre. O registro faz parte do processo de instalação do software do servidor de configuração.

   ```azurepowershell
   #Get the vault object by name and resource group and save it to the $vault PowerShell variable 
   $vault = Get-AzRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

   #Download vault registration key to the path C:\Work
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   ```
   ```
   FilePath
   --------
   C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
   ```

4. Use a chave de registro do cofre baixado e siga as etapas nos artigos fornecidos abaixo para concluir a instalação e o registro do servidor de configuração.
   - [Escolher suas metas de proteção](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [Configurar o ambiente de origem](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>Definir o contexto do cofre

Defina o contexto do cofre usando o cmdlet Set-ASRVaultContext. Uma vez definido, as operações de Azure Site Recovery subsequentes na sessão do PowerShell são executadas no contexto do cofre selecionado.

> [!TIP]
> O módulo Azure Site Recovery PowerShell (módulo AZ. Recoveryservices) é fornecido com aliases fáceis de usar para a maioria dos cmdlets. Os cmdlets no módulo assumem a forma *\<operação >-**AzRecoveryServicesAsr**\<de objeto >* e têm aliases equivalentes que tomam a forma *\<operação re> objeto\<-**ASR*** >. Você pode substituir os aliases de cmdlet para facilitar o uso.

No exemplo a seguir, os detalhes do cofre da variável $vault são usados para especificar o contexto do cofre para a sessão do PowerShell.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

Como alternativa ao cmdlet Set-ASRVaultContext, um também pode usar o cmdlet Import-AzRecoveryServicesAsrVaultSettingsFile para definir o contexto do cofre. Especifique o caminho no qual o arquivo de chave de registro do cofre está localizado como o parâmetro-Path para o cmdlet Import-AzRecoveryServicesAsrVaultSettingsFile. Por exemplo:

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
As seções subsequentes deste artigo pressupõem que o contexto do cofre para operações de Azure Site Recovery foi definido.

## <a name="validate-vault-registration"></a>Validar o registro do cofre

Para este exemplo, temos o seguinte:

- Um servidor de configuração (**ConfigurationServer**) foi registrado neste cofre.
- Um servidor de processo adicional (**scale out-ProcessServer**) foi registrado em *ConfigurationServer*
- As contas (**vCenter_account**, **WindowsAccount**, **LinuxAccount**) foram configuradas no servidor de configuração. Essas contas são usadas para adicionar o servidor vCenter, para descobrir máquinas virtuais e para instalar por push o software do serviço de mobilidade em servidores Windows e Linux que devem ser replicados.

1. Os servidores de configuração registrados são representados por um objeto de malha no Site Recovery. Obtenha a lista de objetos de malha no cofre e identifique o servidor de configuração.

   ```azurepowershell
   # Verify that the Configuration server is successfully registered to the vault
   $ASRFabrics = Get-AzRecoveryServicesAsrFabric
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

2. Identifique os servidores de processo que podem ser usados para replicar computadores.

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

   Da saída acima ***$ProcessServers [0]*** corresponde a *ScaleOut-ProcessServer* e ***$ProcessServers [1]*** corresponde à função de servidor de processo em *ConfigurationServer*

3. Identifique as contas que foram configuradas no servidor de configuração.

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

   Da saída acima ***$AccountHandles [0]*** corresponde à conta *vCenter_account*, ***$AccountHandles [1]*** à conta *WindowsAccount*e ***$AccountHandles [2]*** para a conta *LinuxAccount*

## <a name="create-a-replication-policy"></a>Criar uma política de replicação

Nesta etapa, duas políticas de replicação são criadas. Uma política para replicar máquinas virtuais VMware no Azure e a outra para replicar máquinas virtuais com failover em execução no Azure de volta para o site do VMware local.

> [!NOTE]
> A maioria das operações de Azure Site Recovery são executadas de forma assíncrona. Quando você inicia uma operação, um trabalho de Azure Site Recovery é enviado e um objeto de acompanhamento de trabalho é retornado. Esse objeto de acompanhamento de trabalho pode ser usado para monitorar o status da operação.

1. Crie uma política de replicação chamada *ReplicationPolicy* para replicar máquinas virtuais VMware no Azure com as propriedades especificadas.

   ```azurepowershell
   $Job_PolicyCreate = New-AzRecoveryServicesAsrPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

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

2. Crie uma política de replicação a ser usada para failback do Azure para o site do VMware local.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-AzRecoveryServicesAsrPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   Use os detalhes do trabalho em *$Job _FailbackPolicyCreate* para acompanhar a conclusão da operação.

   * Crie um mapeamento de contêiner de proteção para mapear políticas de replicação com o servidor de configuração.

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server.

   $Job_AssociatePolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

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
    $Job_AssociateFailbackPolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>Adicionar um vCenter Server e descobrir VMs

Adicione um vCenter Server pelo endereço IP ou nome do host. O parâmetro **-Port** especifica a porta no servidor do vCenter ao qual se conectar, o parâmetro **-Name** especifica um nome amigável a ser usado para o servidor vCenter e o parâmetro **-Account** especifica o identificador de conta no servidor de configuração a ser usado para descobrir máquinas virtuais gerenciadas pelo servidor vCenter.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-AzRecoveryServicesAsrvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

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

**Para gravar no disco gerenciado, use o [módulo AZ. recoveryservices do PowerShell 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) em diante.** Ele requer apenas a criação de uma conta de armazenamento de log. É recomendável usar um tipo de conta padrão e redundância LRS, já que ela é usada para armazenar somente logs temporários. Verifique se a conta de armazenamento foi criada na mesma região do Azure que o cofre.

Se você estiver usando uma versão do módulo AZ. Recoveryservices com mais de 2.0.0, use as etapas a seguir para criar contas de armazenamento. Essas contas de armazenamento são usadas posteriormente para replicar máquinas virtuais. Certifique-se de que as contas de armazenamento sejam criadas na mesma região do Azure que o cofre. Você pode ignorar esta etapa se planeja usar uma conta de armazenamento existente para replicação.

> [!NOTE]
> Ao replicar máquinas virtuais locais para uma conta de armazenamento Premium, você precisa especificar uma conta de armazenamento padrão adicional (conta de armazenamento de log). A conta de armazenamento de log mantém os logs de replicação como um armazenamento intermediário até que os logs possam ser aplicados no destino de armazenamento Premium.
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>Replicar VMs VMware

Leva cerca de 15-20 minutos para que as máquinas virtuais sejam descobertas no servidor do vCenter. Depois de descoberto, um objeto de item de proteção é criado no Azure Site Recovery para cada máquina virtual descoberta. Nesta etapa, três das máquinas virtuais descobertas são replicadas para as contas de armazenamento do Azure criadas na etapa anterior.

Você precisará dos seguintes detalhes para proteger uma máquina virtual descoberta:

* O item de proteção a ser replicado.
* A conta de armazenamento na qual replicar a máquina virtual (somente se você estiver replicando para a conta de armazenamento). 
* Um armazenamento de log é necessário para proteger as máquinas virtuais em uma conta de armazenamento Premium ou em um disco gerenciado.
* O servidor de processo a ser usado para replicação. A lista de servidores de processos disponíveis foi recuperada e guardada na ***$ProcessServers [0]***  *(aumento horizontal-ProcessServer)* e ***$ProcessServers [1]*** *(ConfigurationServer)* variáveis.
* A conta a ser usada para instalar por push o software do serviço de mobilidade nos computadores. A lista de contas disponíveis foi recuperada e armazenada na variável ***$AccountHandles*** .
* O mapeamento de contêiner de proteção para a política de replicação a ser usada para replicação.
* O grupo de recursos no qual as máquinas virtuais devem ser criadas no failover.
* Opcionalmente, a rede virtual do Azure e a sub-rede à qual a máquina virtual com failover deve estar conectada.

Agora, replique as seguintes máquinas virtuais usando as configurações especificadas nesta tabela


|Máquina virtual  |Servidor de processo        |Conta de Armazenamento              |Conta de armazenamento de log  |Política           |Conta para instalação do serviço de mobilidade|Grupo de recursos de destino  | Rede virtual de destino  |Sub-rede de destino  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|CentOSVM1       |ConfigurationServer   |N/D| logstorageaccount1                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Sub-rede-1       |
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR-vnet                 |Sub-rede-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| N/D                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Sub-rede-1       |   


```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM2 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
$Job_EnableReplication2 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

```

Depois que o trabalho habilitar replicação for concluído com êxito, a replicação inicial será iniciada para as máquinas virtuais. A replicação inicial pode demorar um pouco dependendo da quantidade de dados a serem replicados e da largura de banda disponível para replicação. Após a conclusão da replicação inicial, a máquina virtual é movida para um estado protegido. Depois que a máquina virtual atingir um estado protegido, você poderá executar um failover de teste para a máquina virtual, adicioná-la aos planos de recuperação etc.

Você pode verificar o estado de replicação e a integridade da replicação da máquina virtual com o cmdlet Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>Definir configurações de failover

As configurações de failover para computadores protegidos podem ser atualizadas usando o cmdlet Set-ASRReplicationProtectedItem. Algumas das configurações que podem ser atualizadas por meio desse cmdlet são:
* Nome da máquina virtual a ser criada no failover
* Tamanho da VM da máquina virtual a ser criada no failover
* Rede virtual e sub-rede do Azure às quais as NICs da máquina virtual devem estar conectadas no failover
* Failover para discos gerenciados
* Aplicar o benefício de uso híbrido do Azure
* Atribua um endereço IP estático da rede virtual de destino a ser atribuída à máquina virtual no failover.

Neste exemplo, atualizamos o tamanho da VM da máquina virtual a ser criada no failover para a máquina virtual *Win2K12VM1* e especificamos que a máquina virtual usa discos gerenciados no failover.

```azurepowershell
$ReplicatedVM1 = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
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

1. Execute uma análise de recuperação de desastre (failover de teste) da seguinte maneira:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. Depois que o trabalho de failover de teste for concluído com êxito, você observará que uma máquina virtual sufixada com *"-Test"* (Win2K12VM1 neste caso) para seu nome é criada no Azure.
3. Agora você pode se conectar à máquina virtual do teste que passou por failover e validar o failover de teste.
4. Limpe o failover de teste usando o cmdlet Start-ASRTestFailoverCleanupJob. Esta operação exclui a máquina virtual criada como parte da operação de failover de teste.

   ```azurepowershell
   $Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Fazer failover para o Azure

Nesta etapa, realizamos failover da máquina virtual Win2K12VM1 para um ponto de recuperação específico.

1. Obtenha uma lista de pontos de recuperação disponíveis a serem usados para o failover:
   ```azurepowershell
   # Get the list of available recovery points for Win2K12VM1
   $RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
   "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
   ```
   ```
   CrashConsistent 11/24/2017 5:28:25 PM
   ```
   ```azurepowershell

   #Start the failover job
   $Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
   do {
           $Job_Failover = Get-ASRJob -Job $Job_Failover;
           sleep 60;
   } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
   $Job_Failover.State
   ```
   ```
   Succeeded
   ```

2. Após o failover bem-sucedido, você pode confirmar a operação de failover e configurar a replicação inversa do Azure de volta para o site do VMware local.

## <a name="next-steps"></a>Passos seguintes
Saiba como automatizar mais tarefas usando a [referência do Azure site Recovery PowerShell](https://docs.microsoft.com/powershell/module/Az.RecoveryServices).

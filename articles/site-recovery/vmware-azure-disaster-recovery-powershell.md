---
title: Configurar a recuperação de desastres VMware usando PowerShell em Azure Site Revoery
description: Aprenda a configurar a replicação e o failover para Azure para a recuperação de desastres de VMware VMs usando PowerShell na Recuperação do Site Azure.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: de25a3f9df04b09a7337dc889a688a171d98db28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86129905"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>Configurar a recuperação após desastre de VMs do VMware para o Azure com o PowerShell

Neste artigo, você vê como replicar e falhar as máquinas virtuais VMware para Azure usando Azure PowerShell.

Saiba como:

> [!div class="checklist"]
> - Crie um cofre dos Serviços de Recuperação e desemote o contexto do cofre.
> - Valide o registo do servidor no cofre.
> - Configurar a replicação, incluindo uma política de replicação. Adicione o seu servidor vCenter e descubra VMs.
> - Adicione um servidor vCenter e descubra
> - Crie contas de armazenamento para conter registos de replicação ou dados e replicar os VMs.
> - Executar uma ativação pós-falha. Configurar as definições de failover, executar uma definição para replicar máquinas virtuais.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

- Certifique-se de que compreende a [arquitetura e os componentes do cenário](vmware-azure-architecture.md).
- Reveja os [requisitos de suporte](./vmware-physical-azure-support-matrix.md) para todos os componentes.
- Tem o módulo Azure `Az`  PowerShell. Se necessitar de instalar ou atualizar o Azure PowerShell, siga este [Guia para instalar e configurar a Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="log-into-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição Azure utilizando o Connect-AzAccount cmdlet:

```azurepowershell
Connect-AzAccount
```
Selecione a subscrição Azure para a quais pretende replicar as suas máquinas virtuais VMware. Use o Get-AzSubscription cmdlet para obter a lista de subscrições Azure a que tem acesso. Selecione a subscrição Azure para trabalhar com a utilização do Select-AzSubscription cmdlet.

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Configurar um cofre dos Serviços de Recuperação

1. Criar um grupo de recursos para criar o cofre dos Serviços de Recuperação. No exemplo abaixo, o grupo de recursos chama-se VMwareDRtoAzurePS e é criado na região da Ásia Oriental.

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

2. Criar um cofre de serviços de recuperação. No exemplo abaixo, o cofre de serviços recovery chama-se VMwareDRToAzurePs, e é criado na região da Ásia Oriental e no grupo de recursos criado no passo anterior.

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

3. Descarregue a chave de registo do cofre para o cofre. A chave de registo do cofre é utilizada para registar o Servidor de Configuração no local para este cofre. O registo faz parte do processo de instalação do software do Servidor de Configuração.

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

4. Utilize a chave de registo do cofre descarregada e siga os passos nos artigos abaixo indicados para completar a instalação e registo do Servidor de Configuração.
   - [Escolha os seus objetivos de proteção](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [Configurar o ambiente de origem](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>Desa parte para o contexto do cofre

Desa estabaia o contexto do cofre utilizando o Set-ASRVaultContext cmdlet. Uma vez definidas, as operações subsequentes de Recuperação do Local de Azure na sessão PowerShell são realizadas no contexto do cofre selecionado.

> [!TIP]
> O módulo PowerShell de Recuperação do Local Azure (módulo Az.RecoveryServices) vem com pseudónimos fáceis de usar para a maioria dos cmdlets. Os cmdlets do módulo assumem a forma * \<Operation> - **AzRecoveryServicesAsr** \<Object> * e têm pseudónimos equivalentes que assumem o formulário * \<Operation> - **ASR** \<Object> *. Pode substituir os pseudónimos cmdlet para facilitar a utilização.

No exemplo abaixo, os detalhes do cofre da variável $vault são usados para especificar o contexto do cofre para a sessão PowerShell.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

Como alternativa ao Set-ASRVaultContext cmdlet, também se pode usar o Import-AzRecoveryServicesAsrVaultSettingsFile cmdlet para definir o contexto do cofre. Especifique o caminho em que o ficheiro da chave de registo do cofre está localizado como o parâmetro do caminho para o Import-AzRecoveryServicesAsrVaultSettingsFile cmdlet. Por exemplo:

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
Secções subsequentes deste artigo assumem que o contexto do cofre para as operações de recuperação do local de Azure foi definido.

## <a name="validate-vault-registration"></a>Validar o registo do cofre

Para este exemplo, temos o seguinte:

- Um servidor de configuração **(ConfigurationServer)** foi registado neste cofre.
- Um servidor de processo adicional **(ScaleOut-ProcessServer)** foi registado no *ConfigurationServer*
- As contas (**vCenter_account**, **WindowsAccount**, **LinuxAccount**) foram criadas no servidor de Configuração. Estas contas são utilizadas para adicionar o servidor vCenter, para descobrir máquinas virtuais e para instalar o software de serviço de mobilidade nos servidores Windows e Linux que devem ser replicados.

1. Os servidores de configuração registados são representados por um objeto de tecido na Recuperação do Site. Obtenha a lista de objetos de tecido no cofre e identifique o servidor de configuração.

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

2. Identifique os servidores de processo que podem ser usados para replicar máquinas.

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

   Da saída acima ***$ProcessServers[0]*** corresponde ao *ScaleOut-ProcessServer* e ***$ProcessServers[1]*** corresponde à função do Servidor de Processo no *ConfigurationServer*

3. Identifique as contas que foram criadas no Servidor de Configuração.

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

   Da produção acima ***$AccountHandles[0]*** corresponde à *conta vCenter_account,* ***$AccountHandles[1]*** à conta *do WindowsAccount*e ***$AccountHandles[2]*** à conta *linuxAccount*

## <a name="create-a-replication-policy"></a>Criar uma política de replicação

Neste passo, são criadas duas políticas de replicação. Uma política para replicar as máquinas virtuais VMware para o Azure, e a outra para replicar falharam sobre máquinas virtuais que funcionavam em Azure de volta ao site VMware no local.

> [!NOTE]
> A maioria das operações de recuperação do local de Azure são executadas assíncronamente. Quando inicia uma operação, é submetido um trabalho de Recuperação do Local Azure e um objeto de rastreio de emprego é devolvido. Este objeto de rastreio de trabalho pode ser utilizado para monitorizar o estado da operação.

1. Crie uma política de replicação chamada *ReplicationPolicy* para replicar máquinas virtuais VMware para Azure com as propriedades especificadas.

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

2. Crie uma política de replicação para usar para o failback de Azure para o site VMware no local.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-AzRecoveryServicesAsrPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   Utilize os detalhes do trabalho em *$Job_FailbackPolicyCreate* para acompanhar a operação até à conclusão.

   * Crie um mapeamento de contentores de proteção para mapear políticas de replicação com o Servidor de Configuração.

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

## <a name="add-a-vcenter-server-and-discover-vms"></a>Adicione um servidor vCenter e descubra VMs

Adicione um servidor vCenter por endereço IP ou nome de anfitrião. O parâmetro **da porta -porta** especifica a porta no servidor vCenter para ligar, **-O** parâmetro nome especifica um nome amigável a utilizar para o servidor vCenter, e o parâmetro  **-Conta** especifica o manípulo da conta no servidor de configuração para descobrir máquinas virtuais geridas pelo servidor vCenter.

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

**Para escrever para o disco gerido, utilize [o módulo Powershell Az.RecoveryServices 2.0.0.](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)** Só requer a criação de uma conta de armazenamento de registo. Recomenda-se a utilização de um tipo de conta padrão e redundância LRS, uma vez que é utilizado para armazenar apenas registos temporários. Certifique-se de que a conta de armazenamento é criada na mesma região de Azure que o cofre.

Se estiver a utilizar uma versão do módulo Az.RecoveryServices com mais de 2.0.0, utilize os seguintes passos para criar contas de armazenamento. Estas contas de armazenamento são usadas mais tarde para replicar máquinas virtuais. Certifique-se de que as contas de armazenamento são criadas na mesma região de Azure que o cofre. Pode saltar este passo se pretender utilizar uma conta de armazenamento existente para replicação.

> [!NOTE]
> Ao replicar máquinas virtuais no local para uma conta de armazenamento premium, precisa de especificar uma conta de armazenamento padrão adicional (conta de armazenamento de registo). A conta de armazenamento de registos contém registos de replicação como uma loja intermédia até que os registos possam ser aplicados no alvo de armazenamento premium.
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>VMware replicado

Leva cerca de 15 a 20 minutos para que as máquinas virtuais sejam descobertas a partir do servidor vCenter. Uma vez descoberto, um objeto de item protegido é criado na Recuperação do Local Azure para cada máquina virtual descoberta. Neste passo, três das máquinas virtuais descobertas são replicadas nas contas de Armazenamento Azure criadas no passo anterior.

Necessitará dos seguintes detalhes para proteger uma máquina virtual descoberta:

* O item protecível a ser replicado.
* A conta de armazenamento para replicar a máquina virtual (apenas se estiver a replicar a conta de armazenamento). 
* É necessário um armazenamento de registos para proteger as máquinas virtuais a uma conta de armazenamento premium ou a um disco gerido.
* O Servidor de Processo a ser utilizado para a replicação. A lista de servidores de processo disponíveis foi recuperada e guardada nas variáveis ***$ProcessServers[0]****(ScaleOut-ProcessServer)* e ***$ProcessServers[1]*** *(ConfigurationServer).*  
* A conta a utilizar para instalar o software de serviço de mobilidade nas máquinas. A lista de contas disponíveis foi recuperada e armazenada na variável ***$AccountHandles.***
* O mapeamento do contentor de proteção para a política de replicação a utilizar para a replicação.
* O grupo de recursos em que as máquinas virtuais devem ser criadas em falha.
* Opcionalmente, a rede virtual Azure e a sub-rede às quais a máquina virtual falhou deve ser ligada.

Agora replique as seguintes máquinas virtuais usando as definições especificadas nesta tabela


|Máquina virtual  |Servidor de Processo        |Conta de Armazenamento              |Conta de Armazenamento de Registo  |Política           |Conta para instalação de serviço de mobilidade|Grupo de recursos-alvo  | Rede virtual de destino  |Sub-rede-alvo  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|CentOSVM1       |ConfiguraçãoServer   |N/D| logstorageaccount1                 |ReplicaçãoPolícia|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Sub-rede 1       |
|Win2K12VM1       |ScaleOut-ProcessServer|contas premiumtorageac1       |logstorageaccount1   |ReplicaçãoPolícia|Contas do Windows                           |VMwareDRToAzurePs      |ASR-vnet                 |Sub-rede 1       |   
|CentOSVM2       |ConfiguraçãoServer   |replicationstdstorageaccount1| N/D                 |ReplicaçãoPolícia|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Sub-rede 1       |   


```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

# Alternatively, if the virtual machine CentOSVM1 has CMK enabled disks, enable replication using Az module 3.3.0 onwards as below
# $diskID is the Disk Encryption Set ID to be used for all replica managed disks and target managed disks in the target region
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId -DiskEncryptionSetId $diskId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM2 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
$Job_EnableReplication2 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

```

Uma vez concluído o trabalho de replicação ativa, inicia-se a replicação inicial para as máquinas virtuais. A replicação inicial pode demorar algum tempo dependendo da quantidade de dados a replicar e da largura de banda disponível para replicação. Após a replicação inicial concluída, a máquina virtual move-se para um estado protegido. Assim que a máquina virtual atingir um estado protegido, pode efetuar uma falha de teste para a máquina virtual, adicione-a a planos de recuperação, etc.

Pode verificar o estado de replicação e a saúde de replicação da máquina virtual com o Get-ASRReplicationProtectedItem cmdlet.

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

## <a name="configure-failover-settings"></a>Configurar definições de failover

As definições de falha para máquinas protegidas podem ser atualizadas utilizando o cmdlet Set-ASRReplicationProtectedItem. Algumas das definições que podem ser atualizadas através deste cmdlet são:
* Nome da máquina virtual a ser criada no failover
* Tamanho VM da máquina virtual a ser criada em failover
* Rede virtual Azure e sub-rede a que os NICs da máquina virtual devem ser ligados no failover
* Falha nos discos geridos
* Aplicar benefício de uso híbrido Azure
* Atribua um endereço IP estático da rede virtual alvo a atribuir à máquina virtual em falha.

Neste exemplo, atualizamos o tamanho VM da máquina virtual a ser criada na falha para a máquina virtual *Win2K12VM1* e especificamos que a máquina virtual utiliza discos geridos na falha.

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

1. Executar um berbequim DR (teste failover) da seguinte forma:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. Uma vez concluído o trabalho de failover do teste com sucesso, irá notar que uma máquina virtual sufixada com *"-Test"* (Win2K12VM1-Test, neste caso) é criada em Azure.
3. Pode agora ligar-se ao teste falhado sobre a máquina virtual e validar o teste de falha.
4. Limpe o teste com a Start-ASRTestFailoverCleanupJob cmdlet. Esta operação elimina a máquina virtual criada como parte da operação de falha de teste.

   ```azurepowershell
   $Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Ativação pós-falha para o Azure

Neste passo, falhamos sobre a máquina virtual Win2K12VM1 para um ponto de recuperação específico.

1. Obtenha uma lista de pontos de recuperação disponíveis para o failover:
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

2. Uma vez falhado com sucesso, pode cometer a operação de failover e configurar a replicação inversa do Azure de volta para o local de VMware no local.

## <a name="next-steps"></a>Passos seguintes
Saiba como automatizar mais tarefas utilizando a [referência PowerShell de recuperação do local de Azure](/powershell/module/Az.RecoveryServices).

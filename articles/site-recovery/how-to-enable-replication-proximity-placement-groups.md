---
title: Replicam VMs Azure em grupos de colocação de proximidade
description: Saiba como replicar Os VMs Azure que executam em Grupos de Colocação de Proximidade utilizando a Recuperação do Site Azure.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 05/25/2020
ms.openlocfilehash: 204ac3be46ac7ba0e1ea96e50379ca417b1299ce
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83847638"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>Replicar máquinas virtuais Azure em execução em Grupos de Colocação de Proximidade para outra região

Este artigo descreve como replicar, falhar e falhar máquinas virtuais que executam num Grupo de Colocação de Proximidade para uma região secundária.

[Os Grupos](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups-portal) de Colocação de Proximidade é uma capacidade de agrupamento lógica da Máquina Virtual Azure que pode utilizar para diminuir a latência da rede inter-VM associada às suas aplicações. Quando os VMs são implantados dentro do mesmo grupo de colocação de proximidade, estão fisicamente localizados o mais próximo possível uns dos outros. Os grupos de colocação de proximidade são particularmente úteis para responder aos requisitos das cargas de trabalho sensíveis à latência.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>Recuperação de desastres com grupos de colocação de proximidade

Num cenário típico, poderá ter as suas máquinas virtuais a funcionar num grupo de colocação de proximidade para evitar a latência da rede entre os vários níveis da sua aplicação. Embora isto possa fornecer a sua aplicação de latência ideal de rede, você gostaria de proteger estas aplicações usando a Recuperação do Site para qualquer falha de nível de região. A Recuperação do Site replica os dados de uma região para outra região de Azure e traz as máquinas na região de recuperação de desastres em caso de falha.

## <a name="considerations"></a>Considerações

- O melhor esforço será falhar/falhar as máquinas virtuais num grupo de colocação de proximidade. No entanto, se a VM não puder ser criada dentro da Colocação de Proximidade durante o failover/failback, então o failover/failback continuará a acontecer, e as máquinas virtuais serão criadas fora de um grupo de colocação de proximidade.
-  Se um Conjunto de Disponibilidade for fixado a um Grupo de Colocação de Proximidade e durante os VMs failover/failback no conjunto de disponibilidade têm um constrangimento de alocação, então as máquinas virtuais serão criadas fora do conjunto de disponibilidade e do grupo de colocação de proximidade.
-  A recuperação do site para grupos de colocação de proximidade não é suportada para discos não geridos.

## <a name="prerequisites"></a>Pré-requisitos

1. Certifique-se de que tem o módulo Azure PowerShell Az. Se precisar de instalar ou atualizar o Azure PowerShell, siga este Guia para instalar e configurar o [PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>Configurar a recuperação do site para máquinas virtuais no grupo de colocação de proximidade

### <a name="azure-to-azure"></a>Azure para o Azure

1. [Inscreva-se](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#sign-in-to-your-microsoft-azure-subscription) na sua conta e desemlete a sua subscrição.
2. Obtenha os detalhes da máquina virtual que está a planear replicar como mencionado [aqui.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#get-details-of-the-virtual-machine-to-be-replicated)
3. [Crie](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-recovery-services-vault) o cofre dos serviços de recuperação e [detete o](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#set-the-vault-context) contexto do cofre.
4. Prepare o cofre para iniciar a replicação da máquina virtual. Isto envolve a criação de um objeto de tecido de [serviço](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region) para as regiões primárias e de recuperação.
5. [Crie](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-site-recovery-protection-container-in-the-primary-fabric) um recipiente de proteção de recuperação do local, tanto para os tecidos primários como para a recuperação.
6. [Criar](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-replication-policy) uma política de replicação.
7. Crie um mapeamento de recipientede proteção entre o recipiente de proteção primário e de recuperação utilizando [estas](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container) etapas e um mapeamento de contentores de proteção para o [recuo,](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover)como aqui referido .
8. Crie uma conta de armazenamento de cache seguindo [estes](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-cache-storage-account-and-target-storage-account) passos.
9. Crie os mapeamentos de rede necessários como mencionado [aqui.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-network-mappings)
10. Para replicar a máquina virtual Azure com discos geridos, utilize o cmdlet de PowerShell abaixo – 

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id ` -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType -RecoveryProximityPlacementGroupId $recPpg.Id

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id ` -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $recPpg.Id
```
Uma vez que a operação de replicação inicial seja bem sucedida, os dados das máquinas virtuais são replicados para a região de recuperação.

O processo de replicação começa por secar inicialmente uma cópia dos discos de replicação da máquina virtual na região de recuperação. Esta fase é chamada de fase de replicação inicial.

Após a replicação inicial completa, a replicação move-se para a fase de sincronização diferencial. Neste ponto, a máquina virtual está protegida e pode ser realizada uma operação de falha de teste. O estado de replicação do item replicado que representa a máquina virtual vai para o estado protegido após a replicação inicial estar concluída.

Monitorize o estado de replicação e a saúde de replicação para a máquina virtual, obtendo detalhes do item protegido de replicação correspondente ao mesmo. 

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. Para fazer uma falha no teste, validar e limpar o teste de failover, siga [estes](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#do-a-test-failover-validate-and-cleanup-test-failover) passos.
12. Para falhar, siga os [here](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#fail-over-to-azure)passos aqui mencionados.
13. Para reproteger e falhar na região de origem, utilize o cmdlet abaixo PowerShell –

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```
14. Para desativar a replicação, siga os passos [aqui](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#disable-replication).

### <a name="vmware-to-azure"></a>VMware para o Azure

1. Certifique-se de que [prepara os seus servidores VMware no local](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises) para a recuperação de desastres para o Azure.
2. Inscreva-se na sua conta e reserve a sua subscrição conforme especificado [aqui](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#log-into-azure).
3. [Instale](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#set-up-a-recovery-services-vault) um Cofre de Serviços de Recuperação e [detetete](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#set-the-vault-context)o contexto do cofre.
4. [Valide](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#validate-vault-registration) o registo do cofre.
5. [Criar](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#create-a-replication-policy) uma política de replicação.
6. [Adicione](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#add-a-vcenter-server-and-discover-vms) um servidor vCenter e descubra máquinas virtuais e [crie](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#create-storage-accounts-for-replication) contas de armazenamento para replicação.
7. Para replicar as Máquinas Virtuais VMware, consulte os detalhes aqui e siga o cmdlet abaixo PowerShell –

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $recPpg.Id
```
8. Pode verificar o estado de replicação e a saúde de replicação da máquina virtual com o cmdlet Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
9. Configure as definições de failover seguindo os passos [aqui](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#configure-failover-settings).
10. [Fazer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#run-a-test-failover) um teste falhado. 
11. Failover para Azure usando [estes](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#fail-over-to-azure) passos.

### <a name="hyper-v-to-azure"></a>Hyper-V para o Azure

1. Certifique-se de que [prepara os seus servidores Hyper-V no local](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial) para a recuperação de desastres para o Azure.
2. [Inscreva-se no](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-1-sign-in-to-your-azure-account) Azure.
3. [Instale](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-2-set-up-the-vault) o seu cofre e [detetete](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-3-set-the-recovery-services-vault-context) o contexto do Cofre dos Serviços de Recuperação.
4. [Crie](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-4-create-a-hyper-v-site) um Hiper-V Site.
5. [Instale](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-5-install-the-provider-and-agent) o fornecedor e o agente.
6. [Criar](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-6-create-a-replication-policy) uma política de replicação.
7. Ativar a replicação utilizando os passos abaixo – 
    
    a. Recupere o item protectável que corresponde ao VM que pretende proteger, da seguinte forma:

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. Proteja o VM. Se o VM que está a proteger tiver mais de um disco ligado ao mesmo, especifique o disco do sistema operativo utilizando o parâmetro OSDiskName.
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $recPpg.Id
    ```
    c. Aguarde que os VMs cheguem a um estado protegido após a replicação inicial. Isto pode demorar algum tempo, dependendo de fatores como a quantidade de dados a replicar, e a largura de banda disponível a montante para Azure. Quando um estado protegido está em vigor, o estado de trabalho e a descrição do Estado são atualizados da seguinte forma: 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. As propriedades de recuperação de atualizações (como o tamanho da função VM) e a rede Azure para anexar o VM NIC após a falha.

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. Executar um [teste failover](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-8-run-a-test-failover).


## <a name="next-steps"></a>Passos seguintes

Para executar reproteger e falhar para a VMware para o Azure, siga os passos [aqui](https://docs.microsoft.com/azure/site-recovery/vmware-azure-prepare-failback)descritos .

Para realizar a failover para Hyper-V para Azure siga os passos [aqui](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) descritos e para executar o failback, siga os passos [aqui](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-failback)descritos .

Para mais informações, consulte [Failover na Recuperação](site-recovery-failover.md)do Site .

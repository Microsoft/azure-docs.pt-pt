---
title: Replicar VMs Azure em execução em grupos de colocação de proximidade
description: Saiba como replicar VMs Azure em execução em Grupos de Colocação de Proximidade usando a Recuperação do Local de Azure.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 05/25/2020
ms.openlocfilehash: f3abdad427e038bb4a853cb6222174dd090cb6b2
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348428"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>Replicar máquinas virtuais do Azure em execução em Grupos de Colocação por Proximidade para outra região

Este artigo descreve como replicar, falhar e falhar máquinas virtuais que estão a funcionar num Grupo de Colocação de Proximidade para uma região secundária.

[Grupos de Colocação de Proximidade](../virtual-machines/windows/proximity-placement-groups-portal.md) é uma capacidade de agrupamento lógico da Máquina Virtual Azure que pode utilizar para diminuir a latência da rede inter-VM associada às suas aplicações. Quando os VMs são implantados dentro do mesmo grupo de colocação de proximidade, estão fisicamente localizados o mais próximo possível uns dos outros. Os grupos de colocação de proximidade são particularmente úteis para responder aos requisitos das cargas de trabalho sensíveis à latência.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>Recuperação de desastres com grupos de colocação de proximidade

Num cenário típico, poderá ter as suas máquinas virtuais a funcionar num grupo de colocação de proximidade para evitar a latência da rede entre os vários níveis da sua aplicação. Embora isto possa fornecer a sua aplicação de latência de rede ideal, você gostaria de proteger estas aplicações usando a Recuperação do Site para qualquer falha no nível da região. A Recuperação do Local replica os dados de uma região para outra região do Azure e traz as máquinas na região de recuperação de desastres em caso de falha.

## <a name="considerations"></a>Considerações

- O melhor esforço será falhar/falhar as máquinas virtuais num grupo de colocação de proximidade. No entanto, se o VM não puder ser criado dentro da Colocação de Proximidade durante o failover/failback, então o failover/failback continuará a acontecer, e máquinas virtuais serão criadas fora de um grupo de colocação de proximidade.
-  Se um Conjunto de Disponibilidade for fixado a um Grupo de Colocação de Proximidade e durante os VMs de failover/failback no conjunto de disponibilidades tiverem uma restrição de atribuição, então as máquinas virtuais serão criadas fora do conjunto de disponibilidade e do grupo de colocação de proximidade.
-  A recuperação do local para grupos de colocação de proximidade não é suportada para discos não geridos.

> [!NOTE]
> A Azure Site Recovery não suporta falhas de discos geridos para cenários De Hiper-V a Azure. Assim, não é suportado o recuo do Grupo de Colocação de Proximidade em Azure para o Hiper-V.

## <a name="prerequisites"></a>Pré-requisitos

1. Certifique-se de que tem o módulo Azure PowerShell Az. Se necessitar de instalar ou atualizar o Azure PowerShell, siga este [Guia para instalar e configurar a Azure PowerShell](/powershell/azure/install-az-ps).
2. A versão mínima Azure PowerShell Az deve ser 4.1.0. Para verificar a versão atual, use o comando abaixo -
    ```
    Get-InstalledModule -Name Az
    ```

## <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>Configurar a recuperação do site para máquinas virtuais no Grupo de Colocação de Proximidade

> [!NOTE]
> Certifique-se de que tem à mão o ID único do Grupo de Colocação de Proximidade alvo. Se está a criar um novo Grupo de Colocação de Proximidade, verifique o comando [aqui](../virtual-machines/windows/proximity-placement-groups.md#create-a-proximity-placement-group) e se estiver a utilizar um Grupo de Colocação de Proximidade existente, use o comando [aqui](../virtual-machines/windows/proximity-placement-groups.md#list-proximity-placement-groups).

### <a name="azure-to-azure"></a>Azure para o Azure

1. [Inscreva-se na](./azure-to-azure-powershell.md#sign-in-to-your-microsoft-azure-subscription) sua conta e descreva a sua subscrição.
2. Obtenha os detalhes da máquina virtual que planeia replicar como mencionado [aqui.](./azure-to-azure-powershell.md#get-details-of-the-virtual-machine-to-be-replicated)
3. [Crie](./azure-to-azure-powershell.md#create-a-recovery-services-vault) o cofre dos seus serviços de recuperação e [desemote](./azure-to-azure-powershell.md#set-the-vault-context) o contexto do cofre.
4. Prepare o cofre para iniciar a máquina virtual de replicação. Isto envolve a criação de um [objeto de tecido de serviço](./azure-to-azure-powershell.md#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region) para as regiões primárias e de recuperação.
5. [Criar](./azure-to-azure-powershell.md#create-a-site-recovery-protection-container-in-the-primary-fabric) um recipiente de proteção de recuperação do local, tanto para os tecidos primário como para a recuperação.
6. [Criar](./azure-to-azure-powershell.md#create-a-replication-policy) uma política de replicação.
7. Crie um mapeamento de recipientes de proteção entre o recipiente de proteção primária e de recuperação utilizando [estas](./azure-to-azure-powershell.md#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container) etapas e um mapeamento de contentores de proteção para falhas, como [mencionado aqui](./azure-to-azure-powershell.md#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover).
8. Crie uma conta de armazenamento de cache seguindo [estes](./azure-to-azure-powershell.md#create-cache-storage-account-and-target-storage-account) passos.
9. Crie os mapeamentos de rede necessários, conforme mencionado [aqui.](./azure-to-azure-powershell.md#create-network-mappings)
10. Para replicar a máquina virtual Azure com discos geridos, use o cmdlet abaixo do PowerShell -

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OS Disk
$OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName $OSdiskResourceGroup
$OSdiskId = $OSdisk.Id
$RecoveryOSDiskAccountType = $OSdisk.Sku.Name
$RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

#Data disk
$datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName $datadiskResourceGroup
$datadiskId1 = $datadisk[0].Id
$RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
$RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```

Ao permitir a zona de replicação de zona com PPG, o comando para iniciar a replicação será trocado com o cmdlet PowerShell -

```azurepowershell
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id -RecoveryAvailabilityZone "2"
```

Uma vez que a operação de replicação inicial tenha sucesso, os dados da máquina virtual são replicados na região de recuperação.

O processo de replicação começa por sementeirar inicialmente uma cópia dos discos replicadores da máquina virtual na região de recuperação. Esta fase é chamada de fase inicial de replicação.

Após a replicação inicial concluída, a replicação move-se para a fase de sincronização diferencial. Neste ponto, a máquina virtual está protegida e pode ser realizada uma operação de falha de teste. O estado de replicação do item replicado que representa a máquina virtual vai para o estado protegido após a replicação inicial.

Monitorize o estado de replicação e a saúde de replicação para a máquina virtual, obtendo detalhes do item protegido de replicação correspondente à sua.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. Para fazer um teste de failover, validar e limpar o teste de falha, siga [estes](./azure-to-azure-powershell.md#do-a-test-failover-validate-and-cleanup-test-failover) passos.
12. Para falhar, siga os passos como mencionado [aqui.](./azure-to-azure-powershell.md#fail-over-to-azure)
13. Para reprotetar e falhar na região de origem, utilize o cmdlet abaixo do PowerShell –

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```

14. Para desativar a replicação, siga os passos [aqui.](./azure-to-azure-powershell.md#disable-replication)

### <a name="vmware-to-azure"></a>VMware para o Azure

1. Certifique-se de que [prepara os seus servidores VMware no local](./vmware-azure-tutorial-prepare-on-premises.md) para a recuperação de desastres para Azure.
2. Inscreva-se na sua conta e descreva a sua subscrição conforme especificado [aqui.](./vmware-azure-disaster-recovery-powershell.md#log-into-azure)
3. [Crie](./vmware-azure-disaster-recovery-powershell.md#set-up-a-recovery-services-vault) um cofre de serviços de recuperação e [coloque o contexto do cofre.](./vmware-azure-disaster-recovery-powershell.md#set-the-vault-context)
4. [Valide](./vmware-azure-disaster-recovery-powershell.md#validate-vault-registration) o registo do cofre.
5. [Criar](./vmware-azure-disaster-recovery-powershell.md#create-a-replication-policy) uma política de replicação.
6. [Adicione](./vmware-azure-disaster-recovery-powershell.md#add-a-vcenter-server-and-discover-vms) um servidor vCenter e descubra máquinas virtuais e [crie](./vmware-azure-disaster-recovery-powershell.md#create-storage-accounts-for-replication) contas de armazenamento para replicação.
7. Para replicar as Máquinas Virtuais VMware, consulte os detalhes aqui e siga o cmdlet abaixo do PowerShell –

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
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $targetPpg.Id
```

8. Pode verificar o estado de replicação e a saúde de replicação da máquina virtual com o Get-ASRReplicationProtectedItem cmdlet.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

9. Configure as definições de failover seguindo os passos [aqui](./vmware-azure-disaster-recovery-powershell.md#configure-failover-settings).
10. [Fazer](./vmware-azure-disaster-recovery-powershell.md#run-a-test-failover) um teste falhado.
11. Falha no Azure utilizando [estes](./vmware-azure-disaster-recovery-powershell.md#fail-over-to-azure) passos.

### <a name="hyper-v-to-azure"></a>Hyper-V para o Azure

1. Certifique-se de que [prepara os seus servidores Hiper-V no local](./hyper-v-prepare-on-premises-tutorial.md) para a recuperação de desastres para Azure.
2. [Inscreva-se no](./hyper-v-azure-powershell-resource-manager.md#step-1-sign-in-to-your-azure-account) Azure.
3. [Configura o](./hyper-v-azure-powershell-resource-manager.md#step-2-set-up-the-vault) seu cofre e [define](./hyper-v-azure-powershell-resource-manager.md#step-3-set-the-recovery-services-vault-context) o contexto do Cofre dos Serviços de Recuperação.
4. [Criar](./hyper-v-azure-powershell-resource-manager.md#step-4-create-a-hyper-v-site) um Site Hiper-V.
5. [Instale](./hyper-v-azure-powershell-resource-manager.md#step-5-install-the-provider-and-agent) o fornecedor e o agente.
6. [Criar](./hyper-v-azure-powershell-resource-manager.md#step-6-create-a-replication-policy) uma política de replicação.
7. Ativar a replicação utilizando os passos abaixo – 
    
    a. Recupere o item protegido que corresponde ao VM que pretende proteger, da seguinte forma:

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. Proteja o VM. Se o VM que está a proteger tiver mais do que um disco ligado a ele, especifique o disco do sistema operativo utilizando o parâmetro OSDiskName.
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```
    c. Aguarde que os VMs atinjam um estado protegido após a replicação inicial. Isto pode demorar algum tempo, dependendo de fatores como a quantidade de dados a replicar, e a largura de banda disponível a montante para Azure. Quando um estado protegido está em vigor, o estado de trabalho e a descrição do Estado são atualizados da seguinte forma: 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. Atualize as propriedades de recuperação (como o tamanho da função VM) e a rede Azure à qual se ligam o VM NIC após a falha.

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. Executar um teste [falhado](./hyper-v-azure-powershell-resource-manager.md#step-8-run-a-test-failover).


## <a name="next-steps"></a>Próximos passos

Para realizar reprotegido e failback para VMware para Azure, siga os passos [aqui](./vmware-azure-prepare-failback.md)descritos .

Para executar o failover para Hyper-V a Azure siga os passos [aqui](./site-recovery-failover.md) descritos e para executar o failback, siga os passos [aqui](./hyper-v-azure-failback.md)descritos .

Para obter mais informações, consulte [Failover na Recuperação do Local.](site-recovery-failover.md)
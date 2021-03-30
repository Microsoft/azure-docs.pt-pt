---
title: Configurar a recuperação de desastres Hyper-V (com VMM) para um local secundário com Azure Site Recovery/PowerShell
description: Descreve como configurar a recuperação de desastres de VMs hiper-V em nuvens VMM para um site secundário de VMM usando Azure Site Recovery e PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: a4f7d330db9a4a0d9b435ebe7527e55e37c254e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87086222"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Configurar a recuperação de desastres de Hiper-VMs para um local secundário utilizando o PowerShell (Gestor de Recursos)

Este artigo mostra como automatizar os passos para a replicação de VMs hiper-V em nuvens de Gerente de Máquina Virtual do System Center para uma nuvem virtual do Gestor de Máquinas num local secundário no local, utilizando [a recuperação do site Azure.](site-recovery-overview.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Reveja a [arquitetura e os componentes do cenário](hyper-v-vmm-architecture.md).
- Reveja os [requisitos de suporte](./vmware-physical-secondary-support-matrix.md) para todos os componentes.
- Certifique-se de que os servidores do Gestor de Máquinas Virtuais e os anfitriões Hiper-V cumprem os [requisitos de suporte](./vmware-physical-secondary-support-matrix.md).
- Verifique se os VMs que pretende replicar cumprem o [suporte replicado da máquina](./vmware-physical-secondary-support-matrix.md).

## <a name="prepare-for-network-mapping"></a>Preparar o mapeamento da rede

[Mapas de mapeamento](hyper-v-vmm-network-mapping.md) de rede entre as redes VM do Gestor de Máquinas Virtuais nas redes de origem e alvo. O mapeamento faz o seguinte:

- Liga as VMs às redes de VMs de destino adequadas após a ativação pós-falha.
- Coloca as VMs de réplica nos servidores dos anfitriões de Hyper-V de destino, de forma otimizada.
- Se não configurar o mapeamento da rede, as réplicas de VMs não serão ligadas a uma rede VM após a falha.

Prepare o Gestor de Máquinas Virtuais da seguinte forma:

- Certifique-se de que tem [redes lógicas do Gestor de Máquinas Virtuais](/system-center/vmm/network-logical) nos servidores do Gestor de Máquinas Virtuais de origem e alvo:
  - A rede lógica no servidor de origem deve ser associada à cloud de origem na qual os anfitriões de Hyper-V estão localizados.
  - A rede lógica no servidor de destino deve ser associada à cloud de destino.
- Certifique-se de que tem [redes VM](/system-center/vmm/network-virtual) na fonte e direcione os servidores Virtual Machine Manager. As redes de VMs devem ser ligadas à rede lógica em cada localização.
- Ligue as VMs nos anfitriões de Hyper-V de origem à rede de VMs de origem.

## <a name="prepare-for-powershell"></a>Preparar para PowerShell

Certifique-se de que tem o Azure PowerShell pronto para ir:

- Se já utilizar o PowerShell, atualize para a versão 0.8.10 ou posterior. [Saiba mais](/powershell/azure/) sobre como configurar o PowerShell.
- Depois de configurar e configurar a PowerShell, reveja os [cmdlets de serviço](/powershell/azure/).
- Para saber mais sobre como usar valores de parâmetros, entradas e saídas em PowerShell, leia o guia [Iniciar.](/powershell/azure/get-started-azureps)

## <a name="set-up-a-subscription"></a>Configurar uma subscrição

1. Da PowerShell, inscreva-se na sua conta Azure.

   ```azurepowershell
   $UserName = "<user@live.com>"
   $Password = "<password>"
   $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
   $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
   Connect-AzAccount #-Credential $Cred
   ```

1. Recupere uma lista das suas subscrições, com os IDs de subscrição. Note o ID da subscrição na qual pretende criar o cofre dos Serviços de Recuperação.

   ```azurepowershell
   Get-AzSubscription
   ```

1. Desemboe a assinatura do cofre.

   ```azurepowershell
   Set-AzContext –SubscriptionID <subscriptionId>
   ```

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

1. Crie um grupo de recursos Azure Resource Manager se não tiver um.

   ```azurepowershell
   New-AzResourceGroup -Name #ResourceGroupName -Location #location
   ```

1. Criar um novo cofre dos Serviços de Recuperação. Guarde o objeto do cofre numa variável para ser usado mais tarde.

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   ```

   Pode recuperar o objeto do cofre depois de o criar utilizando o `Get-AzRecoveryServicesVault` cmdlet.

## <a name="set-the-vault-context"></a>Desa parte para o contexto do cofre

1. Recuperar um cofre existente.

   ```azurepowershell
   $vault = Get-AzRecoveryServicesVault -Name #vaultname
   ```

1. Desa parte do contexto do cofre.

   ```azurepowershell
   Set-AzRecoveryServicesAsrVaultContext -Vault $vault
   ```

## <a name="install-the-site-recovery-provider"></a>Instale o fornecedor de recuperação do site

1. Na máquina Virtual Machine Manager, crie um diretório executando o seguinte comando:

   ```azurepowershell
   New-Item -Path C:\ASR -ItemType Directory
   ```

1. Extrair os ficheiros utilizando o ficheiro de configuração do fornecedor descarregado.

   ```console
   pushd C:\ASR\
   .\AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Instale o fornecedor e aguarde que a instalação termine.

   ```console
   .\SetupDr.exe /i
   $installationRegPath = "HKLM:\Software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
   do
   {
     $isNotInstalled = $true;
     if(Test-Path $installationRegPath)
     {
       $isNotInstalled = $false;
     }
   }While($isNotInstalled)
   ```

1. Registe o servidor no cofre.

   ```console
   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
   ```

## <a name="create-and-associate-a-replication-policy"></a>Criar e associar uma política de replicação

1. Crie uma política de replicação, neste caso para Hyper-V 2012 R2, da seguinte forma:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $RepProvider = HyperVReplica2012R2
   $Recoverypoints = 24                    #specify the number of hours to retain recovery points
   $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
   $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
   $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
   $InitialRepMethod = "Online" #options are "Online" or "Offline"

   $policyresult = New-AzRecoveryServicesAsrPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -NumberOfRecoveryPointsToRetain $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod
   ```

   > [!NOTE]
   > A nuvem Virtual Machine Manager pode conter anfitriões Hiper-V que executam diferentes versões do Windows Server, mas a política de replicação é para uma versão específica de um sistema operativo. Se tiver diferentes anfitriões em execução em diferentes sistemas operativos, crie políticas de replicação separadas para cada sistema. Por exemplo, se tiver cinco anfitriões em execução no Windows Server 2012 e três anfitriões em execução no Windows Server 2012 R2, crie duas políticas de replicação. Cria-se um para cada tipo de sistema operativo.

1. Recupere o recipiente de proteção primária (nuvem de gestor de máquinas virtual primária) e o recipiente de proteção de recuperação (nuvem virtual gestor de máquinas de recuperação).

   ```azurepowershell
   $PrimaryCloud = "testprimarycloud"
   $primaryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloud;

   $RecoveryCloud = "testrecoverycloud"
   $recoveryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $RecoveryCloud;
   ```

1. Recupere a política de replicação que criou utilizando o nome amigável.

   ```azurepowershell
   $policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $policyname
   ```

1. Inicie a associação do recipiente de proteção (Virtual Machine Manager cloud) com a política de replicação.

   ```azurepowershell
   $associationJob  = New-AzRecoveryServicesAsrProtectionContainerMapping -Policy $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
   ```

1. Espere o trabalho de associação política terminar. Para verificar se o trabalho está terminado, utilize o seguinte corte PowerShell:

   ```azurepowershell
   $job = Get-AzRecoveryServicesAsrJob -Job $associationJob

   if($job -eq $null -or $job.StateDescription -ne "Completed")
   {
     $isJobLeftForProcessing = $true;
   }
   ```

1. Após o trabalho terminar o processamento, executar o seguinte comando:

   ```azurepowershell
   if($isJobLeftForProcessing)
   {
     Start-Sleep -Seconds 60
   }
   While($isJobLeftForProcessing)
   ```

Para verificar a conclusão da operação, siga os passos na [atividade do Monitor](#monitor-activity).

##  <a name="configure-network-mapping"></a>Configurar o mapeamento da rede

1. Utilize este comando para recuperar servidores para o cofre atual. O comando armazena os servidores de Recuperação do Site na `$Servers` variável matriz.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesAsrFabric
   ```

1. Executar este comando para recuperar as redes para o servidor Virtual Machine Manager de origem e para o servidor Target Virtual Machine Manager.

   ```azurepowershell
   $PrimaryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[0]

   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[1]
   ```

   > [!NOTE]
   > O servidor De Gestor de Máquinas Virtual de origem pode ser o primeiro ou o segundo na matriz do servidor. Verifique os nomes do servidor do Gestor de Máquinas Virtuais e recupere as redes adequadamente.

1. Este cmdlet cria um mapeamento entre a rede primária e a rede de recuperação. Especifica a rede primária como o primeiro elemento de `$PrimaryNetworks` . Especifica a rede de recuperação como o primeiro elemento de `$RecoveryNetworks` .

   ```azurepowershell
   New-AzRecoveryServicesAsrNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]
   ```

## <a name="enable-protection-for-vms"></a>Permitir proteção para VMs

Após os servidores, nuvens e redes estarem configurados corretamente, permitir a proteção de VMs na nuvem.

1. Para permitir a proteção, executar o seguinte comando para recuperar o recipiente de proteção:

   ```azurepowershell
   $PrimaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloudName
   ```

1. Obtenha a entidade de proteção (VM), da seguinte forma:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
   ```

1. Ativar a replicação para o VM.

   ```azurepowershell
   $jobResult = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $protectionentity -ProtectionContainerMapping $policy -VmmToVmm
   ```

> [!NOTE]
> Se pretender replicar os discos geridos com ativação CMK em Azure, faça os seguintes passos utilizando a az PowerShell 3.3.0 em diante:
>
> 1. Permitir falhas em discos geridos atualizando propriedades VM
> 1. Utilize o `Get-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet para obter o ID do disco para cada disco do item protegido
> 1. Crie um objeto dicionário utilizando `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` o cmdlet para conter o mapeamento do ID do disco para conjunto de encriptação de disco. Estes conjuntos de encriptação de discos devem ser pré-criados por si na região alvo.
> 1. Atualize as propriedades VM utilizando `Set-AzRecoveryServicesAsrReplicationProtectedItem` o cmdlet passando o objeto do dicionário no parâmetro **DiskIdToDiskEncryptionSetMap.**

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Para testar a sua colocação, teste falha para uma única máquina virtual. Também pode criar um plano de recuperação que contenha vários VMs e executar um teste de failover para o plano. A ativação pós-falha de teste simula o mecanismo de ativação pós-falha e recuperação numa rede isolada.

1. Recupere o VM no qual os VMs vão falhar.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesASRFabric
   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[1]
   ```

1. Faça um teste de falha.

   Para um único VM:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity -VMNetwork $RecoveryNetworks[1]
   ```

   Para um plano de recuperação:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan -VMNetwork $RecoveryNetworks[1]
   ```

Para verificar a conclusão da operação, siga os passos na [atividade do Monitor](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Executar execuções planeadas e não planeadas

1. Faça uma falha planeada.

   Para um único VM:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   Para um plano de recuperação:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

1. Execute uma falha não planeada.

   Para um único VM:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   Para um plano de recuperação:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

## <a name="monitor-activity"></a>Monitorizar a atividade

Utilize os seguintes comandos para monitorizar a atividade de falha. Aguarde que o processamento termine entre os empregos.

```azurepowershell
Do
{
    $job = Get-AzRecoveryServicesAsrJob -TargetObjectId $associationJob.JobId;
    Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
    if($job -eq $null -or $job.StateDescription -ne "Completed")
    {
        $isJobLeftForProcessing = $true;
    }

if($isJobLeftForProcessing)
    {
        Start-Sleep -Seconds 60
    }
}While($isJobLeftForProcessing)
```

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](/powershell/module/az.recoveryservices) sobre a Recuperação do Site com cmdlets PowerShell do Gestor de Recursos.

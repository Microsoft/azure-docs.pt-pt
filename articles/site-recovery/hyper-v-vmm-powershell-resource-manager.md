---
title: Configurar a recuperação de desastres hyper-V (com VMM) para um local secundário com recuperação de site azure/PowerShell
description: Descreve como configurar a recuperação de desastres de VMs hiper-V em nuvens VMM para um site de VMM secundário usando a Recuperação do Site Azure e PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: deef7bfdbc28d744cb81da59d3ffc13a1abee54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048605"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Configurar a recuperação de desastres de VMs hiper-V para um local secundário usando powerShell (Gestor de Recursos)

Este artigo mostra como automatizar os passos para a replicação de VMs Hiper-V em System Center Virtual Machine Manager nuvens para uma nuvem de Gestor de Máquinavirtual num local secundário no local, utilizando a Recuperação do [Site Azure.](site-recovery-overview.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Reveja a [arquitetura e os componentes do cenário](hyper-v-vmm-architecture.md).
- Reveja os [requisitos de suporte](site-recovery-support-matrix-to-sec-site.md) de todos os componentes.
- Certifique-se de que os servidores do Gestor de Máquinas Virtuais e os anfitriões Hyper-V cumprem os [requisitos de suporte](site-recovery-support-matrix-to-sec-site.md).
- Verifique se os VMs que pretende replicar cumprem o [suporte à máquina replicado](site-recovery-support-matrix-to-sec-site.md).

## <a name="prepare-for-network-mapping"></a>Preparar o mapeamento da rede

Mapas de [mapeamento](hyper-v-vmm-network-mapping.md) de rede entre as redes VM virtual de machine manager no local em nuvens de origem e alvo. O mapeamento faz o seguinte:

- Liga as VMs às redes de VMs de destino adequadas após a ativação pós-falha.
- Coloca as VMs de réplica nos servidores dos anfitriões de Hyper-V de destino, de forma otimizada.
- Se não configurar o mapeamento da rede, os VMs de réplica não serão ligados a uma rede VM após a falha.

Prepare o Gestor de Máquinas Virtuais da seguinte forma:

- Certifique-se de que dispõe de [redes lógicas de Gestor](https://docs.microsoft.com/system-center/vmm/network-logical) de Máquinas Virtuais na fonte e visa servidores de Gestor de Máquinas Virtuais:
  - A rede lógica no servidor de origem deve ser associada à cloud de origem na qual os anfitriões de Hyper-V estão localizados.
  - A rede lógica no servidor de destino deve ser associada à cloud de destino.
- Certifique-se de que tem [redes VM](https://docs.microsoft.com/system-center/vmm/network-virtual) na fonte e direciona os servidores do Gestor de Máquinas Virtuais. As redes de VMs devem ser ligadas à rede lógica em cada localização.
- Ligue as VMs nos anfitriões de Hyper-V de origem à rede de VMs de origem.

## <a name="prepare-for-powershell"></a>Prepare-se para powerShell

Certifique-se de que tem o Azure PowerShell pronto para ir:

- Se já utilizar o PowerShell, atualize para a versão 0.8.10 ou mais tarde. [Saiba mais](/powershell/azureps-cmdlets-docs) sobre como configurar a PowerShell.
- Depois de configurar e configurar o PowerShell, reveja os [cmdlets](/powershell/azure/overview)de serviço .
- Para saber mais sobre como utilizar valores de parâmetros, inputs e saídas no PowerShell, leia o guia [Get started.](/powershell/azure/get-started-azureps)

## <a name="set-up-a-subscription"></a>Configurar uma subscrição

1. Da PowerShell, inscreva-se na sua conta Azure.

   ```azurepowershell
   $UserName = "<user@live.com>"
   $Password = "<password>"
   $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
   $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
   Connect-AzAccount #-Credential $Cred
   ```

1. Recupere uma lista das suas subscrições, com as iDs de subscrição. Tenha em anotaaaaa a identificação da subscrição na qual pretende criar o cofre dos Serviços de Recuperação.

   ```azurepowershell
   Get-AzSubscription
   ```

1. Detete a assinatura para o cofre.

   ```azurepowershell
   Set-AzContext –SubscriptionID <subscriptionId>
   ```

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

1. Crie um grupo de recursos do Gestor de Recursos Azure se não tiver um.

   ```azurepowershell
   New-AzResourceGroup -Name #ResourceGroupName -Location #location
   ```

1. Crie um novo cofre dos Serviços de Recuperação. Guarde o objeto do cofre numa variável para ser usado mais tarde.

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   ```

   Pode recuperar o objeto do cofre depois `Get-AzRecoveryServicesVault` de o criar utilizando o cmdlet.

## <a name="set-the-vault-context"></a>Definir o contexto do cofre

1. Recuperar um cofre existente.

   ```azurepowershell
   $vault = Get-AzRecoveryServicesVault -Name #vaultname
   ```

1. Desconte o contexto do cofre.

   ```azurepowershell
   Set-AzRecoveryServicesAsrVaultContext -Vault $vault
   ```

## <a name="install-the-site-recovery-provider"></a>Instale o fornecedor de recuperação do site

1. Na máquina Virtual Machine Manager, crie um diretório executando o seguinte comando:

   ```azurepowershell
   New-Item -Path C:\ASR -ItemType Directory
   ```

1. Extraios os ficheiros utilizando o ficheiro de configuração do fornecedor descarregado.

   ```console
   pushd C:\ASR\
   .\AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Instale o fornecedor e aguarde a instalação para terminar.

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

1. Crie uma política de replicação, neste caso para hyper-V 2012 R2, da seguinte forma:

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
   > A nuvem de Gestor de Máquinas Virtuais pode conter anfitriões Hyper-V que executam diferentes versões do Windows Server, mas a política de replicação é para uma versão específica de um sistema operativo. Se tiver diferentes anfitriões a funcionar em diferentes sistemas operativos, crie políticas de replicação separadas para cada sistema. Por exemplo, se tiver cinco anfitriões a funcionar no Windows Server 2012 e três anfitriões a funcionar no Windows Server 2012 R2, crie duas políticas de replicação. Cria-se um para cada tipo de sistema operativo.

1. Recupere o recipiente de proteção primária (nuvem principal do Gestor de Máquinas Virtuais) e o recipiente de proteção de recuperação (nuvem de gestor de máquinas virtuais de recuperação).

   ```azurepowershell
   $PrimaryCloud = "testprimarycloud"
   $primaryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloud;

   $RecoveryCloud = "testrecoverycloud"
   $recoveryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $RecoveryCloud;
   ```

1. Recupere a política de replicação que criou usando o nome amigável.

   ```azurepowershell
   $policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $policyname
   ```

1. Inicie a associação do recipiente de proteção (nuvem de Gestor de Máquinavirtual) com a política de replicação.

   ```azurepowershell
   $associationJob  = New-AzRecoveryServicesAsrProtectionContainerMapping -Policy $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
   ```

1. Espere que o trabalho da associação política termine. Para verificar se o trabalho está concluído, utilize o seguinte corte PowerShell:

   ```azurepowershell
   $job = Get-AzRecoveryServicesAsrJob -Job $associationJob

   if($job -eq $null -or $job.StateDescription -ne "Completed")
   {
     $isJobLeftForProcessing = $true;
   }
   ```

1. Depois de o trabalho terminar o processamento, executar o seguinte comando:

   ```azurepowershell
   if($isJobLeftForProcessing)
   {
     Start-Sleep -Seconds 60
   }
   While($isJobLeftForProcessing)
   ```

Para verificar a conclusão da operação, siga os passos da [atividade do Monitor](#monitor-activity).

##  <a name="configure-network-mapping"></a>Configurar o mapeamento da rede

1. Use este comando para recuperar servidores para o cofre atual. O comando armazena os `$Servers` servidores de Recuperação do Site na variável matriz.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesAsrFabric
   ```

1. Execute este comando para recuperar as redes para o servidor de gestor de máquinas virtuais de origem e para o servidor de Gestor de Máquinas Virtuais alvo.

   ```azurepowershell
   $PrimaryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[0]

   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[1]
   ```

   > [!NOTE]
   > O servidor Source Virtual Machine Manager pode ser o primeiro ou o segundo na matriz do servidor. Verifique os nomes do servidor do Gestor de Máquinas Virtuais e recupere as redes de forma adequada.

1. Este cmdlet cria um mapeamento entre a rede primária e a rede de recuperação. Especifica a rede primária como o `$PrimaryNetworks`primeiro elemento de . Especifica a rede de recuperação `$RecoveryNetworks`como o primeiro elemento de .

   ```azurepowershell
   New-AzRecoveryServicesAsrNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]
   ```

## <a name="enable-protection-for-vms"></a>Ativar a proteção para VMs

Depois de os servidores, nuvens e redes estarem configurados corretamente, permitir a proteção para VMs na nuvem.

1. Para permitir a proteção, dirija o seguinte comando para recuperar o recipiente de proteção:

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
> Se desejar replicar discos geridos pela CMK em Azure, faça os seguintes passos utilizando a Az PowerShell 3.3.0 em diante:
>
> 1. Ativar a falha nos discos geridos atualizando propriedades VM
> 1. Utilize `Get-AzRecoveryServicesAsrReplicationProtectedItem` o cmdlet para obter o ID do disco para cada disco do item protegido
> 1. Crie um objeto `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` de dicionário usando cmdlet para conter o mapeamento do ID do disco para o conjunto de encriptação do disco. Estes conjuntos de encriptação de discos devem ser pré-criados por si na região alvo.
> 1. Atualize as propriedades `Set-AzRecoveryServicesAsrReplicationProtectedItem` VM utilizando cmdlet, passando o objeto do dicionário no parâmetro **DiskIdToDiskEncryptionSetSetMap.**

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Para testar a sua implementação, ecorra uma falha de teste para uma única máquina virtual. Também pode criar um plano de recuperação que contenha vários VMs e executar uma falha de teste para o plano. A ativação pós-falha de teste simula o mecanismo de ativação pós-falha e recuperação numa rede isolada.

1. Recupere o VM no qual os VMs falharão.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesASRFabric
   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[1]
   ```

1. Faça uma falha no teste.

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

Para verificar a conclusão da operação, siga os passos da [atividade do Monitor](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Executar falhas planeadas e não planeadas

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

1. Faça uma falha não planeada.

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

Utilize os seguintes comandos para monitorizar a atividade de failover. Espere que o processamento termine entre os trabalhos.

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

[Saiba mais](/powershell/module/az.recoveryservices) sobre a Recuperação do Site com os cmdlets powerShell do Gestor de Recursos.

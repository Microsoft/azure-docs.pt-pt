---
title: Configurar a recuperação de desastre do Hyper-V (com VMM) em um site secundário com Azure Site Recovery/PowerShell
description: Descreve como configurar a recuperação de desastre de VMs do Hyper-V em nuvens do VMM para um site secundário do VMM usando o Azure Site Recovery e o PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: deef7bfdbc28d744cb81da59d3ffc13a1abee54d
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048605"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Configurar a recuperação de desastre de VMs do Hyper-V para um site secundário usando o PowerShell (Gerenciador de recursos)

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
- Se você não configurar o mapeamento de rede, as VMs de réplica não serão conectadas a uma rede VM após o failover.

Prepare Virtual Machine Manager da seguinte maneira:

- Certifique-se de que dispõe de [redes lógicas de Gestor](https://docs.microsoft.com/system-center/vmm/network-logical) de Máquinas Virtuais na fonte e visa servidores de Gestor de Máquinas Virtuais:
  - A rede lógica no servidor de origem deve ser associada à cloud de origem na qual os anfitriões de Hyper-V estão localizados.
  - A rede lógica no servidor de destino deve ser associada à cloud de destino.
- Certifique-se de que tem [redes VM](https://docs.microsoft.com/system-center/vmm/network-virtual) na fonte e direciona os servidores do Gestor de Máquinas Virtuais. As redes de VMs devem ser ligadas à rede lógica em cada localização.
- Ligue as VMs nos anfitriões de Hyper-V de origem à rede de VMs de origem.

## <a name="prepare-for-powershell"></a>Preparar para o PowerShell

Verifique se você tem Azure PowerShell pronto para começar:

- Se você já usa o PowerShell, atualize para a versão 0.8.10 ou posterior. [Saiba mais](/powershell/azureps-cmdlets-docs) sobre como configurar a PowerShell.
- Depois de configurar e configurar o PowerShell, reveja os [cmdlets](/powershell/azure/overview)de serviço .
- Para saber mais sobre como utilizar valores de parâmetros, inputs e saídas no PowerShell, leia o guia [Get started.](/powershell/azure/get-started-azureps)

## <a name="set-up-a-subscription"></a>Configurar uma assinatura

1. No PowerShell, entre na sua conta do Azure.

   ```azurepowershell
   $UserName = "<user@live.com>"
   $Password = "<password>"
   $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
   $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
   Connect-AzAccount #-Credential $Cred
   ```

1. Recupere uma lista de suas assinaturas com as IDs de assinatura. Observe a ID da assinatura na qual você deseja criar o cofre dos serviços de recuperação.

   ```azurepowershell
   Get-AzSubscription
   ```

1. Defina a assinatura para o cofre.

   ```azurepowershell
   Set-AzContext –SubscriptionID <subscriptionId>
   ```

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

1. Crie um grupo de recursos Azure Resource Manager se você não tiver um.

   ```azurepowershell
   New-AzResourceGroup -Name #ResourceGroupName -Location #location
   ```

1. Crie um novo cofre de serviços de recuperação. Salve o objeto de cofre em uma variável a ser usada posteriormente.

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   ```

   Pode recuperar o objeto do cofre depois de o criar utilizando o `Get-AzRecoveryServicesVault` cmdlet.

## <a name="set-the-vault-context"></a>Definir o contexto do cofre

1. Recuperar um cofre existente.

   ```azurepowershell
   $vault = Get-AzRecoveryServicesVault -Name #vaultname
   ```

1. Defina o contexto do cofre.

   ```azurepowershell
   Set-AzRecoveryServicesAsrVaultContext -Vault $vault
   ```

## <a name="install-the-site-recovery-provider"></a>Instalar o provedor de Site Recovery

1. Na máquina Virtual Machine Manager, crie um diretório executando o seguinte comando:

   ```azurepowershell
   New-Item -Path C:\ASR -ItemType Directory
   ```

1. Extraia os arquivos usando o arquivo de instalação do provedor baixado.

   ```console
   pushd C:\ASR\
   .\AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Instale o provedor e aguarde a conclusão da instalação.

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

1. Registre o servidor no cofre.

   ```console
   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
   ```

## <a name="create-and-associate-a-replication-policy"></a>Criar e associar uma política de replicação

1. Crie uma política de replicação, neste caso, para o Hyper-V 2012 R2, da seguinte maneira:

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
   > O Virtual Machine Manager nuvem pode conter hosts Hyper-V executando versões diferentes do Windows Server, mas a política de replicação é para uma versão específica de um sistema operacional. Se você tiver hosts diferentes em execução em sistemas operacionais diferentes, crie políticas de replicação separadas para cada sistema. Por exemplo, se você tiver cinco hosts em execução no Windows Server 2012 e três hosts em execução no Windows Server 2012 R2, crie duas políticas de replicação. Você cria um para cada tipo de sistema operacional.

1. Recupere o contêiner de proteção principal (primário Virtual Machine Manager Cloud) e o contêiner de proteção de recuperação (recuperação Virtual Machine Manager Cloud).

   ```azurepowershell
   $PrimaryCloud = "testprimarycloud"
   $primaryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloud;

   $RecoveryCloud = "testrecoverycloud"
   $recoveryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $RecoveryCloud;
   ```

1. Recupere a política de replicação que você criou usando o nome amigável.

   ```azurepowershell
   $policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $policyname
   ```

1. Inicie a associação do contêiner de proteção (Virtual Machine Manager nuvem) com a política de replicação.

   ```azurepowershell
   $associationJob  = New-AzRecoveryServicesAsrProtectionContainerMapping -Policy $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
   ```

1. Aguarde a conclusão do trabalho de associação de política. Para verificar se o trabalho foi concluído, use o seguinte trecho do PowerShell:

   ```azurepowershell
   $job = Get-AzRecoveryServicesAsrJob -Job $associationJob

   if($job -eq $null -or $job.StateDescription -ne "Completed")
   {
     $isJobLeftForProcessing = $true;
   }
   ```

1. Depois que o trabalho concluir o processamento, execute o seguinte comando:

   ```azurepowershell
   if($isJobLeftForProcessing)
   {
     Start-Sleep -Seconds 60
   }
   While($isJobLeftForProcessing)
   ```

Para verificar a conclusão da operação, siga os passos da [atividade do Monitor](#monitor-activity).

##  <a name="configure-network-mapping"></a>Configurar o mapeamento da rede

1. Use este comando para recuperar servidores para o cofre atual. O comando armazena os servidores de Recuperação do Site na variável de matriz `$Servers`.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesAsrFabric
   ```

1. Execute este comando para recuperar as redes para o servidor de Virtual Machine Manager de origem e o servidor de Virtual Machine Manager de destino.

   ```azurepowershell
   $PrimaryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[0]

   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[1]
   ```

   > [!NOTE]
   > O servidor de Virtual Machine Manager de origem pode ser o primeiro ou o segundo na matriz de servidor. Verifique os nomes do servidor Virtual Machine Manager e recupere as redes adequadamente.

1. Esse cmdlet cria um mapeamento entre a rede principal e a rede de recuperação. Especifica a rede primária como o primeiro elemento de `$PrimaryNetworks`. Especifica a rede de recuperação como o primeiro elemento de `$RecoveryNetworks`.

   ```azurepowershell
   New-AzRecoveryServicesAsrNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]
   ```

## <a name="enable-protection-for-vms"></a>Habilitar a proteção para VMs

Depois que os servidores, as nuvens e as redes estiverem configurados corretamente, habilite a proteção para VMs na nuvem.

1. Para habilitar a proteção, execute o seguinte comando para recuperar o contêiner de proteção:

   ```azurepowershell
   $PrimaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloudName
   ```

1. Obtenha a entidade de proteção (VM), da seguinte maneira:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
   ```

1. Habilite a replicação para a VM.

   ```azurepowershell
   $jobResult = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $protectionentity -ProtectionContainerMapping $policy -VmmToVmm
   ```

> [!NOTE]
> Se você quiser replicar em discos gerenciados habilitados para o CMK no Azure, execute as seguintes etapas usando AZ PowerShell 3.3.0 em diante:
>
> 1. Habilitar o failover para discos gerenciados atualizando as propriedades da VM
> 1. Utilize o `Get-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet para obter o ID do disco para cada disco do item protegido
> 1. Crie um objeto dicionário usando `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` cmdlet para conter o mapeamento do ID do disco para o conjunto de encriptação do disco. Esses conjuntos de criptografia de disco devem ser criados previamente por você na região de destino.
> 1. Atualize as propriedades VM utilizando `Set-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet, passando o objeto do dicionário no parâmetro **DiskIdToDiskEncryptionSetSetMap.**

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Para testar sua implantação, execute um failover de teste para uma única máquina virtual. Você também pode criar um plano de recuperação que contenha várias VMs e executar um failover de teste para o plano. A ativação pós-falha de teste simula o mecanismo de ativação pós-falha e recuperação numa rede isolada.

1. Recupere a VM na qual as VMs realizarão failover.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesASRFabric
   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[1]
   ```

1. Execute um failover de teste.

   Para uma única VM:

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

## <a name="run-planned-and-unplanned-failovers"></a>Executar failovers planejados e não planejados

1. Execute um failover planejado.

   Para uma única VM:

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

1. Execute um failover não planejado.

   Para uma única VM:

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

## <a name="monitor-activity"></a>Monitorar atividade

Use os comandos a seguir para monitorar a atividade de failover. Aguarde a conclusão do processamento entre os trabalhos.

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

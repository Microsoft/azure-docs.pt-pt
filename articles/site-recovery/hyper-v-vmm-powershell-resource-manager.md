---
title: Configurar a recuperação de desastre do Hyper-V (com VMM) em um site secundário com Azure Site Recovery/PowerShell
description: Descreve como configurar a recuperação de desastre de VMs do Hyper-V em nuvens do VMM para um site secundário do VMM usando o Azure Site Recovery e o PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 2fc66514bdf33611f9e6266d35a2d537fe3b9261
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084913"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Configurar a recuperação de desastre de VMs do Hyper-V para um site secundário usando o PowerShell (Gerenciador de recursos)

Este artigo mostra como automatizar as etapas para a replicação de VMs do Hyper-V em System Center Virtual Machine Manager nuvens para uma nuvem Virtual Machine Manager em um site local secundário usando [Azure site Recovery](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Reveja a [arquitetura e os componentes do cenário](hyper-v-vmm-architecture.md).
- Reveja os [requisitos de suporte](site-recovery-support-matrix-to-sec-site.md) de todos os componentes.
- Certifique-se de que Virtual Machine Manager servidores e hosts Hyper-V estejam em conformidade com [os requisitos de suporte](site-recovery-support-matrix-to-sec-site.md).
- Verifique se as VMs que você deseja replicar estão em conformidade com o [suporte ao computador replicado](site-recovery-support-matrix-to-sec-site.md).


## <a name="prepare-for-network-mapping"></a>Preparar o mapeamento da rede

O [mapeamento de rede](hyper-v-vmm-network-mapping.md) mapeia entre Virtual Machine Manager redes VM locais nas nuvens de origem e de destino. O mapeamento faz o seguinte:

- Liga as VMs às redes de VMs de destino adequadas após a ativação pós-falha. 
- Coloca as VMs de réplica nos servidores dos anfitriões de Hyper-V de destino, de forma otimizada. 
- Se você não configurar o mapeamento de rede, as VMs de réplica não serão conectadas a uma rede VM após o failover.

Prepare Virtual Machine Manager da seguinte maneira:

* Verifique se você tem [Virtual Machine Manager redes lógicas](https://docs.microsoft.com/system-center/vmm/network-logical) nos servidores de Virtual Machine Manager de origem e de destino:

    - A rede lógica no servidor de origem deve ser associada à cloud de origem na qual os anfitriões de Hyper-V estão localizados.
    - A rede lógica no servidor de destino deve ser associada à cloud de destino.
* Verifique se você tem [redes VM](https://docs.microsoft.com/system-center/vmm/network-virtual) nos servidores de Virtual Machine Manager de origem e de destino. As redes de VMs devem ser ligadas à rede lógica em cada localização.
* Ligue as VMs nos anfitriões de Hyper-V de origem à rede de VMs de origem. 

## <a name="prepare-for-powershell"></a>Preparar para o PowerShell

Verifique se você tem Azure PowerShell pronto para começar:

- Se você já usa o PowerShell, atualize para a versão 0.8.10 ou posterior. [Saiba mais](/powershell/azureps-cmdlets-docs) sobre como configurar o PowerShell.
- Depois de configurar e configurar o PowerShell, examine os [cmdlets de serviço](/powershell/azure/overview).
- Para saber mais sobre como usar valores de parâmetro, entradas e saídas no PowerShell, leia o guia de [introdução](/powershell/azure/get-started-azureps) .

## <a name="set-up-a-subscription"></a>Configurar uma assinatura
1. No PowerShell, entre na sua conta do Azure.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Connect-AzAccount #-Credential $Cred
2. Recupere uma lista de suas assinaturas com as IDs de assinatura. Observe a ID da assinatura na qual você deseja criar o cofre dos serviços de recuperação. 

        Get-AzSubscription
3. Defina a assinatura para o cofre.

        Set-AzContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação
1. Crie um grupo de recursos Azure Resource Manager se você não tiver um.

        New-AzResourceGroup -Name #ResourceGroupName -Location #location
2. Crie um novo cofre de serviços de recuperação. Salve o objeto de cofre em uma variável a ser usada posteriormente. 

        $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   
    Você pode recuperar o objeto de cofre depois de criá-lo usando o cmdlet Get-AzRecoveryServicesVault.

## <a name="set-the-vault-context"></a>Definir o contexto do cofre
1. Recuperar um cofre existente.

       $vault = Get-AzRecoveryServicesVault -Name #vaultname
2. Defina o contexto do cofre.

       Set-AzSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>Instalar o provedor de Site Recovery
1. Na máquina Virtual Machine Manager, crie um diretório executando o seguinte comando:

       New-Item c:\ASR -type directory
2. Extraia os arquivos usando o arquivo de instalação do provedor baixado.

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Instale o provedor e aguarde a conclusão da instalação.

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

4. Registre o servidor no cofre.

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>Criar e associar uma política de replicação
1. Crie uma política de replicação, neste caso, para o Hyper-V 2012 R2, da seguinte maneira:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > O Virtual Machine Manager nuvem pode conter hosts Hyper-V executando versões diferentes do Windows Server, mas a política de replicação é para uma versão específica de um sistema operacional. Se você tiver hosts diferentes em execução em sistemas operacionais diferentes, crie políticas de replicação separadas para cada sistema. Por exemplo, se você tiver cinco hosts em execução no Windows Server 2012 e três hosts em execução no Windows Server 2012 R2, crie duas políticas de replicação. Você cria um para cada tipo de sistema operacional.

2. Recupere o contêiner de proteção principal (primário Virtual Machine Manager Cloud) e o contêiner de proteção de recuperação (recuperação Virtual Machine Manager Cloud).

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. Recupere a política de replicação que você criou usando o nome amigável.

       $policy = Get-AzSiteRecoveryPolicy -FriendlyName $policyname
4. Inicie a associação do contêiner de proteção (Virtual Machine Manager nuvem) com a política de replicação.

       $associationJob  = Start-AzSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Aguarde a conclusão do trabalho de associação de política. Para verificar se o trabalho foi concluído, use o seguinte trecho do PowerShell:

       $job = Get-AzSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. Depois que o trabalho concluir o processamento, execute o seguinte comando:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Para verificar a conclusão da operação, siga as etapas em [monitorar atividade](#monitor-activity).

##  <a name="configure-network-mapping"></a>Configurar o mapeamento da rede
1. Use este comando para recuperar servidores para o cofre atual. O comando armazena os servidores de Site Recovery na variável de matriz $Servers.

        $Servers = Get-AzSiteRecoveryServer
2. Execute este comando para recuperar as redes para o servidor de Virtual Machine Manager de origem e o servidor de Virtual Machine Manager de destino.

        $PrimaryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > O servidor de Virtual Machine Manager de origem pode ser o primeiro ou o segundo na matriz de servidor. Verifique os nomes do servidor Virtual Machine Manager e recupere as redes adequadamente.


3. Esse cmdlet cria um mapeamento entre a rede principal e a rede de recuperação. Ele especifica a rede principal como o primeiro elemento de $PrimaryNetworks. Ele especifica a rede de recuperação como o primeiro elemento de $RecoveryNetworks.

        New-AzSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Habilitar a proteção para VMs
Depois que os servidores, as nuvens e as redes estiverem configurados corretamente, habilite a proteção para VMs na nuvem.

1. Para habilitar a proteção, execute o seguinte comando para recuperar o contêiner de proteção:

          $PrimaryProtectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Obtenha a entidade de proteção (VM), da seguinte maneira:

           $protectionEntity = Get-AzSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Habilite a replicação para a VM.

          $jobResult = Set-AzSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Para testar sua implantação, execute um failover de teste para uma única máquina virtual. Você também pode criar um plano de recuperação que contenha várias VMs e executar um failover de teste para o plano. A ativação pós-falha de teste simula o mecanismo de ativação pós-falha e recuperação numa rede isolada.

1. Recupere a VM na qual as VMs realizarão failover.

       $Servers = Get-AzSiteRecoveryServer
       $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

2. Execute um failover de teste.

   Para uma única VM:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   Para um plano de recuperação:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

Para verificar a conclusão da operação, siga as etapas em [monitorar atividade](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Executar failovers planejados e não planejados

1. Execute um failover planejado.

   Para uma única VM:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Para um plano de recuperação:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Execute um failover não planejado.

   Para uma única VM:
        
        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Para um plano de recuperação:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Monitorar atividade
Use os comandos a seguir para monitorar a atividade de failover. Aguarde a conclusão do processamento entre os trabalhos.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
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



## <a name="next-steps"></a>Passos seguintes

[Saiba mais](/powershell/module/az.recoveryservices) sobre site Recovery com os cmdlets do PowerShell do Gerenciador de recursos.

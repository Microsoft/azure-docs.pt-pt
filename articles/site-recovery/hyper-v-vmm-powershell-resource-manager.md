---
title: Configurar a recuperação após desastre de VMs de Hyper-V em clouds do VMM para um site secundário com o Azure Site Recovery e o PowerShell | Documentos da Microsoft
description: Descreve como configurar a recuperação após desastre de VMs de Hyper-V em nuvens VMM para um site VMM secundário com o Azure Site Recovery e o PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 78bd077b5491b093510b9c55bf7b5a42ee9cb578
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787603"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Configurar a recuperação após desastre de VMs de Hyper-V para um site secundário com o PowerShell (Resource Manager)

Este artigo mostra como automatizar os passos para a replicação de VMs de Hyper-V em clouds do System Center Virtual Machine Manager para uma nuvem do Virtual Machine Manager num secundário no local site usando [do Azure Site Recovery](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Reveja a [arquitetura e os componentes do cenário](hyper-v-vmm-architecture.md).
- Reveja os [requisitos de suporte](site-recovery-support-matrix-to-sec-site.md) de todos os componentes.
- Certifique-se de que servidores do Virtual Machine Manager e os anfitriões de Hyper-V estão em conformidade com [suportar requisitos](site-recovery-support-matrix-to-sec-site.md).
- Verifique se as VMs que pretende replicar cumprem [replicados suporte máquina](site-recovery-support-matrix-to-sec-site.md).


## <a name="prepare-for-network-mapping"></a>Preparar o mapeamento da rede

[Mapeamento da rede](hyper-v-vmm-network-mapping.md) mapeia entre as redes no local do Virtual Machine Manager VM em clouds de origem e de destino. O mapeamento faz o seguinte:

- Liga as VMs às redes de VMs de destino adequadas após a ativação pós-falha. 
- Coloca as VMs de réplica nos servidores dos anfitriões de Hyper-V de destino, de forma otimizada. 
- Se não configurar o mapeamento da rede, as VMs de réplica não será ligada a uma rede VM após a ativação pós-falha.

Prepare do Virtual Machine Manager da seguinte forma:

* Certifique-se de que tem [redes lógicas do Virtual Machine Manager](https://docs.microsoft.com/system-center/vmm/network-logical) nos servidores de Gestor de Máquina Virtual de origem e de destino:

    - A rede lógica no servidor de origem deve ser associada à cloud de origem na qual os anfitriões de Hyper-V estão localizados.
    - A rede lógica no servidor de destino deve ser associada à cloud de destino.
* Certifique-se de que tem [redes VM](https://docs.microsoft.com/system-center/vmm/network-virtual) nos servidores de Gestor de Máquina Virtual de origem e de destino. As redes de VMs devem ser ligadas à rede lógica em cada localização.
* Ligue as VMs nos anfitriões de Hyper-V de origem à rede de VMs de origem. 

## <a name="prepare-for-powershell"></a>Preparar para o PowerShell

Certifique-se de que tem o Azure PowerShell pronto para começar:

- Se já utilizar o PowerShell, atualize para versão 0.8.10 ou posterior. [Saiba mais](/powershell/azureps-cmdlets-docs) sobre como configurar o PowerShell.
- Depois de configurar e configurar o PowerShell, reveja os [cmdlets de serviço](/powershell/azure/overview).
- Para saber mais sobre como utilizar os valores de parâmetros, entradas e saídas no PowerShell, veja a [começar](/powershell/azure/get-started-azureps) guia.

## <a name="set-up-a-subscription"></a>Configurar uma subscrição
1. No PowerShell, inicie sessão na sua conta do Azure.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Connect-AzAccount #-Credential $Cred
2. Obter uma lista das suas subscrições, com os IDs de subscrição. Tenha em atenção o ID da subscrição na qual pretende criar o Cofre dos serviços de recuperação. 

        Get-AzSubscription
3. Defina a subscrição do cofre.

        Set-AzContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 
1. Se não tiver uma, crie um grupo de recursos do Azure Resource Manager.

        New-AzResourceGroup -Name #ResourceGroupName -Location #location
2. Crie um novo cofre de serviços de recuperação. Guarde o objeto do cofre numa variável a ser utilizado mais tarde. 

        $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   
    Depois de criar utilizando o cmdlet Get-AzRecoveryServicesVault, pode recuperar o objeto do cofre.

## <a name="set-the-vault-context"></a>Definir o contexto do Cofre
1. Obter um cofre existente.

       $vault = Get-AzRecoveryServicesVault -Name #vaultname
2. Defina o contexto do cofre.

       Set-AzSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>Instalar o fornecedor do Site Recovery
1. Na máquina do Virtual Machine Manager, crie um diretório ao executar o seguinte comando:

       New-Item c:\ASR -type directory
2. Extraia os ficheiros utilizando o ficheiro de configuração do fornecedor transferido.

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Instale o fornecedor e aguarde pela conclusão da instalação.

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

4. Registe o servidor no cofre.

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>Crie e associe uma política de replicação
1. Crie uma política de replicação para o Hyper-V R2 de 2012, da seguinte forma e de neste caso:

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
    > A cloud do Virtual Machine Manager pode conter os anfitriões de Hyper-V que executem versões diferentes do Windows Server, mas a política de replicação é para uma versão específica de um sistema operacional. Se tiver anfitriões diferentes em execução em sistemas operativos diferentes, crie políticas de replicação separados para cada sistema. Por exemplo, se tiver cinco anfitriões em execução no Windows Server 2012 e três anfitriões em execução no Windows Server 2012 R2, crie duas políticas de replicação. Criar um para cada tipo de sistema operativo.

2. Obter o contentor de proteção principal (principal do Virtual Machine Manager cloud) e o contentor de proteção de recuperação (do Virtual Machine Manager cloud de recuperação).

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. Obter a política de replicação que criou com o nome amigável.

       $policy = Get-AzSiteRecoveryPolicy -FriendlyName $policyname
4. Começar a associação do contentor de proteção (do Virtual Machine Manager cloud) com a política de replicação.

       $associationJob  = Start-AzSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Aguarde que a tarefa de associação de política concluir. Para verificar se a tarefa estiver concluída, utilize o seguinte fragmento do PowerShell:

       $job = Get-AzSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. Após a conclusão da tarefa de processamento, execute o seguinte comando:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Para verificar a conclusão da operação, siga os passos em [monitorizar a atividade](#monitor-activity).

##  <a name="configure-network-mapping"></a>Configurar o mapeamento da rede
1. Utilize este comando para obter os servidores do cofre atual. O comando armazena os servidores do Site Recovery na variável de matriz $Servers.

        $Servers = Get-AzSiteRecoveryServer
2. Execute este comando para obter as redes para o servidor de Gestor de Máquina Virtual de origem e o servidor do Virtual Machine Manager de destino.

        $PrimaryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > O servidor do Virtual Machine Manager de origem pode ser o primeiro ou segundo da matriz de servidor. Verificar nomes de servidor do Virtual Machine Manager e obter as redes de forma adequada.


3. Este cmdlet cria um mapeamento entre a rede principal e a rede de recuperação. Especifica a rede principal como o primeiro elemento de $PrimaryNetworks. Especifica a rede de recuperação como o primeiro elemento de $RecoveryNetworks.

        New-AzSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Ativar a proteção para VMs
Depois da servidores, nuvens e as redes estão configuradas corretamente, ative a proteção para as VMs na cloud.

1. Para ativar a proteção, execute o seguinte comando para obter o contentor de proteção:

          $PrimaryProtectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Obter a entidade de proteção (VM), da seguinte forma:

           $protectionEntity = Get-AzSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Ative a replicação para a VM.

          $jobResult = Set-AzSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Para testar a implementação, execute uma ativação pós-falha de teste para uma única máquina virtual. Também pode criar um plano de recuperação que contém várias VMs e executar uma ativação pós-falha para o plano. A ativação pós-falha de teste simula o mecanismo de ativação pós-falha e recuperação numa rede isolada.

1. Obter a VM na qual as VMs irão efetuar a ativação pós-falha.

       $Servers = Get-AzSiteRecoveryServer
       $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

2. Execute uma ativação pós-falha de teste.

   Para uma única VM:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   Para um plano de recuperação:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

Para verificar a conclusão da operação, siga os passos em [monitorizar a atividade](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Executar as ativações pós-falha planeadas e não planeadas

1. Execute uma ativação pós-falha planeada.

   Para uma única VM:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Para um plano de recuperação:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Execute uma ativação pós-falha não planeada.

   Para uma única VM:
        
        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Para um plano de recuperação:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Monitorizar a atividade
Utilize os seguintes comandos para monitorizar a atividade de ativação pós-falha. Aguarde que o processamento concluir a entre tarefas.

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



## <a name="next-steps"></a>Passos Seguintes

[Saiba mais](/powershell/module/az.recoveryservices) sobre o Site Recovery com os cmdlets do PowerShell do Resource Manager.

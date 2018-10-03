---
title: Replicar VMs de Hyper-V com o PowerShell e o Azure Resource Manager | Documentos da Microsoft
description: Automatize a replicação de VMs de Hyper-V para o Azure com o Azure Site Recovery com o PowerShell e do Azure Resource Manager.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: sutalasi
ms.openlocfilehash: b71c381c3ebc2b36c36b862c00de02144f94bd0f
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043658"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Configurar a recuperação após desastre para o Azure das VMs de Hyper-V com o PowerShell e do Azure Resource Manager

[O Azure Site Recovery](site-recovery-overview.md) contribui para a continuidade do negócio e a estratégia de recuperação (BCDR) após desastre através da orquestração da replicação, ativação pós-falha e recuperação de máquinas virtuais do Azure (VMs) e as VMs e servidores físicos no local.

Este artigo descreve como utilizar o Windows PowerShell, juntamente com o Azure Resource Manager, para replicar VMs Hyper-V para o Azure. No exemplo utilizado neste artigo mostra-lhe como replicar uma única VM em execução num anfitrião Hyper-V, para o Azure.

## <a name="azure-powershell"></a>Azure PowerShell

O Azure PowerShell disponibiliza os cmdlets para gerir o Azure com o Windows PowerShell. Cmdlets do PowerShell da recuperação de site, disponíveis com o Azure PowerShell para o Azure Resource Manager, ajudar a proteger e recuperar os seus servidores no Azure.

Não precisa de ser um especialista de PowerShell para utilizar este artigo, mas é necessário compreender os conceitos básicos, como módulos, cmdlets e sessões. Leia [introdução ao Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx), e [utilizar o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md).

> [!NOTE]
> Parceiros da Microsoft no programa de fornecedor de soluções Cloud (CSP) podem configurar e gerir a proteção de servidores de cliente para suas respetivas subscrições de CSP (subscrições de inquilino).
>
>

## <a name="before-you-start"></a>Antes de começar
Certifique-se de que tem estes pré-requisitos necessários:

* R [Microsoft Azure](https://azure.microsoft.com/) conta. Pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). Além disso, pode ler sobre [preços do Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
* Azure PowerShell 1.0. Para obter informações sobre esta versão e como instalá-lo, consulte [do Azure PowerShell 1.0.](https://azure.microsoft.com/)
* O [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) e [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) módulos. Pode obter as versões mais recentes destes módulos do [galeria do PowerShell](https://www.powershellgallery.com/)

Além disso, o exemplo específico, descrito neste artigo tem os seguintes pré-requisitos:

* Anfitrião Hyper-V com o Windows Server 2012 R2 ou Microsoft Hyper-V Server 2012 R2 que contém uma ou mais VMs. Servidores de Hyper-V devem estar ligados à Internet, diretamente ou através de um proxy.
* As VMs que pretende replicar devem estar em conformidade com [estes pré-requisitos](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Passo 1: Iniciar sessão na sua conta do Azure

1. Abra uma consola do PowerShell e execute este comando para iniciar sessão na sua conta do Azure. O cmdlet exibirá uma página da web pede-lhe as credenciais da conta: **Connect-AzureRmAccount**.
    - Em alternativa, pode incluir as credenciais da conta como um parâmetro no **Connect-AzureRmAccount** cmdlet, utilizando o **-Credential** parâmetro.
    - Se estiver a trabalhar em nome de um inquilino de parceiro CSP, especifica o cliente como um inquilino, utilizando o respetivo nome de domínio primário tenantID ou o inquilino. Por exemplo: **Connect-AzureRmAccount-inquilino "fabrikam.com"**
2. Associe a subscrição que pretende utilizar com a conta, uma vez que uma conta pode ter várias subscrições:

    `Select-AzureRmSubscription -SubscriptionName $SubscriptionName`

3. Certifique-se de que a sua subscrição está registada para utilizar os fornecedores do Azure para serviços de recuperação e a recuperação de Site, utilizando estes comandos:

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

4. Certifique-se de que no resultado do comando, o **RegistrationState** está definida como **registado**, pode avançar para o passo 2. Caso contrário, deve registar o fornecedor em falta na sua subscrição, ao executar estes comandos:

    `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery` `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

5. Certifique-se de que os fornecedores registado com êxito, com os comandos seguintes:

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.

## <a name="step-2-set-up-the-vault"></a>Passo 2: Configurar o Cofre

1. Criar um grupo de recursos do Azure Resource Manager no qual pretende criar o cofre ou utilize um grupo de recursos existente. Crie um novo grupo de recursos da seguinte forma. A variável $ResourceGroupName contém o nome do grupo de recursos que pretende criar e a variável $Geo contém a região do Azure na qual pretende criar o grupo de recursos (por exemplo, "sul do Brasil").

    `New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo` 

2. Para obter uma lista de grupos de recursos na sua subscrição, execute o **Get-AzureRmResourceGroup** cmdlet.
2. Crie um novo cofre de serviços de recuperação do Azure da seguinte forma:

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    Pode recuperar uma lista de cofres existentes com o **Get-AzureRmRecoveryServicesVault** cmdlet.


## <a name="step-3-set-the-recovery-services-vault-context"></a>Passo 3: Definir o contexto do cofre dos serviços de recuperação

Defina o contexto do cofre da seguinte forma:

`Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault`

## <a name="step-4-create-a-hyper-v-site"></a>Passo 4: Criar um site de Hyper-V

1. Crie um novo site de Hyper-V da seguinte forma:

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

2. Este cmdlet inicia uma tarefa de recuperação de sites para criar o site e retorna um objeto de tarefa de recuperação de sites. Aguarde a tarefa seja concluída e certifique-se de que a tarefa foi concluída com êxito.
3. Utilize o **cmdlet Get-AzureRmSiteRecoveryJob**, para recuperar o objeto de tarefa e verificar o estado atual da tarefa.
4. Gerar e transferir uma chave de registo para o site, da seguinte forma:

    ```
    $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path
    ```

5. Copie a chave de transferido para o anfitrião de Hyper-V. Precisa da chave para registar o anfitrião de Hyper-V para o site.

## <a name="step-5-install-the-provider-and-agent"></a>Passo 5: Instalar o fornecedor e agente

1. Transferir o instalador para a versão mais recente do fornecedor em [Microsoft](https://aka.ms/downloaddra).
2. Execute o instalador no anfitrião theHyper-V.
3. No final da instalação, continue para o passo de registo.
4. Quando lhe for pedido, forneça a chave de transferido e concluir o registo de anfitrião do Hyper-V.
5. Certifique-se de que o anfitrião de Hyper-V está registrado para o site da seguinte forma:

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy"></a>Passo 6: Criar uma política de replicação

Antes de começar, tenha em atenção que a conta de armazenamento especificada deve ser na mesma região do Azure que o Cofre e deve ter a georreplicação ativada.

1. Crie uma política de replicação da seguinte forma:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

2. Verifique a tarefa retornada para se certificar de que a criação de política de replicação for bem-sucedida.

3. Obter o contentor de proteção que corresponde ao site, da seguinte forma:

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3. Associe o contentor de proteção com a política de replicação, da seguinte forma:

     $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName   $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

4. Aguarde que a tarefa de associação seja concluída com êxito.

## <a name="step-7-enable-vm-protection"></a>Passo 7: Ativar a proteção de VM

1. Obter a entidade de proteção que corresponde à VM que pretende proteger, da seguinte forma:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. Protege a VM. Se a VM estiver a proteger tiver mais do que um disco ligado à mesma, especifique o disco do sistema operativo ao utilizar o *OSDiskName* parâmetro.

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

3. Aguarde que as VMs a atingir um estado protegido após a replicação inicial. Esta ação pode demorar algum tempo, dependendo de fatores como a quantidade de dados a serem replicados e a largura de banda disponível a montante para o Azure. Quando um estado protegido estiver em vigor, o estado da tarefa e StateDescription são atualizados da seguinte forma: 

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. Atualizar propriedades de recuperação (por exemplo, o tamanho da função VM,) e a rede do Azure ao qual pretende anexar o NIC da VM após a ativação pós-falha.

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob

        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>Passo 8: Executar uma ativação pós-falha de teste
1. Execute uma ativação pós-falha de teste da seguinte forma:

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Certifique-se de que o teste de VM é criada no Azure. A tarefa de ativação pós-falha de teste está suspenso depois de criar a VM de teste no Azure.
3. Para limpar e concluir a ativação pós-falha de teste, execute:

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais](https://docs.microsoft.com/powershell/module/azurerm.siterecovery) sobre o Azure Site Recovery com os cmdlets do PowerShell do Azure Resource Manager.

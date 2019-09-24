---
title: Configurar a recuperação de desastres para o Azure para VMs do Hyper-V usando o PowerShell e o Azure Resource Manager | Microsoft Docs
description: Automatize a recuperação de desastre de VMs do Hyper-V no Azure com o serviço de Azure Site Recovery usando o PowerShell e o Azure Resource Manager.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 06/18/2019
ms.author: sutalasi
ms.openlocfilehash: 1779a33e4ac021c1807ce10dc224e0b8c8c53ebb
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200530"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Configurar a recuperação de desastres para o Azure para VMs do Hyper-V usando o PowerShell e Azure Resource Manager

[Azure site Recovery](site-recovery-overview.md) contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) ao orquestrar a replicação, o failover e a recuperação de VMs (máquinas virtuais) do Azure e de servidores físicos e VMs locais.

Este artigo descreve como usar o Windows PowerShell, junto com o Azure Resource Manager, para replicar VMs do Hyper-V para o Azure. O exemplo usado neste artigo mostra como replicar uma única VM em execução em um host Hyper-V, para o Azure.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell fornece cmdlets para gerenciar o Azure usando o Windows PowerShell. Site Recovery cmdlets do PowerShell, disponíveis com Azure PowerShell para Azure Resource Manager, ajudam você a proteger e recuperar seus servidores no Azure.

Você não precisa ser um especialista do PowerShell para usar este artigo, mas precisa entender os conceitos básicos, como módulos, cmdlets e sessões. Leia [introdução ao Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)e [usando Azure PowerShell com Azure Resource Manager](../powershell-azure-resource-manager.md).

> [!NOTE]
> Os parceiros da Microsoft no programa CSP (provedor de soluções de nuvem) podem configurar e gerenciar a proteção de servidores de clientes para suas respectivas assinaturas de CSP (assinaturas de locatário).
>
>

## <a name="before-you-start"></a>Antes de começar
Verifique se você tem estes pré-requisitos em vigor:

* Uma conta de [Microsoft Azure](https://azure.microsoft.com/) . Pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). Além disso, você pode ler sobre os [preços do Azure site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
* Azure PowerShell. Para obter informações sobre esta versão e como instalá-la, consulte [install Azure PowerShell](/powershell/azure/install-az-ps).

Além disso, o exemplo específico descrito neste artigo tem os seguintes pré-requisitos:

* Um host Hyper-V executando o Windows Server 2012 R2 ou Microsoft Hyper-V Server 2012 R2 contendo uma ou mais VMs. Os servidores Hyper-V devem estar conectados à Internet, seja diretamente ou por meio de um proxy.
* As VMs que você deseja replicar devem estar em conformidade com [esses pré-requisitos](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Passo 1: Inicie sessão na sua conta do Azure

1. Abra um console do PowerShell e execute este comando para entrar em sua conta do Azure. O cmdlet abre uma página da Web que solicita suas credenciais de conta: **Connect-AzAccount**.
    - Como alternativa, você pode incluir suas credenciais de conta como um parâmetro no cmdlet **Connect-AzAccount** , usando o parâmetro **-Credential** .
    - Se você for um parceiro CSP trabalhando em nome de um locatário, especifique o cliente como um locatário, usando seu nome de domínio de locatárioid ou de locatário primário. Por exemplo: **Connect-AzAccount -Tenant "fabrikam.com"**
2. Associe a assinatura que você deseja usar com a conta, uma vez que uma conta pode ter várias assinaturas:

    `Select-AzSubscription -SubscriptionName $SubscriptionName`

3. Verifique se sua assinatura está registrada para usar os provedores do Azure para serviços de recuperação e Site Recovery, usando estes comandos:

    `Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`

4. Verifique se, na saída do comando, o **registrostate** está definido como **registrado**, você pode prosseguir para a etapa 2. Caso contrário, você deve registrar o provedor ausente em sua assinatura executando estes comandos:

    `Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

5. Verifique se os provedores foram registrados com êxito usando os seguintes comandos:

    `Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`

## <a name="step-2-set-up-the-vault"></a>Passo 2: Configurar o cofre

1. Crie um grupo de recursos Azure Resource Manager no qual criar o cofre ou use um grupo de recursos existente. Crie um novo grupo de recursos da seguinte maneira. A variável $ResourceGroupName contém o nome do grupo de recursos que você deseja criar e a variável $Geo contém a região do Azure na qual o grupo de recursos será criado (por exemplo, "sul do Brasil").

    `New-AzResourceGroup -Name $ResourceGroupName -Location $Geo`

2. Para obter uma lista de grupos de recursos em sua assinatura, execute o cmdlet **Get-AzResourceGroup** .
2. Crie um novo cofre dos serviços de recuperação do Azure da seguinte maneira:

        $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    Você pode recuperar uma lista de cofres existentes com o cmdlet **Get-AzRecoveryServicesVault** .


## <a name="step-3-set-the-recovery-services-vault-context"></a>Passo 3: Definir o contexto do cofre dos serviços de recuperação

Defina o contexto do cofre da seguinte maneira:

`Set-AsrVaultSettings -Vault $vault`

## <a name="step-4-create-a-hyper-v-site"></a>Passo 4: Criar um site do Hyper-V

1. Crie um novo site do Hyper-V da seguinte maneira:

        $sitename = "MySite"                #Specify site friendly name
        New-AsrFabric -Type HyperVSite -Name $sitename

2. Esse cmdlet inicia um trabalho Site Recovery para criar o site e retorna um objeto de trabalho Site Recovery. Aguarde a conclusão do trabalho e verifique se o trabalho foi concluído com êxito.
3. Use o **cmdlet Get-AsrJob**para recuperar o objeto de trabalho e verificar o status atual do trabalho.
4. Gere e Baixe uma chave de registro para o site, da seguinte maneira:

    ```
    $SiteIdentifier = Get-AsrFabric -Name $sitename | Select -ExpandProperty SiteIdentifier
    $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
    ```

5. Copie a chave baixada para o host do Hyper-V. Você precisa da chave para registrar o host Hyper-V no site.

## <a name="step-5-install-the-provider-and-agent"></a>Passo 5: Instalar o provedor e o agente

1. Baixe o instalador para obter a versão mais recente do provedor da [Microsoft](https://aka.ms/downloaddra).
2. Execute o instalador no host do Hyper-V.
3. No final da instalação, continue na etapa de registro.
4. Quando solicitado, forneça a chave baixada e conclua o registro do host Hyper-V.
5. Verifique se o host Hyper-V está registrado no site da seguinte maneira:

        $server =  Get-AsrFabric -Name $siteName | Get-AsrServicesProvider -FriendlyName $server-friendlyname

Se você estiver executando um servidor de núcleo do Hyper-V, baixe o arquivo de instalação e siga estas etapas:
1. Extraia os arquivos de AzureSiteRecoveryProvider. exe para um diretório local executando este comando:```AzureSiteRecoveryProvider.exe /x:. /q```
2. Os ```.\setupdr.exe /i``` resultados da execução são registrados em%ProgramData%\ASRLogs\DRASetupWizard.log.

3. Registre o servidor executando este comando:

    ```cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"```


## <a name="step-6-create-a-replication-policy"></a>Passo 6: Criar uma política de replicação

Antes de começar, observe que a conta de armazenamento especificada deve estar na mesma região do Azure que o cofre e deve ter a replicação geográfica habilitada.

1. Crie uma política de replicação da seguinte maneira:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

2. Verifique o trabalho retornado para garantir que a criação da política de replicação tenha sucesso.

3. Recupere o contêiner de proteção que corresponde ao site, da seguinte maneira:

        $protectionContainer = Get-AsrProtectionContainer
3. Associe o contêiner de proteção à política de replicação da seguinte maneira:

        $Policy = Get-AsrPolicy -FriendlyName $PolicyName
        $associationJob  = New-AsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
4. Aguarde até que o trabalho de associação seja concluído com êxito.

5. Recupere o mapeamento de contêiner de proteção.

        $ProtectionContainerMapping = Get-ASRProtectionContainerMapping -ProtectionContainer $protectionContainer

## <a name="step-7-enable-vm-protection"></a>Passo 7: Habilitar proteção de VM

1. Recupere o item de proteção que corresponde à VM que você deseja proteger, da seguinte maneira:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $ProtectableItem = Get-AsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. Proteja a VM. Se a VM que você está protegendo tiver mais de um disco anexado a ela, especifique o disco do sistema operacional usando o parâmetro *OSDiskName* .

        $OSType = "Windows"                                 # "Windows" or "Linux"
        $DRjob = New-AsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID

3. Aguarde até que as VMs alcancem um estado protegido após a replicação inicial. Isso pode demorar um pouco, dependendo de fatores como a quantidade de dados a serem replicados e a largura de banda upstream disponível para o Azure. Quando um estado protegido está em vigor, o estado do trabalho e StateDescription são atualizados da seguinte maneira:

        PS C:\> $DRjob = Get-AsrJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. Atualize as propriedades de recuperação (como o tamanho da função VM) e a rede do Azure à qual anexar a NIC da VM após o failover.

        PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $rpi = Get-AsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AsrReplicationProtectedItem --InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AsrJob -Job $UpdateJob

        PS C:\> $UpdateJob| select -ExpandProperty state
        Get-AsrJob -Job $job | select -ExpandProperty state

        Succeeded



## <a name="step-8-run-a-test-failover"></a>Passo 8: Executar uma ativação pós-falha de teste
1. Execute um failover de teste da seguinte maneira:

        $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $rpi = Get-AsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        $TFjob =Start-AsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Verifique se a VM de teste foi criada no Azure. O trabalho de failover de teste é suspenso após a criação da VM de teste no Azure.
3. Para limpar e concluir o failover de teste, execute:

        $TFjob = Start-AsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"

## <a name="next-steps"></a>Passos seguintes
[Saiba mais](https://docs.microsoft.com/powershell/module/az.recoveryservices) sobre Azure Site Recovery com Azure Resource Manager cmdlets do PowerShell.

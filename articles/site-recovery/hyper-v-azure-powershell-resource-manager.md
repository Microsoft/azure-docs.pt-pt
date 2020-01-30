---
title: Recuperação de desastre de VM Hyper-V usando Azure Site Recovery e PowerShell
description: Automatize a recuperação de desastre de VMs do Hyper-V no Azure com o serviço de Azure Site Recovery usando o PowerShell e o Azure Resource Manager.
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: 6499c986bef965848303ee9833fd59f5e3f0889c
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773423"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Configurar a recuperação de desastres para o Azure para VMs do Hyper-V usando o PowerShell e Azure Resource Manager

[Azure site Recovery](site-recovery-overview.md) contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) ao orquestrar a replicação, o failover e a recuperação de VMs (máquinas virtuais) do Azure e de servidores físicos e VMs locais.

Este artigo descreve como usar o Windows PowerShell, junto com o Azure Resource Manager, para replicar VMs do Hyper-V para o Azure. O exemplo usado neste artigo mostra como replicar uma única VM em execução em um host Hyper-V, para o Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell fornece cmdlets para gerenciar o Azure usando o Windows PowerShell. Site Recovery cmdlets do PowerShell, disponíveis com Azure PowerShell para Azure Resource Manager, ajudam você a proteger e recuperar seus servidores no Azure.

Você não precisa ser um especialista do PowerShell para usar este artigo, mas precisa entender os conceitos básicos, como módulos, cmdlets e sessões. Para mais informações, consulte a [Documentação PowerShell](/powershell) e utilizando a [Azure PowerShell com](../powershell-azure-resource-manager.md)o Gestor de Recursos Azure .

> [!NOTE]
> Os parceiros da Microsoft no programa CSP (provedor de soluções de nuvem) podem configurar e gerenciar a proteção de servidores de clientes para suas respectivas assinaturas de CSP (assinaturas de locatário).

## <a name="before-you-start"></a>Antes de começar

Verifique se você tem estes pré-requisitos em vigor:

- Uma conta de [Microsoft Azure](https://azure.microsoft.com/) . Pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). Além disso, você pode ler sobre os [preços do Azure site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
- Azure PowerShell. Para obter informações sobre esta versão e como instalá-la, consulte [install Azure PowerShell](/powershell/azure/install-az-ps).

Além disso, o exemplo específico descrito neste artigo tem os seguintes pré-requisitos:

- Um host Hyper-V executando o Windows Server 2012 R2 ou Microsoft Hyper-V Server 2012 R2 contendo uma ou mais VMs. Os servidores Hyper-V devem estar conectados à Internet, seja diretamente ou por meio de um proxy.
- As VMs que você deseja replicar devem estar em conformidade com [esses pré-requisitos](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Etapa 1: entrar em sua conta do Azure

1. Abra um console do PowerShell e execute este comando para entrar em sua conta do Azure. O cmdlet traz uma página web que lhe pede credenciais de conta: `Connect-AzAccount`.
   - Alternadamente, pode incluir as credenciais da sua conta como parâmetro no `Connect-AzAccount` cmdlet, utilizando o parâmetro **credencial.**
   - Se você é um parceiro csp trabalhando em nome de um inquilino, especifique o cliente como inquilino, usando o seu nome de domínio primário inquilino ou inquilino. Por exemplo: `Connect-AzAccount -Tenant "fabrikam.com"`
1. Associe a assinatura que você deseja usar com a conta, uma vez que uma conta pode ter várias assinaturas:

   ```azurepowershell
   Set-AzContext -Subscription $SubscriptionName
   ```

1. Verifique se sua assinatura está registrada para usar os provedores do Azure para serviços de recuperação e Site Recovery, usando estes comandos:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

1. Verifique se, na saída do comando, o **registrostate** está definido como **registrado**, você pode prosseguir para a etapa 2. Caso contrário, você deve registrar o provedor ausente em sua assinatura executando estes comandos:

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

1. Verifique se os provedores foram registrados com êxito usando os seguintes comandos:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

## <a name="step-2-set-up-the-vault"></a>Etapa 2: configurar o cofre

1. Crie um grupo de recursos Azure Resource Manager no qual criar o cofre ou use um grupo de recursos existente. Crie um novo grupo de recursos da seguinte maneira. A variável `$ResourceGroupName` contém o nome do grupo de recursos que pretende criar, e a variável $Geo contém a região de Azure para criar o grupo de recursos (por exemplo, "Brasil Sul").

   ```azurepowershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Geo
   ```

1. Para obter uma lista de grupos de recursos na sua subscrição, execute o `Get-AzResourceGroup` cmdlet.
1. Crie um novo cofre dos serviços de recuperação do Azure da seguinte maneira:

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>
   ```

Pode recuperar uma lista de cofres existentes com o `Get-AzRecoveryServicesVault` cmdlet.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Etapa 3: definir o contexto do cofre dos serviços de recuperação

Defina o contexto do cofre da seguinte maneira:

```azurepowershell
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="step-4-create-a-hyper-v-site"></a>Etapa 4: criar um site do Hyper-V

1. Crie um novo site do Hyper-V da seguinte maneira:

   ```azurepowershell
   $sitename = "MySite"                #Specify site friendly name
   New-AzRecoveryServicesAsrFabric -Type HyperVSite -Name $sitename
   ```

1. Esse cmdlet inicia um trabalho Site Recovery para criar o site e retorna um objeto de trabalho Site Recovery. Aguarde a conclusão do trabalho e verifique se o trabalho foi concluído com êxito.
1. Utilize o `Get-AzRecoveryServicesAsrJob` cmdlet para recuperar o objeto de trabalho e verifique o estado atual do trabalho.
1. Gere e Baixe uma chave de registro para o site, da seguinte maneira:

   ```azurepowershell
   $SiteIdentifier = Get-AzRecoveryServicesAsrFabric -Name $sitename | Select-Object -ExpandProperty SiteIdentifier
   $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
   ```

1. Copie a chave baixada para o host do Hyper-V. Você precisa da chave para registrar o host Hyper-V no site.

## <a name="step-5-install-the-provider-and-agent"></a>Etapa 5: instalar o provedor e o agente

1. Baixe o instalador para obter a versão mais recente do provedor da [Microsoft](https://aka.ms/downloaddra).
1. Execute o instalador no host do Hyper-V.
1. No final da instalação, continue na etapa de registro.
1. Quando solicitado, forneça a chave baixada e conclua o registro do host Hyper-V.
1. Verifique se o host Hyper-V está registrado no site da seguinte maneira:

   ```azurepowershell
   $server = Get-AzRecoveryServicesAsrFabric -Name $siteName | Get-AzRecoveryServicesAsrServicesProvider -FriendlyName $server-friendlyname
   ```

Se você estiver executando um servidor de núcleo do Hyper-V, baixe o arquivo de instalação e siga estas etapas:

1. Extraios os ficheiros do _AzureSiteRecoveryProvider.exe_ para um diretório local, executando este comando:

   ```console
   AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Execute o seguinte comando:

   ```console
   .\setupdr.exe /i
   ```

   Os resultados são registados em _%ProgramData%\ASRLogs\DRASetupWizard.log_.

1. Registre o servidor executando este comando:

   ```console
   cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
   ```

## <a name="step-6-create-a-replication-policy"></a>Etapa 6: criar uma política de replicação

Antes de começar, a conta de armazenamento especificada deve estar na mesma região de Azure que o cofre, e deve ter geo-replicação ativada.

1. Crie uma política de replicação da seguinte maneira:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $Recoverypoints = 6                    #specify the number of recovery points
   $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select-Object -ExpandProperty Id

   $PolicyResult = New-AzRecoveryServicesAsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds -NumberOfRecoveryPointsToRetain $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID
   ```

1. Verifique o trabalho retornado para garantir que a criação da política de replicação tenha sucesso.

1. Recupere o contêiner de proteção que corresponde ao site, da seguinte maneira:

   ```azurepowershell
   $protectionContainer = Get-AzRecoveryServicesAsrProtectionContainer
   ```

1. Associe o contêiner de proteção à política de replicação da seguinte maneira:

   ```azurepowershell
   $Policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $PolicyName
   $associationJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
   ```

1. Aguarde até que o trabalho de associação seja concluído com êxito.

1. Recupere o mapeamento de contêiner de proteção.

   ```azurepowershell
   $ProtectionContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $protectionContainer
   ```

## <a name="step-7-enable-vm-protection"></a>Etapa 7: habilitar a proteção de VM

1. Recupere o item de proteção que corresponde à VM que você deseja proteger, da seguinte maneira:

   ```azurepowershell
   $VMFriendlyName = "Fabrikam-app"          #Name of the VM
   $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
   ```

1. Proteja a VM. Se o VM que está a proteger tiver mais de um disco ligado ao mesmo, especifique o disco do sistema operativo utilizando o parâmetro **OSDiskName.**

   ```azurepowershell
   $OSType = "Windows"          # "Windows" or "Linux"
   $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID
   ```

1. Aguarde até que as VMs alcancem um estado protegido após a replicação inicial. Isso pode demorar um pouco, dependendo de fatores como a quantidade de dados a serem replicados e a largura de banda upstream disponível para o Azure. Quando um estado protegido está em vigor, o estado do trabalho e StateDescription são atualizados da seguinte maneira:

   ```console
   PS C:\> $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob

   PS C:\> $DRjob | Select-Object -ExpandProperty State
   Succeeded

   PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
   Completed
   ```

1. As propriedades de recuperação de atualizações (como o tamanho da função VM) e a rede Azure para anexar o VM NIC após a falha.

   ```console
   PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

   PS C:\> $VMFriendlyName = "Fabrikam-App"

   PS C:\> $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   PS C:\> $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

   PS C:\> $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

   PS C:\> $UpdateJob | Select-Object -ExpandProperty state

   PS C:\> Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state

   Succeeded
   ```

> [!NOTE]
> Se você quiser replicar em discos gerenciados habilitados para o CMK no Azure, execute as seguintes etapas usando AZ PowerShell 3.3.0 em diante:
>
> 1. Habilitar o failover para discos gerenciados atualizando as propriedades da VM
> 1. Utilize o `Get-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet para obter o ID do disco para cada disco do item protegido
> 1. Crie um objeto dicionário usando `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` cmdlet para conter o mapeamento do ID do disco para o conjunto de encriptação do disco. Esses conjuntos de criptografia de disco devem ser criados previamente por você na região de destino.
> 1. Atualize as propriedades VM utilizando `Set-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet, passando o objeto do dicionário no parâmetro **DiskIdToDiskEncryptionSetSetMap.**

## <a name="step-8-run-a-test-failover"></a>Etapa 8: executar um failover de teste

1. Execute um failover de teste da seguinte maneira:

   ```azurepowershell
   $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

   $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   $TFjob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
   ```

1. Verifique se a VM de teste foi criada no Azure. O trabalho de failover de teste é suspenso após a criação da VM de teste no Azure.
1. Para limpar e concluir o failover de teste, execute:

   ```azurepowershell
   $TFjob = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"
   ```

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](/powershell/module/az.recoveryservices) sobre Azure Site Recovery com Azure Resource Manager cmdlets do PowerShell.

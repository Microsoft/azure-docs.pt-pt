---
title: Recuperação de desastres em Hiper-V VM usando Azure Site Recovery e PowerShell
description: Automatizar a recuperação de desastres de Hiper-V VMs para Azure com o serviço de recuperação do local Azure utilizando o PowerShell e o Azure Resource Manager.
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: a4140a0b22f7ca8164d50cf60fe57c861f826eb4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86132515"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Configurar a recuperação após desastre para o Azure para VMs Hyper-V com o PowerShell e com o Azure Resource Manager

[A Azure Site Recovery](site-recovery-overview.md) contribui para a estratégia de continuidade do seu negócio e recuperação de desastres (BCDR), orquestrando replicação, failover e recuperação de máquinas virtuais Azure (VMs), e VMs no local e servidores físicos.

Este artigo descreve como usar o Windows PowerShell, juntamente com o Azure Resource Manager, para replicar VMs Hiper-V para Azure. O exemplo usado neste artigo mostra-lhe como replicar um único VM em execução num anfitrião Hiper-V, para Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

A Azure PowerShell fornece cmdlets para gerir o Azure utilizando o Windows PowerShell. Os cmdlets PowerShell de Recuperação de Sítios, disponíveis com a Azure PowerShell para Azure Resource Manager, ajudam-no a proteger e a recuperar os seus servidores em Azure.

Não é preciso ser um especialista em PowerShell para usar este artigo, mas precisa de compreender conceitos básicos, como módulos, cmdlets e sessões. Para obter mais informações, consulte a [Documentação PowerShell](/powershell) e [utilizar a Azure PowerShell com o Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).

> [!NOTE]
> Os parceiros da Microsoft no programa Cloud Solution Provider (CSP) podem configurar e gerir a proteção dos servidores dos clientes às respetivas subscrições CSP (subscrições de inquilinos).

## <a name="before-you-start"></a>Antes de começar

Certifique-se de ter estes pré-requisitos no lugar:

- Uma conta [Microsoft Azure.](https://azure.microsoft.com/) Pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). Além disso, pode ler sobre [os preços do Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
- Azure PowerShell. Para obter informações sobre esta versão e como instalá-la, consulte [instalar a Azure PowerShell](/powershell/azure/install-az-ps).

Além disso, o exemplo específico descrito neste artigo tem os seguintes pré-requisitos:

- Um anfitrião Hiper-V com o Windows Server 2012 R2 ou o Microsoft Hyper-V Server 2012 R2 contendo um ou mais VMs. Os servidores hiper-V devem ser ligados à Internet, diretamente ou através de um representante.
- Os VMs que pretende replicar devem estar em conformidade com [estes pré-requisitos](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Passo 1: Inscreva-se na sua conta Azure

1. Abra uma consola PowerShell e execute este comando para iniciar sação na sua conta Azure. O cmdlet traz uma página web que lhe solicita as credenciais da sua conta: `Connect-AzAccount` .
   - Alternadamente, pode incluir as suas credenciais de conta como parâmetro no `Connect-AzAccount` cmdlet, utilizando o parâmetro **Credencial.**
   - Se você é um parceiro da CSP que trabalha em nome de um inquilino, especifique o cliente como inquilino, usando o seu nome de domínio primário de inquilino ou inquilino. Por exemplo: `Connect-AzAccount -Tenant "fabrikam.com"`
1. Associe a subscrição que pretende utilizar com a conta, uma vez que uma conta pode ter várias subscrições:

   ```azurepowershell
   Set-AzContext -Subscription $SubscriptionName
   ```

1. Verifique se a sua subscrição está registada para utilizar os fornecedores Azure para serviços de recuperação e recuperação de locais, utilizando estes comandos:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

1. Verifique se na saída de comando, o **Estado de Registo** está definido para **Registrado,** pode proceder ao Passo 2. Caso contrário, deverá registar o fornecedor desaparecido na sua subscrição, executando estes comandos:

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

1. Verifique se os Fornecedores se registaram com sucesso, utilizando os seguintes comandos:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

## <a name="step-2-set-up-the-vault"></a>Passo 2: Montar o cofre

1. Crie um grupo de recursos Azure Resource Manager para criar o cofre ou utilize um grupo de recursos existente. Criar um novo grupo de recursos da seguinte forma. A `$ResourceGroupName` variável contém o nome do grupo de recursos que pretende criar, e a variável $Geo contém a região de Azure na qual se cria o grupo de recursos (por exemplo, "Brasil Sul").

   ```azurepowershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Geo
   ```

1. Para obter uma lista de grupos de recursos na sua subscrição, execute o `Get-AzResourceGroup` cmdlet.
1. Crie um novo cofre dos Serviços de Recuperação Azure da seguinte forma:

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>
   ```

Pode recuperar uma lista de cofres existentes com o `Get-AzRecoveryServicesVault` cmdlet.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Passo 3: Definir o contexto do cofre dos Serviços de Recuperação

Desa parte do contexto do cofre da seguinte forma:

```azurepowershell
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="step-4-create-a-hyper-v-site"></a>Passo 4: Criar um site hiper-V

1. Crie um novo site Hiper-V da seguinte forma:

   ```azurepowershell
   $sitename = "MySite"                #Specify site friendly name
   New-AzRecoveryServicesAsrFabric -Type HyperVSite -Name $sitename
   ```

1. Este cmdlet inicia um trabalho de Recuperação de Sítio para criar o site e devolve um objeto de trabalho de Recuperação do Local. Aguarde que o trabalho termine e verifique se o trabalho foi concluído com sucesso.
1. Utilize o `Get-AzRecoveryServicesAsrJob` cmdlet para recuperar o objeto de trabalho e verifique o estado atual do trabalho.
1. Gere uma chave de registo para o site, da seguinte forma:

   ```azurepowershell
   $SiteIdentifier = Get-AzRecoveryServicesAsrFabric -Name $sitename | Select-Object -ExpandProperty SiteIdentifier
   $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
   ```

1. Copie a chave descarregada para o anfitrião Hyper-V. Precisa da chave para registar o anfitrião Hyper-V no site.

## <a name="step-5-install-the-provider-and-agent"></a>Passo 5: Instalar o Fornecedor e o agente

1. Descarregue o instalador para a versão mais recente do Fornecedor da [Microsoft](https://aka.ms/downloaddra).
1. Executar o instalador no anfitrião Hyper-V.
1. No final da instalação continue a registar-se.
1. Quando solicitado, forneça a chave descarregada e complete o registo do anfitrião Hyper-V.
1. Verifique se o anfitrião Hyper-V está registado no site da seguinte forma:

   ```azurepowershell
   $server = Get-AzRecoveryServicesAsrFabric -Name $siteName | Get-AzRecoveryServicesAsrServicesProvider -FriendlyName $server-friendlyname
   ```

Se estiver a executar um servidor central Hyper-V, descarregue o ficheiro de configuração e siga estes passos:

1. Extrair os ficheiros de _AzureSiteRecoveryProvider.exe_ para um diretório local executando este comando:

   ```console
   AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Execute o seguinte comando:

   ```console
   .\setupdr.exe /i
   ```

   Os resultados são registados em _%ProgramData%\ASRLogs\DRASetupWizard.log_.

1. Registar o servidor executando este comando:

   ```console
   cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
   ```

## <a name="step-6-create-a-replication-policy"></a>Passo 6: Criar uma política de replicação

Antes de começar, a conta de armazenamento especificada deve estar na mesma região de Azure que o cofre, e deve ter a geo-replicação ativada.

1. Criar uma política de replicação da seguinte forma:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $Recoverypoints = 6                    #specify the number of recovery points
   $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select-Object -ExpandProperty Id

   $PolicyResult = New-AzRecoveryServicesAsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds -NumberOfRecoveryPointsToRetain $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID
   ```

1. Verifique o trabalho devolvido para garantir que a criação da política de replicação seja bem sucedida.

1. Recupere o recipiente de proteção que corresponde ao local, da seguinte forma:

   ```azurepowershell
   $protectionContainer = Get-AzRecoveryServicesAsrProtectionContainer
   ```

1. Associar o recipiente de proteção à política de replicação, da seguinte forma:

   ```azurepowershell
   $Policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $PolicyName
   $associationJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
   ```

1. Espere que o trabalho de associação termine com sucesso.

1. Recupere o mapeamento do recipiente de proteção.

   ```azurepowershell
   $ProtectionContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $protectionContainer
   ```

## <a name="step-7-enable-vm-protection"></a>Passo 7: Ativar a proteção VM

1. Recupere o item protegido que corresponde ao VM que pretende proteger, da seguinte forma:

   ```azurepowershell
   $VMFriendlyName = "Fabrikam-app"          #Name of the VM
   $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
   ```

1. Proteja o VM. Se o VM que está a proteger tiver mais do que um disco ligado a ele, especifique o disco do sistema operativo utilizando o parâmetro **OSDiskName.**

   ```azurepowershell
   $OSType = "Windows"          # "Windows" or "Linux"
   $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID
   ```

1. Aguarde que os VMs atinjam um estado protegido após a replicação inicial. Isto pode demorar algum tempo, dependendo de fatores como a quantidade de dados a replicar, e a largura de banda disponível a montante para Azure. Quando um estado protegido está em vigor, o estado de trabalho e a descrição do Estado são atualizados da seguinte forma:

   ```console
   PS C:\> $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob

   PS C:\> $DRjob | Select-Object -ExpandProperty State
   Succeeded

   PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
   Completed
   ```

1. Atualize as propriedades de recuperação (como o tamanho da função VM) e a rede Azure à qual se ligam o VM NIC após a falha.

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
> Se pretender replicar os discos geridos com ativação CMK em Azure, faça os seguintes passos utilizando a az PowerShell 3.3.0 em diante:
>
> 1. Permitir falhas em discos geridos atualizando propriedades VM
> 1. Utilize o `Get-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet para obter o ID do disco para cada disco do item protegido
> 1. Crie um objeto dicionário utilizando `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` o cmdlet para conter o mapeamento do ID do disco para conjunto de encriptação de disco. Estes conjuntos de encriptação de discos devem ser pré-criados por si na região alvo.
> 1. Atualize as propriedades VM utilizando `Set-AzRecoveryServicesAsrReplicationProtectedItem` o cmdlet passando o objeto do dicionário no parâmetro **DiskIdToDiskEncryptionSetMap.**

## <a name="step-8-run-a-test-failover"></a>Passo 8: Executar um teste de failover

1. Executar um teste de failover da seguinte forma:

   ```azurepowershell
   $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

   $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   $TFjob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
   ```

1. Verifique se o VM de teste é criado em Azure. O trabalho de failover do teste é suspenso após a criação do VM de teste em Azure.
1. Para limpar e completar o teste de failover, corra:

   ```azurepowershell
   $TFjob = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"
   ```

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](/powershell/module/az.recoveryservices) sobre a Recuperação do Site Azure com o Azure Resource Manager PowerShell cmdlets.

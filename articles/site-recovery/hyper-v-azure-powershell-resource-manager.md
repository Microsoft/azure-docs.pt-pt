---
title: Recuperação de desastres de VM hiper-V usando recuperação de site azure e PowerShell
description: Automatizar a recuperação de desastres de VMs Hiper-V para Azure com o serviço de recuperação de sítios Azure utilizando o PowerShell e o Azure Resource Manager.
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: 6499c986bef965848303ee9833fd59f5e3f0889c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79257995"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Configurar a recuperação de desastres para o Azure para VMs hiper-V utilizando o PowerShell e o Azure Resource Manager

[A Recuperação](site-recovery-overview.md) do Site Azure contribui para a continuidade do seu negócio e estratégia de recuperação de desastres (BCDR), orquestrando replicação, failover e recuperação de máquinas virtuais Azure (VMs), e VMs no local e servidores físicos.

Este artigo descreve como usar o Windows PowerShell, juntamente com o Azure Resource Manager, para replicar VMs Hiper-V para Azure. O exemplo usado neste artigo mostra-lhe como replicar um único VM a correr num hospedeiro Hyper-V, para o Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

A Azure PowerShell fornece cmdlets para gerir o Azure utilizando o Windows PowerShell. O Site Recovery PowerShell cmdlets, disponível com o Azure PowerShell para o Azure Resource Manager, ajudam-no a proteger e a recuperar os seus servidores em Azure.

Não é preciso ser um especialista da PowerShell para usar este artigo, mas precisa de compreender conceitos básicos, como módulos, cmdlets e sessões. Para mais informações, consulte a [Documentação PowerShell](/powershell) e utilizando a [Azure PowerShell com](../powershell-azure-resource-manager.md)o Gestor de Recursos Azure .

> [!NOTE]
> Os parceiros da Microsoft no programa Cloud Solution Provider (CSP) podem configurar e gerir a proteção dos servidores dos clientes às respetivas subscrições de CSP (subscrições de inquilinos).

## <a name="before-you-start"></a>Antes de começar

Certifique-se de que dispõe destes pré-requisitos:

- Uma conta [Microsoft Azure.](https://azure.microsoft.com/) Pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). Além disso, pode ler sobre os preços do Gestor de Recuperação do [Site Azure.](https://azure.microsoft.com/pricing/details/site-recovery/)
- Azure PowerShell. Para obter informações sobre esta versão e como instalá-la, consulte [Instalar o Azure PowerShell](/powershell/azure/install-az-ps).

Além disso, o exemplo específico descrito neste artigo tem os seguintes pré-requisitos:

- Um anfitrião Hyper-V que executa o Windows Server 2012 R2 ou o Microsoft Hyper-V Server 2012 R2 contendo um ou mais VMs. Os servidores hiper-V devem ser ligados à Internet, quer diretamente quer através de um proxy.
- Os VMs que pretende replicar devem estar em conformidade com [estes pré-requisitos](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Passo 1: Inscreva-se na sua conta Azure

1. Abra uma consola PowerShell e execute este comando para iniciar sessão na sua conta Azure. O cmdlet traz uma página web que lhe `Connect-AzAccount`pede credenciais de conta: .
   - Alternadamente, pode incluir as credenciais da `Connect-AzAccount` sua conta como parâmetro no cmdlet, utilizando o parâmetro **credencial.**
   - Se você é um parceiro csp trabalhando em nome de um inquilino, especifique o cliente como inquilino, usando o seu nome de domínio primário inquilino ou inquilino. Por exemplo: `Connect-AzAccount -Tenant "fabrikam.com"`
1. Associe a subscrição que pretende utilizar com a conta, uma vez que uma conta pode ter várias subscrições:

   ```azurepowershell
   Set-AzContext -Subscription $SubscriptionName
   ```

1. Verifique se a sua subscrição está registada para utilizar os fornecedores Azure para Serviços de Recuperação e Recuperação do Site, utilizando estes comandos:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

1. Verifique se na saída de comando, o **Estado de Registo** está definido para **Registro,** pode seguir para o Passo 2. Caso contrário, deverá registar o fornecedor em falta na sua subscrição, executando estes comandos:

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

1. Verifique se os Fornecedores registaram com sucesso, utilizando os seguintes comandos:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

## <a name="step-2-set-up-the-vault"></a>Passo 2: Montar o cofre

1. Crie um grupo de recursos do Gestor de Recursos Azure para criar o cofre, ou use um grupo de recursos existente. Crie um novo grupo de recursos da seguinte forma. A `$ResourceGroupName` variável contém o nome do grupo de recursos que pretende criar, e a variável $Geo contém a região de Azure para criar o grupo de recursos (por exemplo, "Brasil Sul").

   ```azurepowershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Geo
   ```

1. Para obter uma lista de grupos `Get-AzResourceGroup` de recursos na sua subscrição, execute o cmdlet.
1. Crie um novo cofre dos Serviços de Recuperação Azure da seguinte forma:

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>
   ```

Pode recuperar uma lista de cofres `Get-AzRecoveryServicesVault` existentes com o cmdlet.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Passo 3: Definir o contexto do cofre dos Serviços de Recuperação

Desloque o contexto do cofre da seguinte forma:

```azurepowershell
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="step-4-create-a-hyper-v-site"></a>Passo 4: Criar um site Hyper-V

1. Crie um novo site Hyper-V da seguinte forma:

   ```azurepowershell
   $sitename = "MySite"                #Specify site friendly name
   New-AzRecoveryServicesAsrFabric -Type HyperVSite -Name $sitename
   ```

1. Este cmdlet inicia um trabalho de Recuperação do Site para criar o site, e devolve um objeto de trabalho de Recuperação do Site. Aguarde que o trabalho esteja concluído e verifique se o trabalho foi concluído com sucesso.
1. Utilize `Get-AzRecoveryServicesAsrJob` o cmdlet para recuperar o objeto de trabalho e verifique o estado atual do trabalho.
1. Gere e descarregue uma chave de registo para o site, da seguinte forma:

   ```azurepowershell
   $SiteIdentifier = Get-AzRecoveryServicesAsrFabric -Name $sitename | Select-Object -ExpandProperty SiteIdentifier
   $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
   ```

1. Copie a chave descarregada para o hospedeiro Hyper-V. Precisa da chave para registar o hospedeiro Hyper-V no site.

## <a name="step-5-install-the-provider-and-agent"></a>Passo 5: Instalar o Fornecedor e o agente

1. Descarregue o instalador para a versão mais recente do Fornecedor da [Microsoft](https://aka.ms/downloaddra).
1. Executar o instalador no hospedeiro Hyper-V.
1. No final da instalação, continue até à etapa de registo.
1. Quando solicitado, forneça a chave descarregada e o registo completo do hospedeiro Hyper-V.
1. Verifique se o hospedeiro Hyper-V está registado no site da seguinte forma:

   ```azurepowershell
   $server = Get-AzRecoveryServicesAsrFabric -Name $siteName | Get-AzRecoveryServicesAsrServicesProvider -FriendlyName $server-friendlyname
   ```

Se estiver a executar um servidor central Hyper-V, descarregue o ficheiro de configuração e siga estes passos:

1. Extraios os ficheiros do _AzureSiteRecoveryProvider.exe_ para um diretório local, executando este comando:

   ```console
   AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Execute o seguinte comando:

   ```console
   .\setupdr.exe /i
   ```

   Os resultados são registados em _%ProgramData%\ASRLogs\DRASetupWizard.log_.

1. Registe o servidor executando este comando:

   ```console
   cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
   ```

## <a name="step-6-create-a-replication-policy"></a>Passo 6: Criar uma política de replicação

Antes de começar, a conta de armazenamento especificada deve estar na mesma região de Azure que o cofre, e deve ter geo-replicação ativada.

1. Crie uma política de replicação da seguinte forma:

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

1. Associe o recipiente de proteção à política de replicação, da seguinte forma:

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

1. Recupere o item protectável que corresponde ao VM que pretende proteger, da seguinte forma:

   ```azurepowershell
   $VMFriendlyName = "Fabrikam-app"          #Name of the VM
   $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
   ```

1. Proteja o VM. Se o VM que está a proteger tiver mais de um disco ligado ao mesmo, especifique o disco do sistema operativo utilizando o parâmetro **OSDiskName.**

   ```azurepowershell
   $OSType = "Windows"          # "Windows" or "Linux"
   $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID
   ```

1. Aguarde que os VMs cheguem a um estado protegido após a replicação inicial. Isto pode demorar algum tempo, dependendo de fatores como a quantidade de dados a replicar, e a largura de banda disponível a montante para Azure. Quando um estado protegido está em vigor, o estado de trabalho e a descrição do Estado são atualizados da seguinte forma:

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
> Se desejar replicar discos geridos pela CMK em Azure, faça os seguintes passos utilizando a Az PowerShell 3.3.0 em diante:
>
> 1. Ativar a falha nos discos geridos atualizando propriedades VM
> 1. Utilize `Get-AzRecoveryServicesAsrReplicationProtectedItem` o cmdlet para obter o ID do disco para cada disco do item protegido
> 1. Crie um objeto `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` de dicionário usando cmdlet para conter o mapeamento do ID do disco para o conjunto de encriptação do disco. Estes conjuntos de encriptação de discos devem ser pré-criados por si na região alvo.
> 1. Atualize as propriedades `Set-AzRecoveryServicesAsrReplicationProtectedItem` VM utilizando cmdlet, passando o objeto do dicionário no parâmetro **DiskIdToDiskEncryptionSetSetMap.**

## <a name="step-8-run-a-test-failover"></a>Passo 8: Executar um teste failover

1. Executar um teste failover da seguinte forma:

   ```azurepowershell
   $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

   $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   $TFjob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
   ```

1. Verifique se o VM de teste é criado em Azure. O trabalho de failover do teste é suspenso após a criação do VM de teste em Azure.
1. Para limpar e completar a falha do teste, corra:

   ```azurepowershell
   $TFjob = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"
   ```

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](/powershell/module/az.recoveryservices) sobre a Recuperação do Site Azure com o Gestor de Recursos Azure PowerShell cmdlets.

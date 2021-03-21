---
title: Use powerShell para apoiar cargas de trabalho de DPM
description: Saiba como implementar e gerir o Azure Backup para o Gestor de Proteção de Dados (DPM) utilizando o PowerShell
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 176cbffe5152462055c4ffdb2367cf9c0ab97c1f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90968296"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Implementar e gerir cópias de segurança para o Azure em servidores do Data Protection Manager (DPM) com o PowerShell

Este artigo mostra-lhe como usar o PowerShell para configurar o Azure Backup num servidor DPM e para gerir a cópia de segurança e a recuperação.

## <a name="setting-up-the-powershell-environment"></a>Criação do ambiente PowerShell

Antes de utilizar o PowerShell para gerir as cópias de segurança do Data Protection Manager para o Azure, precisa de ter o ambiente certo no PowerShell. No início da sessão PowerShell, certifique-se de que executou o seguinte comando para importar os módulos certos e permitir-lhe fazer referência correta aos cmdlets DPM:

```powershell
& "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"
```

```Output
Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Configuração e Registo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para começar, [descarregue o mais recente Azure PowerShell](/powershell/azure/install-az-ps).

As seguintes tarefas de configuração e registo podem ser automatizadas com a PowerShell:

* Criar um cofre dos Serviços de Recuperação 
* Instalação do agente Azure Backup
* Registo com o serviço de backup Azure
* Definições de rede
* Definições de encriptação

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Os passos seguintes levam-no através da criação de um cofre dos Serviços de Recuperação. Um cofre dos Serviços de Recuperação é diferente de um cofre de reserva.

1. Se estiver a utilizar o Azure Backup pela primeira vez, tem de utilizar o **cmdlet Register-AzResourceProvider** para registar o fornecedor do Serviço de Recuperação Azure com a sua subscrição.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. O cofre dos Serviços de Recuperação é um recurso ARM, pelo que é necessário colocá-lo dentro de um Grupo de Recursos. Pode utilizar um grupo de recursos existente ou criar um novo. Ao criar um novo grupo de recursos, especifique o nome e a localização do grupo de recursos.

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Utilize o **cmdlet New-AzRecoveryServicesVault** para criar um novo cofre. Certifique-se de especificar o mesmo local para o cofre que foi usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Especifique o tipo de redundância de armazenamento a utilizar. Pode utilizar [armazenamento localmente redundante (LRS),](../storage/common/storage-redundancy.md#locally-redundant-storage) [armazenamento geo-redundante (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)ou [armazenamento redundante de zona (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage). O exemplo a seguir mostra a opção **BackupStorageRedundancy** para *o testVault*  definido para **GeoRedundant**.

   > [!TIP]
   > Muitos cmdlets do Azure Backup requerem o objeto do cofre dos Serviços de Recuperação como entrada. Por esta razão, é conveniente armazenar o objeto do cofre dos Serviços de Recuperação de Backup numa variável.
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Ver os cofres numa subscrição

Utilize **o Get-AzRecoveryServicesVault** para ver a lista de todos os cofres da subscrição atual. Pode usar este comando para verificar se foi criado um novo cofre, ou para ver que cofres estão disponíveis na subscrição.

Executar o comando, Get-AzRecoveryServicesVault, e todos os cofres da subscrição estão listados.

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Instalação do agente de backup Azure num Servidor DPM

Antes de instalar o agente Azure Backup, precisa de ter o instalador descarregado e presente no Servidor do Windows. Pode obter a versão mais recente do instalador a partir do [Microsoft Download Center](https://aka.ms/azurebackup_agent) ou da página dashboard do cofre dos Serviços de Recuperação. Guarde o instalador para um local facilmente acessível, como `C:\Downloads\*` .

Para instalar o agente, executar o seguinte comando numa consola PowerShell elevada **no servidor DPM:**

```powershell
MARSAgentInstaller.exe /q
```

Isto instala o agente com todas as opções predefinidos. A instalação demora alguns minutos em segundo plano. Se não especificar a opção */nu,* a janela **Do Windows Update** abre no final da instalação para verificar se há atualizações.

O agente aparece na lista de programas instalados. Para ver a lista de programas instalados, vá a Programas e Funcionalidades de **Programas do Painel de**  >    >  Controlo.

![Agente instalado](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Opções de instalação

Para ver todas as opções disponíveis através da linha de comando, utilize o seguinte comando:

```powershell
MARSAgentInstaller.exe /?
```

As opções disponíveis incluem:

| Opção | Detalhes | Predefinição |
| --- | --- | --- |
| /q |Instalação silenciosa |- |
| /p:"Localização" |Caminho para a pasta de instalação para o agente de backup Azure. |C:\Ficheiros de programa\Microsoft Azure Recovery Services Agent |
| /s:"localização" |Caminho para a pasta de cache para o agente Azure Backup. |C:\Ficheiros de programa\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Opt-in para Microsoft Update |- |
| /nu |Não Verifique as atualizações após a conclusão da instalação |- |
| /d |Desinstala o Agente de Serviços de Recuperação do Microsoft Azure |- |
| /ph |Endereço de anfitrião proxy |- |
| /po |Número de porta de anfitrião proxy |- |
| /pu |Nome de utilizador do anfitrião proxy |- |
| /pw |Senha de procuração |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Registo de DPM num cofre dos Serviços de Recuperação

Depois de criar o cofre dos Serviços de Recuperação, descarregue as credenciais mais recentes e as credenciais do cofre e guarde-o num local conveniente como C:\Downloads.

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

No servidor DPM, executar o [cmdlet Start-OBRegistration](/powershell/module/msonlinebackup/start-obregistration) para registar a máquina com o cofre.

```powershell
$cred = $credspath + $credsfilename
Start-OBRegistration-VaultCredentials $cred -Confirm:$false
```

```Output
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Definições iniciais de configuração

Uma vez registado o DPM Server no cofre dos Serviços de Recuperação, começa com as definições de subscrição predefinidos. Estas definições de subscrição incluem Networking, Encryption e a área de Encenação. Para alterar as definições de subscrição, é necessário obter primeiro uma pega nas definições existentes (predefinidos) utilizando o cmdlet [Get-DPMCloudSubscriptionSetting:](/powershell/module/dataprotectionmanager/get-dpmcloudsubscriptionsetting)

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Todas as modificações são feitas neste objeto Local PowerShell ```$setting``` e, em seguida, o objeto completo é comprometido com DPM e Azure Backup para salvá-los usando o [cmdlet set-DPMCloudSubscriptionSetting.](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) É necessário utilizar a ```–Commit``` bandeira para garantir que as alterações são persistidos. As definições não serão aplicadas e utilizadas pela Azure Backup a menos que sejam comprometidas.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Rede

Se a conectividade da máquina DPM com o serviço de Backup Azure na internet for através de um servidor proxy, então as definições do servidor proxy devem ser fornecidas para cópias de segurança bem sucedidas. Isto é feito utilizando o ```-ProxyServer``` ```-ProxyPort``` e, ```-ProxyUsername``` e os ```ProxyPassword``` parâmetros com o [conjunto-DPMCloudSubscriptionSettingSetting.](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) Neste exemplo, não há nenhum servidor proxy, por isso estamos a limpar explicitamente qualquer informação relacionada com procuração.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

O uso de largura de banda também pode ser controlado com opções de ```-WorkHourBandwidth``` e para um determinado conjunto de dias da ```-NonWorkHourBandwidth``` semana. Neste exemplo, não estamos a fazer qualquer estrangulamento.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Configurar a área de encenação

O agente Azure Backup em execução no servidor DPM necessita de armazenamento temporário para dados restaurados a partir da nuvem (área de paragem local). Configure a área de preparação utilizando o [cmdlet set-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) e o ```-StagingAreaPath``` parâmetro.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

No exemplo acima, a área de preparação será definida para *C:\StagingArea* no objeto PowerShell ```$setting``` . Certifique-se de que a pasta especificada já existe, ou então o compromisso final das definições de subscrição falhará.

### <a name="encryption-settings"></a>Definições de encriptação

Os dados de backup enviados para a Azure Backup são encriptados para proteger a confidencialidade dos dados. A palavra-passe de encriptação é a "palavra-passe" para desencriptar os dados no momento da restauração. É importante manter esta informação segura uma vez definida.

No exemplo abaixo, o primeiro comando converte a corda ```passphrase123456789``` numa corda segura e atribui a cadeia de segurança à variável denominada ```$Passphrase``` . o segundo comando define a cadeia de segurança ```$Passphrase``` como a palavra-passe para encriptar cópias de segurança.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Mantenha a palavra-passe segura e segura uma vez definida. Não conseguirá restaurar os dados de Azure sem esta frase.
>
>

Neste momento, deveria ter feito todas as alterações necessárias ao ```$setting``` objeto. Lembre-se de cometer as mudanças.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Proteja os dados para a Azure Backup

Nesta secção, irá adicionar um servidor de produção ao DPM e, em seguida, proteger os dados para o armazenamento local de DPM e, em seguida, para Azure Backup. Nos exemplos, vamos demonstrar como fazer o back up ficheiros e pastas. A lógica pode ser facilmente estendida para fazer backup de qualquer fonte de dados suportada pelo DPM. Todas as suas cópias de segurança DPM são regidas por um Grupo de Proteção (PG) com quatro partes:

1. **Membros do grupo** é uma lista de todos os objetos protegidos (também conhecidos como *Datasources* em DPM) que pretende proteger no mesmo grupo de proteção. Por exemplo, é possível que pretenda proteger os VMs de produção numa base de dados de um grupo de proteção e de um servidor SQL noutro grupo de proteção, uma vez que podem ter diferentes requisitos de backup. Antes de conseguir fazer o backsource de qualquer fonte de dados num servidor de produção, tem de se certificar de que o Agente DPM está instalado no servidor e é gerido por DPM. Siga os passos para [instalar o Agente DPM](/system-center/dpm/deploy-dpm-protection-agent) e ligue-o ao Servidor DPM apropriado.
2. **O método de proteção de dados** especifica os locais de backup do alvo - fita, disco e nuvem. No nosso exemplo, protegeremos os dados para o disco local e para a nuvem.
3. Um **agenda de backup** que especifica quando as cópias de segurança precisam de ser tomadas e com que frequência os dados devem ser sincronizados entre o DPM Server e o servidor de produção.
4. Um **calendário de retenção** que especifica quanto tempo para manter os pontos de recuperação em Azure.

### <a name="creating-a-protection-group"></a>Criar um grupo de proteção

Comece por criar um novo Grupo de Proteção utilizando o [cmdlet new-DProtectionGroup.](/powershell/module/dataprotectionmanager/new-dpmprotectiongroup)

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

O cmdlet acima irá criar um Grupo de Proteção chamado *ProtectGroup01*. Um grupo de proteção existente também pode ser modificado mais tarde para adicionar cópia de segurança à nuvem Azure. No entanto, para escruputor de quaisquer alterações ao Grupo de Proteção - novo ou existente - precisamos de obter uma pega num objeto *modificável* utilizando o cmdlet [Get-DPMModificableProtectionGroup.](/powershell/module/dataprotectionmanager/get-dpmmodifiableprotectiongroup)

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Adicionar membros do grupo ao Grupo de Proteção

Cada Agente DPM conhece a lista de fontes de dados no servidor em que está instalado. Para adicionar uma fonte de dados ao Grupo de Proteção, o Agente DPM precisa primeiro enviar uma lista das fontes de dados de volta para o servidor DPM. Um ou mais recursos de dados são então selecionados e adicionados ao Grupo de Proteção. Os passos powerShell necessários para o conseguir são:

1. Pegue uma lista de todos os servidores geridos pela DPM através do Agente DPM.
2. Escolha um servidor específico.
3. Pegue uma lista de todos os recursos de dados no servidor.
4. Escolha um ou mais fontes de dados e adicione-as ao Grupo de Proteção

A lista de servidores em que o DPM Agent está instalado e está a ser gerido pelo DPM Server é adquirida com o [cmdlet Get-DPMProductionServer.](/powershell/module/dataprotectionmanager/get-dpmproductionserver) Neste exemplo, filtraremos e configuraremos apenas o PowerShell com o nome *de produção produção01* para cópia de segurança.

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains "productionserver01"}
```

Agora obtenha a lista de fontes de dados na ```$server``` utilização do cmdlet [Get-DPMDatasource.](/powershell/module/dataprotectionmanager/get-dpmdatasource) Neste exemplo estamos a filtrar o volume `D:\` que queremos configurar para o backup. Esta fonte de dados é então adicionada ao Grupo de Proteção utilizando o cmdlet [Add-DPMChildDatasource.](/powershell/module/dataprotectionmanager/add-dpmchilddatasource) Lembre-se de utilizar o objeto de grupo de proteção *modificável* ```$MPG``` para fazer as adições.

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains "D:\" }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Repita este passo quantas vezes for necessário, até adicionar todas as fontes de dados escolhidas ao grupo de proteção. Também pode começar com apenas uma fonte de dados, e completar o fluxo de trabalho para criar o Grupo de Proteção, e posteriormente adicionar mais fontes de dados ao Grupo de Proteção.

### <a name="selecting-the-data-protection-method"></a>Selecionando o método de proteção de dados

Uma vez adicionadas as fontes de dados ao Grupo de Proteção, o próximo passo é especificar o método de proteção utilizando o [cmdlet Set-DProtectionType.](/powershell/module/dataprotectionmanager/set-dpmprotectiontype) Neste exemplo, o Grupo de Proteção está configurado para o disco local e para a cópia de segurança na nuvem. Também precisa de especificar a fonte de dados que pretende proteger para a nuvem usando o [cmdlet Add-DPMChildDatasource](/powershell/module/dataprotectionmanager/add-dpmchilddatasource) com -Bandeira online.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Definir a gama de retenção

Desajecto a retenção para os pontos de backup utilizando o [cmdlet Set-DPMPolicyObjective.](/powershell/module/dataprotectionmanager/set-dpmpolicyobjective) Embora possa parecer estranho definir a retenção antes de o calendário de backup ter sido definido, a utilização do ```Set-DPMPolicyObjective``` cmdlet define automaticamente um horário de backup predefinido que pode ser modificado. É sempre possível definir o horário de reserva primeiro e a política de retenção depois.

No exemplo abaixo, o cmdlet define os parâmetros de retenção para cópias de segurança do disco. Isto irá reter cópias de segurança durante 10 dias e sincronizar dados a cada 6 horas entre o servidor de produção e o servidor DPM. O ```SynchronizationFrequencyMinutes``` não define a frequência com que um ponto de backup é criado, mas quantas vezes os dados são copiados para o servidor DPM.  Esta definição evita que as cópias de segurança se tornem demasiado grandes.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Para cópias de segurança que vão para Azure (DPM refere-se a eles como backups online) as gamas de retenção podem ser configuradas para [retenção a longo prazo usando um esquema Avô-Pai-Filho (GFS)](backup-azure-backup-cloud-as-tape.md). Ou seja, pode definir uma política de retenção combinada que envolva políticas de retenção diárias, semanais, mensais e anor as. Neste exemplo, criamos uma matriz que representa o complexo esquema de retenção que queremos e, em seguida, configuramos a gama de retenção utilizando o [cmdlet Set-DPMPolicyObjective.](/powershell/module/dataprotectionmanager/set-dpmpolicyobjective)

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Desa parte do horário de backup

O DPM define automaticamente um horário de backup predefinido se especificar o objetivo de proteção utilizando o ```Set-DPMPolicyObjective``` cmdlet. Para alterar os horários predefinidos, utilize o cmdlet [Get-DPMPolicySchedule](/powershell/module/dataprotectionmanager/get-dpmpolicyschedule) seguido do [cmdlet Set-DPMPolicySchedule.](/powershell/module/dataprotectionmanager/set-dpmpolicyschedule)

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

No exemplo acima, ```$onlineSch``` encontra-se um conjunto com quatro elementos que contém o calendário de proteção online existente para o Grupo de Proteção no regime GFS:

1. ```$onlineSch[0]``` contém a agenda diária
2. ```$onlineSch[1]``` contém o horário semanal
3. ```$onlineSch[2]``` contém o horário mensal
4. ```$onlineSch[3]``` contém o horário anual

Portanto, se precisar de modificar o horário semanal, tem de se referir ao ```$onlineSch[1]``` .

### <a name="initial-backup"></a>Cópia de segurança inicial

Ao fazer o backup de uma fonte de dados pela primeira vez, o DPM precisa de criar uma réplica inicial que cria uma cópia completa do dado fonte a ser protegida no volume de réplica de DPM. Esta atividade pode ser programada para um horário específico, ou pode ser ativada manualmente, utilizando o [cmdlet Set-DPMReplicaCreationMethod](/powershell/module/dataprotectionmanager/set-dpmreplicacreationmethod) com o parâmetro ```-NOW``` .

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Alterar o tamanho da réplica DPM & volume de ponto de recuperação

Também pode alterar o tamanho do volume de réplica DPM e volume de cópia-sombra utilizando [o cmdlet Set-DPMDatasourceDiskAllocation](/powershell/module/dataprotectionmanager/set-dpmdatasourcediskallocation) como no seguinte exemplo: Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### <a name="committing-the-changes-to-the-protection-group"></a>Emissão das alterações ao Grupo de Proteção

Finalmente, as alterações têm de ser cometidas antes que o DPM possa receber a cópia de segurança de acordo com a nova configuração do Grupo de Proteção. Isto pode ser conseguido usando o [cmdlet Set-DPMProtectionGroup.](/powershell/module/dataprotectionmanager/set-dpmprotectiongroup)

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>Ver os pontos de backup

Pode utilizar o [cmdlet Get-DPMRecoveryPoint](/powershell/module/dataprotectionmanager/get-dpmrecoverypoint) para obter uma lista de todos os pontos de recuperação para uma fonte de dados. Neste exemplo, iremos:

* buscar todos os PGs no servidor DPM e armazenados em uma matriz ```$PG```
* obter as fontes de dados correspondentes ao ```$PG[0]```
* obter todos os pontos de recuperação para uma fonte de dados.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Restaurar os dados protegidos no Azure

Restaurar dados é uma combinação de um ```RecoverableItem``` objeto e um ```RecoveryOption``` objeto. Na secção anterior, temos uma lista dos pontos de backup de um dado.

No exemplo abaixo, demonstramos como restaurar uma máquina virtual Hyper-V do Azure Backup, combinando pontos de backup com o alvo para a recuperação. Este exemplo inclui:

* Criar uma opção de recuperação utilizando o cmdlet [New-DPMRecoveryOption.](/powershell/module/dataprotectionmanager/new-dpmrecoveryoption)
* Buscar a matriz de pontos de reserva utilizando o ```Get-DPMRecoveryPoint``` cmdlet.
* Escolhendo um ponto de reserva para restaurar.

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation "C:\VMRecovery"

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Os comandos podem ser facilmente estendidos para qualquer tipo de fonte de dados.

## <a name="next-steps"></a>Passos seguintes

* Para mais informações sobre DPM para Azure Backup consulte [Introdução ao Backup DPM](backup-azure-dpm-introduction.md)
